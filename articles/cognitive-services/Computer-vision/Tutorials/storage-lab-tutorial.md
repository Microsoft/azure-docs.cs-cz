---
title: 'Kurz: Generování metadat pro ibi Azure'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak integrovat službu Azure Computer Vision do webové aplikace pro generování metadat pro image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7c83350dbecaf20e9b35f159b2c01824777bc665
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973709"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Kurz: Generování metadat obrázků ve službě Azure Storage pomocí počítačového vidění

V tomto kurzu se dozvíte, jak integrovat službu Azure Computer Vision do webové aplikace pro generování metadat pro nahrané obrázky. To je užitečné pro scénáře [správy digitálních aktiv (DAM),](../Home.md#computer-vision-for-digital-asset-management) například pokud společnost chce rychle generovat popisné titulky nebo prohledávatelná klíčová slova pro všechny své obrázky.

Úplný průvodce aplikacemi najdete v [Laboratoři úložiště Azure a kognitivních služeb](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) na GitHubu a tento kurz se v podstatě vztahuje na cvičení 5 testovacího prostředí. Můžete chtít vytvořit úplnou aplikaci podle následujících každý krok, ale pokud se chcete dozvědět, jak integrovat počítačové vidění do existující webové aplikace, přečtěte si zde.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření prostředku počítačového zpracování obrazu v Azure
> * Provádění analýzy obrázků na ibi Azure Storage
> * Připojení metadat k bitovým kopiím Služby Azure Storage
> * Kontrola metadat bitových obrázků pomocí Průzkumníka úložiště Azure

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. 

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) nebo vyšší, s nainstalovanými úlohami "ASP.NET a vývoj webových aplikací" a "Vývoj Azure".
- Účet Azure Storage s kontejnerem blob nastavený pro úložiště bitových bitových obrázků (postupujte [podle cvičení 1 z Azure Storage Lab,](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) pokud potřebujete pomoc s tímto krokem).
- Nástroj Průzkumník úložiště Azure (postupujte podle [cvičení 2 azure storage lab,](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) pokud potřebujete pomoc s tímto krokem).
- ASP.NET webová aplikace s přístupem k Azure Storage (postupujte podle [cvičení 3 laboratoře Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) a rychle vytvořte takovou aplikaci).

## <a name="create-a-computer-vision-resource"></a>Vytvoření prostředku počítačového vidění

Budete muset vytvořit prostředek počítačového zpracování obrazu pro váš účet Azure; tento prostředek spravuje váš přístup ke službě Počítačové zpracování obrazu Azure. 

1. Podle pokynů v [části Vytvoření prostředku Služby Azure Cognitive Services](../../cognitive-services-apis-create-account.md) vytvořte prostředek počítačového vidění.

