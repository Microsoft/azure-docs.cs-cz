---
title: Ověřování do služby Azure Key Vault pomocí rozhraní .NET
description: V knihovně Microsoft.Azure.Services.AppAuthentication k ověření do Azure Key Vault pomocí rozhraní .NET.
keywords: místní přihlašovací údaje Azure key vault ověřování
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: ''
ms.service: key-vault
ms.technology: ''
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: db0b0ca72f41c68e19db6635d9ba0e9144183204
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Ověřování do služby Azure Key Vault pomocí rozhraní .NET

K ověření do Azure Key Vault, je nutné pověření Azure Active Directory (AD), sdílený tajný klíč nebo certifikát. Tyto přihlašovací údaje pro správu může být složité a je tempting sady přihlašovací údaje do aplikace včetně je v konfiguraci nebo zdrojové soubory.

`Microsoft.Azure.Services.AppAuthentication` Pro knihovny .NET zjednodušuje tento problém. Přihlašovací údaje pro vývojáře se používá k ověřování během místní vývoj. Při řešení později je nasazena do Azure, knihovny automaticky přepne na přihlašovací údaje aplikací.  

Pomocí přihlašovacích údajů vývojáře během vývoje místní je bezpečnější, protože není nutné vytvořit přihlašovací údaje Azure AD nebo ke sdílení přihlašovacích údajů mezi vývojáři.

`Microsoft.Azure.Services.AppAuthentication` Knihovny spravuje ověřování automaticky, která zase umožňuje zaměřit se na řešení, nikoli vaše přihlašovací údaje.

`Microsoft.Azure.Services.AppAuthentication` Knihovny podporuje místní vývoj pomocí sady Microsoft Visual Studio, rozhraní příkazového řádku Azure nebo integrované ověřování služby Azure AD. Když se nasadí aplikační služby Azure nebo Azure virtuálního počítače (VM), automaticky používá knihovna [identita spravované služby](/azure/active-directory/msi-overview) (MSI). Nejsou vyžadovány žádné změny kódu nebo konfigurace. Knihovna také podporuje rovnou použít Azure AD [pověření klienta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) při MSI není k dispozici nebo pokud nelze určit kontext zabezpečení pro vývojáře během místní vývoj.

<a name="asal"></a>
## <a name="using-the-library"></a>Použití knihovny

Pro aplikace .NET je nejjednodušší způsob, jak pracovat s identita spravované služby (MSI) prostřednictvím `Microsoft.Azure.Services.AppAuthentication` balíčku. Zde je postup Začínáme:

1. Přidat odkaz na [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) balíček NuGet do vaší aplikace.

