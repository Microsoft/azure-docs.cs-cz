---
title: 'Kurz: Generovat metadata pro Image služby Azure Storage'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak integrovat službu Azure pro počítačové zpracování obrazu do webové aplikace generovat metadata pro Image.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: pafarley
ms.openlocfilehash: 650696d1eb3979447bffa7312e91b4fe1a57652c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865008"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Kurz: Můžete generovat metadata obrázků ve službě Azure Storage pro počítačové zpracování obrazu

V tomto kurzu se dozvíte, jak integrovat službu Azure pro počítačové zpracování obrazu do webové aplikace ke generování metadat pro nahraných obrázků. Úplná příručku můžete najít v [služby Azure Storage a Cognitive Services Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) na Githubu a v tomto kurzu v podstatě zahrnuje vykonávat 5 tohoto prostředí. Možná chcete vytvořit aplikaci začátku do konce pomocí následujících každý krok, ale pokud chcete pouze naleznete v tématu jak pro počítačové zpracování obrazu je možné integrovat do existující webové aplikace, přečíst podél tady.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření prostředku pro počítačové zpracování obrazu v Azure
> * Proveďte analýzu obrázků na Image služby Azure Storage
> * Připojit metadata do Image služby Azure Storage
> * Zkontrolujte metadata obrázků pomocí Průzkumníka služby Azure Storage

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2017 Community edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) nebo vyšší s "vývoj aplikací ASP.NET a web" a "Vývoj pro Azure" nainstalovaných úlohách.
- Účet služby Azure Storage s přidělené pro Image kontejneru objektů blob (podle [cvičení 1 z Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) Pokud potřebujete pomoc s tímto krokem).
- Nástroj Průzkumník služby Azure Storage (podle [cvičení 2 Azure Lab úložiště](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) Pokud potřebujete pomoc s tímto krokem).
- Webové aplikace ASP.NET s přístupem ke službě Azure Storage (podle [cvičení 3 z Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) takové aplikace rychle vytvořit).

## <a name="create-a-computer-vision-resource"></a>Vytvoření prostředku pro počítačové zpracování obrazu

Budete muset vytvořit prostředek pro počítačové zpracování obrazu k vašemu účtu Azure; Tento prostředek řídí přístup ke službě Azure pro počítačové zpracování obrazu.

