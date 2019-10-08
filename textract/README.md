# Amazon Textract : Extracting revenue from sample pages of 10-k Example

This example notebook shows how to use Amazon Textract service to automatically extract information from PDF. It demonstrates the following:
* Basic setup of s3 to place pdf file, information from this pdf file will be extracted.
* Analyzing Document Text with Amazon Textract using python SDK
** https://docs.aws.amazon.com/textract/latest/dg/analyzing-document-text.html
** https://docs.aws.amazon.com/textract/latest/dg/detecting-document-text.html
* Using AWS SDK for Python (Boto 3), start_document_analysis and get_document_analysis.
** https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/textract.html
** https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/textract.html#Textract.Client.start_document_analysis
** https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/textract.html#Textract.Client.get_document_analysis	
* For details of Detecting or Analyzing Text in a Multipage Document can be found below
** https://docs.aws.amazon.com/textract/latest/dg/async-analyzing-with-sqs.html
** Example SDK implementation can be found here https://github.com/aws-samples/amazon-textract-serverless-large-scale-document-processing




# Textractor

textractor helps speed up PoCs by allowing you to quickly extract text, forms and tables from documents using Amazon Textract. It can generate output in different formats including raw JSON, JSON for each page in the document, text, text in reading order, key/values exported as CSV, tables exported as CSV. It can also generate insights or translate detected text by using Amazon Comprehend, Amazon Comprehend Medical and Amazon Translate. It takes advantage of [Textract response parser library](https://github.com/aws-samples/amazon-textract-response-parser) to easily consume JSON returned by Amazon Textract.

## Prerequisites

- Python3 (https://www.python.org/downloads/)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
- [Pandas](https://pandas.pydata.org/)

## Setup

- Download [code](./zip/textract_revenue.zip) and unzip on your local machine.

## Usage

Format:
- jupyter notebook Textract Revenue.ipynb


## Generated Output

Tool generates several files in the format below:

#### Text, forms and tables related output files

- document-response.json: Raw JSON response of Amazon Textract API call.
- document-page-n-response.json: Raw JSON blocks for each page document.
- document-page-n-text.txt: Detected text for each page in the document.
- document-page-n-text-inreadingorder.txt: Detected text in reading order (multi-column) for each page in the document.
- document-page-n-forms.csv: Key/Value pairs for each page in the document.
- document-page-n-tables.csv: Tables detected for each page in the document.

#### Insights related output files

- document-page-n-insights-entities.csv: Entities in detected text for each page in the document.
- document-page-n-insights-sentiment.csv: Sentiment in detected text for each page in the document.
- document-page-n-insights-keyPhrases.csv: Key phrases in detected text for each page in the document.
- document-page-n-insights-syntax.csv: Syntax in detected text for each page in the document.



## Source Code
- [Textract Revenue.ipynb](./src/Textract Revenue.ipynb) is the entry point. It has detailed instructions to run the notebook.
- [OutputGenerator](./src/og.py) takes Textract response and uses [Textract response parser](https://github.com/aws-samples/amazon-textract-response-parser) to process response and generate output.
- Example below shows how [response parser library](https://github.com/aws-samples/amazon-textract-response-parser) helps process JSON returned from Amazon Textract.

```

# Call Amazon Textract and get JSON response
docproc = DocumentProcessor(bucketName, filePath, awsRegion, detectText, detectForms, tables)
response = docproc.run()

# Get DOM
doc = Document(response)

# Iterate over elements in the document
for page in doc.pages:
    # Print lines and words
    for line in page.lines:
        print("Line: {}--{}".format(line.text, line.confidence))
        for word in line.words:
            print("Word: {}--{}".format(word.text, word.confidence))
    
    # Print tables
    for table in page.tables:
        for r, row in enumerate(table.rows):
            for c, cell in enumerate(row.cells):
                print("Table[{}][{}] = {}-{}".format(r, c, cell.text, cell.confidence))

    # Print fields
    for field in page.form.fields:
        print("Field: Key: {}, Value: {}".format(field.key.text, field.value.text))

    # Get field by key
    key = "Phone Number:"
    field = page.form.getFieldByKey(key)
    if(field):
        print("Field: Key: {}, Value: {}".format(field.key, field.value))

    # Search fields by key
    key = "address"
    fields = page.form.searchFieldsByKey(key)
    for field in fields:
        print("Field: Key: {}, Value: {}".format(field.key, field.value))

```

## Cost
  - As you run this tool, it calls different APIs (Amazon Textract, optionally Amazon Comprehend, Amazon Comprehend Medical, Amazon Translate) in your AWS account. You will get charged for all the API calls made as part of the analysis.

## Other Resources

- [Textractor](https://github.com/aws-samples/amazon-textract-textractor/)
- [Large scale document processing with Amazon Textract - Reference Architecture](https://github.com/aws-samples/amazon-textract-serverless-large-scale-document-processing)
- [Batch processing tool](https://github.com/aws-samples/amazon-textract-textractor)
- [JSON response parser](https://github.com/aws-samples/amazon-textract-response-parser)

## License

This library is licensed under the Apache 2.0 License. 