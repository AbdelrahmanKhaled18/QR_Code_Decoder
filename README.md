# Application Preview 
In this Application we have 16 test case of differnert QRcodes with special needs they need some image processing then decoding to extract the string from the QRcode, we have ver1,ver3,ver4 Qrcodes. we implemented a generic code that all the test cases go through to check if the image needs specific filter,rotation etc, then we start decodind by turning the image into grid cells then we apply the mask according to the ver of the qrcode then we apply error correction to correct any erros in the extrcting of the data 

# Installations 
## Packges installation 
* openCV: it is used for image processing 
* numpy: it is used for data manipulation 
* Plt: used for Show the images 
* reedsolo: used for error correction 
* galois: used for error correction using BCH





