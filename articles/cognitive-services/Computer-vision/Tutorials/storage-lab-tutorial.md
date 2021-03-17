---
title: 'Kurz: generování metadat pro Image Azure'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak integrovat službu Azure Počítačové zpracování obrazu do webové aplikace pro generování metadat pro image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: b51234ff121071de27ec8c91425f4a769c8d833e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008782"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Kurz: použití Počítačové zpracování obrazu k vygenerování metadat imagí v Azure Storage

V tomto kurzu se dozvíte, jak integrovat službu Azure Počítačové zpracování obrazu do webové aplikace pro generování metadat pro nahrané obrázky. To je užitečné pro scénáře [správy digitálních prostředků (pro PŘEhradování)](../overview.md#computer-vision-for-digital-asset-management) , jako třeba když společnost chce rychle vygenerovat popisné titulky nebo vyhledat klíčová slova pro všechny její obrázky.

Úplný Průvodce aplikací najdete v [Azure Storage a Cognitive Services testovacím prostředí](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) na GitHubu a tento kurz v podstatě pokrývá cvičení 5 testovacího prostředí. Můžete chtít vytvořit úplnou aplikaci v rámci každého kroku, ale pokud chcete zjistit, jak Počítačové zpracování obrazu do existující webové aplikace, přečtěte si také téma.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření prostředku Počítačové zpracování obrazu v Azure
> * Provedení analýzy obrázků u Azure Storage imagí
> * Připojení metadat k Azure Storage imagí
> * Kontrolovat metadata imagí pomocí Průzkumník služby Azure Storage

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services). 

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) nebo vyšší s nainstalovanou úlohou vývoj ASP.NET a web a vývoj pro Azure.
- Účet Azure Storage s kontejnerem objektů BLOB nastaveným pro úložiště imagí (Pokud potřebujete s tímto krokem nápovědu, postupujte podle [cvičení 1 Azure Storage laboratorního prostředí](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) ).
- Nástroj pro Průzkumník služby Azure Storage (Pokud potřebujete s tímto krokem pomáhat, postupujte podle [cvičení 2 Azure Storage testovacího prostředí](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) ).
- ASP.NET webová aplikace s přístupem k Azure Storage (pro rychlé vytvoření takové aplikace použijte [cvičení 3 Azure Storage testovacího prostředí](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) ).

## <a name="create-a-computer-vision-resource"></a>Vytvoření prostředku Počítačové zpracování obrazu

Budete muset vytvořit prostředek Počítačové zpracování obrazu pro svůj účet Azure. Tento prostředek spravuje váš přístup k Počítačové zpracování obrazu službě Azure. 

1. Podle pokynů v části [vytvoření prostředku Azure Cognitive Services](../../cognitive-services-apis-create-account.md) vytvořte prostředek počítačové zpracování obrazu.

