---
title: 'Kurz: Rozhraní API pro počítačové zpracování obrazu v Javě'
titlesuffix: Azure Cognitive Services
description: Prozkoumejte základní aplikaci v Java Swingu, která v Azure Cognitive Services používá rozhraní API pro počítačové zpracování obrazu. Provádějte optické rozpoznávání znaků (OCR), vytvářejte miniatury a pracujte s vizuálními funkcemi obrázku.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.date: 09/21/2017
ms.openlocfilehash: cca35d031e860e014c8fd84b0daf6b4d60d18046
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985843"
---
# <a name="tutorial-computer-vision-api-java"></a>Kurz: Rozhraní API pro počítačové zpracování obrazu v Javě

Tento kurz ukazuje funkce rozhraní REST API pro počítačové zpracování obrazu služby Azure Cognitive Services.

Prozkoumejte aplikaci v Java Swingu, která používá rozhraní REST API pro počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytvořte chytře ořezané miniatury a rozpoznávejte, kategorizujte, označujte a popisujte vizuální vlastnosti na obrázku včetně obličejů. Tento příklad vám umožňuje odeslat adresu URL obrázku k analýze nebo zpracování. Tento opensourcový příklad můžete použít jako šablonu k vytvoření vlastní aplikace v Javě, která bude používat rozhraní REST API pro počítačové zpracování obrazu.

V tomto kurzu se naučíte používat Počítačové zpracování obrazu k:

> [!div class="checklist"]
> * Analýze obrázku
> * Identifikaci přirozené nebo umělé památky na obrázku
> * Identifikaci celebrity na obrázku
> * Vytvoření kvalitní miniatury obrázku
> * Přečtení tištěného textu na obrázku
> * Přečtení rukou psaného textu na obrázku

