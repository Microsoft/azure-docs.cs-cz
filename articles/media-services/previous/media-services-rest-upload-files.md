---
title: Nahrání souborů do účtu Azure Media Services pomocí rozhraní REST | Dokumentace Microsoftu
description: Další informace o získání mediálního obsahu do Media Services pomocí vytváření a nahrávání prostředky.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: juliako
ms.openlocfilehash: 3b5c277f51b8ff1b2d3babf23329dcde829573a9
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813955"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Nahrání souborů do účtu Azure Media Services pomocí rozhraní REST
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Azure Portal](media-services-portal-upload-files.md)
> 

Ve službě Media Services můžete digitální soubory nahrát do assetu. [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) entita může obsahovat video, zvuk, obrázky, kolekci miniatur, text sleduje a titulků soubory (a metadata o těchto souborech.)  Jakmile soubory odešlete do assetu, váš obsah bezpečně uložen v cloudu pro další zpracování a streamování. 

V tomto kurzu se dozvíte, jak nahrát soubor a jiné operace spojené s ní:

> [!div class="checklist"]
> * Nastavení nástroje Postman pro všechny operace nahrávání
> * Připojení ke službě Media Services 
> * Vytvoření zásad přístupu se oprávnění k zápisu
> * Vytvoření assetu
> * Vytvoření lokátoru SAS a vytvořit adresu URL pro odeslání
> * Nahrání souboru do úložiště objektů blob pomocí adresy URL pro odeslání
> * Vytvoření metadat v prostředku pro soubor média, který jste nahráli

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Vytvoření účtu služby Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
- Zkontrolujte [přístup k API služby Azure Media pomocí ověřování AAD, přehled](media-services-use-aad-auth-to-access-ams-api.md) článku.
- Konfigurovat **Postman** jak je popsáno v [volá konfigurace nástroje Postman pro REST API služby Media Services](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Požadavky

Při použití rozhraní REST API pro Media Services, platí následující aspekty:
 
* Při přístupu k entit pomocí REST API služby Media Services, musíte nastavit specifická pole hlaviček a hodnoty v požadavcích HTTP. Další informace najdete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md). <br/>Kolekce Postman používají v tomto kurzu se postará o nastavení všechny potřebné hlavičky.
* Služba Media Services využívá hodnoty vlastnosti IAssetFile.Name při vytváření adres URL pro streamování obsahu (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování procent. Hodnota **název** vlastností nesmí obsahovat žádný z následujících [procent kódování – vyhrazené znaky](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Kromě toho může existovat pouze jeden "." pro příponu názvu souboru.
* Délka názvu nesmí být delší než 260 znaků.
* Maximální velikost souboru podporovaná při zpracování ve službě Media Services je omezená. Podrobnosti o omezení velikosti souboru najdete v [tomto článku](media-services-quotas-and-limitations.md).

## <a name="set-up-postman"></a>Nastavení nástroje Postman

Pokyny k nastavení nástroje Postman pro účely tohoto kurzu, najdete v článku [nakonfigurovat Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

1. Hodnoty připojení přidejte do svého prostředí. 

    Některé proměnné, které jsou součástí **MediaServices** [prostředí](postman-environment.md) nutné ručně nastavit před zahájením provádění operace definované v [kolekce](postman-collection.md).

    K získání hodnot pro prvních pět proměnných, naleznete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-env.png)
2. Zadejte hodnotu **MediaFileName** proměnné prostředí.

    Zadejte název souboru médií, které se chystáte nahrát. V tomto příkladu budeme nahrávat BigBuckBunny.mp4. 
3. Zkontrolujte **AzureMediaServices.postman_environment.json** souboru. Uvidíte, že téměř všechny operace v kolekci spuštění skriptu "test". Skript provést některé hodnoty vrácené odpovědi a nastavit příslušné proměnné prostředí.

    Například první operace získá přístupový token a nastavte ho na **AccessToken** proměnnou prostředí, která se používá v jiné operace.

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
4. Na levé straně **Postman** okna, klikněte na **1. Získání tokenu ověřování AAD** -> **získat Azure AD tokenu instančního objektu pro**.

    Část adresy URL je vyplněna **AzureADSTSEndpoint** proměnné prostředí (dříve v tomto kurzu nastavíte hodnoty proměnných prostředí, které podporují kolekci).

    ![Nahrání souboru](./media/media-services-rest-upload-files/postment-get-token.png)

5. Stiskněte **Odeslat**.

    Zobrazí se odpověď, která obsahuje "access_token". "Test" skript používá tuto hodnotu a nastaví **AccessToken** proměnné prostředí (jak je popsáno výše). Když si zblízka proměnných prostředí, uvidíte, že tato proměnná teď obsahuje hodnotu přístup token (nosný token), který se používá ve zbývající části operace. 

    Pokud vyprší platnost tokenu projděte "Získat Azure AD Token pro službu objektu zabezpečení" krok znovu. 

## <a name="create-an-access-policy-with-write-permission"></a>Vytvoření zásad přístupu se oprávnění k zápisu

### <a name="overview"></a>Přehled 

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Před nahráním všech souborů do úložiště objektů blob, nastavte přístup zásady oprávnění pro zápis do assetu. K tomuto účelu POŠLE požadavek HTTP do sady entit AccessPolicies. Definovat hodnotu doba trvání v minutách, po vytvoření nebo se zobrazí chybovou zprávu 500 interní Server vrátit v odpovědi. Další informace o AccessPolicies najdete v tématu [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Vytvoření zásad přístupu

1. Vyberte **AccessPolicy** -> **vytvoření AccessPolicy pro nahrávání**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-access-policy.png)

    "Test" skript získá AccessPolicy Id a nastaví proměnnou příslušné prostředí.

## <a name="create-an-asset"></a>Vytvoření assetu

### <a name="overview"></a>Přehled

[Asset](https://docs.microsoft.com/rest/api/media/operations/asset) je kontejner pro více typy nebo množiny objektů ve službě Media Services, včetně videa, zvuk, obrázky, kolekci miniatur, textové stopy a soubory s titulky. Vytvoření prostředku v rozhraní REST API vyžaduje odesílá požadavek POST do služby Media Services a umístit všechny informace o vlastnosti týkající se vašeho prostředku v textu požadavku.

Jedna z vlastností, které můžete přidat při vytváření prostředku je **možnosti**. Můžete určit jednu z následujících možností šifrování: **Žádný** (výchozí, bez šifrování se používá), **StorageEncrypted** (pro obsah, který byl předem šifrované pomocí šifrování na straně klienta úložiště), **CommonEncryptionProtected**, nebo  **EnvelopeEncryptionProtected**. Až budete mít šifrované asset, musíte nakonfigurovat zásady doručení. Další informace najdete v tématu [konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).

Pokud váš asset se šifrují, musíte vytvořit **ContentKey** a odkaz na váš asset, jak je popsáno v následujícím článku: [Jak vytvořit ContentKey](media-services-rest-create-contentkey.md). Po nahrání souborů do assetu, budete muset aktualizovat vlastnosti šifrování na **AssetFile** entity hodnotami, které jste získali při **Asset** šifrování. To udělat pomocí **sloučit** požadavku HTTP. 

V tomto příkladu vytváříme prostředek nezašifrované. 

### <a name="create-an-asset"></a>Vytvoření assetu

1. Vyberte **prostředky** -> **vytvoření prostředku**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-asset.png)

    "Test" skript získá Id prostředku a nastaví proměnnou příslušné prostředí.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Vytvořit lokátor SAS a adresa URL pro odeslání

### <a name="overview"></a>Přehled

Jakmile budete mít AccessPolicy a Lokátor nastavit, skutečný soubor nahraje do kontejneru úložiště objektů Blob v Azure pomocí rozhraní REST API služby Azure Storage. Musíte nahrát soubory jako objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.  

Další informace o práci s objekty BLOB služby Azure storage najdete v tématu [rozhraní REST API služby Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Získat adresu URL skutečné nahrávání, vytvořte Lokátor SAS (viz dole). Lokátory definují čas spuštění a typ koncového bodu připojení pro klienty, kteří mají přístup k souborům v prostředku. Můžete vytvořit více Lokátor entit pro danou AccessPolicy a Asset dvojici pro zpracování různých klientských požadavků a potřebám. Každá z těchto lokátory používá hodnoty StartTime plus hodnotu doba trvání v minutách AccessPolicy určit dobu, kterou lze použít adresu URL. Další informace najdete v tématu [Lokátor](https://docs.microsoft.com/rest/api/media/operations/locator).

Adresa URL SAS má následující formát:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Požadavky

Musí být splněny určité předpoklady:

* Nemůžete mít více než pět jedinečné lokátory přidružené k dané Assetu najednou. Další informace najdete v tématu Lokátor.
* Pokud je potřeba nahrát soubory hned, byste měli nastavit položka StartTime hodnotu 5 minut před aktuálním časem. Je to proto můžou existovat hodiny nerovnoměrné rozdělení mezi klientské počítače a služby Media Services. Také vaše hodnoty StartTime musí být ve formátu data a času: RRRR-MM-: ssZ (například "2014-05-23T17:53:50Z").    
* Může být druhý 30 – 40 zpoždění, jakmile se vytvoří Lokátor, až bude k dispozici pro použití.

### <a name="create-a-sas-locator"></a>Vytvoření lokátoru SAS

1. Vyberte **Lokátor** -> **vytvořit lokátor SAS**.
2. Stiskněte **Odeslat**.

    "Test" skript vytvoří "Adresa URL pro odeslání" podle názvu souboru média, který jste zadali a informace o lokátoru SAS a nastaví proměnnou příslušné prostředí.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Nahrání souboru do úložiště objektů blob pomocí adresy URL pro odeslání

### <a name="overview"></a>Přehled

Teď, když máte adresu URL pro odeslání, musíte kódu s využitím rozhraní API služby Azure Blob přímo k nahrání souboru do kontejneru SAS. Další informace najdete v následujících článcích:

- [Pomocí rozhraní REST API služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [PUT Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Nahrání objektů BLOB do služby Blob storage](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Nahrát soubor pomocí nástroje Postman

Jako příklad použijeme Postman k nahrání souboru MP4 malé. Na nahrávání binárního souboru pomocí Postman může být limit velikosti souboru.

Žádost o nahrání není součástí **AzureMedia** kolekce. 

Vytvořit a nastavit novou žádost:
1. Stisknutím klávesy **+**, chcete-li vytvořit novou kartu žádosti.
2. Vyberte **UMÍSTIT** operace a vložit **{{UploadURL}}** v adrese URL.
2. Ponechte **autorizace** kartě je (nenastavujte na **nosného tokenu**).
3. V **záhlaví** kartu, zadejte: **Klíč**: "x-ms-blob-type" a **hodnotu**: "BlockBlob".
2. V **tělo** klikněte na tlačítko **binární**.
4. Vyberte soubor s názvem, který jste zadali v **MediaFileName** proměnné prostředí.
5. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Vytvoření metadat v prostředku

Jakmile soubor odeslal, je potřeba vytvořit metadat v prostředku pro soubor média, který jste nahráli do úložiště objektů blob přidružený asset.

1. Vyberte **AssetFiles** -> **CreateFileInfos**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-file-info.png)

Soubor musí být nahrán a nastavení jeho metadata.

## <a name="validate"></a>Ověření

K ověření, že soubor byl úspěšně nahrán, chcete zadat dotaz [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) a porovnání **ContentFileSize** (nebo jiných podrobnostech) můžete očekávat v nový prostředek. 

Například následující **získat** operace přináší datový soubor k nahrání souboru prostředků (v nebo v případě souboru BigBuckBunny.mp4). Pomocí dotazu [proměnné prostředí](postman-environment.md) , který jste nastavili dříve.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Odpověď bude obsahovat velikost, název a další informace.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Další postup

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

Můžete také použít službu Azure Functions k aktivaci úlohy kódování při příchodu souboru do nakonfigurovaného kontejneru. Další informace najdete v [této ukázce](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

