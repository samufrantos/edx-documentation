.. _Open edX Maple Release:

######################
Open edX Maple Release
######################

These are the release notes for the Maple release, the 13th community release of the Open edX Platform, spanning changes from April 9 2021 to October 15 2021.  You can also review details about `earlier releases`_ or learn more about the `Open edX Platform`_.

.. _earlier releases: https://edx.readthedocs.io/projects/edx-developer-docs/en/latest/named_releases.html
.. _Open edX Platform: https://open.edx.org

.. contents::
 :depth: 1
 :local:

================
Breaking Changes
================

Studio login changed to OAuth
-----------------------------

In versions prior to Maple, Studio (CMS) shared a session cookie with the LMS, and redirected to the LMS for login. Studio is changing to become an OAuth client of the LMS, using the same SSO configuration that other IDAs use. (See [ARCHBOM-1860](https://openedx.atlassian.net/browse/ARCHBOM-1860); [OEP-42](https://open-edx-proposals.readthedocs.io/en/latest/best-practices/oep-0042-bp-authentication.html).) ```
This is a breaking change; follow the Studio OAuth migration runbook as part of upgrading to Maple. For devstack, run::

    ./provision-ida-user.sh studio studio 18010


django-cors-headers version updgraded
-------------------------------------

django-cors-headers version upgraded to 3.2.0. CORS_ORIGIN_WHITELIST now requires URI schemes. You will need to update your whitelist to include schemes, for example from this::

    CORS_ORIGIN_WHITELIST = ["foo.com"]

to this::

    CORS_ORIGIN_WHITELIST = ["https://foo.com"]

Learning Micro-Frontend (MFE) becomes the default courseware experience
--------------------------------------------------------------

Warning: Entrance exams, non-standard XML and HTML tags, and course hierarchies are not supported. See below for more details.

===================
Learner Experiences
===================

Learning Micro-Frontend (MFE)
-----------------------------

The Learning MFE (including both "courseware" and the "course home") is the default course experience in Maple. The Nutmeg release will likely entirely drop support for the legacy (that is, LMS-rendered) course experience entirely.

Configuration
^^^^^^^^^^^^^
- MFE branding elements can be set in the Tutor MFE plugin. See the `tutor mfe plugin README`_ for more details.
- The courseware.use_legacy_frontend and course_home.course_home_use_legacy_frontend Waffle flags can be toggled on (either globally or per-course-run) in order to revert to the Legacy (LMS Django-rendered) courseware experience.
- The domain name for your learning MFE should be added to the CORS_ORIGIN_WHITELIST for ecommerce, discovery, lms, and studio.
- course_home.course_home_mfe : Enable in conjunction with one or more of the following (copied from Lilac, still relevant?)
  - course_home.course_home_mfe_dates_tab : Display the “Dates” course tab in the MFE.
  - course_home.course_home_mfe_outline_tab : Display the course outline (the target of the “Course” course tab) in the MFE.
  - course_home.course_home_mfe_progress_tab: Display the “Progress” course tab in the MFE.
- (there must be more: how to set the name/path? MFE config docs?)

Removed Features
^^^^^^^^^^^^^^^^
- Entrance Exams are slated to be deprecated and are never enabled on the MFE. Attempting to start a course with an entrance exam on the MFE results in an error.    Using the waffle flags to enable the legacy experience should enable their usage for the time being, but their deprecation is forthcoming.
- Problemsets and videosequences, which are deprecated variations of the Sequence block will not render in the MFE
- **Non-standard course hierarchies** Legacy courseware was willing to render some content that didn’t strictly follow that hierarchy, and that content will break in the MFE. This should only affect courses authored directly in OLX. Studio-authored courses already follow these hierarchy requirements. Essentially, courses must follow a stricter hierarchy in order to work in the MFE:

  * The direct children of the root Course block should be Section (aka Chapter) blocks.
  * The children of the Sections must be Subsections (aka Sequence) blocks. Each Subsection must be part of at most one Section.
  * Children of the Subsections should be "Unit-like" blocks (most commonly Verticals, but HTML/Problem/etc are okay too)

Altered Features
^^^^^^^^^^^^^^^^
- The ability for course authors to preview units in the learner experience before they are published will preview in the legacy experience, not the MFE. Work enabling preview using the MFE is anticipated.
- According to the HTML standard, <script> and <iframe> tags are not self-closing; they must be closed with </script> and </iframe> tags. Legacy courseware incidentally corrected this error when it occurred in course content. MFE courseware does not do that correction. Course authors should update their courses to use well-formed HTML if they happened to rely on self-closing <script> or <iframe> tags.
- Courses which use the  course key pattern ORG/COURSE/RUN instead of the new pattern, course-v1:ORG+COURSE+RUN,  are stored in our legacy storage service, Old Mongo, and will not be served by the new MFE. Instead they default to the legacy experience. But this pattern has been deprecated and will be removed.
- Author-written JS inside a Custom Javascript Problem block which acts outside the boundary of a unit will fail. Problem blocks will no longer be able to modify other problem blocks or access any parent elements using javascript. The main use case is pulling in content from a students’ previous answers or state. This is still possible with the get_statefn attribute all within the iframe. Although this may remove some small pieces of custom functionality, it is in the interests of adhering to security protocols.
- Course Navigation on the MFE and legacy experience will have minor differences.
  * The breadcrumbs displayed at the top of a page in the legacy experience were organized by Course -> Sequence -> Unit -> Content Block Title, but in the new MFE breadcrumbs only includes Course -> Sequence -> Unit. This removes visual clutter of having the same title repeated in a small space on the page.
  * the MFE does change the URL scheme from:
  * LMS_BASE/courses/COURSE_KEY/courseware/SECTION_URLNAME/SEQUENCE_URLNAME/UNIT_INDEX?activate_block_id=COMPONENT_KEYto: LEARNING_MFE_BASE/course/COURSE_KEY/SEQUENCE_KEY/UNIT_KEY
- If all content inside a unit should be invisible to a cohort, but the sequence or the unit is not hidden, learners may be able to still see the titles of the content on the course outline, as well as the title of the sequence which contains only what should be hidden content to that learner. This issue can be removed by setting the learning_sequences.use_for_outlines waffle flag to true

Maintained Features
^^^^^^^^^^^^^^^^^^^
- Features which remain functional within MFE courses, but still will be served by the legacy experience in Maple are:
  * The XBlock student view, as exposed via the unit iframe in MFE courseware
  * Static tabs (aka Custom Pages)
  * Discussions tab
  * Wiki tab
  * Teams tab
  * Notes tab
  * Instructor dashboard.
- Special exams (timed and proctored) will be functional within the Learning MFE for MFE enabled courses.

Added Features
^^^^^^^^^^^^^^
- Course outlines will now feature automatic effort estimates for subsections. Courses have to be republished before they show estimates, and all videos in the course must also have durations in edx-val.
- There are some in-course celebrations of progress. A modal popup when a learner finishes their first section. And a 3-day streak celebration modal popup.
- The end of a course now has its own landing page. It congratulates the learner and points them at what to do next (certificate status, other courses to pursue, etc)


Certificates
------------

Various bug fixes and updates around course certificate generation

- Removal of the allow_certificate field on the UserProfile model has been completed, and the column has been dropped (
  * Note: if your UserProfile table has a lot of rows, the migration to drop the column could lock the table and necessitate a status page/downtime.
- The temporary waffle flag certificates_revamp.use_allowlist has been removed, as testing during the rollout of this feature has been completed. All course runs now use the new allowlist behavior, which is described here (need link)
- Code to generate a new or update an existing course certificate has been consolidated:
  * The temporary waffle flag certificates_revamp.use_updated has been removed, as testing during the rollout of this feature has been completed. All course runs now use the new consolidated course certificate behavior, which is described here.
  * Code to generate (create or update) PDF course certificates has been removed from edx-platform.
  * The fix_ungraded_certs, regenerate_user, resubmit_error_certificates, and ungenerated_certs management commands have been removed. In their place, please use the cert_generation command. (needs formatting & links)
- In an effort to be more inclusive, code referencing a Certificate Whitelist has been updated to instead refer to a Certificate Allowlist. The CertificateWhitelistmodel has been replaced by the CertificateAllowlistmodel (data is automatically copied over to the new model by a data migration).
- The management command named cert_whitelist has been removed. In its place, please use the Certificate Allowlist, which can be accessed from the Instructor tab on the course page in the LMS. (needs formatting & links)
- The Segment event edx.bi.user.certificate.generate will no longer emit from the courseware when self-generated certificate generation is attempted by a user. There was some overlap in this Certificate event with the edx.certificate.createdevent sent during certificate generation. A self-generated certificate event will have a generation_mode of self (versus batch for certificates generated automatically).
- Removed use of the modulestore wherever possible in the certificates Django app of edx-platform. Changes include:
  * Using a course’s CourseOverview over retrieving course data from the modulestore
  * Supporting change: Update the list_with_level function in the Instructor Dashboard to accept a course-id over the entire course object (PR: 27646)
- Removed the AUDIT_CERT_CUTOFF_DATE setting. Awarding Audit certificates will not be supported in V2 of Course Certificates
- Removed the openedx/core/djangoapps/certificates app by merging the single api.py file into lms/djangoapps/certificates. All APIs functions have been been moved as is, so if you have any code in a third party repository that used this API, please point them to the new path. openedx/core/djangoapps/certificates/api.py → lms/djangoapps/certificates/api.py
- Removed backpopulate_program_credentials management command in place of an updated notify_credentials command (@Albert (AJ) St. Aubin (Deactivated) )


Open-Response Assessments
-------------------------
- extend frontend feedback limit to 1k chars
- Make submission feedback full-width


Account Micro-frontend
----------------------
- removed hard-coded edX string

Payment Micro-frontend
-----------------------
(anything new here? I guess it works now)

Course Dates & Milestones
-------------------------
(Sarina had a question about how to configure the course end page)

Mobile Experience
-----------------

Android
^^^^^^^
- Allow word_cloud as supported xBlock
- allow specialExam xBlock to open through View on Web
- open rendered HTML block having iframe in mobile browser
- add self-paced course dates events in calendar
- add support of lti_consumer xblocks
- add alerts prior to course due dates

iOS
^^^
- Open rendered HTML block that contains an iframe in the mobile browser
- add word cloud to acceptable list of xblocks
- add course events to calendar
- Add support of lti_consumer xblocks

Special Exams Experience
------------------------
(any news here? Seems like there must be something about proctortrack)


=====================
Authoring Experiences
=====================

Studio
------

- Course and library creation rights can now be granted on a per-organization basis.
  * Controlled content creation rights feature must be enabled via the FEATURES['ENABLE_CREATOR_GROUP'] flag.
  * Creation rights are requested by new users on the Studio page.
  * Administrators handle requests by modifying records in the course_creators admin app: <STUDIO_ROOT>/admin/course_creators/coursecreator/
- Administrators will now have a new capability when granting access:
  * Admins may now uncheck “All Organizations”, and instead select one or more particular organizations from the list.
  * Users granted creation access in this manner will only be able to create courses or libraries under the specified organizations.
  * This change is backwards-compatible: existing creation right grants will continue to apply to all organizations, and “All Organizations” remains the default option when granting new rights.
  * However, administrators can safely modify the organization settings on existing creation right grants if they would like to retroactively use this feature.

Open-Response Assessments
-------------------------
- extend frontend feedback limit to 1k chars
- Add a new button to edit an ORA in Studio
- Make submission feedback full-width
- UI Changes for Rubric Reuse
- add toggle feature for enhanced staff grade



LTI 1.3 and LTI Advantage Support
---------------------------------

lti-consumer-xblock (also known as xblock-lti-consumer) has been updated to support LTI 1.3, as well as the Deep Linking (LTI-DL) and Assignments and Grades services (LTI-AGS) features of LTI Advantage. Note that this xBlock is not installed in Lilac by default. Information on configuring lti-consumer-xblock can be found at https://github.com/edx/xblock-lti-consumer/blob/master/README.rst

- LTI 1.3 and LTI Advantage features are now enabled by default.
- LTI 1.3 settings were simplified to reduce confusion when setting up a LTI tool.
- The URL of the LTI Config Model has been updated. This configuration is used to enable LTI PII sharing per course.  The impact of this update is that anyone who has bookmarked the LTI Django Admin model will need to update their pointer.  The new model admin is available in studio admin at : “admin/lti_consumer/courseallowpiisharinginltiflag/”.
- Move CourseEditLTIFieldsEnabledFlag from edx-platform to this repo while retaining data from existing model.
- Use CourseAllowPIISharingInLTIFlag for LTI1.3 in lieu of the current CourseWaffleFlag.
- Rename CourseEditLTIFieldsEnabledFlag to CourseAllowPIISharingInLTIFlag to highlight its increased scope.
- The modal to confirm information transfer on open of lti in new tab/window has been updated because of a change in how browsers handle iframe permissions.
- Long-term fix for cross-origin iFrames  (if we update to lti-consumer-xblock==3.1.1)


Gradebook MFE
-------------
(still working on support. Can it be turned on? How?)

Special Exams Experience
------------------------
(any news here? Seems like there must be something about proctortrack)


=========================
Administrator Experiences
=========================

Migrations
----------
(somewhere above there is a comment about migrations?)

Studio
------
(repeat notes about oauth)
(repeat or move note about orgs & studio)

Settings and Toggles
--------------------
Documentation for settings and toggles is much improved, but still incomplete. See https://edx.readthedocs.io/projects/edx-platform-technical/en/latest/index.html

New settings introduced in Lilac include:

TBD


Dependency updates
------------------

- Django was upgrades to 3.2, which required many library updates


Deprecations
------------

- The sysadmin dashboard has been removed. Similarly functionality is availabe via the edx-sysadmin plugin from (url)


=============================
Researcher & Data Experiences
=============================

- Tracking metrics based on the anonymized session ID will experience a discontinuity or other anomaly at the time of deployment, as the anonymized IDs will change. [PR] This will likely appear as if everyone logged out and back in again, although only from a metrics perspective. In a green-blue deployment scenario, it may briefly appear as if there are twice as many sessions active.

(anything?)

=====================
Developer Experiences
=====================

(anything?)

.. include:: links.rst
.. include:: ../../links/links.rst