Aplikace v Java Swingu už je napsaná, ale nemá žádnou funkci. V tomto kurzu přidáte konkrétní kód rozhraní REST API pro počítačové zpracování obrazu, kterým aplikaci funkci přidáte.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Tento kurz jsme vyvinuli v integrovaném vývojovém prostředí (IDE) NetBeans. Konkrétně ve verzi **Java SE** prostředí NetBeans, kterou si můžete [stáhnout zde](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Získejte předplatné rozhraní API pro počítačové zpracování obrazu a získejte klíč předplatného 

Před vytvořením příkladu musíte získat předplatné rozhraní API pro počítačové zpracování obrazu, které je součástí Azure Cognitive Services. Podrobnosti o správě předplatného a klíče najdete v [předplatných](https://azure.microsoft.com/try/cognitive-services/). V tomto kurzu můžete použít jak primární, tak i sekundární klíče. 

## <a name="download-the-tutorial-project"></a>Stáhněte si výukový projekt

1. Přejděte do úložiště [Cognitive Services Java Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Klikněte na tlačítko **Naklonovat nebo stáhnout**.
1. Klikněte na **Stáhnout ZIP** a stáhněte soubor ZIP výukového projektu.

Obsah souboru ZIP není nutné extrahovat, protože NetBeans projekt importuje přímo z něj.

## <a name="import-the-tutorial-project"></a>Import výukového projektu

Soubor **cognitive-services-java-computer-vision-tutorial-master.zip** importujte do NetBeans.

1. V NetBeans, klikněte na **File** > **Import Project** > **From ZIP...** (Soubor > Importovat projekt > Ze souboru ZIP...). Zobrazí se dialogové okno **Import Project(s) from ZIP** (Import projektu/projektů ze souboru ZIP).
1. V poli **ZIP File:** (Soubor ZIP:) klikněte na tlačítko **Browse** (Procházet), vyhledejte soubor **cognitive-services-java-computer-vision-tutorial-master.zip** a potom klikněte na **Open** (Otevřít).
1. V dialogovém okně **Import Project(s) from ZIP** klikněte na **Import**.
1. Na panelu **Projects** (Projekty) rozbalte **ComputerVision** > **Source Packages** > **&lt;default package&gt;** (ComputerVision > Zdrojové balíčky > výchozí balíček). 
   Některé verze NetBeans místo **Source Packages** > **&lt;default package&gt;** používají **src**. V takovém případě rozbalte **src**.
1. Dvakrát klikněte na **Mainframe.java**, aby se soubor načetl do editoru NetBeans. Zobrazí se karta **Design** (Návrh) souboru **MainFrame.java**.
1. Klikněte na kartu **Source** (Zdroj), abyste si zobrazili zdrojový kód v Javě.

## <a name="build-and-run-the-tutorial-project"></a>Sestavení a spuštění výukového projektu

1. Stisknutím klávesy **F6** sestavte a spusťte výukovou aplikaci.

    Ve výukové aplikaci klikněte na kartu, aby se zobrazilo podokno dané funkce. Tlačítka nic neprovádí, protože mají prázdné metody.

    V dolní části okna se nachází pole **Subscription Key** (Klíč předplatného) a **Subscription Region** (Oblast předplatného). Tato pole se musí vyplnit platným klíčem předplatného a správnou oblastí daného klíče. Pokud potřebujete získat klíč předplatného, přejděte na [předplatná](https://azure.microsoft.com/try/cognitive-services/). Pokud jste z tohoto odkazu získali klíč předplatného zkušební verze, pak je výchozí oblast **westcentralus** správnou oblastí vašich klíčů předplatného.

1. Výukovou aplikaci ukončete.

## <a name="add-the-tutorial-code"></a>Přidání výukového kódu

Aplikace v Java Swingu má nastavených šest karet. Každá karta představuje jinou funkci počítačového zpracování obrazu (analýza, OCR atd.). Následujících šest částí kurzu na sobě nezávisí, takže jich můžete přidat kolik chcete – jednu, několik nebo všech šest. A můžete je přidávat v libovolném pořadí.

Pusťme se do toho.

## <a name="analyze-an-image"></a>Analýza obrázku

Funkce Analyze počítačového zpracování obrazu během analýzy obrázku prohledá více než 2 000 rozpoznatelných objektů, živých bytostí, krajin a akcí. Po dokončení analýzy se vrátí objekt JSON, který popisuje obrázek popisnými značkami, analýzou barev, titulky a dalšími položkami.

K dokončení funkce Analyze výukové aplikace musíte provést následující kroky:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Funkce Analyze krok 1: Přidání kódu obslužné rutiny události k tlačítku formuláře

Metoda obslužné rutiny události **analyzeImageButtonActionPerformed** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá metodu **AnalyzeImage**, která obrázek analyzuje. Když se metoda **AnalyzeImage** vrátí, zobrazí v textové oblasti **Response** (Odpověď) formátovanou odpověď JSON, z objektu **JSONObject** extrahuje první titulek a zobrazí ho spolu s úrovní spolehlivosti, že je titulek správný.

Do metody **analyzeImageButtonActionPerformed** zkopírujte a vložte následující kód.

> [!NOTE]
> NetBeans vám neumožní ho vložit do řádku definice této metody (```private void```) ani do pravé složené závorky. Abyste mohli tento kód zkopírovat, musíte zkopírovat řádky mezi definicí metody a pravou složenou závorkou a nahradit jimi obsah metody.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Funkce Analyze krok 2: Přidání obálky ke zbytku volání rozhraní REST API

Metoda **AnalyzeImage** zabalí volání rozhraní REST API k analýze obrázku. Tato metoda vrátí objekt **JSONObject** popisující obrázek, nebo pokud došlo k chybě, vrátí **null**.

Metodu **AnalyzeImage** zkopírujte a vložte těsně pod metodu **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

### <a name="analyze-step-3-run-the-application"></a>Funkce Analyze krok 3: Spuštění aplikace

Stisknutím klávesy **F6** spusťte aplikaci. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Zadejte adresu URL obrázku, který chcete analyzovat, potom klikněte na tlačítko **Analyze Image** (Analyzovat obrázek) a podívejte se na výsledek.

## <a name="recognize-a-landmark"></a>Rozpoznání památky

Funkce Landmark počítačového zpracování obrazu vyhledá v obrázku přirozené nebo umělé památky, jako jsou hory nebo známé budovy. Po dokončení analýzy vrátí funkce Landmark objekt JSON, který památky nalezené v obrázku identifikuje.

K dokončení funkce Landmark výukové aplikace musíte provést následující kroky:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Funkce Landmark krok 1: Přidání kódu obslužné rutiny události k tlačítku formuláře

Metoda obslužné rutiny události **landmarkImageButtonActionPerformed** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá metodu **LandmarkImage**, která obrázek analyzuje. Když se metoda **LandmarkImage** vrátí, zobrazí v textové oblasti **Response** (Odpověď) formátovanou odpověď JSON, z objektu **JSONObject** extrahuje první název památky a zobrazí ho v okně spolu s úrovní spolehlivosti, že je památka identifikovaná správně.

Do metody **landmarkImageButtonActionPerformed** zkopírujte a vložte následující kód.

> [!NOTE]
> NetBeans vám neumožní ho vložit do řádku definice této metody (```private void```) ani do pravé složené závorky. Abyste mohli tento kód zkopírovat, musíte zkopírovat řádky mezi definicí metody a pravou složenou závorkou a nahradit jimi obsah metody.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Funkce Landmark krok 2: Přidání obálky ke zbytku volání rozhraní REST API

Metoda **LandmarkImage** zabalí volání rozhraní REST API k analýze obrázku. Tato metoda vrátí objekt **JSONObject** popisující památky nalezené na obrázku, nebo pokud došlo k chybě, vrátí **null**.

Metodu **LandmarkImage** zkopírujte a vložte těsně pod metodu **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="landmark-step-3-run-the-application"></a>Funkce Landmark krok 3: Spuštění aplikace

Stisknutím klávesy **F6** spusťte aplikaci. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Klikněte na kartu **Landmark** (Památka), zadejte adresu URL obrázku s památkou, potom klikněte na tlačítko **Analyze Image** (Analyzovat obrázek) a podívejte se na výsledek.

## <a name="recognize-celebrities"></a>Rozpoznání celebrit

Funkce Celebrities počítačového zpracování obrazu vyhledá na obrázku známé osobnosti. Po dokončení analýzy vrátí funkce Celebrities objekt JSON, který celebrity nalezené v obrázku identifikuje.

K dokončení funkce Celebrities výukové aplikace musíte provést následující kroky:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Funkce Celebrities krok 1: Přidání kódu obslužné rutiny události k tlačítku formuláře

Metoda obslužné rutiny události **celebritiesImageButtonActionPerformed** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá metodu **CelebritiesImage**, která obrázek analyzuje. Když se metoda **CelebritiesImage** vrátí, zobrazí v textové oblasti **Response** (Odpověď) formátovanou odpověď JSON, z objektu **JSONObject** extrahuje první jméno celebrity a zobrazí ho v okně spolu s úrovní spolehlivosti, že je celebrita identifikovaná správně.

Do metody **celebritiesImageButtonActionPerformed** zkopírujte a vložte následující kód.

> [!NOTE]
> NetBeans vám neumožní ho vložit do řádku definice této metody (```private void```) ani do pravé složené závorky. Abyste mohli tento kód zkopírovat, musíte zkopírovat řádky mezi definicí metody a pravou složenou závorkou a nahradit jimi obsah metody.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Funkce Celebrities krok 2: Přidání obálky ke zbytku volání rozhraní REST API

Metoda **CelebritiesImage** zabalí volání rozhraní REST API k analýze obrázku. Tato metoda vrátí objekt **JSONObject** popisující celebrity nalezené na obrázku, nebo pokud došlo k chybě, vrátí **null**.

Metodu **CelebritiesImage** zkopírujte a vložte těsně pod metodu **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="celebrities-step-3-run-the-application"></a>Funkce Celebrities krok 3: Spuštění aplikace

Stisknutím klávesy **F6** spusťte aplikaci. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Klikněte na kartu **Celebrities** (Celebrity), zadejte adresu URL obrázku s celebritou, potom klikněte na tlačítko **Analyze Image** (Analyzovat obrázek) a podívejte se na výsledek.

## <a name="intelligently-generate-a-thumbnail"></a>Inteligentní vygenerování miniatury

Funkce Thumbnail počítačového zpracování obrazu vygeneruje z obrázku miniaturu. Pomocí funkce **Smart Crop** (Inteligentní oříznutí) identifikuje funkce Thumbnail na obrázku oblast zájmu a kolem této oblasti vycentruje miniaturu, aby se vygenerovaly co nejhezčí miniatury obrázku.

K dokončení funkce Thumbnail výukové aplikace musíte provést následující kroky:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Funkce Thumbnail krok 1: Přidání kódu obslužné rutiny události k tlačítku formuláře

Metoda obslužné rutiny události **thumbnailImageButtonActionPerformed** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá metodu **getThumbnailImage**, která vytvoří miniaturu. Když se metoda **getThumbnailImage** vrátí, zobrazí vygenerovanou miniaturu.

Do metody **thumbnailImageButtonActionPerformed** zkopírujte a vložte následující kód.

> [!NOTE]
> NetBeans vám neumožní ho vložit do řádku definice této metody (```private void```) ani do pravé složené závorky. Abyste mohli tento kód zkopírovat, musíte zkopírovat řádky mezi definicí metody a pravou složenou závorkou a nahradit jimi obsah metody.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Funkce Thumbnail krok 2: Přidání obálky ke zbytku volání rozhraní REST API

Metoda **getThumbnailImage** zabalí volání rozhraní REST API k analýze obrázku. Tato metoda vrátí objekt **BufferedImage** obsahující miniaturu, nebo pokud došlo k chybě, vrátí **null**. Chybová zpráva se vrátí v prvním elementu pole řetězců **jsonError**.

Metodu **getThumbnailImage** zkopírujte a vložte těsně pod metodu **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

### <a name="thumbnail-step-3-run-the-application"></a>Funkce Thumbnail krok 3: Spuštění aplikace

Stisknutím klávesy **F6** spusťte aplikaci. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Klikněte na kartu **Thumbnail** (Miniatura), zadejte adresu URL obrázku, potom klikněte na tlačítko **Generate Thumbnail** (Vygenerovat miniaturu) a podívejte se na výsledek.

## <a name="read-printed-text-ocr"></a>Přečtení tištěného textu (OCR)

Funkce optického rozpoznávání znaků (OCR) počítačového zpracování obrazu analyzuje obrázky s tištěným textem. Po dokončení analýzy vrátí funkce OCR objekt JSON, který obsahuje text a umístění textu na obrázku.

K dokončení funkce OCR výukové aplikace musíte provést následující kroky:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>Funkce OCR krok 1: Přidání kódu obslužné rutiny události k tlačítku formuláře

Metoda obslužné rutiny události **ocrImageButtonActionPerformed** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá metodu **OcrImage**, která obrázek analyzuje. Když se metoda **OcrImage** vrátí, zobrazí v textové oblasti **Response** (Odpověď) rozpoznaný text jako formátovanou odpověď JSON.

Do metody **ocrImageButtonActionPerformed** zkopírujte a vložte následující kód.

> [!NOTE]
> NetBeans vám neumožní ho vložit do řádku definice této metody (```private void```) ani do pravé složené závorky. Abyste mohli tento kód zkopírovat, musíte zkopírovat řádky mezi definicí metody a pravou složenou závorkou a nahradit jimi obsah metody.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>Funkce OCR krok 2: Přidání obálky ke zbytku volání rozhraní REST API

Metoda **OcrImage** zabalí volání rozhraní REST API k analýze obrázku. Tato metoda vrátí objekt **JSONObject** z dat JSON vrácených z volání, nebo pokud došlo k chybě, vrátí **null**.

Metodu **OcrImage** zkopírujte a vložte těsně pod metodu **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="ocr-step-3-run-the-application"></a>Funkce OCR krok 3: Spuštění aplikace

Stisknutím klávesy **F6** spusťte aplikaci. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Klikněte na kartu **OCR**, zadejte adresu URL obrázku s tištěným textem, potom klikněte na tlačítko **Read Image** (Přečíst obrázek) a podívejte se na výsledek.

## <a name="read-handwritten-text-handwriting-recognition"></a>Přečtení rukou psaného textu (rozpoznávání rukopisu)

Funkce Handwriting Recognition počítačového zpracování obrazu analyzuje obrázek s ručně psaným textem. Po dokončení analýzy vrátí funkce Handwriting Recognition objekt JSON, který obsahuje text a umístění textu na obrázku.

K dokončení funkce Handwriting Recognition výukové aplikace musíte provést následující kroky:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Funkce Handwriting Recognition krok 1: Přidání kódu obslužné rutiny události k tlačítku formuláře

Metoda obslužné rutiny události **handwritingImageButtonActionPerformed** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá metodu **HandwritingImage**, která obrázek analyzuje. Když se metoda **HandwritingImage** vrátí, zobrazí v textové oblasti **Response** (Odpověď) rozpoznaný text jako formátovanou odpověď JSON.

Do metody **handwritingImageButtonActionPerformed** zkopírujte a vložte následující kód.

> [!NOTE]
> NetBeans vám neumožní ho vložit do řádku definice této metody (```private void```) ani do pravé složené závorky. Abyste mohli tento kód zkopírovat, musíte zkopírovat řádky mezi definicí metody a pravou složenou závorkou a nahradit jimi obsah metody.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Funkce Handwriting Recognition krok 2: Přidání obálky ke zbytku volání rozhraní REST API

Metoda **HandwritingImage** zabalí dvě volání rozhraní REST API potřebné k analýze obrázku. Rozpoznávání rukopisu je časově náročný proces, takže se provádí ve dvou krocích. První volání odešle obrázek ke zpracování a jakmile se zpracování dokončí, druhé volání načte rozpoznaný text.

Po načtení textu vrátí metoda **HandwritingImage** objekt **JSONObject** popisující text a umístění textu, nebo pokud došlo k chybě, vrátí **null**.

Metodu **HandwritingImage** zkopírujte a vložte těsně pod metodu **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occured. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>Funkce Handwriting Recognition krok 3: Spuštění aplikace

Stisknutím klávesy **F6** spusťte aplikaci. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Klikněte na kartu **Read Handwritten Text** (Přečíst rukou psaný text), zadejte adresu URL obrázku s rukou psaným textem, potom klikněte na tlačítko **Read Image** (Přečíst obrázek) a podívejte se na výsledek.

## <a name="next-steps"></a>Další kroky

- [Kurz – rozhraní API pro počítačové zpracování obrazu s C&#35;](CSharpTutorial.md)
- [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](PythonTutorial.md)
