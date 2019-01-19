---
title: Ověřování služba služba do služby Azure Key Vault pomocí rozhraní .NET
description: Použijte knihovnu Microsoft.Azure.Services.appauthentication přistupovat k ověření do služby Azure Key Vault pomocí rozhraní .NET.
keywords: pověření pro místní ověřování služby Azure key vault
author: bryanla
manager: mbaldwin
services: key-vault
ms.author: bryanla
ms.date: 01/04/2019
ms.topic: conceptual
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: 216a2e31074f7afe944611de12fe2d45d9d236cf
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413749"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Ověřování služba služba do služby Azure Key Vault pomocí rozhraní .NET

K ověření do služby Azure Key Vault, budete potřebovat pověření Azure Active Directory (AD), sdílený tajný klíč nebo certifikát. Správa těchto přihlašovacích údajů může být obtížné a je lákavé sadu přihlašovacích údajů do aplikace včetně ve zdrojové nebo konfigurační soubory.

`Microsoft.Azure.Services.AppAuthentication` Pro knihovny .NET zjednodušuje tento problém. Použije přihlašovací údaje pro vývojáře k ověřování během místního vývoje. Při řešení je později nasadit do Azure, knihovny, automaticky se přepne do aplikace přihlašovacích údajů.  

Pomocí přihlašovacích údajů pro vývojáře při místním vývojovém je bezpečnější, protože není potřeba vytvořit přihlašovací údaje služby Azure AD nebo sdílet přihlašovacích údajů mezi vývojáři.

`Microsoft.Azure.Services.AppAuthentication` Knihovny spravuje ověřování automaticky, která zase umožňuje zaměřit se na řešení, nikoli vaše přihlašovací údaje.

`Microsoft.Azure.Services.AppAuthentication` Knihovna podporuje místní vývoj pomocí sady Microsoft Visual Studio, Azure CLI nebo integrované ověřování Azure AD. Při nasazení na prostředek Azure, který podporuje spravovanou identitu, automaticky používá knihovna [spravovaných identit pro prostředky Azure](/azure/active-directory/msi-overview). Nejsou potřeba žádné změny kódu nebo konfigurace. Knihovna také podporuje s přímým přístupem pomocí služby Azure AD [přihlašovací údaje pro klienta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) když spravovaná identita není k dispozici, nebo když při místním vývojovém nelze určit kontext zabezpečení pro vývojáře.

<a name="asal"></a>
## <a name="using-the-library"></a>Použití knihovny

Pro aplikace .NET, je nejjednodušší způsob, jak pracovat s spravovanou identitu prostřednictvím `Microsoft.Azure.Services.AppAuthentication` balíčku. Tady je postup, jak začít:

1. Přidat odkazy [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) balíčky NuGet pro vaši aplikaci. 

