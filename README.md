# Object-detection-using-flask
Flask with tensorflow object detection demo

Frontend and backend are separated, not with common flask template.
Since flask is very simple and wroted by python, we build it with only a few lines of code.

This function receive base64 encoded image from front end page, converted it to PIL Image, then do the object detection step.

@app.route('/api/', methods=["POST"])
def main_interface():
    response = request.get_json()
    data_str = response['image']
    point = data_str.find(',')
    base64_str = data_str[point:]  # remove unused part like this: "data:image/jpeg;base64,"

    image = base64.b64decode(base64_str)       
    img = Image.open(io.BytesIO(image))

    if(img.mode!='RGB'):
        img = img.convert("RGB")
    
    # convert to numpy array.
    img_arr = np.array(img)

    # do object detection in inference function.
    results = inference(sess, detection_graph, img_arr, conf_thresh=0.5)
    print(results)

    return jsonify(results)
    
 Front end part
 
In front end page, with the help of jQuery ajax, we can send the base64 image to backend, wait for the result, then draw the bounding box on the page.

Core code is:

// handle image files uploaded by user, send it to server, then draw the result.
function parseFiles(files) {
  const file = files[0];
  const imageType = /image.*/;
  if (file.type.match(imageType)) {
    warning.innerHTML = '';
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onloadend = () => {
      image.src = reader.result;
      // send the img to server
      communicate(reader.result);
    }
  } else {
    setup();
    warning.innerHTML = 'Please drop an image file.';
  }
}

How to run.
Step 1: Open the server with

python app.py

The server is setup on port 5000.

Step 2: Open the front end page.

If you want to use python.

// python3
python -m http.server
// python2
python -m SimpleHTTPServer

If you prefer Node.js

npm install serve -g // install serve
serve // this will open a mini web serve
// or http-serve
npm install http-server -g
http-server

HERE ARE SOME IMAGES WE HAVE DETECTED:

![image](https://user-images.githubusercontent.com/100552250/177747490-65cce281-8211-4df0-8570-40c131ca7bb9.png)

