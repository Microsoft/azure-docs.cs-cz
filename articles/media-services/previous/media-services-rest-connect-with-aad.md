---
title: Použití ověřování Azure AD pro přístup k rozhraní Azure Media Services API pomocí REST | Microsoft Docs
description: Přečtěte si, jak získat přístup k rozhraní Azure Media Services API pomocí Azure Active Directory ověřování pomocí REST.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: 958bfa605e0195b5f4fde2c0ff53a8ce567f50a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89257139"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Použití ověřování AD pro přístup k rozhraní API služby Media Services s využitím REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-from-v2-to-v3.md) .

Pokud používáte ověřování Azure AD s Azure Media Services, můžete ověřit jedním ze dvou způsobů:

- **Ověřování uživatele** ověřuje osobu, která aplikaci používá k interakci s Azure Media Servicesmi prostředky. Interaktivní aplikace by měla nejdřív vyzvat uživatele k zadání přihlašovacích údajů. Příkladem je aplikace konzoly pro správu, kterou používají autorizovaní uživatelé k monitorování úloh kódování nebo živého streamování. 
- **Ověřování instančního objektu** ověřuje službu. Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby démon, služby střední vrstvy nebo naplánované úlohy, jako jsou webové aplikace, aplikace Function App, Logic Apps, rozhraní API nebo mikroslužby.

    V tomto kurzu se dozvíte, jak používat ověřování **instančního objektu služby** Azure AD pro přístup k rozhraní API AMS s REST. 

    > [!NOTE]
    > **Instanční objekt** je doporučeným osvědčeným postupem pro většinu aplikací, které se připojují k Azure Media Services. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Získat informace o ověřování z Azure Portal
> * Získání přístupového tokenu pomocí metody post
> * Testování rozhraní API **assetů** pomocí přístupového tokenu