2. Přidejte následující kód:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` Třídy ukládá do mezipaměti v mezipaměti a načte ze služby Azure AD těsně před vypršení platnosti. V důsledku toho již máte ke kontrole platnosti před voláním `GetAccessTokenAsync` metody. Pokud chcete použít token právě volejte metodu. 

`GetAccessTokenAsync` Metoda vyžaduje identifikátor prostředku. Další informace najdete v tématu [které služby Azure podporují spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/msi-overview).


<a name="samples"></a>
## <a name="samples"></a>Ukázky

Následující ukázky show `Microsoft.Azure.Services.AppAuthentication` knihovny v akci:

1. [Získání tajného klíče ze služby Azure Key Vault za běhu pomocí spravované identity](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programové nasazení šablony Azure Resource Manageru z virtuálního počítače Azure s využitím spravované identity](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Volání služby Azure z virtuálního počítače Azure s Linuxem pomocí ukázky .NET Core a spravovaná identita](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Místní vývoj ověřování

Pro místní vývoj existují dva primární ověřování scénáře:

- [Ověřování do služby Azure](#authenticating-to-azure-services)
- [Ověřování do vlastní služby](#authenticating-to-custom-services)

Tady přečtěte si požadavky pro každý scénář a podporované nástroje.


### <a name="authenticating-to-azure-services"></a>Ověřování do služby Azure

Místní počítače nepodporují spravovaných identit pro prostředky Azure.  V důsledku toho `Microsoft.Azure.Services.AppAuthentication` knihovna používá ke spuštění ve vašem místním vývojovém prostředí svoje přihlašovací údaje pro vývojáře. Po nasazení řešení do Azure používá knihovna spravovanou identitu přejděte do tok udělování přihlašovacích údajů klienta OAuth 2.0.  To znamená, že stejný kód můžete otestovat místně i vzdáleně bez obav.

Pro místní vývoj `AzureServiceTokenProvider` načítá tokeny pomocí **sady Visual Studio**, **rozhraní příkazového řádku Azure** (CLI), nebo **integrované ověřování Azure AD**. Jednotlivé možnosti zkusí se postupně a knihovny použije první možnost, která byla úspěšná. Pokud není možnost funguje, `AzureServiceTokenProviderException` je vyvolána výjimka s podrobnými informacemi.

### <a name="authenticating-with-visual-studio"></a>Ověřování pomocí sady Visual Studio

Použití sady Visual Studio, zkontrolujte:

1. Když jste nainstalovali [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) nebo novější.

2. [Ověřování aplikace rozšíření pro Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) je nainstalována.
 
3. Přihlášení k sadě Visual Studio a zvolili účet, který chcete použít pro místní vývoj. Použití **nástroje**&nbsp;>&nbsp;**možnosti**&nbsp;>&nbsp;**ověření služby Azure**zvolit účet místní vývoj. 

Pokud narazíte na problémy pomocí sady Visual Studio, jako jsou chyby týkající se soubor zprostředkovatele tokenů, pečlivě si prostudujte tyto kroky. 

Může být také nutné donutit svůj token pro vývojáře.  Uděláte to tak, přejděte na **nástroje**&nbsp;>&nbsp;**možnosti**>**Azure&nbsp;služby&nbsp;ověřování**  a vyhledejte **opakované ověření** odkazu pomocí vybraného účtu.  Vyberte ji k ověření. 

### <a name="authenticating-with-azure-cli"></a>Ověřování pomocí Azure CLI

Použití rozhraní příkazového řádku Azure pro místní vývoj:

1. Nainstalujte [rozhraní příkazového řádku Azure v2.0.12](/cli/azure/install-azure-cli) nebo novější. Upgrade starší verze. 

2. Použití **az login** pro přihlášení k Azure.

Použití `az account get-access-token` se ověřit přístup.  Pokud obdržíte chybu, ověřte, že krok 1 byla úspěšně dokončena. 

Pokud k výchozímu adresáři není nainstalované rozhraní příkazového řádku Azure, může se zobrazit chyba monitorování, které `AzureServiceTokenProvider` nemůže najít cestu pro rozhraní příkazového řádku Azure.  Použití **AzureCLIPath**proměnnou prostředí k definování instalační složce nástroje rozhraní příkazového řádku Azure. `AzureServiceTokenProvider` Přidá do adresáře určeného v **AzureCLIPath** proměnnou prostředí, aby **cesta** proměnné prostředí, pokud je to nezbytné.

Pokud jste přihlášení k Azure CLI pomocí více účtů nebo váš účet nemá přístup k několika předplatným, budete muset zadat konkrétní předplatné, které chcete použít.  Uděláte to tak, použijte:

```
az account set --subscription <subscription-id>
```

Tento příkaz vygeneruje výstup pouze při selhání.  Pokud chcete ověřit nastavení aktuálního účtu, použijte:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Ověřování pomocí ověřování Azure AD integrace

Pokud chcete používat ověřování Azure AD, ověřte, že:

- V místní active directory [synchronizuje do služby Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- Váš kód běží na počítači připojeném k doméně.


### <a name="authenticating-to-custom-services"></a>Ověřování do vlastní služby

Při volání služby Azure services v předchozích krocích fungovat, protože Azure services povolit přístup pro uživatele nebo aplikace.  

Při vytváření služby, která volá vlastní službu, použijte přihlašovací údaje pro klienta služby Azure AD pro ověřování místního vývojového.  Existují dvě možnosti: 

1.  Přihlásit k Azure pomocí instančního objektu:

    1.  [Vytvoření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Přihlaste se pomocí Azure CLI:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Vzhledem k tomu, že instanční objekt služby nemusí mít přístup k předplatnému, použít `--allow-no-subscriptions` argument.

2.  Použití proměnných prostředí k určení podrobnosti instančního objektu služby.  Podrobnosti najdete v tématu [spuštění aplikace pomocí hlavního názvu služby](#running-the-application-using-a-service-principal).

Jakmile budete přihlášení k Azure, `AzureServiceTokenProvider` používá instančního objektu k načtení tokenu pro místní vývoj.

To platí pouze pro místní vývoj. Pokud vaše řešení je nasadit do Azure, knihovny přepne na spravovanou identitu pro ověřování.

<a name="msi"></a>
## <a name="running-the-application-using-managed-identity"></a>Spuštění aplikace pomocí spravované identity 

Při spuštění kódu na služby Azure App Service nebo Virtuálním počítači Azure pomocí spravované identity povolené knihovny automaticky použije spravovaná identita. Nejsou potřeba žádné změny kódu. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Spuštění aplikace pomocí instančního objektu 

Může být potřeba vytvořit pověření pro ověření klienta služby Azure AD. Mezi běžné příklady patří:

1. Váš kód běží na místním vývojovém prostředí, ale ne pod identitou pro vývojáře.  Service Fabric, například používá [účtu NetworkService](/azure/service-fabric/service-fabric-application-secret-management) pro místní vývoj.
 
2. Váš kód běží na místním vývojovém prostředí a ověřování do vlastní služby, takže nelze použít svou identitu pro vývojáře. 
 
3. Váš kód běží na výpočetním prostředkem Azure, která zatím nepodporuje spravované identity pro prostředky Azure, jako je Azure Batch.

Použití certifikátu k přihlašování do služby Azure AD:

1. Vytvoření [certifikátu instančního objektu](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Nasaďte certifikát do buď *LocalMachine* nebo *CurrentUser* ukládat. 

3. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Nahraďte *{AppId}*, *{TenantId}*, a *{Thumbprint}* s hodnotami, které jsou generovány v kroku 1. Nahraďte *{CertificateStore}* s oběma `LocalMachine` nebo `CurrentUser`, podle plánu nasazení.

4. Spusťte aplikaci. 

Přihlaste se pomocí Azure AD sdíleného tajného kódu přihlašovacích údajů:

1. Vytvoření [instanční objekt s heslem](/azure/azure-resource-manager/resource-group-authenticate-service-principal) a jí udělit přístup ke službě Key Vault. 

2. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Nahraďte _{AppId}_, _{TenantId}_, a _{ClientSecret}_ s hodnotami, které jsou generovány v kroku 1.

3. Spusťte aplikaci. 

Jakmile je všechno nastavené správně, žádné další změny kódu jsou nezbytné.  `AzureServiceTokenProvider` proměnné prostředí a certifikát, který používá k ověření do služby Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Podpora řetězců připojení

Ve výchozím nastavení `AzureServiceTokenProvider` používá několik metod načíst token. 

K řízení procesu, použijte připojovací řetězec předaný `AzureServiceTokenProvider` konstruktor nebo zadané v poli *AzureServicesAuthConnectionString* proměnné prostředí. 

Podporovány jsou následující možnosti:

| Připojení&nbsp;řetězec&nbsp;možnost | Scénář | Komentáře|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Místní vývoj | AzureServiceTokenProvider AzureCli používá k získání tokenu. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Místní vývoj | AzureServiceTokenProvider používá Visual Studio k získání tokenu. |
| `RunAs=CurrentUser;` | Místní vývoj | AzureServiceTokenProvider používá integrované ověřování Azure AD k získání tokenu. |
| `RunAs=App;` | Spravované identity pro prostředky Azure | AzureServiceTokenProvider používá spravované identity k získání tokenu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Instanční objekt | `AzureServiceTokenProvider` certifikát se používá k získání tokenu z Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Instanční objekt | `AzureServiceTokenProvider` certifikát se používá k získání tokenu z Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Instanční objekt |`AzureServiceTokenProvider` tajný kód používá k získání tokenu z Azure AD. |


## <a name="next-steps"></a>Další postup

- Další informace o [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/).
- Další informace o [scénáře ověřování služby Azure AD](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).
