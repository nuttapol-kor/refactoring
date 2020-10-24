# Refactoring

## In Controller.java
From Nuttapol's FlashGet code: https://github.com/nuttapol-kor/Final-Project-FlashGet

### downloadPressed(), managingThread(), clearPressed() Method

In the `src/flashget/Controller.java` class

https://github.com/nuttapol-kor/Final-Project-FlashGet/blob/master/src/flashget/Controller.java

consider this code:

```java
    public void downloadPressed(ActionEvent event) {
        ...
        threadProgressBar1.setVisible(false);
        threadProgressBar2.setVisible(false);
        threadProgressBar3.setVisible(false);
        threadProgressBar4.setVisible(false);
        threadProgressBar5.setVisible(false);
        ...
    }

    public void managingThread(URL url, File file, long size, int nThread) {
        ...
        List<ProgressBar> progressBarList = new ArrayList<>();
        progressBarList.add(threadProgressBar1);
        progressBarList.add(threadProgressBar2);
        progressBarList.add(threadProgressBar3);
        progressBarList.add(threadProgressBar4);
        progressBarList.add(threadProgressBar5);
        ...
        for (int i = 0; i < nThread; i++) {
            ...
            progressBarList.get(i).progressProperty().bind(task[i].progressProperty());
            progressBarList.get(i).setVisible(true);
        }
    }

    public void clearPressed(ActionEvent event) {
        ...
        threadProgressBar1.setVisible(false);
        threadProgressBar2.setVisible(false);
        threadProgressBar3.setVisible(false);
        threadProgressBar4.setVisible(false);
        threadProgressBar5.setVisible(false);
    }
```

* Refactoring Signs:
    - many calls progressBar.setVisible() and progressBarList.add()
    - duplicate Code setVisible()

* Refactoring: add arrayProgressBar as attribute of class and collect a bunch of progress bar in array when pressed the download button and move to downloadPressed()

```java
    public void downloadPressed(ActionEvent event) {
        ...
        arrayProgressBar = new ProgressBar[]{threadProgressBar1, 
                        threadProgressBar2, threadProgressBar3, 
                        threadProgressBar4, threadProgressBar5};
        ...
    }

    public void managingThread(URL url, File file, long size, int nThread) {
        ...
        for (int i = 0; i < nThread; i++) {
            ...
            arrayProgressBar[i].progressProperty().bind(task[i].progressProperty());
            arrayProgressBar[i].setVisible(true);
        }
    }
```
- Refactoring: create new method to set progress bar

```java
    private void invisibleAllThreadProgressBar() {
        for (ProgressBar progressBar : arrayProgressBar) {
            progressBar.setVisible(false);
        }
    }

    public void downloadPressed(ActionEvent event) {
        ...
        arrayProgressBar = new ProgressBar[]{threadProgressBar1,
                        threadProgressBar2, threadProgressBar3,
                        threadProgressBar4, threadProgressBar5};
        invisibleAllThreadProgressBar();
        ...
    }

    public void clearPressed(ActionEvent event) {
        ...
        invisibleAllThreadProgressBar();
    }
```