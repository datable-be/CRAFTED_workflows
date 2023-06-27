# CRAFTED_workflows
Knime workflows for the CRAFTED project (https://pro.europeana.eu/project/crafted).

This repo contains workflows for content analysis and enrichment using Knime and some external services (e.g. Google Vision).
The workflows were used to enrich data in the CRAFTED project, that were published on Europeana in the EDM format.



## CRAFTED_get-data-Mint_public
### Workflow components:
1. reads data in the EDM format (from Mint)
2. creates a unique filename for each file with an URI in the isShownBy value
3. writes the original image to a local disk (folder 'ORIGINAL')
4. writes the normalized images (max width 500px) to a local disk (folder 'NORMALIZED')
5. writes the metadata to a local MongoDB instance

### Configuration:
- set path to local EDM file (exported from Mint)
- set CP name and dataset ID
- set path to local disk for downloaded and normalized files
- set mongoDB connection

### Requirements:
- Python environment for Knime, as explained here: https://docs.knime.com/2018-12/python_installation_guide
- MongoDB Compass (see https://www.mongodb.com/products/compass)

## CRAFTED_objectdetector_GVision_base64_public
This workflow will detect objects in images, using the Google vision API. The workflow builds upon the results from the workflow 'CRAFTED_get-data-MINT_public. Results can be used to feed the Color detection workflow ('CRAFTED_get-color-from-region_OpenCV_with_object_public').

### Workflow components:
1. read data about the image from a MongoDB and load the image
2. base65 encode the image
3. execute a request to Google Vision object detection
4. recalculate result to xywh-pixel coordinates
5. write the result in W3C annotation format to a file
6. write the result in W3C annotation format to the MongoDB

### Configuration:
- set mongoDB connection to read image metadata [see workflow 'CRAFTED_get-data-MINT_public]
- set CP name and dataset ID
- set API key in Post Request node
- set mongoDB connection to write results
- set path to annotations folder

### Requirements:
- Knime (https://www.knime.com/)
- MongoDB Compass (see https://www.mongodb.com/products/compass)
- a Google Vision account and API_KEY (see https://cloud.google.com/vision; https://cloud.google.com/vision/docs/object-localizer)

## CRAFTED_get-color-from-region_OpenCV_no-object_public
This workflow will determine the colors of a foreground object in an image. The workflow builds upon the results from the workflow 'CRAFTED_get-data-MINT_public'

Note: this workflow guesses the foreground using the grabcut algorithm. For more precise foreground detection, pass the foreground detection results from the 'CRAFTED_objectdetector_GVision_base64' workflow and use the 'CRAFTED_get-color-from-region_OpenCV_with-object_public' workflow.

### Workflow components:
1. read data about the image from a MongoDB and loads the image
2. determine the main colors
3. map the colors tot he colours of the European Fashion Thesaurus
4. write the image without background to a local disk
5. write the result in W3C annotation format to a file
6. write the result in W3C annotation format to the MongoDB

### Configuration:
- set mongoDB connection to read image metadata [see workflow 'CRAFTED_get-data-MINT_public]
- set CP name and dataset ID
- set path to local disk for forground image files (in folder 'IMAGES_NO_BCKGRND')
- set mongoDB connection to write results
- set path to annotations folder
- Optional: set number of clusters in the 'get colors' node
- Optional: set threshhold for infrequemnt colours in 'filter colors <x%' node

### Requirements:
- Knime (https://www.knime.com/)
- Python3 environment for Knime, as explained here: https://docs.knime.com/2018-12/python_installation_guide
- MongoDB Compass (see https://www.mongodb.com/products/compass)
 
## CRAFTED_get-color-from-region_OpenCV_with-object_public
This workflow will determine the colors of a foreground object in an image. The workflow builds upon the results from the workflow 'CRAFTED_get-data-MINT_public' and 'CRAFTED_objectdetector_GVision_base64'

Note: this workflow guesses the foreground using coordinates of the foreground object generated by the 'CRAFTED_objectdetector_GVision_base64' workflow.
If you don't want/need to use the object detection workflow, use the workflow 'CRAFTED_get-color-from-region_OpenCV_no-object_public'.

### Workflow components:
1. read data about the image and detected object(s) from a MongoDB and loads the image
2. determine the main colors
3. map the colors tot he colours of the European Fashion Thesaurus
4. write the image without background to a local disk
5. write the result in W3C annotation format to a file
6. write the result in W3C annotation format to the MongoDB

### Configuration:
- set mongoDB connection to read image metadata [see workflow 'CRAFTED_get-data-MINT_public]
- set CP name and dataset ID
- set path to local disk for foreground image files (in folder 'IMAGES_NO_BCKGRND')
- set mongoDB connection to write results
- set path to annotations folder
- Optional: set number of clusters in the 'get colors' node
- Optional: set threshhold for infrequemnt colours in 'filter colors <x%' node

### Requirements:
- Knime (https://www.knime.com/)
- Python3 environment for Knime, as explained here: https://docs.knime.com/2018-12/python_installation_guide
- MongoDB Compass (see https://www.mongodb.com/products/compass)

## CRAFTED_textdetect_GVision_base64_public
This workflow will extract text from an image, using the Google vision API. The workflow builds upon the results from the workflow 'CRAFTED_get-data-MINT_public'.

### Workflow components:
1. read data about the image from a MongoDB and loads the image
2. base65 encode the image
3. execute a request to Google Vision text extraction
5. write the result in W3C annotation format to a file
6. write the result in W3C annotation format to the MongoDB

### Configuration:
- set mongoDB connection to read image metadata [see workflow 'CRAFTED_get-data-MINT_public]
- set CP name and dataset ID
- set API key in Post Request node
- set mongoDB connection to write results
- set path to annotations folder

### Requirements:
- Knime (https://www.knime.com/)
- MongoDB Compass (see https://www.mongodb.com/products/compass)
- a Google Vision account and API_KEY (see https://cloud.google.com/vision; https://cloud.google.com/vision/docs/ocr)

## CRAFTED_speech2text_public
This workflow will extract text from a sound file, using the Google vision API. The workflow builds upon the results from the workflow 'CRAFTED_get-data-MINT_public'.

### Workflow components:
1. read data about the sound file from a MongoDB and loads the image
3. execute a request to Google Vision speech to text engine
5. write the result in W3C annotation format to a file
6. write the result in W3C annotation format to the MongoDB

### Configuration:
- set mongoDB connection to read image metadata [see workflow 'CRAFTED_get-data-MINT_public]
- set CP name and dataset ID
- set API key in Post Request node
- set mongoDB connection to write results
- set path to annotations folder

### Requirements:
- Knime (https://www.knime.com/)
- MongoDB Compass (see https://www.mongodb.com/products/compass)
- a Google Vision account and API_KEY (see https://cloud.google.com/speech-to-text)
