# Downloading-Files
How to download a file from the web in Android with AsyncTask


  //Executing the AsyncTask 
  
    DownloadFile dFile = new DownloadFile();
    dFile.execute("your url here");

  //Showing progress bar  
  
    @Override
    protected Dialog onCreateDialog(int id) {
        switch (id) {
            case 0:
                pDialog = new ProgressDialog(this);
                pDialog.setMessage("Downloading file...");
                pDialog.setIndeterminate(false);
                pDialog.setMax(100);
                pDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
                pDialog.setCancelable(false);
                pDialog.show();
                return pDialog;
            default:
                return null;
        }
    }

  //AsycTask to download a file
        
        class DownloadFile extends AsyncTask<String, String, String> {

        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            showDialog(0);
        }

        @Override
        protected String doInBackground(String... urlArr) {

            InputStream input = null;
            int count;
            try {
                URL url = new URL(urlArr[0]);
                HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();
                urlConnection.setConnectTimeout(15000);
                urlConnection.setReadTimeout(10000);
                urlConnection.setRequestMethod("GET");
                urlConnection.setDoInput(true);
                urlConnection.connect();

                int fileSize = urlConnection.getContentLength();
                File sdcard = Environment.getExternalStorageDirectory();
                input = urlConnection.getInputStream();
                OutputStream output = new FileOutputStream(sdcard+"/app_file.apk");

                byte[] buffer = new byte[1024];
                long total = 0;

                while ((count = input.read(buffer)) != -1) {
                    total += count;
                    publishProgress(""+(int)((total*100)/fileSize));
                    output.write(buffer, 0, count);
                    output.flush();
                }

                output.flush();
                output.close();
                input.close();

            } catch (MalformedURLException e) {
            } catch (IOException e) {
            } catch (Exception e){
            }
            return null;
        }

        @Override
        protected void onProgressUpdate(String... progress) {
            pDialog.setProgress(Integer.parseInt(progress[0]));
        }

        @Override
        protected void onPostExecute(String file_url) {
            pDialog.dismiss();
            
            //Download completed
            AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
            builder.setMessage("Download completed!");
            builder.setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {

              }
            });
            AlertDialog dialog = builder.create();
            dialog.show();
        }
        
    }
