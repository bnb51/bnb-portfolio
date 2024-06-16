---
title: "ArborDB Database Management System"
author_profile: true
key: 1
toc: true
excerpt: "Java, PostgresSQL, Database Management"
header:
  teaser: /bre_images/arborDB_diagram.png
classes: wide
---
## Group Members
This project is a group effort with Evan Marnik 

## Project Overview

The ArborDB Java Application is a forestry management system designed to interact with a PostgreSQL database. It facilitates various operations related to forests, tree species, workers, and sensors. The application provides a menu-driven interface that allows users to perform tasks such as adding forests, managing tree species, placing sensors, generating reports, and more. It emphasizes robust database interaction, error handling, and resource management to ensure efficient data management and user experience.

## Database Connection Setup

- **Loads the PostgreSQL JDBC driver.**
- **Prompts the user for database connection details** (username, password, URL).
- **Establishes a connection to the database.**

## Menu-driven Interface

- **Displays a menu with options for different operations.**
- **Handles user input to execute selected operations.**

# SQL Schema and Table Setup

The SQL code below sets up the schema and tables required for the ArborDB application. It ensures a clean slate by dropping any existing schema and tables, then recreates the necessary tables and domains.

## Schema and Table Creation

### Set Schema and Drop Existing Tables

- **Schema Creation:** Creates the schema `phase2` for organizing tables.
- **Drop Existing Tables:** Ensures no conflicts with existing structures by dropping tables if they exist.

### Create Tables

![Table Diagram]({{ site.url }}{{ site.baseurl }}/bre_images/arborDB_ER.png)

1. **FOREST:**
   - Stores forest details including boundaries, area, and acid level.

2. **STATE:**
   - Stores state information including name, abbreviation, area, and population.

3. **WORKER:**
   - Stores worker details such as SSN, name, and rank.

4. **EMPLOYED:**
   - Maps workers to states where they are employed.

5. **PHONE:**
   - Stores worker phone numbers.

6. **SENSOR:**
   - Stores sensor details including ID, coordinates, energy level, and maintainer.

7. **REPORT:**
   - Stores sensor reports with details like timestamp and temperature.

8. **TREE_SPECIES:**
   - Stores tree species details including genus, epithet, ideal temperature, and Raunkiaer life form.

9. **TREE_COMMON_NAME:**
   - Maps common names to tree species.

10. **COVERAGE:**
    - Maps forests to states, indicating coverage percentage and area.

11. **FOUND_IN:**
    - Maps tree species to forests where they are found.

12. **Clock:**
    - Manages synthetic time for the system, initialized to September 1, 2023.

![Schema ER Diagram]({{ site.url }}{{ site.baseurl }}/bre_images/arborDB_diagram.png)

### Create Domains

- **RANK:** Enforces valid rank values for workers (`Lead`, `Senior`, `Associate`).
- **RAUNKIAER_LIFE_FORM:** Enforces valid life form values for tree species.

## Operations

1. **Add Forest**
   - Adds a new forest with details like name, area, acid level, and coordinates.
2. **Add Tree Species**
   - Adds a new tree species with details like genus, epithet, ideal temperature, height, and Raunkiaer life form.
3. **Add Species to Forest**
   - Associates a tree species with a forest.
4. **Add Worker**
   - Adds a new worker with details like SSN, name, rank, and state.
5. **Employ Worker to State**
   - Assigns a worker to a state.
6. **Place Sensor**
   - Places a sensor in the forest with details like energy, coordinates, and maintainer's SSN.
7. **Generate Report**
   - Generates a report for a sensor with details like timestamp and temperature.
8. **Remove Species from Forest**
   - Removes a tree species from a forest.
9. **Delete Worker**
   - Deletes a worker by SSN.
10. **Move Sensor**
    - Moves a sensor to new coordinates.
11. **Remove Worker from State**
    - Removes a worker from a state.
12. **Remove Sensor**
    - Removes a sensor by ID.
13. **List Sensors**
    - Lists all sensors in a forest.
14. **List Maintained Sensors**
    - Lists all sensors maintained by a worker.
15. **Locate Tree Species**
    - Locates forests containing a specific tree species.
16. **Rank Forest Sensors**
    - Ranks forests based on the number of sensors.
17. **Habitable Environment**
    - Finds habitable environments for a tree species over a specified number of years.
18. **Top Sensors**
    - Lists top sensors based on the number of reports over a specified period.
19. **Three Degrees**
    - Finds a connection path between two forests within three degrees of separation.
20. **Exit**
    - Exits the application.

## Error Handling

- **Uses try-catch blocks to handle exceptions.**
- **Prints error messages and stack traces for debugging.**

## Resource Management

- **Uses try-with-resources for automatic closing of database connections and statements.**
- **Ensures the scanner used for user input is closed properly.**

## Triggers

### 1. Add Forest Coverage Trigger

- **Purpose:** Automatically calculates and updates the `COVERAGE` table when a new forest is added. It determines which states the new forest overlaps with and calculates the overlapping area and coverage percentage for each state.
- **Function:** `addForestCoverage()`
  - Iterates over all states and checks for overlapping regions with the new forest.
  - Calculates overlapping area and coverage percentage if overlaps exist.
  - Inserts this data into the `COVERAGE` table.
- **Trigger:** `addForestCoverageTrigger`
  - Activated after a new row is inserted into the `FOREST` table.
  - Executes the `addForestCoverage()` function.

### 2. Check Maintainer Employment Trigger

- **Purpose:** Ensures that a sensor is maintained by a worker employed in the state where the sensor is located.
- **Function:** `checkMaintainerEmployment()`
  - Checks if the maintainer of a sensor is employed by the state covering the sensor's coordinates.
  - Raises a notice and reverts the operation if the maintainer is not employed by the state.
- **Trigger:** `checkMaintainerEmploymentTrigger`
  - Activated before inserting or updating a row in the `SENSOR` table.
  - Executes the `checkMaintainerEmployment()` function.

This setup ensures data integrity and automates certain operations within the ArborDB application, facilitating efficient management of forestry data.


