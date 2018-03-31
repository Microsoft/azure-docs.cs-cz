---
title: Ověření s Mobile Engagementem rozhraní REST API
description: Popisuje, jak ověřit pomocí rozhraní API REST služby Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 5979ded9afaa31054f835b5f16fe525809f5730d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Ověření s Mobile Engagementem rozhraní REST API
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

## <a name="overview"></a>Přehled

Tento dokument popisuje, jak se získat token k ověření s rozhraními API sady Mobile Engagement REST platný OAuth Azure Active Directory (Azure AD).

Tento postup předpokládá, že máte platné předplatné Azure a jste vytvořili aplikaci Mobile Engagement pomocí jedné z [vývojáře kurzy](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentication

Token OAuth na základě Microsoft Azure Active Directory se používá k ověřování. 

K ověření požadavek rozhraní API, je nutné přidat hlavičku ověřování pro každou žádost. Autorizační hlavičky je v následujícím formátu:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory tokeny vyprší za jednu hodinu.
> 
> 

Existuje několik způsobů, jak získat token. Protože rozhraní API se nazývají z cloudové služby, kterou chcete použít klíč rozhraní API. Klíč rozhraní API v Azure terminologii nazývá instanční objekt hesla. Následující postup popisuje jeden způsob, jak jej nastavit ručně.

### <a name="one-time-setup-using-a-script"></a>Jednorázové instalace (pomocí skriptu.)

K provedení instalace pomocí skriptu prostředí PowerShell, postupujte podle kroků v následujících pokynech. Skript prostředí PowerShell pro instalační program vyžaduje minimálně množství času, ale používá většina povolenou výchozí hodnoty. 

Volitelně můžete také podle pokynů [ruční instalaci](mobile-engagement-api-authentication-manual.md) přímo tohoto postupu z portálu Azure. Při nastavování z portálu Azure, můžete provést podrobnější konfigurace.

1. Získat nejnovější verzi prostředí Azure PowerShell pomocí [Stáhnout](http://aka.ms/webpi-azps). Další informace o pokyny ke stažení najdete v tématu [tento přehled](/powershell/azure/overview).

2. Po instalaci prostředí PowerShell, použijte následující příkazy k zajištění, že máte **Azure modulu** nainstalován:

    a. Zkontrolujte, zda že je k dispozici v seznamu dostupných modulů modulu Azure PowerShell.

        Get-Module –ListAvailable

    ![K dispozici moduly Azure][1]

    b. Pokud nenajdete modulu Azure PowerShell v předchozím seznamu, budete muset spustit:

        Import-Module Azure
3. Přihlaste se do Azure Resource Manageru z prostředí PowerShell spuštěním následujícího příkazu. Zadejte uživatelské jméno a heslo pro účet Azure: 

        Login-AzureRmAccount
4. Pokud máte více předplatných, proveďte následující kroky:

    a. Získejte seznam všech vašich předplatných. Zkopírujte **SubscriptionId** odběru, který chcete použít. Zajistěte, aby že toto předplatné se aplikace Mobile Engagement. Chcete tuto aplikaci používat k interakci se rozhraní API. 

        Get-AzureRmSubscription

    b. Spusťte následující příkaz. Zadejte **SubscriptionId** nakonfigurovat odběr, který se chystáte použít:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Zkopírujte text pro [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) skriptu do místního počítače. Pak ho uložte jako rutiny prostředí PowerShell (například `APIAuth.ps1`), a potom ho spusťte.

         `.\APIAuth.ps1`.

6. Skript požaduje zadání vstup pro **principalName**. Zadejte vhodný název, který chcete použít pro aplikaci služby Active Directory (například APIAuth). 

7. Po dokončení spuštění skriptu zobrazí následujícími čtyřmi hodnotami. Nezapomeňte zkopírovat, protože je k ověření prostřednictvím kódu programu se službou Active Directory potřebujete: 

   - **TenantId**
   - **ID předplatného**
   - **ApplicationId**
   - **Tajný kód**

   Použít TenantId jako `{TENANT_ID}`, ApplicationId jako `{CLIENT_ID}` a tajný jako `{CLIENT_SECRET}`.

   > [!NOTE]
   > Výchozí zásady zabezpečení mohou blokovat spouštění skriptů prostředí PowerShell. Pokud ano, použijte následující příkaz dočasně konfigurace vaší zásady spouštění povolit spuštění skriptu:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Zde je, jak vypadá sadu rutin prostředí PowerShell.
    ![Rutiny PowerShellu][3]
9. V portálu Azure přejděte do služby Active Directory, vyberte **registrace aplikace**, a pak hledání své aplikace a ujistěte se, zda existuje.
    ![Vyhledávání pro vaši aplikaci.][4]

### <a name="steps-to-get-a-valid-token"></a>Kroky, chcete-li získat platný token

1. Volání rozhraní API s následujícími parametry. Nezapomeňte nahradit **klienta\_ID**, **klienta\_ID**, a **klienta\_tajný klíč**:
   
   * **Adresa URL požadavku** jako `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **Hlavičku HTTP Content-Type** jako `application/x-www-form-urlencoded`
   
   * **Text žádosti HTTP** jako `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Toto je požadavek příklad:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Toto je odpověď příklad:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Tento příklad obsahuje kódování URL parametry POST, ve kterém `resource` hodnota je ve skutečnosti `https://management.core.windows.net/`. Dávejte pozor, abyste také kódování URL `{CLIENT_SECRET}`, protože ho může obsahovat speciální znaky.

     > [!NOTE]
     > Pro testování, můžete použít nástroj pro klienta na protokolu HTTP, jako [Fiddler](http://www.telerik.com/fiddler) nebo [Chrome Postman rozšíření](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Každé volání rozhraní API nyní obsahují hlavičce autorizace požadavku:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Pokud vaše žádost vrátí stavový kód 401, zkontrolujte text odpovědi. Ho vás může upozornit, že vypršela platnost tokenu. V takovém případě získáte nový token.

## <a name="use-the-apis"></a>Použití rozhraní API
Teď, když máte platný token, jste připraveni k volání rozhraní API.

1. V každé žádosti o rozhraní API musíte předat token platný, neprošlé. Jste získali v předchozí části neprošlé token.

2. Zařaďte některé parametry na žádost o identifikátor URI, který identifikuje vaši aplikaci. Požadavek URI vypadá následující kód:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Chcete-li získat parametry, vyberte název aplikace. Potom vyberte **řídicí panel**. Zobrazí stránka s všechny tři parametry, následujícím způsobem:
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** název vaše skupiny prostředků bude **MobileEngagement** Pokud jste vytvořili novou. 

> [!NOTE]
> Kořenové adresu rozhraní API ignorujte, protože byl pro předchozí rozhraní API.
> 
> Pokud jste vytvořili aplikaci pomocí portálu Azure, budete muset použít název prostředku aplikace, který se liší od názvu aplikace, sám sebe. Pokud jste vytvořili aplikaci na portálu Azure, měli byste použít název aplikace. (Není žádný rozdíl mezi název prostředku aplikace a název aplikace pro aplikace, které jsou vytvořené v nového portálu.)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



