.. _Discussions:

##################################
Creating Course Discussions
##################################

Discussions in an edX course include both course-wide topics of interest to
all learners (such as "Feedback", "Troubleshooting", or "Technical Help") as
well as content-specific topics that you add to course units as discussion
components. You create both types of discussion topics in Studio.

For information about creating discussion topics, see the following sections.

.. contents::
 :local:
 :depth: 1


For information about creating discussions in which learners in a group such as
a cohort or in a particular enrollment track only interact with posts from other
learners in the same group, see :ref:`About Divided Discussions`.

For information about running and moderating course discussions, see
:ref:`Running_discussions` and :ref:`Moderating_discussions`.


.. _Create CourseWide Discussion Topics:

*************************************
Create Course-Wide Discussion Topics
*************************************

All courses include a page named **Discussion**. When you create a course, a
course-wide discussion topic named "General" is already included by default.

You can add additional course-wide discussion topics to guide how learners
share and find information during your course. Such course-wide topics might
include Introduction and Announcements, Feedback, or Troubleshooting.
Discussions in these topics can begin as soon as your course is available.

.. note:: Make sure each discussion topic in your course has a unique name,
   whether it is a course-wide topic or a content-specific discussion topic
   that you add as a discussion component. If different discussion topics
   share the same name, learners might be confused as to which discussion
   topic they are participating in. For example, do not add a content-specific
   discussion topic named "General", because a course-wide discussion topic
   named "General" already exists in every course.

To create a course-wide discussion topic, follow these steps.

#. Open your course in Studio.

#. Select **Content**, then **Pages & Resources**.

#. Click on the gear icon on top-right of **Discussion** tile (as seen below). This will take you to a configuration page where you can select a discussion provider.

.. image:: ../../../shared/images/Discussion_tile_in_pages_and_resources.png
  :width: 300
  :alt: Appearance of Discussion tile in Pages & Resources.

#. Select edx as discussion provider by checking the box on top-right of **edx** tile. Click Next.

.. image:: ../../../shared/images/Tile_for_edx_discussion_provider.png
  :width: 300
  :alt: Appearance of tile for edx discussion provider in configuration.

#. Scroll to **General discussion topics**. You would see a topic named **General** already there. This topic cannot be deleted but can be renamed.

.. image:: ../../../shared/images/General_discussion_topics_edx_discussions.png
  :width: 300
  :alt: A topic named General will already exist in General discussion topics.

#. Click on **Add topic** and add a topic name (e.g. "Course Q&A")

.. image:: ../../../shared/images/Add_general_topic_name_edx_discussions.png
  :width: 300
  :alt: Adding general topic name.

#. Click Save.

When learners select the **Discussion** page for your course, the drop-down
Discussion list now includes the topic you added.

 .. image:: ../../../shared/images/New_general_discussion_topic.png
  :width: 300
  :alt: A new topic named Course Q&A in the list of discussion topics.

.. note:: In courses that use cohorts, the course-wide discussion topics that
   you add are unified. All posts can be read and responded to by every
   learner, regardless of the cohort that they belong to. You can optionally
   configure these topics to be divided by cohort. For more information, see
   :ref:`Specify Which Course Wide Discussion Topics are Divided`.


.. _Create ContentSpecific Discussion Topics:

*********************************************
Create Content-Specific Discussion Topics
*********************************************

To create a content-specific discussion topic, you add a discussion component
to a unit. Typically, you do this while you are designing and creating your
course in Studio. Follow the instructions in :ref:`Working with Discussion
Components`. The result is a discussion topic associated with a unit and its
content. Learners can access content-specific topics both in the course unit
and on the **Discussion** page.

.. warning:: Follow the recommended steps to add discussion components. Do not
   create discussion topics by using the **Duplicate** button in Studio, and do
   not reference the same discussion ID in more than one place in your course.
   Duplicated discussion components result in discussion topics that contain the
   same conversations, even if learners post in different discussion topics.

For information about the visibility of content-specific discussion
topics, see :ref:`Visibility of Discussion Topics`.

.. note:: In courses with cohorts enabled, when you add discussion components to
   units in Studio, these discussion topics are by default unified. All learners
   in the course can see and respond to posts from all other learners. You can
   change content-specific discussion topics to be divided, so that only members
   of the same group can see and respond to each other's posts. For information,
   see :ref:`Content Specific Discussion Topics and Groups`.


.. _Visibility of Discussion Topics:

*********************************************************
Understanding When Learners Can See Discussion Topics
*********************************************************

The names that you specify as the category and subcategory names for discussion
components are not visible on the **Discussion** page in the LMS until after
the course has started and the unit is released.

However, "seed" posts that you create in content-specific discussion topics
before a course starts, or before the unit is released, are immediately visible
on the **Discussion** page, even though the containing category or subcategory
names are not visible. EdX recommends that you do not create posts in
content-specific discussion topics before a unit is released. For more
information about release dates and the visibility of components, see
:ref:`Controlling Content Visibility`.

In contrast, :ref:`course-wide discussion topics<Create CourseWide Discussion
Topics>` that you create on the **Advanced Settings** page in Studio,
including the default "General" discussion topic, are immediately visible,
regardless of whether the course has started. They are not associated with any
particular section or subsection of the course, and are not subject to
release dates.


.. _Anonymous_posts:

*****************************************************
Allowing Learners to Make Anonymous Discussion Posts
*****************************************************

By default, when learners participate in a discussion, their usernames are
visible in the discussion. You can allow learners to make discussion posts
that are anonymous to their peers: their usernames are not visible to other
learners. Their usernames will still be visible to course staff.

To allow anonymous discussion posts in your course, follow these steps.

#. In Studio, select **Settings**, then select **Advanced Settings**.

#. Locate the **Allow Anonymous Discussion Posts to Peers** field. The default
   value is ``false``.

#. Change the value of the setting to ``true``.

#. Select **Save Changes**.

Learners can now create discussion posts in your course that are anonymous
to other learners.

.. note:: The **Advanced Settings** page in Studio includes another field named
   **Allow Anonymous Discussion Posts**. Setting the value of that field to
   ``true`` enables learners to create discussion posts that are anonymous to
   course staff, as well as to other learners. As a best practice, edX
   recommends that you do not set this field to ``true``.


.. _Discussions on Mobile Apps:

***************************************
Discussions in the edX Mobile App
***************************************

Learners can participate in course discussions using the edX mobile app as
they do on the edX site, but there are some differences in the actions that
moderators can take in discussions using the mobile app. To perform moderation
or administrative tasks for your course discussions, you need to work in a web
browser.

The following actions are not supported on the edX mobile apps.

  * Pinning posts
  * Marking responses to question posts as answers
  * Endorsing responses to discussion posts
  * Closing posts



.. include:: ../../../links/links.rst
