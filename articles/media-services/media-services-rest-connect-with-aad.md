---
title: Ověřování pomocí služby Azure AD pro přístup k rozhraní API služby Azure Media Services se zbytkem | Microsoft Docs
description: Zjistěte, jak získat přístup k rozhraní API služby Azure Media Services pomocí ověřování Azure Active Directory pomocí REST.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: ed78d6c6d4c695b841dbfbf917cd1681adc44ee7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
ms.locfileid: "27566802"
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Používat pro přístup k rozhraní API Azure Media Services se zbytkem ověřování Azure AD

Pokud používáte ověřování Azure AD pomocí služby Azure Media Services, můžete ověřovat v jednom ze dvou způsobů:

- **Ověření uživatele** ověřuje osoba, která používá aplikace komunikovat s prostředky Azure Media Services. Interaktivní aplikace by měla nejprve vyzvou uživatele k zadání přihlašovacích údajů. Příkladem je konzolovou aplikaci správy, která se používá Autorizovaní uživatelé k monitorování kódování úloh nebo živé streamování. 
- **Objekt zabezpečení ověřování služby** ověřuje služby. Aplikace, které běžně používají tuto metodu ověřování jsou aplikace, které běží služby démon, střední vrstvy služby nebo naplánované úlohy, jako jsou webové aplikace, funkce aplikací, aplikace logiky, rozhraní API nebo mikroslužeb.

    V tomto kurzu se dozvíte, jak používat Azure AD **instanční objekt** ověřování pro přístup k rozhraní API AMS pomocí REST. 

    > [!NOTE]
    > **Instanční objekt** je doporučené doporučený postup pro většinu aplikací připojení k Azure Media Services. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Získání informací o ověřování z portálu Azure
> * Získání tokenu přístupu pomocí Postman
> * Testovací **prostředky** rozhraní API pomocí přístupového tokenu