1. Přihlaste se k [webu Azure portal](https://ms.portal.azure.com) a klikněte na tlačítko **vytvořit prostředek**následovaný **AI a strojové učení** a **pro počítačové zpracování obrazu**.

    ![Vytváří se nový odběr rozhraní API pro počítačové zpracování obrazu](../Images/new-vision-api.png)

1. V dialogovém okně zadejte "pro zpracování obrazu api-key" **název** pole a vyberte **F0** jako **cenová úroveň**. Vyberte stejné **umístění** , kterou jste vybrali při nastavování účtu Azure Storage. V části **skupiny prostředků**vyberte **použít existující** a vyberte také stejnou skupinu prostředků. Zkontrolujte **potvrzuji, že** pole a potom klikněte na tlačítko **vytvořit**.

    ![Subcribing k Computer Vision API](../Images/create-vision-api.png)

1. Vraťte se do nabídky pro skupinu prostředků a klikněte na předplatné rozhraní API pro počítačové zpracování obrazu, který jste právě vytvořili. Zkopírujte adresu URL v části **koncový bod** k někde měli snadno k dispozici to za chvíli. Pak klikněte na tlačítko **zobrazení přístupových klíčů**.

    ![Zobrazení přístupových klíčů](../Images/copy-vision-endpoint.png)

1. V dalším okně, zkopírujte hodnotu **klíč 1** do schránky.

    ![Zkopírování přístupového klíče](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Přidat přihlašovací údaje pro počítačové zpracování obrazu

V dalším kroku přidáte požadované přihlašovací údaje do vaší aplikace tak, aby měl přístup k prostředkům pro počítačové zpracování obrazu

Otevřete vaši webovou aplikaci ASP.NET v sadě Visual Studio a přejděte **Web.config** souboru v kořenovém adresáři projektu. Přidejte následující příkazy, čímž `<appSettings>` část souboru, nahrazení `VISION_KEY` s klíčem, který jste zkopírovali v předchozím kroku, a `VISION_ENDPOINT` s adresou URL, který jste uložili v kroku před tímto.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Potom v Průzkumníku řešení klikněte pravým tlačítkem na projekt a využijte **spravovat balíčky NuGet** příkaz k instalaci balíčku **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Tento balíček obsahuje typy potřebných k volání rozhraní API pro počítačové zpracování obrazu.

## <a name="add-metadata-generation-code"></a>Přidat do kódu generování metadat

V dalším kroku přidáte kód, který ve skutečnosti využívá službu pro počítačové zpracování obrazu vytvořit metadata pro Image. Tyto kroky se použijí na aplikaci technologie ASP.NET v testovacím prostředí, ale můžete je upravit podle vašich vlastních aplikací. Důležité je, že v tomto okamžiku můžete mít webovou aplikaci ASP.NET, který můžete nahrávat obrázky do kontejneru služby Azure Storage, čtení z něj Image a zobrazí je v zobrazení. Pokud si nejste jisti, o tom, je nejlepší postupovat podle [cvičení 3 z Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Otevřít *HomeController.cs* souboru v projektu **řadiče** složky a přidejte následující `using` příkazů v horní části souboru:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Potom pokračujte **nahrát** metoda; tato metoda převede a nahrávání imagí do úložiště objektů blob. Přidejte následující kód bezprostředně po bloku, který začíná `// Generate a thumbnail` (nebo na konci procesu vytvoření bitové kopie objektů blob). Tento kód používá objekt blob obsahující bitovou kopii (`photo`) a používá ke generování popis obrázku pro počítačové zpracování obrazu. Rozhraní API pro počítačové zpracování obrazu také vygeneruje seznam klíčových slov, které se vztahují k bitové kopii. Vygenerovaný popis a klíčová slova jsou uloženy v metadata objektu blob tak, že je možné načíst později.

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

1. Dále přejděte na **Index** metoda ve stejném souboru; Tato metoda vytvoří výčet uloženého obrázku objektů BLOB v kontejneru cílové objektů blob (jako **IListBlobItem** instance) a předává je do zobrazení aplikace. Nahradit `foreach` blokovat v této metodě následujícím kódem. Tento kód volá **CloudBlockBlob.FetchAttributes** zobrazíte každý objekt blob je připojen metadat. Extrahuje popis počítačem generované (`caption`) z metadat a přidá jej do **BlobInfo** objektu, který bude předána do zobrazení.
    
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

Uložte změny v sadě Visual Studio a stiskněte klávesu **Ctrl + F5** ke spuštění aplikace v prohlížeči. Můžete nahrát několik obrázků ve složce "fotografie" v prostředky testovacího prostředí nebo z vlastní složky aplikace. Pokud můžete najet myší na některou k imagí v zobrazení, by měla okno popisku se zobrazí a zobrazit počítačem generované titulek pro bitovou kopii.

![Počítačem generované titulek](../Images/thumbnail-with-tooltip.png)

Chcete-li zobrazit všechny připojené metadat, zobrazíte kontejner úložiště, které používáte pro obrázky pomocí Průzkumníka služby Azure Storage. Klikněte pravým tlačítkem na některý z objektů BLOB v kontejneru a vyberte **vlastnosti**. V dialogovém okně uvidíte seznam párů klíč hodnota. Popis počítačem vytvořené bitové kopie je uložena v položky "Titulek" a hledat klíčová slova jsou uloženy v "Tag0," "značky 1" a tak dále. Jakmile budete hotovi, klikněte na tlačítko **zrušit** zavřete dialogové okno.

![Metadata objektu blob](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci na vaší webové aplikace, najdete v článku [další kroky](#next-steps) oddílu. Pokud nemáte v úmyslu pokračovat v používání této aplikace, odstraňte všechny prostředky specifické pro aplikace. K tomuto účelu můžete jednoduše odstranit skupinu prostředků, která obsahuje vaše předplatné služby Azure Storage a prostředků pro počítačové zpracování obrazu. Tato akce odebere účet úložiště, objekty BLOB nahrané do ní a prostředek služby App Service, které jsou potřebné pro připojení s webovou aplikací ASP.NET. 

Chcete-li odstranit skupinu prostředků, otevřete **skupiny prostředků** okno na portálu přejděte do skupiny prostředků, který jste použili pro tento projekt a klikněte na tlačítko **odstranit skupinu prostředků** v horní části stránky zobrazení. Zobrazí výzva k zadání názvu skupiny prostředků pro potvrzení, že chcete odstranit, protože po odstranění, nejde obnovit skupinu prostředků.

## <a name="next-steps"></a>Další postup

V tomto kurzu služby Azure pro počítačové zpracování obrazu integrována do existující webové aplikace se automaticky vygenerovat titulků a klíčová slova pro objekt blob bitové kopie, protože jste nahráli. V dalším kroku najdete Azure Storage testovacího prostředí, cvičení 6, a zjistěte, jak přidat integraci vyhledávacích funkcí do webové aplikace. Toto využívá Hledat klíčová slova, která generuje služba pro počítačové zpracování obrazu.

> [!div class="nextstepaction"]
> [Přidání vyhledávání do vaší aplikace](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
