---
title: Použití ověřování Azure AD pro přístup k rozhraní API Mediálních služeb Azure pomocí REST | Dokumenty společnosti Microsoft
description: Zjistěte, jak přistupovat k rozhraní API Azure Media Services pomocí ověřování Azure Active Directory pomocí rest.
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
ms.openlocfilehash: a693eb374365670da3fe8c4b2bb8ce664a024217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295445"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Použití ověřování AD pro přístup k rozhraní API služby Media Services s využitím REST

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Když používáte ověřování Azure AD pomocí Azure Media Services, můžete se ověřit jedním ze dvou způsobů:

- **Ověření uživatele** ověřuje osobu, která používá aplikaci k interakci s prostředky Azure Media Services. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření. Příkladem je aplikace konzoly pro správu, kterou používají oprávnění uživatelé ke sledování úloh kódování nebo živého streamování. 
- **Ověření instančního objektu** ověřuje službu. Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které spouštějí služby daemon, služby střední vrstvy nebo naplánované úlohy, jako jsou webové aplikace, aplikace funkcí, aplikace logiky, api nebo mikroslužby.

    Tento kurz ukazuje, jak používat ověřování **primárního zabezpečení služby** Azure AD pro přístup k rozhraní API AMS s REST. 

    > [!NOTE]
    > **Instanční objekt** je doporučeným osvědčeným postupem pro většinu aplikací, které se připojují ke službě Azure Media Services. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Získání ověřovacích informací z webu Azure Portal
> * Získání přístupového tokenu pomocí Pošťáka
> * Testování rozhraní **ASSETS** API pomocí přístupového tokenu