> [!IMPORTANT]
> V současné době Media Services podporuje model ověřování služeb Azure Access Control. Access Control ověřování se však bude zastaralá od 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Vytvořte účet Azure Media Services pomocí Azure Portal](media-services-portal-create-account.md).
- Přečtěte si článek [Přehled přístupu k rozhraní Azure Media Services API pomocí služby Azure AD Authentication](media-services-use-aad-auth-to-access-ams-api.md) .
- Nainstalujte klienta [post](https://www.getpostman.com/) REST, který SPUSTÍ rozhraní REST API uvedená v tomto článku. 

    V tomto kurzu používáme **metodu post** , ale jakýkoli nástroj REST by byl vhodný. Další alternativy: **Visual Studio Code** pomocí modulu plug-in REST nebo **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Získat informace o ověřování z Azure Portal

### <a name="overview"></a>Přehled

Chcete-li získat přístup k rozhraní Media Services API, je nutné shromáždit následující datové body.

|Nastavení|Příklad|Description|
|---|-------|-----|
|Azure Active Directory doménu tenanta|microsoft.onmicrosoft.com|Služba Azure AD jako koncový bod služby tokenů zabezpečení (STS) je vytvořena v následujícím formátu: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> . Služba Azure AD vydá token JWT, aby mohl získat přístup k prostředkům (přístupovému tokenu).|
|Koncový bod rozhraní REST API|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Toto je koncový bod, proti kterému jsou vytvářena všechna Media Services REST API volání ve vaší aplikaci.|
|ID klienta (ID aplikace)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID aplikace (klienta) služby Azure AD. Pro získání přístupového tokenu se vyžaduje ID klienta. |
|Tajný klíč klienta|+ mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq + Dbim0 =|Klíče aplikace služby Azure AD (tajný klíč klienta). K získání přístupového tokenu se vyžaduje tajný klíč klienta.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Získat informace o ověřování AAD z Azure Portal

Chcete-li získat informace, postupujte podle následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Přejděte do své instance AMS.
3. Vyberte **přístup přes rozhraní API**.
4. Klikněte na **připojit k Azure Media Services rozhraní API s instančním objektem**.

    ![API access](./media/connect-with-rest/connect-with-rest01.png)

5. Vyberte existující **aplikaci Azure AD** nebo vytvořte novou (viz níže).

    > [!NOTE]
    > Aby požadavek na médium Azure byl úspěšný, volající uživatel musí mít roli **Přispěvatel** nebo **vlastník** pro účet Media Services, ke kterému se pokouší získat přístup. Pokud se zobrazí výjimka oznamující, že vzdálený server vrátil chybu: (401) Neautorizováno, "viz [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Pokud potřebujete vytvořit novou aplikaci AD, postupujte podle těchto kroků:
    
   1. Stiskněte **vytvořit nové**.
   2. Zadejte název.
   3. Znovu stiskněte **vytvořit nové** .
   4. Stiskněte **Uložit**.

      ![API access](./media/connect-with-rest/new-app.png)

      Nová aplikace se zobrazí na stránce.

6. Získejte **ID klienta** (ID aplikace).
    
   1. Vyberte aplikaci.
   2. Získejte **ID klienta** z okna na pravé straně. 

      ![API access](./media/connect-with-rest/existing-client-id.png)

7. Získá **klíč** aplikace (tajný klíč klienta). 

   1. Klikněte na tlačítko **Spravovat aplikaci** (Všimněte si, že informace o ID klienta se nachází pod položkou **ID aplikace**). 
   2. Stiskněte **klávesy**.
    
       ![API access](./media/connect-with-rest/manage-app.png)
   3. Vygenerujte klíč aplikace (tajný klíč klienta) vyplněním **popisu** a **vypršení platnosti** a stisknutím tlačítka **Uložit**.
    
       Po stisknutí tlačítka **Uložit** se zobrazí hodnota klíč. Před zavřením okna Zkopírujte hodnotu klíče.

   ![API access](./media/connect-with-rest/connect-with-rest03.png)

Do souboru web.config nebo app.config můžete přidat hodnoty pro parametry připojení AD pro pozdější použití ve vašem kódu.

> [!IMPORTANT]
> **Klíč klienta** je důležitý tajný klíč, který by měl být správně zabezpečený v trezoru klíčů nebo zašifrovaný v produkčním prostředí.

## <a name="get-the-access-token-using-postman"></a>Získání přístupového tokenu pomocí metody post

V této části se dozvíte, jak použít **metodu post** ke spuštění REST API, který vrací token nosiče JWT (přístupový token). Chcete-li volat jakékoli Media Services REST API, je nutné přidat do volání hlavičku "autorizace" a přidat hodnotu "nosiče *your_access_token*" do každého volání (jak je uvedeno v další části tohoto kurzu). 

1. Otevřete **post**.
2. Vyberte **POST**.
3. Zadejte adresu URL, která obsahuje název vašeho tenanta, a to v následujícím formátu: název tenanta by měl končit **příponou. onmicrosoft.com** a adresa URL by měla končit **OAuth2/tokenem**: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Vyberte kartu **hlavičky** .
5. Zadejte informace o **hlavičkách** pomocí datové mřížky "klíč/hodnota". 

    ![Datová mřížka](./media/connect-with-rest/headers-data-grid.png)

    Případně klikněte na odkaz **Hromadná úprava** na pravé straně okna po odeslání a vložte následující kód.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Stiskněte kartu **tělo** .
7. Zadejte informace o textu pomocí datové mřížky "klíč/hodnota" (nahraďte ID klienta a tajné hodnoty). 

    ![Datová mřížka](./media/connect-with-rest/data-grid.png)

    Případně klikněte na tlačítko **Hromadná úprava** na pravé straně okna po odeslání a vložte následující text (nahraďte ID klienta a tajné hodnoty):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Stiskněte **Odeslat**.

    ![získat token](./media/connect-with-rest/connect-with-rest04.png)

Vrácená odpověď obsahuje **přístupový token** , který potřebujete použít pro přístup k rozhraním API AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Testování rozhraní API **assetů** pomocí přístupového tokenu

V této části se dozvíte, jak získat přístup k rozhraní API **assetů** pomocí **metody post**.

1. Otevřete **post**.
2. Vyberte **GET**.
3. Vložte REST API koncový bod (například https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Vyberte kartu **autorizace** . 
5. Vyberte **token nosiče**.
6. Vložte token, který byl vytvořen v předchozí části.

    ![získat token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > UŽIVATELSKÉ prostředí pro vystavování může být odlišné mezi počítači Mac a počítačem. Pokud verze Mac nemá v rozevíracím seznamu oddílu **ověřování** možnost nosný token, měli byste ručně přidat **autorizační** hlavičku na klienta systému Mac.

   ![Ověřovací hlavička](./media/connect-with-rest/auth-header.png)

7. Vyberte **záhlaví**.
5. V pravé části okna po kliknutí klikněte na odkaz **hromadné úpravy** .
6. Vložte následující hlavičky:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Stiskněte **Odeslat**.

Vrácená odpověď obsahuje prostředky, které jsou ve vašem účtu.

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte si tento vzorový kód v [ověřování Azure AD pro přístup k Azure Media Services: přes REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Nahrávání souborů s využitím .NET](media-services-dotnet-upload-files.md)
