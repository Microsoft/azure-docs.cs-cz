---
title: Nahrání souborů do účtu Azure Media Services pomocí REST | Microsoft Docs
description: Naučte se, jak získat mediální obsah do Media Services vytvořením a nahráním prostředků pomocí REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 9f27a427df07302840ce719d35c7876f9dc17dbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012934"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Odeslání souborů do účtu Azure Media Services pomocí REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Azure Portal](media-services-portal-upload-files.md)
> 

Ve službě Media Services můžete digitální soubory nahrát do assetu. Entita [assetu](/rest/api/media/operations/asset) může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech.)  Po nahrání souborů do assetu je váš obsah bezpečně uložen v cloudu pro další zpracování a streamování. 

V tomto kurzu se dozvíte, jak nahrát soubor a další operaci s ním spojenou:

> [!div class="checklist"]
> * Nastavit post pro všechny operace odeslání
> * Připojení ke službě Media Services 
> * Vytvoření zásady přístupu s oprávněním k zápisu
> * Vytvoření prostředku
> * Vytvořit Lokátor SAS a vytvořit adresu URL pro odeslání
> * Nahrání souboru do úložiště objektů BLOB pomocí adresy URL pro nahrání
> * V assetu vytvořte metadata pro mediální soubor, který jste nahráli.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Vytvořte účet Azure Media Services pomocí Azure Portal](media-services-portal-create-account.md).
- Přečtěte si článek [Přehled přístupu k rozhraní Azure Media Services API pomocí ověřování AAD](media-services-use-aad-auth-to-access-ams-api.md) .
- Další informace najdete také v tématu [použití ověřování Azure AD pro přístup k rozhraní Media Services API pomocí REST](./media-services-rest-connect-with-aad.md) .
- Nakonfigurujte **metodu post** , jak je popsáno v tématu [Configure the post for Media Services REST API Calls](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Požadavky

Při použití Media Services REST API platí následující předpoklady:
 
* Při přístupu k entitám pomocí Media Services REST API musíte v požadavcích HTTP nastavit konkrétní pole a hodnoty hlaviček. Další informace najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md). <br/>Kolekce po použití v tomto kurzu se postará o nastavení všech potřebných hlaviček.
* Media Services používá hodnotu vlastnosti IAssetFile.Name při vytváření adres URL pro obsah streamování (například http://{AMSAccount}. Origin. MediaServices. Windows. NET/{GUID}/{IAssetFile. Name}/streamingParameters.) Z tohoto důvodu není povolena procentuální kódování. Hodnota vlastnosti **Name** nemůže obsahovat žádný z následujících [znaků rezervovaných v procentech](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Přípona názvu souboru může mít taky jenom jeden znak.
* Délka názvu by neměla být větší než 260 znaků.
* Maximální velikost souboru podporovaná při zpracování ve službě Media Services je omezená. Podrobnosti o omezení velikosti souboru najdete v [tomto článku](media-services-quotas-and-limitations.md).

## <a name="set-up-postman"></a>Nastavení nástroje Postman

Postup pro nastavení post pro tento kurz najdete v tématu [Configure a post](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Připojení ke službě Media Services

1. Přidejte hodnoty připojení do svého prostředí. 

    Některé proměnné, které jsou součástí prostředí **MediaServices** [](postman-environment.md) , je třeba nastavit ručně, aby bylo možné zahájit provádění operací definovaných v [kolekci](postman-collection.md).

    Pokud chcete získat hodnoty pro prvních pět proměnných, přečtěte si téma [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Snímek obrazovky zobrazující ikonu "ozubeného kola" vybraný v pravém horním rohu a prvních pět proměnných vybraných z karty "prostředí pro správu".](./media/media-services-rest-upload-files/postman-import-env.png)
2. Zadejte hodnotu pro proměnnou prostředí **MediaFileName** .

    Zadejte název souboru média, které hodláte odeslat. V tomto příkladu budeme nahrávat BigBuckBunny.mp4. 
3. Prověřte **AzureMediaServices.postman_environment.jsv** souboru. Uvidíte, že téměř všechny operace v kolekci spouštějí skript "test". Skripty přebírají některé hodnoty vrácené odpovědí a nastavily příslušné proměnné prostředí.

    Například první operace načte přístupový token a nastaví ji na proměnnou prostředí **AccessToken** , která se používá ve všech ostatních operacích.

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
4. Vlevo od okna **po** klikněte na **1. Získejte** token služby AAD pro ověření tokenu  ->  **služby Azure AD pro instanční objekt**.

    Část adresy URL je vyplněna proměnnou prostředí **AzureADSTSEndpoint** (dříve v tomto kurzu jste nastavili hodnoty proměnných prostředí, které podporují kolekci).

    ![Snímek obrazovky zobrazující "1. Získání ověřovacího tokenu D – získáte token Azure A D pro instanční objekt vybraný v okně "post" a vybrané tlačítko Odeslat.](./media/media-services-rest-upload-files/postment-get-token.png)

5. Stiskněte **Odeslat**.

    Můžete zobrazit odpověď obsahující "access_token". Skript "test" převezme tuto hodnotu a nastaví proměnnou prostředí **AccessToken** (jak je popsáno výše). Pokud prohlížíte proměnné prostředí, uvidíte, že tato proměnná nyní obsahuje hodnotu přístupového tokenu (token nosiče), která se používá ve zbývající části operací. 

    Pokud vyprší platnost tokenu, přečtěte si krok znovu získat token služby Azure AD pro instanční objekt. 

## <a name="create-an-access-policy-with-write-permission"></a>Vytvoření zásady přístupu s oprávněním k zápisu

### <a name="overview"></a>Přehled 

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

Před nahráním jakýchkoli souborů do úložiště objektů BLOB nastavte práva zásad přístupu pro zápis do assetu. Provedete to tak, že ODEŠLEte požadavek HTTP do sady entit AccessPolicies. Při vytváření Definujte hodnotu DurationInMinutes nebo v odpovědi obdržíte interní chybovou zprávu serveru 500. Další informace o AccessPolicies najdete v tématu [AccessPolicy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Vytvoření zásady přístupu

1. Vyberte **AccessPolicy**  ->  **vytvořit AccessPolicy pro nahrání**.
2. Stiskněte **Odeslat**.

    ![Snímek obrazovky, který zobrazuje "AccessPolicy-Create AccessPolicy for upload" Selected z nabídky vlevo a vybrané tlačítko Odeslat.](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skript test získá ID AccessPolicy a nastaví příslušnou proměnnou prostředí.

## <a name="create-an-asset"></a>Vytvoření prostředku

### <a name="overview"></a>Přehled

[Asset](/rest/api/media/operations/asset) je kontejner pro více typů nebo sad objektů v Media Services, včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a souborů titulků. V REST API vytvoření Assetu vyžaduje odeslání žádosti POST do Media Services a umístění jakýchkoli vlastností informací o prostředku do textu žádosti.

Jedna z vlastností, které lze přidat při vytváření assetu, je **možností**. Můžete určit jednu z následujících možností šifrování: **žádné** (výchozí, bez šifrování se nepoužívá), **StorageEncrypted** (pro obsah, který byl předem šifrovaný pomocí šifrování úložiště na straně klienta), **CommonEncryptionProtected** nebo **EnvelopeEncryptionProtected**. Pokud máte zašifrovaný prostředek, musíte nakonfigurovat zásady doručování. Další informace najdete v tématu [Konfigurace zásad doručení assetu](media-services-rest-configure-asset-delivery-policy.md).

Pokud je váš Asset zašifrovaný, musíte vytvořit **ContentKey** a propojit ho s vaším Assetem, jak je popsáno v následujícím článku: [jak vytvořit ContentKey](media-services-rest-create-contentkey.md). Po nahrání souborů do assetu je potřeba aktualizovat vlastnosti šifrování v entitě **AssetFile** s hodnotami, které jste získali během šifrování **assetu** . K tomu použijte požadavek HTTP **Merge** . 

V tomto příkladu vytváříme nešifrovaný prostředek. 

### <a name="create-an-asset"></a>Vytvoření prostředku

1. Vyberte **prostředky**  ->  **vytvořit Asset**.
2. Stiskněte **Odeslat**.

    ![Snímek obrazovky, který zobrazuje "Asset – vytvořit Asset" vybraný v nabídce kolekce a vybrané tlačítko Odeslat.](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skript test získá ID Assetu a nastaví příslušnou proměnnou prostředí.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Vytvořit Lokátor SAS a vytvořit adresu URL pro odeslání

### <a name="overview"></a>Přehled

Jakmile budete mít AccessPolicy a lokátor, vlastní soubor se nahraje do kontejneru Azure Blob Storage pomocí rozhraní Azure Storage REST API. Soubory je třeba nahrát jako objekty blob bloku. Azure Media Services nepodporuje objekty blob stránky.  

Další informace o práci s objekty blob služby Azure Storage najdete v tématu [REST API služby BLOB](/rest/api/storageservices/blob-service-rest-api).

Chcete-li přijmout skutečnou adresu URL pro odeslání, vytvořte Lokátor SAS (zobrazený níže). Lokátory definují čas spuštění a typ koncového bodu připojení pro klienty, kteří chtějí získat přístup k souborům v prostředku. Můžete vytvořit více entit lokátoru pro určitý AccessPolicy a dvojici prostředků pro zpracování různých požadavků klientů a potřeb. Každá z těchto lokátorů používá hodnotu StartTime a hodnotu DurationInMinutes AccessPolicy k určení doby, po kterou lze adresu URL použít. Další informace najdete v tématu [Lokátor](/rest/api/media/operations/locator).

Adresa URL SAS má následující formát:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Požadavky

Musí být splněny určité předpoklady:

* K danému prostředku nelze současně přidružit více než pět jedinečných lokátorů. Další informace najdete v tématu lokátor.
* Pokud potřebujete nahrávat soubory hned, měli byste nastavit hodnotu Čas_spuštění na pět minut před aktuálním časem. Důvodem je, že může dojít k naklonění času mezi klientským počítačem a Media Services. Hodnota StartTime musí být také v následujícím formátu data a času: RRRR-MM-DDTHH: mm: ssZ (například "2014-05-23T17:53:50Z").    
* Po vytvoření lokátoru v případě, že je k dispozici pro použití, může docházet ke zpoždění 30-40 sekund.

### <a name="create-a-sas-locator"></a>Vytvoření lokátoru SAS

1. Vyberte **Lokátor**  ->  **vytvořit Lokátor SAS**.
2. Stiskněte **Odeslat**.

    Skript "test" vytvoří adresu URL pro odeslání na základě zadaného názvu mediálního souboru a informací lokátoru SAS a nastaví příslušnou proměnnou prostředí.

    ![Snímek obrazovky, který zobrazuje "lokátor – vytvořit S Lokátor A S lokátoru" vybraný v nabídce kolekce a vybrané tlačítko Odeslat.](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Nahrání souboru do úložiště objektů BLOB pomocí adresy URL pro nahrání

### <a name="overview"></a>Přehled

Teď, když máte adresu URL pro nahrání, je potřeba napsat nějaký kód s využitím rozhraní API Azure Blob přímo k nahrání souboru do kontejneru SAS. Další informace najdete v následujících článcích:

- [Použití rozhraní REST API pro službu Azure Storage](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [VLOŽENÍ objektu BLOB](/rest/api/storageservices/put-blob)
- [Nahrání objektů blob do úložiště objektů BLOB](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Nahrání souboru pomocí metody post

Jako příklad používáme příkaz post k nahrání malého souboru. MP4. Může se jednat o omezení velikosti souboru při nahrávání binárního souboru prostřednictvím post.

Požadavek na nahrání není součástí kolekce **AzureMedia** . 

Vytvořit a nastavit novou žádost:
1. Stisknutím klávesy **+** vytvořte novou kartu žádosti.
2. Vyberte operaci **Put** a v adrese URL vložte **{{UploadURL}}** .
2. Kartu **autorizace** nechte beze změny (nenastavte ji na **token nosiče**).
3. Na kartě **hlavičky** zadejte: **klíč**: "x-MS-BLOB-Type" a **Value**: "BlockBlob".
2. Na kartě **tělo** klikněte na **binární**.
4. Vyberte soubor s názvem, který jste zadali v proměnné prostředí **MediaFileName** .
5. Stiskněte **Odeslat**.

    ![Snímek obrazovky, který zobrazuje kartu "(nahrané R L)".](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Vytvoření metadat v assetu

Po nahrání souboru budete muset v assetu vytvořit metadata pro mediální soubor, který jste nahráli do úložiště objektů BLOB přidružených k vašemu prostředku.

1. Vyberte **AssetFiles**  ->  **CreateFileInfos**.
2. Stiskněte **Odeslat**.

    ![Nahrání souboru](./media/media-services-rest-upload-files/postman-create-file-info.png)

Soubor by měl být nahrán a jeho sada metadat.

## <a name="validate"></a>Ověření

Pokud chcete ověřit, jestli se soubor úspěšně nahrál, možná budete chtít zadat dotaz na [AssetFile](/rest/api/media/operations/assetfile) a porovnat **ContentFileSize** (nebo jiné podrobnosti) na to, co očekáváte, aby se v novém assetu zobrazil. 

Následující operace **Get** například přináší data souborů pro soubor assetu (v případě BigBuckBunny.mp4 souboru). Dotaz používá [proměnné prostředí](postman-environment.md) , které jste nastavili dříve.

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

Odpověď bude obsahovat velikost, název a další informace.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Další kroky

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

Můžete také použít službu Azure Functions k aktivaci úlohy kódování při příchodu souboru do nakonfigurovaného kontejneru. Další informace najdete v [této ukázce](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).
