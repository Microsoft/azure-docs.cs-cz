---
title: Vytvoření koncového bodu SCIM pro zřizování uživatelů pro aplikace ze služby Azure AD
description: Systém pro správu identit mezi doménami (SCIM) standardizace Automatické zřizování uživatelů. Naučte se vyvíjet SCIM koncový bod, Integrujte své rozhraní SCIM API pomocí Azure Active Directory a začněte automatizovat zřizování uživatelů a skupin do svých cloudových aplikací.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900126"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Kurz: vývoj ukázkového koncového bodu SCIM

Nikdo nechce vytvořit nový koncový bod od začátku, takže jsme vytvořili pár [referenčních kódů](https://aka.ms/scimreferencecode) , abyste mohli začít s [SCIM](https://aka.ms/scimoverview). V tomto kurzu se dozvíte, jak nasadit referenční kód SCIM v Azure a otestovat ho pomocí post nebo integrací s klientem Azure AD SCIM. Svůj koncový bod SCIM můžete hned po dobu 5 minut začít používat bez kódu. Tento kurz je určený pro vývojáře, kteří chtějí začít s SCIM nebo jinými uživateli, kteří mají zájem o testování koncového bodu SICM. 

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Stažení referenčního kódu
> * Nasazení koncového bodu SCIM v Azure
> * Testování koncového bodu SCIM

K dispozici jsou možnosti koncového bodu:

|Koncový bod|Popis|
|---|---|
|`/User`|Provádění operací CRUD u prostředku uživatele: **vytvořit**, **aktualizovat**, **Odstranit**, **získat**, **vypsat**, **filtrovat**|
|`/Group`|Provádění operací CRUD pro prostředek skupiny: **vytvořit**, **aktualizovat**, **Odstranit**, **získat**, **vypsat**, **filtrovat**|
|`/Schemas`|Načíst jedno nebo více podporovaných schémat.<br/><br/>Sada atributů prostředku podporovaného jednotlivými poskytovateli služeb se může lišit, například poskytovatel služeb A podporuje "název", "title" a "e-maily", zatímco poskytovatel služeb B podporuje "název", "title" a "phoneNumbers" pro uživatele.|
|`/ResourceTypes`|Načte podporované typy prostředků.<br/><br/>Počet a typy prostředků podporovaných jednotlivými poskytovateli služeb se mohou lišit, například poskytovatel služeb A podporuje uživatele, zatímco poskytovatel služeb B podporuje uživatele a skupiny.|
|`/ServiceProviderConfig`|Načíst konfiguraci SCIM poskytovatele služeb<br/><br/>Funkce SCIM podporované jednotlivými poskytovateli služeb se můžou lišit, třeba poskytovatel služeb A podporuje operace oprav, zatímco poskytovatel služeb B podporuje operace opravy a zjišťování schématu.|

## <a name="download-the-reference-code"></a>Stažení referenčního kódu

[Referenční kód](https://github.com/AzureAD/SCIMReferenceCode) , který se má stáhnout, zahrnuje následující projekty:

- **Microsoft.SystemForCrossDomainIdentityManagement**, webové rozhraní API .NET Core MVC k sestavení a zřízení rozhraní SCIM API
- **Microsoft. SCIM. WebHostSample**, pracovní příklad koncového bodu SCIM

Projekty obsahují následující složky a soubory:

|Soubor nebo složka|Popis|
|-|-|
|Složka **schémat**| Modely pro **uživatele** a **skupiny** prostředků spolu s některými abstraktními třídami, jako je schematized pro sdílené funkce.<br/><br/> Složka **atributů** , která obsahuje definice třídy pro složité atributy **uživatelů** a **skupin** , jako jsou adresy.|
|Složka **služby** | Obsahuje logiku pro akce týkající se způsobu dotazování a aktualizace prostředků.<br/><br/> Referenční kód obsahuje služby pro vrácení uživatelů a skupin.<br/><br/>Složka **Controllers** obsahuje různé koncové body SCIM. Řadiče prostředků zahrnují příkazy HTTP pro provádění operací CRUD na prostředku (operace **Get**, **post**, **Put**, **patch**, **Delete**). Řadiče při provádění těchto akcí spoléhají na služby.|
|Složka **protokolu**|Obsahuje logiku pro akce týkající se způsobu, jakým jsou vráceny prostředky podle specifikace RFC SCIM, například:<br/><ul><li>Vrácení více prostředků jako seznamu.</li><li>Vracení pouze konkrétních prostředků na základě filtru.</li><li>Přepíná dotaz na seznam propojených seznamů s jedním filtry.</li><li>Zapnutím žádosti o opravu na operaci s atributy, které se vztahují k cestě k hodnotě.</li><li>Definování typu operace, kterou lze použít k provedení změn v objektech prostředků.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| Ukázkový zdrojový kód.|
|`Microsoft.SCIM.WebHostSample`| Ukázková implementace knihovny SCIM|
|*. gitignore*|Definujte, co se má ignorovat v době potvrzení.|
|*CHANGELOG.md*|Seznam změn v ukázce|
|*CONTRIBUTING.md*|Pokyny pro přispívání k ukázce.|
|*README.md*|Tento soubor **Readme** .|
|*PRŮKAZ*|Licence k ukázce|

> [!NOTE]
> Tento kód je určený k tomu, aby mohl začít sestavovat koncový bod SCIM a je k dispozici **jako**. Zahrnuté odkazy nemají žádnou záruku na aktivní maintainence ani podporu.
>
> Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Protože tyto [příspěvky](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) od komunity jsou Vítá vás při sestavování a údržbě úložiště a podobně jako jiné Open-Source příspěvky, budete souhlasit s licenční smlouvou přispěvatele (cla). Tato smlouva prohlašuje, že máte a udělujete práva k používání vašeho příspěvku, podrobnosti najdete v tématu [Microsoft Open Source](https://cla.opensource.microsoft.com).
>
> Další informace najdete v [nejčastějších dotazech k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/). V případě jakýchkoli dotazů nebo připomínek kontaktujte [opencode@microsoft.com](mailto:opencode@microsoft.com).

###  <a name="use-multiple-environments"></a>Používání více prostředí

Zahrnutý SCIM kód používá prostředí ASP.NET Core k řízení jeho autorizace pro použití při vývoji a po nasazení, viz [použití více prostředí v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1).

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>Nasazení koncového bodu SCIM v Azure

Zde uvedené kroky nasadí koncový bod SCIM do služby pomocí sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) a [Azure App Services](https://docs.microsoft.com/azure/app-service/). Referenční kód SCIM lze také spustit místně, hostovaný místním serverem nebo nasadit do jiné externí služby. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Otevřete řešení a nasaďte ho do Azure App Service

1. Z GitHubu můžete přejít na [referenční kód](https://github.com/AzureAD/SCIMReferenceCode) a vybrat **klonovat nebo stáhnout**.

1. Zvolte buď možnost **otevřít v aplikaci Desktop**, nebo zkopírujte odkaz, otevřete **aplikaci Visual Studio** a vyberte možnost **klonovat nebo rezervovat kód** pro zadání zkopírovaného odkazu a vytvoření místní kopie.

1. V **aplikaci Visual Studio** se ujistěte, že se přihlašujete k účtu, který má přístup k vašim hostitelským prostředkům.

1. V **Průzkumník řešení** otevřete soubor *Microsoft. SCIM. sln* a klikněte pravým tlačítkem myši na soubor *Microsoft. SCIM. WebHostSample* . Vyberte **Publikovat**.

    ![publikování v cloudu](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Pokud chcete toto řešení spustit lokálně, poklikejte na projekt a vyberte **IIS Express** pro spuštění projektu jako webové stránky s adresou URL místního hostitele.

1. Vyberte **vytvořit profil** a ujistěte se, že jsou vybrané **App Service** a **vytvořit nové** .

    ![publikování v cloudu 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Projděte si možnosti dialogového okna a přejmenujte aplikaci na název podle vašeho výběru. Tento název se používá jak v aplikaci, tak i v adrese URL koncového bodu SCIM.

    ![publikování v cloudu 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Vyberte skupinu prostředků, kterou chcete použít, a zvolte **publikovat**.

1. Přejděte do aplikace ve **službě Azure App Services**  >  **Konfigurace** a výběrem **nastavení nová aplikace** přidejte nastavení *Token__TokenIssuer* s hodnotou `https://sts.windows.net/<tenant_id>/` . Nahraďte `<tenant_id>` tenant_idm Azure AD a pokud chcete otestovat koncový bod SCIM pomocí [post](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), přidejte také *ASPNETCORE_ENVIRONMENT* nastavení s hodnotou `Development` . 

   ![nastavení AppService](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Při testování koncového bodu s podnikovou aplikací v Azure Portal vyberte možnost zachovat prostředí jako `Development` a poskytněte token vygenerovaný z `/scim/token` koncového bodu pro účely testování nebo změňte prostředí na `Production` a nechte pole tokenu v [Azure Portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)v podnikové aplikaci prázdné. 

A to je vše! Váš koncový bod SCIM je teď publikovaný a umožňuje vám použít Azure App Service URL k otestování koncového bodu SCIM.

## <a name="test-your-scim-endpoint"></a>Testování koncového bodu SCIM

Požadavky na koncový bod SCIM vyžadují autorizaci a SCIM Standard ponechává více možností pro ověřování a autorizaci, jako jsou soubory cookie, základní ověřování, ověřování klienta TLS nebo kterákoli z metod uvedených v [dokumentu RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Nepoužívejte nezabezpečené metody, jako je uživatelské jméno a heslo, a přihlaste se k bezpečnější metodě, jako je třeba OAuth. Azure AD podporuje dlouhodobé tokeny nosiče (pro aplikace v galerii a mimo galerii) a udělení autorizace OAuth (pro aplikace publikované v galerii aplikací).

> [!NOTE]
> Metody autorizace, které jsou k dispozici v úložišti, jsou určené pouze pro testování. Při integraci s Azure AD si můžete projít pokyny k autorizaci, viz [plánování zřizování pro koncový bod SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

Vývojové prostředí umožňuje, aby byly funkce nebezpečné pro produkční prostředí, například referenční kód pro řízení chování ověření tokenu zabezpečení. Ověřovací kód tokenu je nakonfigurován tak, aby používal token zabezpečení podepsaný svým vlastníkem a podpisový klíč je uložen v konfiguračním souboru, viz parametr **token: IssuerSigningKey** v *appsettings.Development.jsv* souboru.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Odesláním žádosti o **získání** na `/scim/token` koncový bod se token vydá pomocí nakonfigurovaného klíče a dá se použít jako nosný token pro následnou autorizaci.

Výchozí kód pro ověření tokenu je nakonfigurovaný tak, aby používal token vydaný Azure Active Directory a vyžaduje, aby byl vydávající tenant nakonfigurovaný pomocí parametru **token: TokenIssuer** v *appsettings.jsv* souboru.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Použít post k testování koncových bodů

Po nasazení koncového bodu SCIM můžete otestovat, abyste ověřili, že je SCIM RFC kompatibilní. Tento příklad poskytuje sadu testů v nástroji **post** za účelem ověření operací CRUD pro uživatele a skupiny, filtrování, aktualizace členství ve skupinách a zakázání uživatelů.

Koncové body jsou umístěné v `{host}/scim/` adresáři a lze je používat s použitím standardních požadavků HTTP. Postup změny `/scim/` trasy naleznete v tématu *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **Controllers**.

> [!NOTE]
> Pro místní testy můžete použít jenom koncové body HTTP, protože služba zřizování Azure AD vyžaduje, aby koncový bod podporoval HTTPS.

#### <a name="open-postman-and-run-tests"></a>Otevřete post a spusťte testy.

1. Stáhněte si aplikaci pro [publikování](https://www.getpostman.com/downloads/) a spuštění.
1. Zkopírujte odkaz [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) a vložte ho do příspěvku pro import kolekce testů.

    ![kolekce post](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Vytvořte testovací prostředí s proměnnými uvedenými níže:

   |Prostředí|Proměnná|Hodnota|
   |-|-|-|
   |Spustit projekt místně pomocí IIS Express|||
   ||**Server**|`localhost`|
   ||**Port**|`:44359`*(nezapomeňte na **:**)*|
   ||**API**|`scim`|
   |Spustit projekt místně pomocí Kestrel|||
   ||**Server**|`localhost`|
   ||**Port**|`:5001`*(nezapomeňte na **:**)*|
   ||**API**|`scim`|
   |Hostování koncového bodu v Azure|||
   ||**Server**|*(zadejte adresu URL SCIM)*|
   ||**Port**|*(ponechte prázdné)*|
   ||**API**|`scim`|

1. Použijte **Get Key** z kolekce post a odešlete požadavek **Get** na koncový bod tokenu a načtěte token zabezpečení, který bude uložen v proměnné **tokenu** pro následné požadavky. 

   ![klíč pro odeslání po načtení](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Aby byly SCIM koncové body zabezpečené, potřebujete před připojením token zabezpečení a kurz používá `{host}/scim/token` ke generování tokenu podepsaného svým držitelem koncový bod.

A to je vše! Nyní můžete spustit kolekci **post** a otestovat funkci koncového bodu SCIM.

## <a name="next-steps"></a>Další kroky

K vývoji koncového bodu uživatele a skupiny kompatibilního s SCIM s interoperabilitou pro klienta nástroje si přečtěte téma [Implementace klienta SCIM](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Kurz: vývoj a plánování zřizování pro koncový bod SCIM](use-scim-to-provision-users-and-groups.md) 
>  [Kurz: Konfigurace zřizování pro aplikaci Galerie](configure-automatic-user-provisioning-portal.md)