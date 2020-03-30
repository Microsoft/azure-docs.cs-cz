---
title: Nahrání souborů do účtu Azure Media Services pomocí REST | Dokumenty společnosti Microsoft
description: Naučte se, jak získat mediální obsah do mediálních služeb vytvořením a nahráním datových zdrojů.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d5b84a9d216457720e9bd4e17b002d6ab9490f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888605"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Odeslání souborů do účtu Azure Media Services pomocí REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [Odpočinku](media-services-rest-upload-files.md)
> * [Portál](media-services-portal-upload-files.md)
> 

Ve službě Media Services můžete digitální soubory nahrát do assetu. Entita [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory skrytých titulků (a metadata o těchto souborech).)  Jakmile jsou soubory nahrány do datového zdroje, váš obsah je bezpečně uložen v cloudu pro další zpracování a streamování. 

V tomto kurzu se dozvíte, jak nahrát soubor a další operace s ním spojené:

> [!div class="checklist"]
> * Nastavit Pošťáka pro všechny operace nahrávání
> * Připojení ke službě Media Services 
> * Vytvoření zásadpřístupu s oprávněním k zápisu
> * Vytvoření datového zdroje
> * Vytvoření lokátoru SAS a vytvoření adresy URL pro nahrávání
> * Nahrání souboru do úložiště objektů blob pomocí adresy URL pro nahrávání
> * Vytvoření metadat v datovém zdroji pro mediální soubor, který jste nahráli

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete.
- [Vytvořte účet Azure Media Services pomocí portálu Azure](media-services-portal-create-account.md).
- Projděte si článek [O přístupu k rozhraní AZURE Media Services API s přehledem ověřování AAD.](media-services-use-aad-auth-to-access-ams-api.md)
- Další informace: Projděte [si také použití ověřování Azure AD pro přístup k rozhraní API mediálních služeb s](https://docs.microsoft.com/azure/media-services/previous/media-services-rest-connect-with-aad) rest článku.
- Nakonfigurujte **Postmana,** jak je popsáno v [příkazu Konfigurovat postman pro volání rozhraní REST API služby Media Services](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Požadavky

Při použití rozhraní REST API služby Media Services platí následující důležité informace:
 
* Při přístupu k entitám pomocí rozhraní API REST služby Media Services je nutné nastavit konkrétní pole záhlaví a hodnoty v požadavcích HTTP. Další informace naleznete [v tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md). <br/>Kolekce Postman použitá v tomto kurzu se postará o nastavení všech potřebných záhlaví.
* Služba Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres URL pro streamovaný obsah (například http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Z tohoto důvodu není povoleno kódování procentuální chod. Hodnota **Name** vlastnost nemůže mít žádné z následujících [procent-kódování vyhrazené znaky:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Také může existovat pouze jeden '.' pro příponu názvu souboru.
* Délka názvu by neměla být větší než 260 znaků.
* Maximální velikost souboru podporovaná při zpracování ve službě Media Services je omezená. Podrobnosti o omezení velikosti souboru najdete v [tomto článku](media-services-quotas-and-limitations.md).

## <a name="set-up-postman"></a>Nastavení nástroje Postman

Postup nastavení Pošťáka pro tento kurz naleznete v [tématu Konfigurace pořazovat.](media-rest-apis-with-postman.md)

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

1. Přidejte hodnoty připojení do vašeho prostředí. 

    Některé proměnné, které jsou součástí [prostředí](postman-environment.md) **MediaServices** je třeba nastavit ručně před spuštěním provádění operací definovaných v [kolekci](postman-collection.md).

    Pokud chcete získat hodnoty pro prvních pět proměnných, [přečtěte si přístup k rozhraní API Azure Media Services s ověřováním Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-import-env.png)
2. Zadejte hodnotu proměnné prostředí **MediaFileName.**

    Zadejte název souboru média, které chcete nahrát. V tomto příkladu budeme nahrát BigBuckBunny.mp4. 
3. Zkontrolujte soubor **AzureMediaServices.postman_environment.json.** Uvidíte, že téměř všechny operace v kolekci provést skript "test". Skripty trvat některé hodnoty vrácené odpověď a nastavit příslušné proměnné prostředí.

    Například první operace získá přístupový token a nastavte jej na proměnné prostředí **AccessToken,** který se používá ve všech ostatních operacích.

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
4. Na levé straně okna **Pošťák,** klikněte na **1. Získejte token** -> auth AAD**Získejte token Azure AD pro instanční objekt**.

    Část URL je vyplněna proměnnou prostředí **AzureADSTSEndpoint** (dříve v kurzu nastavíte hodnoty proměnných prostředí, které podporují kolekci).

    ![Nahrání souboru](./media/media-services-rest-upload-files/postment-get-token.png)

5. Stiskněte **Odeslat**.

    Můžete vidět odpověď, která obsahuje "access_token". Skript "test" převezme tuto hodnotu a nastaví proměnnou prostředí **AccessToken** (jak je popsáno výše). Pokud zkontrolujete proměnné prostředí, uvidíte, že tato proměnná nyní obsahuje přístupový token (token nosiče) hodnota, která se používá ve zbývajících operacích. 

    Pokud vyprší platnost tokenu projít krok "Získat token Azure AD pro instanční objekt" znovu. 

## <a name="create-an-access-policy-with-write-permission"></a>Vytvoření zásadpřístupu s oprávněním k zápisu

### <a name="overview"></a>Přehled 

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace naleznete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Před nahráním souborů do úložiště objektů blob nastavte práva zásad přístupu pro zápis do datového zdroje. Chcete-li to provést, post požadavek HTTP na accesspolicies entity set. Definujte hodnotu DurationInMinutes při vytvoření nebo se v odpovědi zobrazí chybová zpráva 500 Internal Server. Další informace o zásadách přístupu naleznete v [tématu AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Vytvoření zásad přístupu

1. Vyberte **možnost AccessPolicy** -> **Create AccessPolicy for Upload**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skript "test" získá ID AccessPolicy a nastaví příslušnou proměnnou prostředí.

## <a name="create-an-asset"></a>Vytvoření datového zdroje

### <a name="overview"></a>Přehled

Datový [zdroj](https://docs.microsoft.com/rest/api/media/operations/asset) je kontejner pro více typů nebo sad objektů ve službě Media Services, včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a souborů skrytých titulků. V rozhraní REST API vyžaduje vytvoření datového zdroje odeslání požadavku POST službě Media Services a umístění veškerých informací o vlastnostech o vašem datovém zdroji do těla požadavku.

Jednou z vlastností, které můžete přidat při vytváření datového zdroje, jsou **možnosti**. Můžete zadat jednu z následujících možností šifrování: **Žádné** (výchozí, žádné šifrování se používá), **StorageEncrypted** (pro obsah, který byl předem zašifrován pomocí šifrování úložiště na straně klienta), **CommonEncryptionProtected**nebo **EnvelopeEncryptionProtected**. Pokud máte šifrovaný datový zdroj, je třeba nakonfigurovat zásady doručování. Další informace naleznete [v tématu Konfigurace zásad poskytování majetku](media-services-rest-configure-asset-delivery-policy.md).

Pokud je váš prostředek zašifrován, musíte vytvořit **contentkey** a propojit ho s datovým zdrojem, jak je popsáno v následujícím článku: [Jak vytvořit ContentKey](media-services-rest-create-contentkey.md). Po nahrání souborů do datového zdroje je třeba aktualizovat vlastnosti šifrování entity **AssetFile** hodnotami, které jste získali během šifrování **datového zdroje.** Udělejte to pomocí požadavku **MERGE** HTTP. 

V tomto příkladu vytváříme nešifrovaný datový zdroj. 

### <a name="create-an-asset"></a>Vytvoření datového zdroje

1. Vyberte **položku Vytvořit** -> **datový zdroj**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skript "test" získá ID prostředku a nastaví příslušnou proměnnou prostředí.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Vytvoření lokátoru SAS a vytvoření adresy URL pro nahrávání

### <a name="overview"></a>Přehled

Jakmile budete mít accesspolicy a locator nastavit, skutečný soubor se nahraje do kontejneru úložiště objektů blob Azure pomocí azure storage REST API. Soubory je nutné nahrát jako objekty BLOB bloku. Objekty BLOB stránky nejsou podporovány službou Azure Media Services.  

Další informace o práci s objekty BLOB úložiště Azure najdete v [tématu rozhraní REST API služby blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Chcete-li získat skutečnou adresu URL pro nahrání, vytvořte lokátor SAS (viz níže). Lokátory definují čas zahájení a typ koncového bodu připojení pro klienty, kteří chtějí získat přístup k souborům ve datovém zdroji. Můžete vytvořit více lokátoru entity pro daný PřístupZásad a Asset dvojice pro zpracování různých požadavků a potřeb klientů. Každý z těchto lokátorů používá hodnotu StartTime plus hodnotu DurationInMinutes zásad accesspolicy k určení doby, po kterou lze použít adresu URL. Další informace naleznete v [tématu Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

Adresa URL SAS má následující formát:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Požadavky

Musí být splněny určité předpoklady:

* K danému prostředku nelze najednou přidružit více než pět jedinečných lokátorů. Další informace naleznete v tématu Locator.
* Pokud potřebujete okamžitě nahrát soubory, měli byste nastavit hodnotu StartTime na pět minut před aktuálním časem. Důvodem je, že může být hodiny zkosení mezi klientským počítačem a media services. Hodnota StartTime musí být také v následujícím formátu DateTime: YYYY-MM-DDTHH:mm:ssZ (například "2014-05-23T17:53:50Z").    
* Může být 30-40 sekund zpoždění po vytvoření lokátoru, když je k dispozici pro použití.

### <a name="create-a-sas-locator"></a>Vytvoření lokátoru SAS

1. Vyberte **lokátor** -> **vytvořit lokátor SAS**.
2. Stiskněte **Odeslat**.

    Skript "test" vytvoří "Nahrát adresu URL" na základě názvu mediálního souboru, který jste zadali, a informací o lokátoru SAS a nastaví příslušnou proměnnou prostředí.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Nahrání souboru do úložiště objektů blob pomocí adresy URL pro nahrávání

### <a name="overview"></a>Přehled

Teď, když máte adresu URL pro nahrávání, musíte napsat nějaký kód pomocí azure blob API přímo k nahrání souboru do kontejneru SAS. Další informace najdete v těchto článcích:

- [Použití rozhraní REST API pro službu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [UMÍSTIT blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Nahrání objektů BLOB do úložiště objektů Blob](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Nahrání souboru pomocí Pošťáka

Jako příklad používáme Postman k nahrání malého souboru .mp4. Může být limit velikosti souboru pro nahrávání binární prostřednictvím Postman.

Požadavek na nahrání není součástí kolekce **AzureMedia.** 

Vytvoření a nastavení nového požadavku:
1. Stisknutím **+** klávesy vytvořte novou kartu požadavku.
2. Vyberte **operaci PUT** a vložte **{{UploadURL}}** do adresy URL.
2. Ponechte kartu **Autorizace** tak, jak je (nenastavovat ji na **žeton nosiče**).
3. Na kartě **Záhlaví** zadejte: **Klíč**: "x-ms-blob-type" a **Value**: "BlockBlob".
2. Na kartě **Tělo** klepněte na **binární položku**.
4. Zvolte soubor s názvem, který jste zadali v proměnné prostředí **MediaFileName.**
5. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Vytvoření metadat v datovém zdroji

Po nahrání souboru je třeba vytvořit metadata ve datovém zdroji pro mediální soubor, který jste nahráli do úložiště objektů blob přidruženého k vašemu datovému zdroji.

1. Vyberte **položku AssetFiles** -> **CreateFileInfos**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-file-info.png)

Soubor by měl být odeslán a jeho metadata nastavena.

## <a name="validate"></a>Ověření

Chcete-li ověřit, zda byl soubor úspěšně odeslán, můžete chtít zadat dotaz na [soubor AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) a porovnat **contentfilesize** (nebo jiné podrobnosti) s tím, co očekáváte, že uvidíte v novém datovém zdroji. 

Například následující operace **GET** přináší data souboru pro soubor datového zdroje (v nebo případ souboru BigBuckBunny.mp4). Dotaz používá [proměnné prostředí,](postman-environment.md) které jste nastavili dříve.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Odpověď bude obsahovat velikost, název a další informace.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Další kroky

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

Můžete také použít službu Azure Functions k aktivaci úlohy kódování při příchodu souboru do nakonfigurovaného kontejneru. Další informace najdete v [této ukázce](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