> [!IMPORTANT]
> V současné době media services podporuje model ověřování služby Azure Access Control. Ověřování řízení přístupu však bude zastaralé 1. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete.
- [Vytvořte účet Azure Media Services pomocí portálu Azure](media-services-portal-create-account.md).
- Projděte si článek [O přístupu k rozhraní AZURE Media Services API s přehledem ověřování Azure AD.](media-services-use-aad-auth-to-access-ams-api.md)
- Nainstalujte klienta [Postman](https://www.getpostman.com/) REST pro spuštění rest API uvedené v tomto článku. 

    V tomto tutoriálu používáme **Pošťáka,** ale jakýkoliv nástroj REST by byl vhodný. Další alternativy jsou: **Visual Studio Code** s pluginem REST nebo **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Získání ověřovacích informací z webu Azure Portal

### <a name="overview"></a>Přehled

Chcete-li získat přístup k rozhraní API služby Media Services, je třeba shromáždit následující datové body.

|Nastavení|Příklad|Popis|
|---|-------|-----|
|Doména klienta Služby Azure Active Directory|microsoft.onmicrosoft.com|Azure AD jako koncový bod služby zabezpečeného tokenu (STS) se vytvoří v následujícím formátu: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. Azure AD vydává JWT pro přístup k prostředkům (přístupový token).|
|Koncový bod rozhraní REST API|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Toto je koncový bod, proti kterému jsou všechna volání rozhraní API media services REST ve vaší aplikaci.|
|ID klienta (ID aplikace)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID aplikace (klienta) azure. ID klienta je nutné získat přístupový token. |
|Tajný klíč klienta|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Klíče aplikace Azure AD (tajný klíč klienta). Tajný klíč klienta je nutné získat přístupový token.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Získání informací o authu ad z webu Azure Portal

Chcete-li získat informace, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte na instanci AMS.
3. Vyberte **přístup k rozhraní API**.
4. Klikněte na **Připojit k rozhraní API Mediálních služeb Azure s instančním objektem .**

    ![API access](./media/connect-with-rest/connect-with-rest01.png)

5. Vyberte existující **aplikaci Azure AD** nebo vytvořte novou (viz níže).

    > [!NOTE]
    > Aby byl požadavek Azure Media REST úspěšný, musí mít volající uživatel roli **přispěvatele** nebo **vlastníka** pro účet Mediální ch služeb, ke které se pokouší získat přístup. Pokud se zobrazí výjimka s nápisem "Vzdálený server vrátil chybu: (401) Neautorizováno", přečtěte si [informace o řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Pokud potřebujete vytvořit novou aplikaci AD, postupujte takto:
    
   1. Stiskněte **klávesu Create New**.
   2. Zadejte název.
   3. Stiskněte znovu **klávesu Create New** .
   4. Stiskněte **Uložit**.

      ![API access](./media/connect-with-rest/new-app.png)

      Na stránce se zobrazí nová aplikace.

6. Získejte **ID klienta** (ID aplikace).
    
   1. Vyberte aplikaci.
   2. Získejte **ID klienta** z okna na pravé straně. 

      ![API access](./media/connect-with-rest/existing-client-id.png)

7. Získejte **klíč** aplikace (tajný klíč klienta). 

   1. Klikněte na tlačítko **Spravovat aplikaci** (všimněte si, že informace o ID klienta jsou pod **ID aplikace**). 
   2. Stiskněte **klávesy**.
    
       ![API access](./media/connect-with-rest/manage-app.png)
   3. Vygenerujte klíč aplikace (tajný klíč klienta) vyplněním **popisu** a **vyprší platnost** a stisknutím **tlačítka Uložit**.
    
       Po stisknutí tlačítka **Uložit** se zobrazí hodnota klíče. Před opuštěním okna zkopírujte hodnotu klíče.

   ![API access](./media/connect-with-rest/connect-with-rest03.png)

Můžete přidat hodnoty parametrů připojení služby AD do souboru web.config nebo app.config, aby se později použily ve vašem kódu.

> [!IMPORTANT]
> **Klientský klíč** je důležitým tajným klíčem a měl by být správně zabezpečen v trezoru klíčů nebo šifrován v produkčním prostředí.

## <a name="get-the-access-token-using-postman"></a>Získání přístupového tokenu pomocí Pošťáka

Tato část ukazuje, jak použít **Postman** ke spuštění rozhraní REST API, které vrací Token nosiče JWT (přístupový token). Chcete-li volat jakékoli rozhraní REST API mediálních služeb, musíte do volání přidat hlavičku "Autorizace" a přidat hodnotu "Bearer *your_access_token*" ke každému volání (jak je znázorněno v další části tohoto kurzu). 

1. Otevřít **pošťák**.
2. Vyberte **POST**.
3. Zadejte adresu URL, která obsahuje název vašeho klienta v následujícím formátu: název klienta by měl končit **na .onmicrosoft.com** a adresa URL by měla končit **oauth2/token**: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Vyberte kartu **Záhlaví.**
5. Zadejte informace záhlaví pomocí datové **mřížky** Klíč/hodnota. 

    ![Datová mřížka](./media/connect-with-rest/headers-data-grid.png)

    Případně klikněte na odkaz **Hromadné úpravy** vpravo od okna Posnič a vložte následující kód.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Stiskněte kartu **Tělo.**
7. Zadejte informace o těle pomocí datové mřížky "Klíč/hodnota" (nahraďte ID klienta a tajné hodnoty). 

    ![Datová mřížka](./media/connect-with-rest/data-grid.png)

    Případně klikněte na **hromadné úpravy** na pravé straně okna Poučování a vložte následující text (nahraďte ID klienta a tajné hodnoty):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Stiskněte **Odeslat**.

    ![získat token](./media/connect-with-rest/connect-with-rest04.png)

Vrácená odpověď obsahuje **přístupový token,** který je třeba použít pro přístup k všem ams api.

## <a name="test-the-assets-api-using-the-access-token"></a>Testování rozhraní **ASSETS** API pomocí přístupového tokenu

Tato část ukazuje, jak získat přístup k rozhraní **API datových zdrojů** pomocí **Postman**.

1. Otevřít **pošťák**.
2. Vyberte **GET**.
3. Vložte koncový bod rozhraní REST API (napříkladhttps://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Vyberte kartu **Autorizace.** 
5. Vyberte **nosné tokeny**.
6. Vložte token, který byl vytvořen v předchozí části.

    ![získat token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Pošťák UX by se mohl lišit mezi Mac a PC. Pokud verze Macu nemá možnost "Nosný token" v rozevíracím seznamu **Ověřovací** část, měli byste ručně přidat hlavičku **autorizace** v klientovi Mac.

   ![Záhlaví ověřovací chudou](./media/connect-with-rest/auth-header.png)

7. Vyberte **záhlaví**.
5. V pravém okně Poslíček klikněte na odkaz **Hromadná úprava.**
6. Vložte následující záhlaví:

        x-ms-version:2.19
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Stiskněte **Odeslat**.

Vrácená odpověď obsahuje prostředky, které jsou ve vašem účtu.

## <a name="next-steps"></a>Další kroky

* Vyzkoušejte tento ukázkový kód v [Azure AD Authentication for Azure Media Services Access: Oba prostřednictvím rozhraní REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Nahrávání souborů pomocí rozhraní .NET](media-services-dotnet-upload-files.md)