1. Pak přejděte do nabídky pro skupinu prostředků a klikněte na předplatné rozhraní API pro počítačové zpracování obrazu, které jste právě vytvořili. Zkopírujte adresu URL pod **koncovým bodem** , abyste ji mohli snadno načíst za chvíli. Pak klikněte na **Zobrazit přístupové klíče**.

    ![Stránka Azure Portal s odkazem adresa URL koncového bodu a přístup k klávesovým zkratkám](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. V dalším okně Zkopírujte hodnotu **klíče 1** do schránky.

    ![Dialogové okno Správa klíčů s popsaným tlačítkem kopírování](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Přidat pověření Počítačové zpracování obrazu

V dalším kroku přidáte požadovaná pověření do vaší aplikace, aby mohla přistupovat k Počítačové zpracování obrazum prostředkům.

Otevřete webovou aplikaci v ASP.NET v aplikaci Visual Studio a přejděte do souboru **Web.config** v kořenovém adresáři projektu. Do části souboru přidejte následující příkazy `<appSettings>` , které nahradíte klíčem, `VISION_KEY` který jste zkopírovali v předchozím kroku, a `VISION_ENDPOINT` adresou URL, kterou jste v kroku předtím uložili.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Pak v Průzkumník řešení klikněte pravým tlačítkem na projekt a pomocí příkazu **Spravovat balíčky NuGet** nainstalujte balíček **Microsoft. Azure. cognitiveservices Account. Vision. ComputerVision**. Tento balíček obsahuje typy potřebné pro volání rozhraní API pro počítačové zpracování obrazu.

## <a name="add-metadata-generation-code"></a>Přidat kód pro generování metadat

Dále přidáte kód, který ve skutečnosti využívá službu Počítačové zpracování obrazu k vytváření metadat pro image. Tyto kroky se vztahují na aplikaci ASP.NET v testovacím prostředí, ale můžete je přizpůsobit na vlastní aplikaci. Důležité je, že v tomto okamžiku máte webovou aplikaci ASP.NET, která může nahrávat obrázky do kontejneru Azure Storage, číst z něj obrázky a zobrazovat je v zobrazení. Pokud si nejste jisti tímto krokem, je vhodné postupovat podle [cvičení 3 Azure Storageho testovacího prostředí](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Otevřete soubor *HomeController.cs* ve složce **Controllers** projektu a na začátek souboru přidejte následující `using` příkazy:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Pak přejít na metodu **odeslání** ; Tato metoda převádí a nahrává obrázky do úložiště objektů BLOB. Přidejte následující kód hned za blok, který začíná `// Generate a thumbnail` (nebo na konci procesu vytváření objektů BLOB v obraze). Tento kód přebírá objekt BLOB obsahující obrázek ( `photo` ) a používá počítačové zpracování obrazu k vygenerování popisu pro tento obrázek. Rozhraní API pro počítačové zpracování obrazu také generuje seznam klíčových slov, která se vztahují na obrázek. Vygenerovaný popis a klíčová slova jsou uloženy v metadatech objektu blob, aby je bylo možné později načíst.

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

1. Potom ve stejném souboru pokračujte na metodu **index** . Tato metoda vytvoří výčet uložených objektů BLOB imagí v cílovém kontejneru objektů BLOB (jako instance **položky ilistblobitem** ) a předává je do zobrazení aplikace. Nahraďte `foreach` blok v této metodě následujícím kódem. Tento kód volá **CloudBlockBlob. FetchAttributes** , aby získal všechna připojená metadata objektu BLOB. Extrahuje z metadat popis generovaný počítačem ( `caption` ) a přidá je do objektu **BlobInfo** , který se předává do zobrazení.
    
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

Uložte změny v aplikaci Visual Studio a stisknutím **kombinace kláves CTRL + F5** spusťte aplikaci v prohlížeči. Použijte aplikaci k nahrání několika imagí buď ze složky fotek v prostředcích testovacího prostředí, nebo z vaší vlastní složky. Když najedete kurzorem na jeden z obrázků v zobrazení, zobrazí se okno s popisem tlačítka a zobrazí se název obrázku generovaný počítačem.

![Titulek generovaný počítačem](../Images/thumbnail-with-tooltip.png)

Chcete-li zobrazit všechna připojená metadata, použijte Průzkumník služby Azure Storage k zobrazení kontejneru úložiště, který používáte pro obrázky. Klikněte pravým tlačítkem na kterýkoli z objektů BLOB v kontejneru a vyberte **vlastnosti**. V dialogovém okně se zobrazí seznam párů klíč-hodnota. Popis obrázku vygenerovaného počítačem je uložený v položce Caption a klíčová slova hledání jsou uložená v části "Tag0", "značky 1" atd. Až skončíte, zavřete dialogové okno kliknutím na tlačítko **Storno** .

![Dialogové okno Vlastnosti obrázku s uvedenými značkami metadat](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete ve své webové aplikaci dál pracovat, přečtěte si část [Další kroky](#next-steps) . Pokud tuto aplikaci nechcete dál používat, měli byste odstranit všechny prostředky specifické pro danou aplikaci. Pokud chcete odstranit prostředky, můžete odstranit skupinu prostředků, která obsahuje vaše předplatné Azure Storage a prostředek Počítačové zpracování obrazu. Tím se odebere účet úložiště, do kterého se nahrály objekty blob, a App Service prostředek potřebný pro připojení k webové aplikaci ASP.NET. 

Pokud chcete odstranit skupinu prostředků, otevřete na portálu kartu **skupiny prostředků** , přejděte do skupiny prostředků, kterou jste použili pro tento projekt, a v horní části zobrazení klikněte na **Odstranit skupinu prostředků** . Budete vyzváni k zadání názvu skupiny prostředků, abyste potvrdili, že ji chcete odstranit, protože po jejím odstranění nebude možné obnovit skupinu prostředků.

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte Počítačové zpracování obrazu službu Azure v existující webové aplikaci tak, aby automaticky generovala titulky a klíčová slova pro obrázky objektů BLOB při jejich nahrávání. V dalším kroku se dozvíte, jak Azure Storage testovací prostředí, cvičení 6, abyste se dozvěděli, jak do své webové aplikace přidat funkce hledání. To využívá klíčová slova pro hledání, která služba Počítačové zpracování obrazu generuje.

> [!div class="nextstepaction"]
> [Přidání vyhledávání do aplikace](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
