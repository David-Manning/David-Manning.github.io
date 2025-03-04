+++
title = 'XML'
draft = false
toc = true
type = 'docs'
+++


This prompt will turn a messy table (such as a table copy and pasted from the front end of a website) into XML format.

If the row or column names are not always in the same format in every table you copy you might need to specify the element names or exclude some rows or columns.

A very large table may need multiple rows, in which case you can type “continue” and join the parts later.

```yaml
Task:
    Description: "Convert a table into XML format based on specified requirements."
    Rules:
    - "The output XML must include the elements []"
    - "Exclude the rows []"
    - "Exclude the columns []"
    - "Maintain the structure and data of the original table in the XML output."

Role:
    Description: "Data Conversion Specialist"
    Rules:
    - "Expertise in transforming data between various formats, including tables and XML."
    - "Ability to understand and follow specific data conversion requirements."
    - "Meticulous attention to detail to ensure accurate and complete data transformation."

Audience:
    Description: "Data Analysts and Developers"
    Rules:
    - "Familiar with working with structured data in tables and XML format."
    - "Require the data to be in a specific XML structure for further processing or analysis."
    - "Expect the converted data to be accurate, complete, and adhere to the specified requirements."

Create:
    Description: "XML Data"
    Rules:
    - "Generate well-formatted XML that adheres to the XML specification."
    - "Ensure the XML structure matches the elements specified in the original prompt."
    - "Exclude the specified rows and columns from the XML output."

Intent:
    Description: "To convert a table into a XML format that meets the specific requirements provided, enabling easier data processing, analysis, and integration with other systems."
    Rules:
    - "Facilitate data exchange and interoperability between different systems or applications."
    - "Provide data in a structured and machine-readable format for further manipulation and analysis."
    - "Ensure the accuracy and completeness of the converted data while adhering to the specified requirements."

The table:
[Copy and paste table here]

```