A solid conceptual data model for a project management app centers on work items (tasks/issues) organized in projects and hierarchies, with users, permissions, scheduling, and tracking (time, comments, files) around them.[1][2]

## Core work hierarchy

At the heart is a generic “Work Item” that supports multiple types (task, bug, story, epic, milestone, etc.) so you can evolve the product without schema churn.[3][4]

- Organization / Workspace  
  - Team (optional, for grouping users and projects)[5]
  - Project (belongs to an Organization, optionally to a Team)[2]
- WorkItem  
  - Attributes: type, title, description, status, priority, created/updated, due date, start date, estimate, story points, custom fields.[1][2]
  - Relationships:  
    - Project 1‑n WorkItem  
    - Parent WorkItem 1‑n Child WorkItem (epic → story → subtask, etc.)[3][5]
    - Section/Column 1‑n WorkItem (for Kanban or list sections).[6][7]

A Section (or Column) is a subdivision of a Project used for stages (To Do, In Progress, Done) or categories.[7][6]

## Users, membership, and permissions

Users participate in organizations and projects with roles that drive authorization.[2][5]

- User  
  - Core identity and profile.  
- Membership / Role assignment  
  - OrganizationMembership (User, Organization, role: owner/admin/member).[5]
  - ProjectMembership (User, Project, role: manager/contributor/viewer).[2]
- Assignee / Watcher  
  - WorkItemAssignment (WorkItem, User, role: assignee, reviewer, follower).[3]

This separation between membership and assignment lets you support cross‑project visibility, guests, and followers/mentions cleanly.[4][3]

## Tracking progress and history

To capture collaboration and state changes, model time, comments, and events as separate entities linked to WorkItem.[1][3]

- Comment (WorkItem, User, body, createdAt; supports mentions and reactions).[3]
- WorkLog / TimeEntry (User, WorkItem, date, duration, billable flag, notes).[8][9]
- StatusHistory / Event  
  - Attributes: WorkItem, changedBy, changedAt, field, oldValue, newValue.[3]

This enables activity feeds, audit trails, burn‑down charts, and reporting on time spent per project, user, or client.[9][8]

## Dependencies, relationships, and tagging

Work items often relate to each other beyond simple parent/child, so model n‑n relationships explicitly.[4][3]

- WorkItemLink  
  - fromWorkItem, toWorkItem, linkType (blocks, is blocked by, duplicates, relates to, etc.).[3]
- Tag / Label  
  - Tag (Organization‑scoped); WorkItemTag (WorkItem, Tag) n‑n.[5][4]
- Milestone / Release  
  - Milestone (Project or Organization, date range); WorkItemMilestone (WorkItem, Milestone).[10][9]

These constructs support dependency graphs, cross‑project initiatives, and flexible categorization.[9][3]

## Files, integrations, and custom fields

Separate attachments and custom fields so core entities stay stable while allowing extensibility.[11][1]

- Attachment (WorkItem, Comment, or Project; URL or blob reference, filename, type, size).[4][2]
- ExternalLink / IntegrationRef (WorkItem → Git commit/PR, document, etc.).[12][4]
- CustomFieldDefinition (scope: Organization/Project; type: text/number/select/date).[13][11]
- CustomFieldValue (WorkItem, CustomFieldDefinition, value).[13][11]

This pattern lets teams define their own fields per project without schema migrations while preserving type information for querying and reporting.[11][13]

***

### High-level entity table

| Area        | Main entities                            | Key relationships                                                                 |
|------------|-------------------------------------------|-----------------------------------------------------------------------------------|
| Structure  | Organization, Team, Project, Section      | Organization 1‑n Team, Organization 1‑n Project, Project 1‑n Section             |
| Work       | WorkItem, WorkItemLink, Milestone         | Project 1‑n WorkItem, WorkItem parent‑child, WorkItem n‑n via WorkItemLink       |
| People     | User, OrganizationMembership, ProjectMembership, WorkItemAssignment | Users in orgs/projects; users assigned/following WorkItems                       |
| Tracking   | Comment, TimeEntry, StatusHistory         | WorkItem 1‑n Comment/TimeEntry/StatusHistory                                     |
| Meta       | Tag, WorkItemTag, Attachment, CustomFieldDefinition, CustomFieldValue | WorkItem n‑n Tag; WorkItem 1‑n Attachment; WorkItem 1‑n CustomFieldValue         |[1][2][3]  

If you share constraints (multi‑tenant, reporting needs, soft deletes, etc.), a more concrete logical model with keys and indexes can be sketched out next.

[1](https://www.linkedin.com/pulse/building-project-management-data-model-step-by-step-guide-hassan-4c4yf)
[2](https://www.geeksforgeeks.org/sql/how-to-design-er-diagrams-for-project-management-software/)
[3](https://arxiv.org/pdf/2201.08368.pdf)
[4](https://www.atlassian.com/agile/project-management/issue-tracking-software)
[5](https://asana.com/resources/asana-tips-structure-work)
[6](https://help.asana.com/s/article/sections)
[7](https://developers.asana.com/reference/sections)
[8](https://budibase.com/blog/data/how-to-create-a-data-model/)
[9](https://www.openproject.org/blog/open-source-jira-alternative/)
[10](https://www.redmineup.com/pages/blog/open-source-jira)
[11](https://www.activityinfo.org/blog/posts/2024-10-21-a-guide-to-data-modelling-key-principles-and-design-considerations.html)
[12](https://monday.com/blog/rnd/best-jira-alternative-for-issue-tracking/)
[13](https://learn.microsoft.com/en-us/common-data-model/)
[14](https://uwaterloo.ca/vpaf-project-management-office/tools-and-templates/tools/process-and-data-modelling-tools/data-models)
[15](https://forum.asana.com/t/show-section-of-projects-in-column-in-my-tasks-and-projects-list-view/154304)
[16](https://openclassrooms.com/fr/courses/8371431-manage-your-project-using-v-model-methodology/8417400-design-a-conceptual-data-model)
[17](https://www.youtube.com/watch?v=FJxBl5HsoVw)
[18](https://www.reddit.com/r/SQL/comments/17mchuo/project_management_platform_entity_relationship/)
[19](https://devrev.ai/blog/jira-alternatives)
[20](https://forum.asana.com/t/how-to-have-a-section-template/182604)
