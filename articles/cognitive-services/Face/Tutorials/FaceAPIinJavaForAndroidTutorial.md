---
title: Čelí Java rozhraní API pro Android kurzu | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Vytvořte jednoduchou aplikaci pro Android, která kognitivní Services vzhled API používá ke zjišťování a lidské řezy do image s rámečkem.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343633"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Začínáme s vzhled rozhraní API v jazyce Java pro Android kurzu

V tomto kurzu se dozvíte vytvářet a vytvářet jednoduché aplikace pro Android, která volá rozhraní API řez k detekci lidského tyto řezy v obraze. Aplikace zobrazuje výsledek podle rámců řezy, které zjistí.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Příprava

Pokud chcete použít tento kurz, budete potřebovat následující požadavky:

- Android Studio a nainstalovat sadu SDK
- Zařízení se systémem Android (pro testování volitelné).

## <a name="step1"></a>Krok 1: Přihlášení k odběru pro rozhraní API vzhled a získat klíč předplatného

Před použitím jakéhokoli rozhraní API řez, musíte se přihlásit k odběru vzhled API na portálu kognitivní služby společnosti Microsoft. V tématu [odběry](https://azure.microsoft.com/try/cognitive-services/). Primární a sekundární klíč můžete použít v tomto kurzu.

## <a name="step2"></a>Krok 2: Vytvoření rozhraní

V tomto kroku vytvoříte projekt aplikace Android k implementaci základní uživatelské rozhraní pro výběr a zobrazování bitovou kopii. Postupujte podle pokynů níže: 

1. Otevřete Android Studio.
2. V nabídce Soubor klikněte na tlačítko **nový projekt...**
3. Název aplikace **MyFirstApp**a pak klikněte na tlačítko Další. 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Zvolte cílovou platformu podle potřeby a pak klikněte na tlačítko Další. 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Vyberte **základní aktivity** a pak klikněte na tlačítko Další.
6. Název aktivity následujícím způsobem a potom klikněte na tlačítko Dokončit. 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Otevřete **activity_main.xml**, měli byste vidět editoru rozložení této aktivity.
8. Zobrazení textu zdrojový soubor a upravte rozložení aktivity následujícím způsobem:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Otevřete **MainActivity.java** a na začátek souboru vložte následující direktivy importu:

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
    ```
      
    Za druhé upravte třídy následujícím způsobem:  
    
    ```java
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
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Aplikaci teď můžete vyhledat fotografie z galerie a zobrazí v okně podobná následující obrázek:

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>Krok 3: Konfigurace rozhraní API vzhled klientské knihovny

Rozhraní API řez je v cloudu, které požadavky rozhraní API, který lze vyvolat pomocí protokolu HTTPS. Pro více pohodlný způsob pomocí rozhraní API řez v aplikacích platformy .NET knihovna klienta je k dispozici také pro zapouzdření webové požadavky. V tomto příkladu používáme klientské knihovny pro zjednodušení našich pracovních. 

Postupujte podle následujících pokynů nakonfigurujte klientské knihovny: 

1. Vyhledejte nejvyšší úrovně **build.gradle** souboru projektu z panelu projektu v příkladu. Všimněte si, že existuje několik dalších **build.gradle** nutné soubory vašeho projektu stromu a otevřete nejvyšší úrovně **build.gradle** souboru na první.
2. Přidat **mavenCentral()** do vašich projektů úložiště. Můžete také použít jcenter(), což je výchozí úložiště Android Studio, protože jcenter() je nadmnožinou mavenCentral().  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Otevřete **build.gradle** souboru v projektu "aplikace".
4. Přidáte závislost pro naše klientské knihovny uložené v centrálním úložišti Maven:

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Otevřete **MainActivity.java** v projektu "aplikace" a vložení importovat následující direktivy: 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Ve třídě, vložte následující kód:

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   První parametr výše nahraďte koncový bod rozhraní API, který byl přiřazen klíč v kroku 1. Příklad:
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   Druhý parametr nahraďte klíč předplatného, který jste získali v kroku 1.
   
6. Otevřete soubor **AndroidManifest.xml** v projektu "aplikace". Vložte následující prvek jako podřízený **manifest** element:  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. Nyní jste připraveni k volání rozhraní API vzhled z vaší aplikace. 

## <a name="step4"></a>Krok 4: Nahrání bitové kopie k detekci řezy

Nejjednodušší způsob, jak můžete zjistit řezy je voláním [čelí – zjišťovat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní API tím, že nahrajete soubor bitové kopie přímo. Při použití klientské knihovny, to můžete provést pomocí asynchronní metody **DetectAsync** z **FaceServiceClient** třídy. Každý vrácený vzhled obsahuje obdélníku indikující její umístění v kombinaci s řadou volitelné vzhled atributy. V tomto příkladu je potřeba jenom načíst umístění řez. Zde musíme vložit metoda do **MainActivity** třídy pro zjišťování řez: 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
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
```

## <a name="step5"></a>Krok 5: Označte otočená v bitové kopii

V tomto posledním kroku jsme společně kombinovat všechny výše uvedené kroky a označte zjištěné řezy s rámce v bitové kopii. Nejprve otevřete **MainActivity.java** a vložte metodu helper kreslení obdélníků: 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
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

Nyní dokončit části TODO v **detectAndFrame** za účelem rámce řezy a sestav stavu.

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
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
Nakonec přidejte volání **detectAndFrame** metoda z **onActivityResult** metoda, jak je uvedeno níže. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Spusťte tuto aplikaci a vyhledat bitovou kopii obsahující řez. Počkejte několik sekund, povolit cloudu API reagovat. Potom způsobí získání výsledku podobná následující obrázek: 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Souhrn

V tomto kurzu naučili základní proces pomocí rozhraní API vzhled a vytvořit aplikaci zobrazíte vzhled značky v bitové kopie. Další informace o rozhraní API řez, najdete v části postupy a [referenční dokumentace rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Související kurzy

- [Začínáme s vzhled rozhraní API v kurzu CSharp](FaceAPIinCSharpTutorial.md)
- [Začínáme s vzhled rozhraní API v kurzu Python](FaceAPIinPythonTutorial.md)