> [!IMPORTANT]
> V současné době Media Services podporuje model řízení přístupu Azure služby ověřování. Řízení přístupu ověřování však bude zastaralá 1. června 2018. Doporučujeme, abyste na model ověřování Azure AD migrovali co nejdříve.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.
- [Vytvoření účtu Azure Media Services pomocí webu Azure portal](media-services-portal-create-account.md).
- Zkontrolujte [přístup k Azure Media Services API s Přehled ověřování AAD](media-services-use-aad-auth-to-access-ams-api.md) článku.
- Nainstalujte [Postman](https://www.getpostman.com/) klienta REST provést rozhraní REST API uvedené v tomto článku. 

    V tomto kurzu jsme jsou naplánovaném **Postman** ale jakéhokoli REST nástroje bude vhodné. Další možnosti jsou: **Visual Studio Code** pomocí modulu plug-in REST nebo **webu Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Získání informací o ověřování z portálu Azure

### <a name="overview"></a>Přehled

Pro přístup k rozhraní API Media Services, která potřebujete shromáždit těchto datových bodů.

|Nastavení|Příklad:|Popis|
|---|-------|-----|
|Doména tenanta služby Azure Active Directory|microsoft.onmicrosoft.com|Azure AD jako koncový bod zabezpečení tokenu služby (STS) je vytvořen v následujícím formátu: https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token. Azure AD vydá token JWT pro zpřístupnění prostředků (přístupový token).|
|Koncový bod rozhraní REST API|https://amshelloworld.restv2.westus.media.azure.net/api/|Toto je koncový bod, na které všechny Media Services REST API v aplikaci volání.|
|ID klienta (ID aplikace)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD ID aplikace (klient). ID klienta se používá k získání tokenu přístupu. |
|Tajný klíč klienta|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD klíče aplikace (tajný klíč klienta). Tajný klíč klienta se používá k získání tokenu přístupu.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Získání informací o ověřování AAD na portálu Azure

Získání informací o, postupujte takto:

1. Přihlaste se k portálu [Azure Portal](http://portal.azure.com).
2. Přejděte k vaší instanci AMS.
3. Vyberte **přístup pomocí rozhraní API**.
4. Klikněte na **připojit k rozhraní API služby Azure Media Services s instanční objekt**.

    ![Přístup přes rozhraní API](./media/connect-with-rest/connect-with-rest01.png)

5. Vyberte existující **aplikaci Azure AD** nebo vytvořte novou (zobrazené dole).

    > [!NOTE]
    > Pro úspěšné požadavku Azure Media REST volání uživatel musí mít **Přispěvatel** nebo **vlastníka** role pro účet Media Services, se pokouší o přístup. Pokud dojde k výjimce, která uvádí, že "vzdálený server vrátil chybu: (401) Neautorizováno" v tématu [řízení přístupu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Pokud potřebujete vytvořit novou aplikaci AD, postupujte podle těchto kroků:
    
    1. Stiskněte klávesu **vytvořit nový**.
    2. Zadejte název.
    3. Stiskněte klávesu **vytvořit nový** znovu.
    4. Stiskněte klávesu **Uložit**.

    ![Přístup přes rozhraní API](./media/connect-with-rest/new-app.png)

    Nová aplikace se zobrazí na stránce.

6. Získat **ID klienta** (ID aplikace).
    
    1. Vyberte aplikaci.
    2. Získat **ID klienta** z okna na pravé straně. 

    ![Přístup přes rozhraní API](./media/connect-with-rest/existing-client-id.png).

7.  Získat aplikace **klíč** (tajný klíč klienta). 

    1. Klikněte **správě aplikace** tlačítko (Všimněte si, že informace ID klienta je pod **ID aplikace**). 
    2. Stiskněte klávesu **klíče**.
    
        ![Přístup přes rozhraní API](./media/connect-with-rest/manage-app.png)
    3. Vygenerování klíče aplikace (tajný klíč klienta) vyplněním **popis** a **EXPIRES** a stisknutím klávesy **Uložit**.
    
        Jednou **Uložit** stisknutí tlačítka, zobrazí se hodnota klíče. Zkopírujte hodnotu klíče před opuštěním okna.

    ![Přístup přes rozhraní API](./media/connect-with-rest/connect-with-rest03.png)

Přidáním hodnoty pro parametry připojení AD do souboru web.config nebo app.config pro pozdější použití v kódu.

> [!IMPORTANT]
> **Klíč klienta** je důležité tajný klíč a by měla být správně zabezpečená trezoru klíčů nebo šifrované v produkčním prostředí.

## <a name="get-the-access-token-using-postman"></a>Získání tokenu přístupu pomocí Postman

Tato část ukazuje způsob použití **Postman** provést REST API, která vrací nosného tokenu JWT (přístupový token). Volat jakékoli Media Services REST API, budete muset přidat hlavičku "Autorizace" k volání a přidejte hodnotu "nosiče *your_access_token*" pro každé volání (jak je znázorněno v následující části tohoto kurzu). 

1. Otevřete **Postman**.
2. Vyberte **POST**.
3. Zadejte adresu URL, která zahrnuje název vašeho klienta v následujícím formátu: název klienta musí končit **. onmicrosoft.com** a adresa URL musí končit **tokenuoauth2/**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Vyberte **hlavičky** kartě.
5. Zadejte **hlavičky** informací pomocí datové mřížce "Klíč/hodnota". 

    ![Datové mřížky](./media/connect-with-rest/headers-data-grid.png)

    Případně, kliknutím na tlačítko **hromadně upravovat** odkaz na pravé straně okna Postman a vložte následující kód.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Stiskněte **textu** kartě.
7. Zadejte informace o obsahu pomocí datové mřížce "Klíč/hodnota" (nahraďte ID klienta a tajný hodnoty). 

    ![Datové mřížky](./media/connect-with-rest/data-grid.png)

    Případně, kliknutím na tlačítko **hromadně upravovat** na pravé straně okna Postman a vložte následující text (nahraďte ID klienta a tajný hodnoty):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your AAD Application}
        client_secret:{Your client secret that you got from your AAD Application's Keys}
        resource:https://rest.media.azure.net

8. Stiskněte **Odeslat**.

    ![Získání tokenu](./media/connect-with-rest/connect-with-rest04.png)

Vrácená odpověď obsahuje **přístupový token** , budete muset použít pro přístup k žádné rozhraní API pro AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Testovací **prostředky** rozhraní API pomocí přístupového tokenu

V této části ukazuje, jak získat přístup **prostředky** rozhraní API pomocí **Postman**.

1. Otevřete **Postman**.
2. Vyberte **GET**.
3. Koncový bod REST API vložit (například) https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Vyberte **autorizace** kartě. 
5. Vyberte **tokenu nosiče**.
6. Vložte token, který byl vytvořen v předchozí části.

    ![Získání tokenu](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX může být Mac a počítače liší. Pokud verze Mac nemá možnost "Nosného tokenu" v **ověřování** části rozevírací, měli byste přidat **autorizace** záhlaví na klienta se systémem Mac ručně.

   ![Hlavička ověření](./media/connect-with-rest/auth-header.png)

7. Vyberte **hlavičky**.
5. Klikněte na tlačítko **hromadně upravovat** odkaz na pravé straně okna Postman.
6. Vložte následující hlavičky:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Stiskněte **Odeslat**.

Odpověď vrácená obsahuje prostředky, které jsou ve vašem účtu.

## <a name="next-steps"></a>Další postup

* Zkuste tento ukázkový kód v [ověřování Azure AD pro Azure Media Services přístup: I přes REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Nahrání souborů s rozhraním .NET](media-services-dotnet-upload-files.md)
