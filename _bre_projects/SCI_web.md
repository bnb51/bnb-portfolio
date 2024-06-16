---
title: "SCI Inventory Management Website"
author_profile: true
key: 1
toc: true
excerpt: "Python, Database Creation and Management, Web Desgin"
header:
  teaser: /bre_images/sci_design.png
classes: wide
---
## Featured Video
<iframe width="560" height="315" src="https://www.youtube.com/embed/TyzVoNQBR9M" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Group Members 
This project is a group effort with including Samantha Aziz, Chang Li and Breanna Burns.

## Project Overview 
This project involved developing a robust inventory management system for the buildings within the School of Computing and Information(SCI) at Pitt. This system features a structured database that efficiently organizes information about devices, rooms, buildings, ports, and users. Designed with two key components, the system allows Pitt staff to manage equipment by adding and removing devices, tracking their locations, and documenting essential details. Additionally, it includes a loans management component that enables student users to search for equipment and submit loan requests, thereby streamlining the borrowing and returning process. This comprehensive solution enhances inventory oversight and facilitates efficient equipment utilization within the SCI buildings.

## Technologies used 
**Database:**  
We utilized a MySQL database hosted on the Pitt SCI servers, ensuring reliable and secure data storage.

**Front End:**  
Our front-end was developed using HTML, enhanced with the Jinja2 templating engine for dynamic content rendering.

**Back End:**  
The back-end was built with Python Flask, leveraging PyMySQL for efficient SQL query execution, simplifying database interactions and overall system functionality.

## Database Structure 
![Database]({{ site.url }}{{ site.baseurl }}/bre_images/sci_database.png)

## Database Tables
Our database schema is designed to manage both device information and loan management efficiently. The tables are structured as follows:

### Tables for Device Management:

- **device**:  
  Stores vital information about individual devices, including serial number, model number, borrowable status, owner ID, room location, port location, and device type.

- **deviceType**:  
  Records the various types of devices in the system (e.g., desktop, laptop, HDMI cable) and indicates whether the type is a computer.

- **computer**:  
  Contains additional information for devices classified as computers, such as MAC Address, RAM, CPU, etc.

- **room**:  
  Stores room numbers and their corresponding buildings. It also categorizes rooms by type (e.g., classroom, lab, quiet study room).

- **roomType**:  
  Defines the types of rooms available.

- **building**:  
  Lists SCI buildings. Currently, there are three (Bellefield, Information Sciences, and Sennott), but the schema allows for future additions.

- **zone**:  
  Indicates the zone within a building, utilized by the port table.

- **port**:  
  Details the various ports available in SCI buildings.

### Tables for Loan Management:

- **user**:  
  Contains user information, roles, and email addresses for loan requests.

- **role**:  
  Defines the roles users can have in the system, including Inventory Manager, Rental Manager, and Admin, with flexibility for future roles.

- **UserRoles**:  
  Tracks the roles assigned to each user, accommodating multiple roles for a single user.

- **loans**:  
  Records all device loans in the system.

## Equipment Management System
Our equipment management system is designed to allow users to efficiently manage device information across various tables. Key features include:

- **Adding Entries**:  
  Users can add entries to most tables seamlessly. A notable challenge was enabling users to select a building and have the corresponding list of rooms update dynamically. We implemented a script that listens for changes in the building dropdown. When a building is selected, the script sends a POST request to `/get_rooms`, which returns the list of rooms in that building. The dropdown menu is then populated with the appropriate rooms, ensuring users can only select rooms associated with the chosen building.

- **Indexing Entries**:  
  The system provides an indexed listing of table entries, making it easy for users to view and manage the data.

- **Editing and Deleting Entries**:  
  Users can edit and delete entries from the tables, maintaining up-to-date and accurate information.

- **Inventory Viewing System**:  
  In addition to adding and editing inventory, we implemented a user-friendly system to easily view the inventory and retrieve necessary information. This is achieved through a set of two pages:
  - **Device Inventory Page**: This page presents each item in the database along with its name and device type. It includes a search function that allows users to filter devices based on type, building location, or both, facilitating easy navigation through a growing database.

  ![Inventory]({{ site.url }}{{ site.baseurl }}/bre_images/sci_inven.png)

  - **Device View Page**: Accessible via the action button on the inventory page, this page provides a detailed breakdown of all information stored in the database for an individual item. This allows users to thoroughly understand the specifics of each item to ensure it meets their requirements.

  ![View]({{ site.url }}{{ site.baseurl }}/bre_images/sci_view.png)

By incorporating these features, our equipment management system ensures efficient and user-friendly management of devices and their associated data.

## Implementing Roles
Implementing roles in our system required a more complex approach due to the need for users to have multiple roles. Initially, the user table included a column for `FK_roleID`, but this only allowed a user to have one role, which was insufficient for our needs. To resolve this, we created an additional table named `UserRoles` with two columns: `FK_userID` and `FK_roleID`. This structure allowed multiple entries for one user, enabling the assignment of multiple roles.

To add users with multiple roles, we implemented the following process:

- **Fetching Roles**:  
  Similar to how buildings are fetched, roles are retrieved and displayed as checkboxes instead of dropdowns, allowing users to select multiple roles.

- **Backend Implementation**:  
  On the backend, we loop through each role, checking if it was selected. If a role is selected, it is added to an array of selected roles. Since the user is created in the table before roles are added, the process of adding user roles for the first time is the same as editing roles for a user with no roles.

This implementation ensures that users can have multiple roles, providing flexibility and comprehensive role management within the system.

## User Permissions
When users log in, their roles and personal information (such as name and email) are fetched from the user table. This information is stored in the `userDetails` and `userRoles` global variables, allowing easy access for any functions that need them. Roles are also integrated into menu control variables, which are then passed to the HTML templates. On the front end, the management tab is disabled for users who do not have the Inventory Manager or Admin roles.

## Design
For the design aspect of our project, we aimed for seamless integration with the current Pitt system. To achieve this, we used existing university websites as references to create our main template and menu, ensuring a cohesive look and feel. Our design choices were driven by the goal of maintaining consistency and user familiarity within the Pitt environment.

![Design]({{ site.url }}{{ site.baseurl }}/bre_images/sci_design.png)



