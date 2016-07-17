title: 使用HttpUrlConnection下载文件
categories: Android
date: 2015-09-17 17:16:44
tags: [HttpUrlConnection,下载文件]
---


记录一下使用HttpUrlConnection的`Get`方式下载文件的方法.

Show the code:

```Java
private String mUrl="http://download.fir.im/v2/app/install/559d229d692d791592000016?download_token=6adb9ccc081979064c9d453b1a9d7bf5";

private void download() {

    try {
        //file:/storage/emulated/0/test.apk
        File file = new File(Environment.getExternalStorageDirectory(), "test.apk");
        Log.d(TAG, "download: file:" + file.getPath());
        URL url = new URL(mUrl);
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        InputStream in = connection.getInputStream();
        BufferedInputStream bis = new BufferedInputStream(in);
        if (!file.exists()) {
            file.createNewFile();
        } else {
            file.delete();
            file.createNewFile();
        }
        FileOutputStream fos = new FileOutputStream(file);

        byte[] buffer = new byte[1024];
        int size = -1;
        while ((size = bis.read(buffer)) != -1) {
            fos.write(buffer, 0, size);
        }

        fos.flush();
        fos.close();
        bis.close();
        connection.disconnect();
    } catch (MalformedURLException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }
}

```
