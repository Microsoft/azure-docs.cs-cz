---
title: 'Kurz: Detekce a orámování tváří na obrázku – rozhraní API pro rozpoznávání tváře, Java pro Android'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte jednoduchou aplikaci pro Android, která využívá rozhraní API pro rozpoznávání tváře k rozpoznání a orámování tváří na obrázku.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: 50185588b77f01bced9462d5fd1ad67bb5de6e08
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129722"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Kurz: Vytvoření aplikace pro Android k rozpoznání a orámování tváří na obrázku

V tomto kurzu vytvoříte jednoduchou aplikaci pro Android, která využívá javovou knihovnu tříd službu Rozpoznávání tváře k detekci lidských tváří na obrázku. Aplikace zobrazí vybraný obrázek, ve kterém je každá zjištěná tvář orámovaná obdélníkem. Kompletní kód ukázky je dostupný na GitHubu v tématu [Detekce a orámování tváří na obrázku v Androidu](https://github.com/Azure-Samples/cognitive-services-face-android-sample).

![Snímek fotografie s tvářemi orámovanými červeným obdélníkem v Androidu](../Images/android_getstarted2.1.PNG)

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace pro Android
> - Instalace klientské knihovny služby Rozpoznávání tváře
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře

## <a name="prerequisites"></a>Požadavky

- Ke spuštění této ukázky budete potřebovat klíč předplatného. Klíče bezplatného zkušebního předplatného můžete získat v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- [Android Studio](https://developer.android.com/studio/) se sadou SDK minimálně verze 22 (vyžadováno klientskou knihovnou služby Rozpoznávání tváře).
- Klientská knihovna služby Rozpoznávání tváře [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) z Mavenu. Stažení tohoto balíčku není nutné. Pokyny k instalaci jsou uvedené dál.

## <a name="create-the-project"></a>Vytvoření projektu

Při vytváření projektu aplikace pro Android postupujte takto:

1. Otevřete Android Studio. V tomto kurzu se používá Android Studio 3.1.
1. Vyberte **Start a new Android Studio project** (Začít nový projekt Android Studio).
1. Na obrazovce **Create Android Project** (Vytvořit projekt pro Android) v případě potřeby upravte výchozí pole a potom klikněte na **Next** (Další).
1. Na obrazovce **Target Android Devices** (Cílové zařízení s Androidem) použijte selektor rozevíracího seznamu a vyberte **API 22** nebo novější. Potom klikněte na **Next** (Další).
1. Vyberte **Empty Activity** (Prázdná aktivita) a potom klikněte na **Next** (Další).
1. Zrušte zaškrtnutí políčka **Backwards Compatibility** (Zpětná kompatibilita) a potom klikněte na **Finish** (Dokončit).

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>Vytvoření uživatelského rozhraní pro výběr a zobrazení obrázku

Otevřete *activity_main.xml*. Měl by se zobrazit editor rozložení. Vyberte kartu **Text** a její obsah nahraďte následujícím kódem.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Otevřete *MainActivity.java* a všechno kromě prvního příkazu `package` nahraďte následujícím kódem.

Tento kód nastaví obslužnou rutinu události pro `Button`, která spouští novou aktivitu umožňující uživateli vybrat obrázek. Jakmile je obrázek vybraný, zobrazí se v `ImageView`.

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;

public class MainActivity extends Activity {
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Button button1 = (Button)findViewById(R.id.button1);
            button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Aplikace teď může vyhledat fotografii a zobrazit ji v okně podobně jako na následujícím obrázku.

![Snímek fotografie s tvářemi v Androidu](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Konfigurace klientské knihovny služby Rozpoznávání tváře

Rozhraní API pro rozpoznávání tváře je cloudové rozhraní API, které můžete volat pomocí požadavků HTTPS. Tento kurz používá pro zjednodušení vaší práce klientskou knihovnu služby Rozpoznávání tváře, která tyto webové požadavky zapouzdřuje.

V podokně **Project** (Projekt) použijte selektor rozevíracího seznamu a vyberte **Android**. Rozbalte **Gradle Scripts** (Skripty Gradle) a potom otevřete *build.gradle (Module: app)*.

Přidejte závislost pro klientskou knihovnu služby Rozpoznávání tváře `com.microsoft.projectoxford:face:1.4.3`, jak ukazuje následující snímek obrazovky, a potom klikněte na **Sync Now** (Synchronizovat).

![Snímek souboru build.gradle aplikace v sadě Android Studio](../Images/face-tut-java-gradle.png)

Otevřete **MainActivity.java** a přidejte následující direktivy import:

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Přidání kódu klientské knihovny služby Rozpoznávání tváře

Následující kód vložte před metodu `onCreate` ve třídě `MainActivity`:

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

`<API endpoint>` nahraďte koncovým bodem rozhraní API, který byl přiřazený vašemu klíči. Klíče bezplatného zkušebního předplatného se generují v oblasti **westcentralus**. To znamená, že pokud používáte klíč bezplatného zkušebního předplatného, použije se příkaz:

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

Místo `<Subscription Key>` použijte váš klíč předplatného. Příklad:

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

V podokně **Project** rozbalte **app** a potom **manifests** a otevřete *AndroidManifest.xml*.

Vložte následující element jako přímý podřízený elementu `manifest`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Sestavte váš projekt a zkontrolujte případné chyby. Teď můžete volat službu Rozpoznávání tváře.

## <a name="upload-an-image-to-detect-faces"></a>Nahrání obrázku pro rozpoznávání tváří

Nejjednodušším způsobem rozpoznávání tváří je volání metody `FaceServiceClient.detect`. Tato metoda zabalí metodu [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní API a vrátí pole položek `Face`.

Každá vrácená položka `Face` zahrnuje obdélník indikující její umístění a také sérii nepovinných atributů tváře. V tomto příkladu se vyžaduje jenom umístění tváře.

Pokud dojde k chybě, `AlertDialog` zobrazí příslušný důvod.

Do třídy `MainActivity` vložte následující metody.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>Orámování tváří v obrázku

Do třídy `MainActivity` vložte následující pomocné metody. Tato metoda nakreslí obdélník kolem každé rozpoznané tváře.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

Proveďte metody `AsyncTask`, které jsou v metodě `detectAndFrame` označené komentáři `TODO`. V případě úspěchu se vybraný obrázek zobrazí v `ImageView` s orámovanými tvářemi.

```java
@Override
protected void onPreExecute() {
    detectionProgressDialog.show();
}
@Override
protected void onProgressUpdate(String... progress) {
    detectionProgressDialog.setMessage(progress[0]);
}
@Override
protected void onPostExecute(Face[] result) {
    detectionProgressDialog.dismiss();
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

Nakonec v metodě `onActivityResult` odkomentujte volání metody `detectAndFrame`, jak je uvedené níž.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Potom byste měli vidět podobné výsledky jako na následujícím obrázku:

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Souhrn

V tomto kurzu jste se naučili základní postup použití služby Rozpoznávání tváře a vytvořili aplikaci pro zobrazení orámovaných tváří v obrázku.

## <a name="next-steps"></a>Další kroky

Přečtěte si víc o detekci a používání význačných rysů tváří.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Prozkoumejte rozhraní API pro rozpoznávání tváře používané k detekci tváří a jejich atributů, jako je například póza, pohlaví, věk, pozice hlavy, vousy nebo brýle.

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)