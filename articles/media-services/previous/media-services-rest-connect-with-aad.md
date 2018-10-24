---
title: Ověřování pomocí služby Azure AD pro přístup k rozhraní API služby Azure Media Services pomocí REST | Dokumentace Microsoftu
description: Zjistěte, jak získat přístup k rozhraní API služby Azure Media Services pomocí ověřování Azure Active Directory pomocí REST.
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
ms.date: 10/22/2018
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: d9d1fa7d8cf25057339e560c371deb939997b578
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945073"
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services pomocí REST

Pokud používáte ověřování Azure AD pomocí služby Azure Media Services, můžete se ověřit jedním ze dvou způsobů:

- **Ověřování uživatelů** ověřuje osoba, která používá aplikace k interakci s prostředky služby Azure Media Services. Interaktivní aplikace by měly nejprve vyzvou uživatele pro přihlašovací údaje. Příkladem je aplikace konzoly správy, který slouží ke sledování úloh kódování nebo živé streamování Autorizovaní uživatelé. 
- **Ověřování instančních objektů** služba se ověřuje. Aplikace, které se běžně používají tuto metodu ověřování jsou aplikace, na kterých běží služby démonů, služby střední vrstvy nebo naplánované úlohy, jako jsou webové aplikace, aplikace function App, logic apps, rozhraní API nebo mikroslužeb.

    V tomto kurzu se dozvíte, jak používat Azure AD **instanční objekt služby** ověřování pro přístup k rozhraní API pro AMS s využitím REST. 

    > [!NOTE]
    > **Instanční objekt služby** je doporučené osvědčené postupy pro většinu aplikací, připojení k Azure Media Services. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Získejte informace o ověřování na webu Azure Portal
> * Získání přístupového tokenu pomocí nástroje Postman
> * Test **prostředky** rozhraní API pomocí přístupového tokenu