2. Přidejte následující kód:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` Třídy ukládá do mezipaměti v paměti token a načte z Azure AD těsně před vypršení platnosti. V důsledku toho již nebude muset zkontrolovat vypršení platnosti před voláním `GetAccessTokenAsync` metoda. Pokud chcete použít token stačí zavoláte metodu. 

`GetAccessTokenAsync` Metoda vyžaduje identifikátor prostředku. Další informace najdete v tématu [služby Azure který podporovat identita spravované služby](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity).


<a name="samples"></a>
## <a name="samples"></a>Ukázky

Následující ukázky zobrazit `Microsoft.Azure.Services.AppAuthentication` knihovny v akci:

1. [Umožňuje načíst tajného klíče z Azure Key Vault za běhu a spravovaná služba Identity (MSI)](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Prostřednictvím kódu programu nasadit šablonu Azure Resource Manageru z virtuálního počítače Azure s MSI](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Použití ukázkové .NET Core a souboru MSI volat Azure služby z virtuálního počítače Azure Linux](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Vývoj pro místní ověřování

Pro místní vývoj existují dva scénáře primární ověřování:

- [Ověřování služby Azure](#authenticating-to-azure-services)
- [Ověřování vlastních služeb](#authenticating-to-custom-services)

Zde zjistíte požadavky pro každý scénář a podporované nástroje.


### <a name="authenticating-to-azure-services"></a>Ověřování služby Azure

Místní počítače nepodporují identita spravované služby (MSI).  V důsledku toho `Microsoft.Azure.Services.AppAuthentication` knihovna používá přihlašovací údaje vývojář ke spuštění ve vašem místním vývojovém prostředí. Když je řešení nasazeno do Azure, používá knihovna MSI přepnout do tok udělení pověření klienta OAuth 2.0.  To znamená, že stejný kód můžete otestovat místně i vzdáleně bez obav.

Pro místní vývoj `AzureServiceTokenProvider` načte tokeny pomocí **Visual Studio**, **rozhraní příkazového řádku Azure** (CLI), nebo **integrované ověřování služby Azure AD**. Každá možnost zkusí se postupně a knihovny používá první možnost, která byla úspěšná. Pokud žádná možnost funguje, `AzureServiceTokenProviderException` s podrobnými informacemi je vyvolána výjimka.

### <a name="authenticating-with-visual-studio"></a>Ověřování pomocí sady Visual Studio

Pomocí sady Visual Studio, ověřte:

1. Jste nainstalovali [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) nebo novější.

2. [Ověřovací aplikaci rozšíření pro Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) je nainstalovaná.
 
3. Přihlášení k sadě Visual Studio a vybrali účet, který chcete použít pro místní vývoj. Použití **nástroje**&nbsp;>&nbsp;**možnosti**&nbsp;>&nbsp;**ověřování služby Azure**zvolit účet místní vývoj. 

Pokud narazíte na problémy pomocí sady Visual Studio, jako je například chyby týkající se soubor zprostředkovatele tokenu, pečlivě zkontrolujte tyto kroky. 

Může být také nutné k novému ověření tokenu developer.  Chcete-li to provést, přejděte na **nástroje**&nbsp;>&nbsp;**možnosti**>**Azure&nbsp;služby&nbsp;ověřování**  a podívejte se **opakované ověření** odkazu na vybraný účet.  Vyberte k ověřování. 

### <a name="authenticating-with-azure-cli"></a>Ověřování pomocí rozhraní příkazového řádku Azure

Použití Azure CLI pro místní vývoj:

1. Nainstalujte [rozhraní příkazového řádku Azure v2.0.12](/cli/azure/install-azure-cli) nebo novější. Upgrade starší verze. 

2. Použití **az přihlášení** pro přihlášení k Azure.

Použití `az account get-access-token` ověřte přístup.  Pokud narazíte na chyby, ověřte, že krok 1 dokončena úspěšně. 

Pokud rozhraní příkazového řádku Azure není nainstalovaná na výchozí adresář, může se zobrazit chyba, které `AzureServiceTokenProvider` nelze najít cestu k rozhraní příkazového řádku Azure.  Použití **AzureCLIPath**proměnnou prostředí k definování instalační složce nástroje příkazového řádku Azure CLI. `AzureServiceTokenProvider` Přidá zadaný v adresář **AzureCLIPath** proměnnou prostředí **cesta** proměnné prostředí v případě potřeby.

Pokud jste přihlášeni k více účtů pomocí rozhraní příkazového řádku Azure nebo účtu má přístup k více předplatných, budete muset zadat konkrétní předplatné, který se má použít.  Chcete-li to provést, použijte:

```
az account set --subscription <subscription-id>
```

Tento příkaz generuje výstup pouze při selhání.  Pokud chcete ověřit nastavení aktuálního účtu, použijte:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Ověřování pomocí Azure AD integrací ověřování

Pokud chcete používat ověřování Azure AD, ověřte, že:

- Vaše místní active directory [synchronizacemi do služby Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- Váš kód běží na počítači připojeném k doméně.


### <a name="authenticating-to-custom-services"></a>Ověřování vlastních služeb

Při volání služby služby Azure předchozí kroky fungovat, protože Azure services povolit přístup uživatelům a aplikacím.  

Když vytváříte službu, která volá vlastní službu, použijte pro místní vývoj ověřování pověření klienta Azure AD.  Existují dvě možnosti: 

1.  Použijte objekt služby pro přihlášení do Azure:

    1.  [Vytvoření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Přihlaste se pomocí rozhraní příkazového řádku Azure:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Protože instanční objekt nemusí mít přístup k odběru, použijte `--allow-no-subscriptions` argument.

2.  Pomocí proměnné prostředí můžete určit hlavní podrobnosti ze služby.  Podrobnosti najdete v tématu [běhu aplikace pro používání objektu služby](#running-the-application-using-a-service-principal).

Jakmile jste přihlášeni do Azure, `AzureServiceTokenProvider` používá objekt služby získat token pro místní vývoj.

To platí pouze pro místní vývoj. Po nasazení řešení do Azure knihovny se přepne do MSI ověřování.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Spuštění aplikace pomocí Identity spravované služby 

Při spuštění kódu na aplikační službu Azure nebo virtuální počítač Azure s MSI povolené, knihovny automaticky použije identita spravované služby. Nejsou vyžadovány žádné změny kódu. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Spuštění aplikace při používání objektu služby 

Může být nutné vytvořit pověření klienta služby Azure AD k ověření. Běžné mezi příklady patří:

1. Kód spuštěný na místním vývojovém prostředí, ale není v rámci identity pro vývojáře.  Například používá Service Fabric [účet NetworkService](/azure/service-fabric/service-fabric-application-secret-management) pro místní vývoj.
 
2. Kód spuštěný na místním vývojovém prostředí a ověřování do vlastní služby, takže nemůžete použít svoji identitu vývojáře. 
 
3. Váš kód běží na prostředek výpočtů Azure, který zatím nepodporuje spravované identitu služby, jako je například Azure Batch.

Chcete-li použít certifikát pro přihlášení do Azure AD:

1. Vytvoření [hlavní certifikát služby](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Nasazení certifikátu do buď _LocalMachine_ nebo _CurrentUser_ uložit. 

3. Nastavit proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Nahraďte _{AppId}_, _{TenantId}_, a _{Thumbprint}_ s hodnotami vygenerované v kroku 1.

    **CertificateStoreLocation** musí být buď _CurrentUser_ nebo _LocalMachine_, podle plánu nasazení.

4. Spusťte aplikaci. 

Přihlaste se pomocí Azure AD sdílených tajný přihlašovacích údajů:

1. Vytvoření [instanční objekt heslem](/azure/azure-resource-manager/resource-group-authenticate-service-principal) a jí udělit přístup do služby Key Vault. 

2. Nastavit proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Nahraďte _{AppId}_, _{TenantId}_, a _{ClientSecret}_ s hodnotami vygenerované v kroku 1.

3. Spusťte aplikaci. 

Až všechno, co nastavíte správně, žádné další změny kódu jsou nezbytné.  `AzureServiceTokenProvider` používá proměnnou prostředí a certifikát k ověření služby Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Podpora řetězců připojení

Ve výchozím nastavení `AzureServiceTokenProvider` používá několik metod k načtení tokenu. 

K řízení procesu, použijte připojovací řetězec předaný `AzureServiceTokenProvider` konstruktor nebo zadaný v *AzureServicesAuthConnectionString* proměnné prostředí. 

Podporovány jsou následující možnosti:

| Připojení&nbsp;řetězec&nbsp;možnost | Scénář | Komentáře|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Místní vývoj | AzureServiceTokenProvider používá AzureCli k získání tokenu. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Místní vývoj | AzureServiceTokenProvider používá Visual Studio k získání tokenu. |
| `RunAs=CurrentUser;` | Místní vývoj | AzureServiceTokenProvider používá integrované ověřování služby Azure AD se získat token. |
| `RunAs=App;` | Managed Service Identity | AzureServiceTokenProvider používá identita spravované služby se získat token. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Instanční objekt | `AzureServiceTokenProvider` certifikát se používá k získání tokenu z Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Instanční objekt | `AzureServiceTokenProvider` používá certifikát k získání tokenu z Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Instanční objekt |`AzureServiceTokenProvider` tajný klíč se používá k získání tokenu z Azure AD. |


## <a name="next-steps"></a>Další postup

- Další informace o [identita spravované služby](/azure/app-service/app-service-managed-service-identity).

- Další informace různé způsoby, jak [ověřování a autorizaci aplikace](/azure/app-service/app-service-authentication-overview).

- Další informace o Azure AD [scénáře ověřování](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application).