<script>
  import axios from "axios";

  let img;
  let files;
  let prediction = "";
  let huggingFaceLoading = false;
  let huggingFaceErrorMessage = "";
  let titleOptions = ["Boss", "Friends", "Brother", "Sister", "Teacher", "favourite Footballplayer", "Mum", "Dad", "Dog", "Girlfriend", "Boyfriend"];
  let titleIndex = 0;
  let title = "You ever wondered what your " + titleOptions[titleIndex] + " votes for?";

  // Create an interval to update the title every second
  setInterval(() => {
    titleIndex = (titleIndex + 1) % titleOptions.length;
    title = "You ever wondered what your " + titleOptions[titleIndex] + " votes for?";
  }, 1000);

  $: if (files) {
      // Note that `files` is of type `FileList`, not an Array:
      // https://developer.mozilla.org/en-US/docs/Web/API/FileList
      console.log(files);

      //Adds a onload for the image. The callback onload is called when the image is loaded.
      actionOnImageLoad();

      let reader = new FileReader();
      reader.onload = (e) => {
          img.src = e.target.result;
          console.log(e.target.result);
      };
      reader.readAsDataURL(files[0]);

      for (const file of files) {
          console.log(`${file.name}: ${file.size} bytes`);
      }
  }

  function actionOnImageLoad() {
      // Set up the image onload event
      img.onload = function () {
          // Create a new canvas
          var canvas = document.createElement("canvas");
          canvas.width = img.width;
          canvas.height = img.height;

          // Get the canvas 2D context
          var ctx = canvas.getContext("2d");

          // Draw the image onto the canvas, ignoring the alpha channel
          ctx.drawImage(img, 0, 0);

          canvas.toBlob((blob) => {
              predict(blob);
          }, "image/jpeg");
      };
  }

  function predict(data) {
      let url =
          "https://api-inference.huggingface.co/models/Irgendsoeine/FaceTheVote3";
      axios
          .post(
              url,
              data, //image data
              {
                  headers: {
                      Authorization:
                          "Bearer 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",
                  },
              }
          )
          .then((response) => {
              console.log(response.data);
              huggingFaceLoading = false;
              huggingFaceErrorMessage = "";
              prediction = response.data.map(obj => `${obj.label}: ${(obj.score * 100).toFixed(2)}%`).join(', ');
          })
          .catch(function (error) {
              // error 503 Object { error: "Model kuhs/cats_classficiation is currently loading", estimated_time: 20 }1
              if (error.response.status === 503) {
                  //503 is service unavaiulable
                  huggingFaceLoading = true;
                  huggingFaceErrorMessage = error.response.data.error;
                  console.log(error.response.data);
                  console.log(error.response.status);
                  console.log(error.response.headers);
              } else if (error.request) {
                  // The request was made but no response was received
                  // `error.request` is an instance of XMLHttpRequest in the browser and an instance of
                  // http.ClientRequest in node.js
                  console.log(error.request);
              } else {
                  // Something happened in setting up the request that triggered an Error
                  console.log("Error", error.message);
              }
              console.log(error.config);
          });
  }
</script>


<h1>{title}</h1>
{#if huggingFaceLoading}
  <h3>{huggingFaceErrorMessage}</h3>
{/if}
<label for="file_upload">Lets find it out:</label>
<input
  accept="image/*"
  bind:files
  id="file_upload"
  name="file_upload"
  type="file"
/>

{#if !huggingFaceLoading}
  <p>{prediction}</p>
{/if}
<img id="file_upload" bind:this={img} alt="" />