> [!IMPORTANT]
> V současné době služba Media Services podporuje model ověřování Azure Access Control services. Ale řízení přístupu na ověřování se přestanou používat 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Vytvoření účtu služby Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
- Zkontrolujte [přístup k API služby Azure Media pomocí ověřování Azure AD – přehled](media-services-use-aad-auth-to-access-ams-api.md) článku.
- Nainstalujte [Postman](https://www.getpostman.com/) klienta REST k provedení rozhraní REST API služby uvedené v tomto článku. 

    V tomto kurzu jsme naplánovaném **Postman** ale bude vhodné libovolný nástroj REST. Další možnosti jsou: **Visual Studio Code** s pluginem REST nebo **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Získejte informace o ověřování na webu Azure Portal

### <a name="overview"></a>Přehled

Pro přístup k rozhraní API služby Media Services, budete muset shromažďování těchto datových bodů.

|Nastavení|Příklad:|Popis|
|---|-------|-----|
|Doména tenanta služby Azure Active Directory|microsoft.onmicrosoft.com|Azure AD jako koncový bod zabezpečit službu tokenů (STS) se vytvoří v následujícím formátu: https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token. Aby bylo možné přistupovat k prostředkům (přístupový token), Azure AD vydá token JWT.|
|Koncový bod rozhraní REST API|https://amshelloworld.restv2.westus.media.azure.net/api/|Toto je koncový bod, oproti které všechny REST API služby Media Services ve vaší aplikaci volání.|
|ID klienta (ID aplikace)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD ID aplikace (klient). ID klienta je požadováno pro získání přístupového tokenu. |
|Tajný kód klienta|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD klíče aplikace (tajný klíč klienta). Chcete-li získat přístupový token se vyžaduje tajný klíč klienta.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Získání informací o ověřování AAD z webu Azure portal

Pokud chcete získat informace, postupujte takto:

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Přejděte do instance služby AMS.
3. Vyberte **přístup přes rozhraní API**.
4. Klikněte na **připojit k rozhraní API služby Azure Media Services pomocí instančního objektu**.

    ![Přístup přes rozhraní API](./media/connect-with-rest/connect-with-rest01.png)

5. Vyberte existující **aplikaci Azure AD** nebo vytvořte novou (viz dole).

    > [!NOTE]
    > Azure Media REST žádosti úspěšné, musí mít volajícího uživatele **Přispěvatel** nebo **vlastníka** roli pro tento účet Media Services se pokouší o přístup. Pokud se zobrazí výjimka, která říká "vzdálený server vrátil chybu: (401) Neoprávněno" v tématu [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Pokud je potřeba vytvořit novou aplikaci AD, postupujte podle těchto kroků:
    
    1. Stisknutím klávesy **vytvořit nový**.
    2. Zadejte název.
    3. Stisknutím klávesy **vytvořit nový** znovu.
    4. Stiskněte **Uložit**.

    ![Přístup přes rozhraní API](./media/connect-with-rest/new-app.png)

    Nová aplikace se zobrazí na stránce.

6. Získejte **ID klienta** (ID aplikace).
    
    1. Vyberte aplikaci.
    2. Získejte **ID klienta** z okna na pravé straně. 

    ![Přístup přes rozhraní API](./media/connect-with-rest/existing-client-id.png)

7.  Získat aplikace **klíč** (tajný klíč klienta). 

    1. Klikněte na tlačítko **spravovat aplikaci** tlačítko (Všimněte si, že informace o ID klienta je v části **ID aplikace**). 
    2. Stisknutím klávesy **klíče**.
    
        ![Přístup přes rozhraní API](./media/connect-with-rest/manage-app.png)
    3. Vygenerovat klíč aplikace (tajný klíč klienta) vyplněním **popis** a **EXPIRES** a stisknutím klávesy **Uložit**.
    
        Jakmile **Uložit** stiskne tlačítko, zobrazí se hodnota klíče. Hodnotu klíče si zkopírujte před zavřením okna.

    ![Přístup přes rozhraní API](./media/connect-with-rest/connect-with-rest03.png)

Přidejte hodnoty pro parametry připojení AD do souboru web.config nebo app.config pro pozdější použití v kódu.

> [!IMPORTANT]
> **Klientský klíč** je důležité tajný kód a by měly být správně zabezpečená ve službě key vault nebo šifrovaná v produkčním prostředí.

## <a name="get-the-access-token-using-postman"></a>Získání přístupového tokenu pomocí nástroje Postman

Tato část ukazuje způsob použití **Postman** ke spuštění rozhraní REST API, která vrací tokenů nosiče JWT (přístupový token). K volání jakékoli REST API služby Media Services, budete muset přidat hlavičku "Autorizace" do volání a přidejte hodnotu "nosiče *your_access_token*" ke každému volání (jak je znázorněno v následující části tohoto kurzu). 

1. Otevřít **Postman**.
2. Vyberte **POST**.
3. Zadejte adresu URL, která zahrnuje název tenanta v následujícím formátu: název klienta by měl končit **. onmicrosoft.com** a adresa URL by měla končit **tokenuoauth2/**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Vyberte **záhlaví** kartu.
5. Zadejte **záhlaví** informace pomocí mřížky dat "Klíč/hodnota". 

    ![Datová mřížka](./media/connect-with-rest/headers-data-grid.png)

    Alternativně klepněte na tlačítko **hromadných úprav** odkaz na pravé straně okna nástroje Postman a vložte následující kód.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Stisknutím klávesy **tělo** kartu.
7. Zadejte informace o textu pomocí mřížky dat "Klíč/hodnota" (nahraďte ID klienta a hodnoty tajných kódů). 

    ![Datová mřížka](./media/connect-with-rest/data-grid.png)

    Alternativně klepněte na tlačítko **hromadných úprav** na pravé straně okna nástroje Postman a vložte následující text (nahraďte ID klienta a hodnoty tajných kódů):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Stiskněte **Odeslat**.

    ![získání tokenu](./media/connect-with-rest/connect-with-rest04.png)

Vrácená odpověď obsahuje **přístupový token** , budete muset použít pro přístup k API pro AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Test **prostředky** rozhraní API pomocí přístupového tokenu

Tato část ukazuje, jak získat přístup k **prostředky** pomocí rozhraní API **Postman**.

1. Otevřít **Postman**.
2. Vyberte **GET**.
3. Vložte koncový bod rozhraní REST API (například) https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Vyberte **autorizace** kartu. 
5. Vyberte **nosný Token**.
6. Vložte token, který byl vytvořen v předchozí části.

    ![získání tokenu](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman uživatelského prostředí mohou být odlišná mezi PC a Mac. Pokud verze Mac nemá možnost "Nosného tokenu" v **ověřování** části rozevírací seznam, měli byste přidat **autorizace** záhlaví ručně na klienta se systémem Mac.

   ![Hlavička ověření](./media/connect-with-rest/auth-header.png)

7. Vyberte **záhlaví**.
5. Klikněte na tlačítko **hromadných úprav** odkaz na pravé straně okna nástroje Postman.
6. Vložte následující hlavičky:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Stiskněte **Odeslat**.

Vrácená odpověď obsahuje prostředky, které jsou ve vašem účtu.

## <a name="next-steps"></a>Další postup

* Vyzkoušejte tento ukázkový kód v [ověřování Azure AD pro přístup ke službě Azure Media Services: I přes rozhraní REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Nahrávání souborů s využitím .NET](media-services-dotnet-upload-files.md)
