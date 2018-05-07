---
title: "Nahrání souborů do účtu Azure Media Services pomocí REST | Microsoft Docs"
description: "Další informace o získání mediálního obsahu ve službě Media Services pomocí vytvoření a odeslání prostředky."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: juliako
ms.openlocfilehash: 4ba6fdcec8d71326b02d71dbad429be8c2052171
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2018
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Nahrání souborů do účtu Media Services pomocí REST
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Azure Portal](media-services-portal-upload-files.md)
> 

Ve službě Media Services můžete digitální soubory nahrát do assetu. [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) entita může obsahovat video, zvuk, obrázky, kolekci miniatur, text sleduje a titulků soubory (a metadata o těchto souborech.)  Jakmile soubory odešlete do assetu, váš obsah bezpečně uložen v cloudu pro další zpracování a streamování. 

V tomto kurzu zjistěte, jak nahrát soubor a další operace s ním spojená:

> [!div class="checklist"]
> * Nastavení pro všechny operace nahrávání Postman
> * Připojení ke službě Media Services 
> * Vytvoření zásad přístupu se oprávnění k zápisu
> * Vytvořit prostředek
> * Vytvoření lokátoru SAS a vytvořit adresu URL pro odeslání
> * Nahrát soubor do úložiště objektů blob pomocí adresy URL pro odeslání
> * Vytvoření metadata v prostředku pro soubor média, který jste nahráli

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Vytvoření účtu Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
- Zkontrolujte [přístup k Azure Media Services API s Přehled ověřování AAD](media-services-use-aad-auth-to-access-ams-api.md) článku.
- Konfigurace **Postman** jak je popsáno v [Postman nakonfigurovat pro Media Services REST API volá](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Požadavky

Při použití Media Services REST API, platí následující aspekty:
 
* Při přístupu k entity pomocí Media Services REST API, musíte nastavit specifická pole hlaviček a hodnoty ve své žádosti HTTP. Další informace najdete v tématu [instalační program pro Media Services REST API vývoj](media-services-rest-how-to-use.md). <br/>Kolekce Postman použili v tomto kurzu má na starosti nastavení všechny potřebné hlavičky.
* Služba Media Services použije hodnotu vlastnosti IAssetFile.Name při sestavování adresy URL pro streamování obsah (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování v procentech. Hodnota **název** vlastnost nemůže mít žádné z následujících [procent kódování vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Navíc může existovat pouze jedna '.' pro příponu názvu souboru.
* Délka názvu nesmí být větší než 260 znaků.
* Maximální velikost souboru podporovaná při zpracování ve službě Media Services je omezená. V tématu [to](media-services-quotas-and-limitations.md) článku podrobnosti o omezení velikosti souboru.

## <a name="set-up-postman"></a>Nastavit Postman

Pokyny o tom, jak nastavit Postman pro účely tohoto kurzu, najdete v části [konfigurace Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

1. Přidáte připojení hodnoty do vašeho prostředí. 

    Některé proměnné, které jsou součástí **MediaServices** [prostředí](postman-environment.md) je nutné ručně nastavit před zahájením provádění operací, které jsou definované v [kolekce](postman-collection.md).

    Pokud chcete získat hodnoty pro proměnné prvních pět, najdete v části [přístup k Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-env.png)
2. Zadejte hodnotu pro **MediaFileName** proměnné prostředí.

    Zadejte název souboru média, které máte v úmyslu nahrát. V tomto příkladu přidáme nahrát BigBuckBunny.mp4. 
3. Zkontrolujte **AzureMediaServices.postman_environment.json** souboru. Zobrazí se, že téměř všechny operace v kolekci spuštění skriptu "test". Skript provést některé hodnoty vrácené odpovědi a nastavte příslušné proměnné prostředí.

    Například první operace získá přístupový token a nastavte ji na **AccessToken** proměnné prostředí, která se používá v jiné operace.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. Na levé straně **Postman** klikněte na **1. Získání tokenu ověřování AAD** -> **získat Azure AD tokenu pro instanční objekt**.

    Část adresy URL, naplní se **AzureADSTSEndpoint** proměnnou prostředí (která je nastavena hodnota dříve v tomto kurzu).
    
5. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postment-get-token.png)

    Můžete zobrazit odpověď, který obsahuje "access_token". "Test" skript používá tuto hodnotu a nastaví **AccessToken** proměnnou prostředí (jak je popsáno výše). Pokud si projdete proměnných prostředí, zobrazí se tato proměnná nyní obsahuje token (token nosiče) hodnotu přístupu, která se používá ve zbývající části operace. 

    Pokud platnost tokenu vyprší projít "Získat Azure AD tokenu pro služby hlavní" krok opakujte. 

## <a name="create-an-access-policy-with-write-permission"></a>Vytvoření zásad přístupu se oprávnění k zápisu

### <a name="overview"></a>Přehled 

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Před nahráním do úložiště objektů blob všechny soubory, nastavení přístupu zásady oprávnění pro zápis do prostředek. Kvůli tomu odeslat požadavek HTTP do sady entit AccessPolicies. Zadejte hodnotu doba trvání v minutách, po vytvoření nebo zobrazí 500 chybová zpráva Interní Server zpět v odpovědi. Další informace o AccessPolicies najdete v tématu [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Vytvoření zásad přístupu

1. Vyberte **AccessPolicy** -> **vytvořit AccessPolicy pro nahrávání**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skript "test" AccessPolicy Id získá a nastaví proměnnou příslušné prostředí.

## <a name="create-an-asset"></a>Vytvořit prostředek

### <a name="overview"></a>Přehled

[Asset](https://docs.microsoft.com/rest/api/media/operations/asset) je kontejner pro více typů nebo sady objektů ve službě Media Services, včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory titulků. Vytvoření prostředek v rozhraní REST API vyžaduje odesílání požadavku POST ke službě Media Services a umístění žádné vlastnosti informace o váš asset v textu požadavku.

Jedna z vlastností, které můžete přidat při vytváření prostředek je **možnosti**. Můžete zadat jeden z následujících možností šifrování: **žádné** (použije se výchozí, žádné šifrování), **StorageEncrypted** (pro obsah, který byl předem šifrované pomocí šifrování úložiště na straně klienta), **CommonEncryptionProtected**, nebo **EnvelopeEncryptionProtected**. Až budete mít šifrované asset, musíte nakonfigurovat zásady doručení. Další informace najdete v tématu [konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).

Pokud je váš asset šifrovat, musíte vytvořit **ContentKey** a tu propojit na váš asset, jak je popsáno v následujícím článku: [postup vytvoření ContentKey](media-services-rest-create-contentkey.md). Po odeslání souborů do assetu, musíte aktualizovat vlastnosti šifrování na **AssetFile** entity s hodnotami, které jste získali při **Asset** šifrování. To provést pomocí **SLOUČENÍ** požadavek HTTP. 

V tomto příkladu vytváříme prostředek nešifrované. 

### <a name="create-an-asset"></a>Vytvořit prostředek

1. Vyberte **prostředky** -> **vytvoření prostředku**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skript "test" získá Id prostředku a nastaví proměnnou příslušné prostředí.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Vytvoření lokátoru SAS a vytvořit adresa URL pro odeslání

### <a name="overview"></a>Přehled

Jakmile máte AccessPolicy a Lokátor nastavit, skutečný soubor nahraje do kontejneru Azure Blob Storage pomocí rozhraní API REST úložiště Azure. Soubory musíte nahrát jako objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.  

Další informace o práci s objekty BLOB úložiště Azure najdete v tématu [rozhraní API REST služby objektů Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Pokud chcete získat adresu URL skutečného odeslání, vytvoření lokátoru SAS (zobrazené dole). Lokátory zadejte čas spuštění a typ koncového bodu připojení pro klienty, kteří mají přístup k souborům v prostředek. Můžete vytvořit více Lokátor entit pro danou AccessPolicy a Asset dvojici pro zpracování různých klientských požadavků a potřebách. Každá z těchto lokátory používá hodnoty StartTime plus hodnota Doba trvání v minutách AccessPolicy určit dobu, kterou lze použít adresu URL. Další informace najdete v tématu [Lokátor](https://docs.microsoft.com/rest/api/media/operations/locator).

Adresa URL typu SAS má následující formát:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Požadavky

Musí být splněny určité předpoklady:

* Nemůže mít více než pět jedinečný lokátory spojené s danou Asset najednou. Další informace najdete v tématu lokátoru.
* Pokud potřebujete k nahrání souborů okamžitě, byste měli nastavit vaše hodnoty StartTime 5 minut před aktuálním časem. Je to proto, že je možné, hodiny zkosení mezi klientský počítač a služba Media Services. V následujícím formátu data a času musí být také hodnota pro čas spuštění: rrrr-MM-ddTHH (například "2014-05-23T17:53:50Z").    
* Může být druhý 30-40 zpoždění po vytvoření lokátoru k případě, že je k dispozici pro použití.

### <a name="create-a-sas-locator"></a>Vytvoření lokátoru SAS

1. Vyberte **Lokátor** -> **vytvořit lokátor SAS**.
2. Stiskněte **Odeslat**.

    Skript "test" vytvoří "Adresa URL pro odeslání" na základě názvu souboru média, které jste zadali a informace o lokátoru SAS a nastavuje proměnnou příslušné prostředí.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Nahrát soubor do úložiště objektů blob pomocí adresy URL pro odeslání

### <a name="overview"></a>Přehled

Teď, když máte adresu odeslání, budete muset napsat kód pomocí rozhraní API objektů Blob Azure přímo k odeslání souboru do kontejneru SAS. Další informace najdete v následujících článcích:

- [Pomocí rozhraní REST API Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [UVEĎTE objektů Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Nahrát objektů BLOB do úložiště objektů Blob](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Nahrát soubor s Postman

Jako příklad používáme Postman k nahrání souboru malé MP4. Na odesílání binární prostřednictvím Postman může být omezení velikosti souboru.

Žádost o odeslání není součástí **AzureMedia** kolekce. 

Vytvoření a nastavení novou žádost:
1. Stiskněte klávesu  **+** , chcete-li vytvořit novou kartu požadavku.
2. Vyberte **PUT** operace a vložení **{{UploadURL}}** v adrese URL.
2. Nechte **autorizace** kartě, jako je (nenastavujte na **nosného tokenu**).
3. V **hlavičky** kartě, zadejte: **klíč**: "x-ms-blob-type" a **hodnotu**: "BlockBlob".
2. V **textu** , klikněte na **binární**.
4. Vybrat soubor s názvem, který jste zadali v **MediaFileName** proměnné prostředí.
5. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Vytvoření metadata v prostředku

Jakmile soubor byl odeslán, musíte vytvořit metadata v prostředku pro soubor média, který jste nahráli do úložiště objektů blob přidružený asset.

1. Vyberte **AssetFiles** -> **CreateFileInfos**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-file-info.png)

Soubor musí být nahrán a nastavit jeho metadata.

## <a name="validate"></a>Ověření

K ověření, že soubor byl úspěšně odeslán, můžete chtít zadat dotaz [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) a porovnat **ContentFileSize** (nebo další podrobnosti) na co byste měli vidět v nový prostředek. 

Například následující **získat** operace přináší souborová data asset souboru (v nebo v případě souboru BigBuckBunny.mp4). Dotaz používá [proměnné prostředí](postman-environment.md) dříve nastavený.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Odpověď bude obsahovat velikost, název a další informace.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Další postup

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

Můžete také použít službu Azure Functions k aktivaci úlohy kódování při příchodu souboru do nakonfigurovaného kontejneru. Další informace najdete v [této ukázce](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

