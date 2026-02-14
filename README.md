# Maintaining a Knowledge Base in GitHub using CSV/ Google Sheets as a Source

A repository of attribute definitions and relations, used by the RDA TRSP WG, EOSC EDEN, and EOSC FIDELIS projects.

An app that allows users to edit data in Google Sheets or a CSV, and then push the data to a Github repository as pull requests, each row in the sheet represents an individual JSON-LD file in the Github repository. In addition, the content of the Google Sheet/ CSV is also used to update a JSON inventory file in the Github repository that serves as an index to the individual files. 

The app provides a web page UI that takes the Google Sheet URL or CSV fileupload, the target JSON-LD schema, a mapping between the sheet columns and the JSON-LD schema, and the Github repo URL as parameters. 

The Javascript functions that interacts with Github need to be planned in such a way that it can be replaced by a Python-based API in future iterations of the application.

For the first attempt to sync data to GitHub, a wizardlike preview and approval is provided, after that on-click sync will be most common and can be repeated if no schema changes are needed.