1. Potom přejděte do nabídky pro vaši skupinu prostředků a klikněte na předplatné rozhraní API pro počítačové zpracování obrazu, které jste právě vytvořili. Zkopírujte adresu URL v části **Koncový bod** někam, kde ji můžete snadno načíst během okamžiku. Potom klepněte na tlačítko **Zobrazit přístupové klávesy**.

    ![Stránka portálu Azure s nastíněnou adresou URL koncového bodu a odkazem pro přístupové klíče](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. V dalším okně zkopírujte hodnotu **KLÁVESY 1** do schránky.

    ![Dialogové okno Spravovat klávesy s načrtnutým tlačítkem kopírování](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Přidání pověření pro počítačové zpracování obrazu

Dále přidáte do aplikace požadovaná pověření, aby mohla přistupovat k prostředkům počítačového vidění.

Otevřete ASP.NET webové aplikace v sadě Visual Studio a přejděte do souboru **Web.config** v kořenovém adresáři projektu. Přidejte do části `<appSettings>` souboru následující příkazy, které nahradí `VISION_KEY` klíč, který `VISION_ENDPOINT` jste zkopírovali v předchozím kroku, a adresu URL, kterou jste uložili v předchozím kroku.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Potom v Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a pomocí příkazu **Spravovat balíčky NuGet** nainstalujte balíček **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Tento balíček obsahuje typy potřebné k volání rozhraní API pro počítačové zpracování obrazu.

## <a name="add-metadata-generation-code"></a>Přidání kódu generování metadat

Dále přidáte kód, který ve skutečnosti využívá službu Počítačové vidění k vytvoření metadat pro obrázky. Tyto kroky se budou vztahovat na ASP.NET aplikaci v testovacím prostředí, ale můžete je přizpůsobit vlastní aplikaci. Důležité je, že v tomto okamžiku máte ASP.NET webovou aplikaci, která může nahrát ifotky do kontejneru úložiště Azure, číst z něj obrázky a zobrazit je v zobrazení. Pokud si tímto krokem nejste jisti, je nejlepší sledovat [cvičení 3 v laboratoři Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Otevřete *soubor HomeController.cs* ve složce **Řadiče** projektu `using` a v horní části souboru přidejte následující příkazy:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Potom přejděte na metodu **Upload;** Tato metoda převádí a nahrává obrázky do úložiště objektů blob. Přidejte následující kód ihned za `// Generate a thumbnail` blok, který začíná (nebo na konci procesu vytváření objektů blob image). Tento kód přebírá objekt blob`photo`obsahující bitovou kopii ( ) a používá počítačové vidění ke generování popisu pro tento obrázek. Rozhraní API pro počítačové zpracování obrazu také generuje seznam klíčových slov, která se vztahují k obrázku. Generovaný popis a klíčová slova jsou uloženy v metadatech objektu blob, aby je bylo možné později načíst.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Dále přejděte na metodu **Index** ve stejném souboru. Tato metoda vyjmenovává uložené objekty BLOB v cílovém kontejneru objektů blob (jako instance **IListBlobItem)** a předá je zobrazení aplikace. Nahraďte `foreach` blok v této metodě následujícím kódem. Tento kód volá **CloudBlockBlob.FetchAttributes** získat každý objekt blob připojená metadata. Extrahuje počítačově generovaný`caption`popis ( ) z metadat a přidá jej do objektu **BlobInfo,** který se předává zobrazení.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Otestování aplikace

Uložte změny v sadě Visual Studio a stisknutím **kombinace kláves Ctrl+F5** spusťte aplikaci v prohlížeči. Pomocí aplikace můžete nahrát několik obrázků, buď ze složky "fotografie" ve zdrojích testovacího prostředí, nebo z vlastní složky. Když najedete kurzorem na jeden z obrazů v zobrazení, mělo by se zobrazit okno s popiskem a zobrazit popisek pro obrázek generovaný počítačem.

![Titulek generovaný počítačem](../Images/thumbnail-with-tooltip.png)

Pokud chcete zobrazit všechna připojená metadata, zobrazte pomocí Průzkumníka úložiště Azure kontejner úložiště, který používáte pro image. Klepněte pravým tlačítkem myši na některý z objektů BLOB v kontejneru a vyberte **příkaz Vlastnosti**. V dialogovém okně se zobrazí seznam párů klíč-hodnota. Popis obrázku generovaného počítačem je uložen v položce Titulek a klíčová slova vyhledávání jsou uložena v položkách "Tag0", "Tag1" a tak dále. Po dokončení zavřete dialogové okno klepnutím na **tlačítko Storno.**

![Dialogové okno vlastností obrazu s uvedenými značkami metadat](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci na webové aplikaci, přečtěte si část [Další kroky.](#next-steps) Pokud neplánujete pokračovat v používání této aplikace, měli byste odstranit všechny prostředky specifické pro aplikaci. Chcete-li odstranit prostředky, můžete odstranit skupinu prostředků, která obsahuje vaše předplatné azure storage a prostředek počítačového zpracování obrazu. Tím odeberete účet úložiště, objekty BLOB nahrané do něj a prostředek služby App Service potřebný k připojení k ASP.NET webové aplikaci. 

Chcete-li skupinu zdrojů odstranit, otevřete na portálu kartu **Skupiny zdrojů,** přejděte do skupiny zdrojů, kterou jste použili pro tento projekt, a v horní části zobrazení klikněte na **Odstranit skupinu zdrojů.** Budete vyzváni k zadání názvu skupiny prostředků, abyste potvrdili, že ji chcete odstranit, protože po odstranění nelze skupinu prostředků obnovit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte službu Počítačové zpracování počítače Azure v existující webové aplikaci tak, aby při nahrávání automaticky generovala titulky a klíčová slova pro bitové kopie objektů blob. Dále najdete v lab Azure Storage Lab, Cvičení 6, kde se dozvíte, jak přidat funkce vyhledávání do webové aplikace. To využívá klíčová slova vyhledávání, které generuje služba počítačového zpracování obrazu.

> [!div class="nextstepaction"]
> [Přidání vyhledávání do aplikace](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
