---
title: "Advanced Attribute-Based Access Control for Forensic Investigations"
author_profile: true
key: 1
toc: true
excerpt: "Python, Data Analysis, Privacy"
header:
  teaser: /bre_images/sci_design.png
classes: wide
---
## Overview
For this project I wanted to implement an Attribute-Based Access Control system. The Attribute-Based Access Control (ABAC) is an advanced access control model that utilizes attributes, such as user roles, file types, and access levels, to dynamically determine access rights to resources. In traditional models, access is often role-based, whereas ABAC considers a broader range of attributes and conditions for policy evaluation. In my code, I implemented a simplified version of ABAC by generating random attributes and associating them with users, files, and access controls. I also incorporated features like role inheritance, where certain roles inherit permissions from parent roles, and administrative delegation, allowing specific users control over particular files. These implementations aim to mimic key aspects of ABAC, providing a foundation for evaluating the efficiency of processing access queries against the generated policy.

Implementing an ABAC system has many different privacy benefits. By enforcing fine-grained access control based on attributes rather than explicit user permissions, ABAC can limit the exposure of sensitive data to unauthorized users. This way, only individuals with specific attributes can access relevant information, reducing the risk of privacy breaches from unknown parties.Using these techniques, ABAC contributes to improved privacy by allowing organizations to manage access to sensitive information effectively while complying with privacy regulations and standards.

## Base Program 

My base program, `foren_abac_adv.py`, reads and parses a file (`foren_abac_file.txt`), using dictionaries to test access queries about the file's contents. Below is a detailed breakdown of each function.

## Functions Breakdown

#### Reading the File
- **Function:** `read_file(file_path)`
- **Description:** Opens and reads the content of a file at `file_path`. Returns the content as a list of lines.

#### Parsing the File
- **Function:** `parse_file(content)`
- **Description:** Processes the file content to extract information into dictionaries for attributes, access controls, users, and files.
  - Extracts attribute names and values.
  - Assigns roles to users.
  - Determines file types.
  - Records access control names and permissions.
  - Converts all parsed data to lowercase.

#### Evaluating Access Queries
- **Function:** `evaluate_access_query(query, access_controls, attributes, users, files)`
- **Description:** Processes user queries to determine access permissions.
  - Validates query format.
  - Checks user existence and role validity.
  - Ensures file existence and type validity.
  - Determines if the user's role has the required access level for the file type.
  - Provides feedback on whether access is granted or denied.

#### Main Function
- **Function:** `main()`
- **Description:** Serves as the program's entry point.
  - Calls `read_file()` to get the configuration file content.
  - Uses `parse_file()` to extract and organize data.
  - Prompts the user to input an access query.
  - Utilizes `evaluate_access_query()` to process the query.
  - Offers an option to check another query or exit.

![Database](/bre_images/abac_basic.png)

## Policy File Syntax

The policy file is divided into sections labeled for parsing:
- **Attributes:** `Attribute: attribute name or User: user name`
  - Key term: `value`
- **Sections:** Attributes, users, files, access control, role inheritance, and admin delegation.

### Attribute Definitions
- Define possible attributes and their values.

### Users
- List specific users and their roles.

### Files
- Specify files and their types.

### Access Control
- Define access permissions for roles on specific file types.

### Role Inheritance
- Specify parent-child relationships between roles.

### Admin Delegation
- Indicate admin-level user access rights to specific files.

## Features

### Indirection
- **Concept:** Access controls are determined based on file types and user roles, allowing for complex and fine-grained policies.
- **Benefits:** Expressiveness, efficiency, reduced administrative overhead.
- **Drawbacks:** Usability issues, potential for misunderstanding, complexity in automated analysis.

### Administrative Delegation
- **Concept:** Files are associated with specific users who have administrative control.
- **Benefits:** Flexibility, efficiency.
- **Drawbacks:** Increased complexity, potential security risks.

![Database](/bre_images/abac_AD.png)

### Role Inheritance
- **Concept:** Establish parent-child relationships between roles.
- **Benefits:** Simplifies access control management, ensures consistency.
- **Drawbacks:** Potential for ambiguity and over-privilege.

![Database](/bre_images/abac_RI.png)

## Timing and Random Generation Program

I created a timing program (`random_policy.py`) to run randomly generated queries on a randomly generated policy.

### Functions Breakdown

#### Generating Random Policy
- **Function:** `generate_random_policy()`
- **Description:** Generates random user roles, file types, access levels, access controls, users, files, role inheritance, and admin delegations.

#### Benchmarking Policy Processing
- **Function:** `benchmark_policy_processing(policy)`
- **Description:** Benchmarks policy processing by executing 10 random access queries and measuring the average time.

### Main Function
- **Function:** `main()`
- **Description:** Generates a random policy and benchmarks the policy processing time.

## Timing Tests Results

### Test 1
- **Setup:** High complexity and large dataset.
- **Results:** Average query time: 0.0015220 seconds.

### Test 2
- **Setup:** Reduced role inheritance and admin delegations.
- **Results:** Average query time: 0.0007937 seconds.

### Test 3
- **Setup:** Reduced users and files.
- **Results:** Average query time: 0.0013948 seconds.

### Test 4
- **Setup:** Reduced user roles and file types.
- **Results:** Average query time: 0.0003196 seconds.

### Test 5
- **Setup:** Reduced access levels.
- **Results:** Average query time: 0.0008469 seconds.

### Test 6
- **Setup:** Combined reduced user roles and file types with increased role inheritance and admin delegations.
- **Results:** Average query time: 0.0004740 seconds.

## Conclusion

The access control system performed well in the tests, with significant impacts on query runtime observed due to the number of attribute values and the complexity of role inheritance and admin delegations. This system is plausible for use on a personal computer with moderately sized policies.
