---
title: Vytvoření koncového bodu SCIM pro zřizování uživatelů pro aplikace z Azure Active Directory
description: Naučte se vyvíjet SCIM koncový bod, integrovat rozhraní SCIM API do služby Azure AD a automaticky zřizovat uživatele a skupiny do svých cloudových aplikací pomocí Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 4e933000c8e700d8bfd193b542e3855b2fca26f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689330"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Kurz: vývoj ukázkového koncového bodu SCIM

Nikdo nechce vytvořit nový koncový bod od začátku, takže jsme vytvořili pár [referenčních kódů](https://aka.ms/scimreferencecode) , abyste mohli začít pracovat se [systémem pro správu identit mezi doménami (SCIM)](https://aka.ms/scimoverview). Svůj koncový bod SCIM můžete začít používat bez kódu za pouhých pět minut.

V tomto kurzu se dozvíte, jak nasadit referenční kód SCIM v Azure a otestovat ho pomocí post nebo integrací s klientem služby Azure Active Directory (Azure AD) SCIM. Tento kurz je určený pro vývojáře, kteří chtějí začít pracovat s SCIM, nebo kdokoli, kdo se zajímá o testování koncového bodu SCIM.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Nasaďte koncový bod SCIM v Azure.
> * Otestujte koncový bod SCIM.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Nasazení koncového bodu SCIM v Azure

Tento postup umožňuje nasadit koncový bod SCIM do služby pomocí sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) a [Azure App Service](../../app-service/index.yml). Referenční kód SCIM lze také spustit místně, hostovaný místním serverem nebo nasadit do jiné externí služby.

1. Z GitHubu můžete přejít na [referenční kód](https://github.com/AzureAD/SCIMReferenceCode) a vybrat **klonovat nebo stáhnout**.

1. Vyberte možnost **otevřít v počítači** nebo zkopírujte odkaz, otevřete aplikaci Visual Studio a vyberte možnost **klonovat nebo rezervovat kód** pro zadání zkopírovaného odkazu a vytvoření místní kopie.

1. V aplikaci Visual Studio se přihlaste k účtu, který má přístup k vašim hostitelským prostředkům.

1. V Průzkumník řešení otevřete soubor *Microsoft. SCIM. sln* a klikněte pravým tlačítkem myši na soubor *Microsoft. SCIM. WebHostSample* . Vyberte **Publikovat**.

    ![Snímek obrazovky, který zobrazuje ukázkový soubor.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Pokud chcete toto řešení spustit lokálně, poklikejte na projekt a vyberte **IIS Express** pro spuštění projektu jako webové stránky s adresou URL místního hostitele.

1. Vyberte **vytvořit profil** a ujistěte se, že jsou vybrané **App Service** a **vytvořit nové** .

    ![Snímek obrazovky, který zobrazuje okno pro publikování.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Projděte si možnosti dialogového okna a přejmenujte aplikaci na název podle vašeho výběru. Tento název se používá jak v aplikaci, tak i v adrese URL koncového bodu SCIM.

    ![Snímek obrazovky, který ukazuje vytvoření nové služby App Service.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Vyberte skupinu prostředků, kterou chcete použít, a vyberte **publikovat**.

    ![Snímek obrazovky, který ukazuje publikování nové služby App Service.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. Chcete-li   >  Přidat nastavení *Token__TokenIssuer* s hodnotou, v části **Konfigurace** aplikace Azure App Service vyberte možnost **nové nastavení aplikace** `https://sts.windows.net/<tenant_id>/` . Nahraďte `<tenant_id>` ID tenanta Azure AD. Pokud chcete otestovat koncový bod SCIM pomocí [metody post](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), přidejte nastavení *ASPNETCORE_ENVIRONMENT* s hodnotou `Development` .

   ![Snímek obrazovky, který zobrazuje okno nastavení aplikace.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Při testování koncového bodu s podnikovou aplikací v [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)máte dvě možnosti. Můžete ponechat prostředí v nástroji `Development` a poskytnout testovací token z `/scim/token` koncového bodu, nebo můžete změnit prostředí na `Production` a nechat pole tokenu prázdné.

A to je vše! Váš koncový bod SCIM je teď publikovaný a pomocí Azure App Service URL můžete testovat koncový bod SCIM.

## <a name="test-your-scim-endpoint"></a>Testování koncového bodu SCIM

Požadavky na koncový bod SCIM vyžadují autorizaci. SCIM Standard obsahuje několik možností pro ověřování a autorizaci, včetně souborů cookie, základního ověřování, ověřování klientů TLS nebo kterékoli z metod uvedených v [dokumentu RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Snažte se vyhnout metodám, které nejsou zabezpečené, jako je uživatelské jméno a heslo, a přihlaste se tak bezpečnější metodou, jako je například OAuth. Azure AD podporuje dlouhodobé tokeny nosiče (pro aplikace v galerii a mimo galerii) a udělení autorizace OAuth (pro aplikace v galerii).

> [!NOTE]
> Metody autorizace, které jsou k dispozici v úložišti, jsou určené pouze pro testování. Když provádíte integraci s Azure AD, můžete si projít pokyny k autorizaci. Viz [plánování zřizování pro koncový bod SCIM](use-scim-to-provision-users-and-groups.md).

Vývojové prostředí povoluje funkce, které jsou nebezpečné pro produkci, například referenční kód pro řízení chování ověření tokenu zabezpečení. Ověřovací kód tokenu používá token zabezpečení podepsaný svým držitelem a podpisový klíč je uložen v konfiguračním souboru. Podívejte se na parametr **token: IssuerSigningKey** v souboru *appsettings.Development.js* .

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Když odešlete požadavek **Get** na `/scim/token` koncový bod, vystaví se token pomocí nakonfigurovaného klíče. Tento token se dá použít jako nosný token pro následnou autorizaci.

Výchozí kód pro ověření tokenu je nakonfigurovaný tak, aby používal token Azure AD, a vyžaduje, aby vydaný tenant byl nakonfigurovaný pomocí parametru **token: TokenIssuer** v *appsettings.jsv* souboru.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Použít post k testování koncových bodů

Po nasazení koncového bodu SCIM můžete otestovat a ujistit se, že je kompatibilní s dokumentem RFC SCIM. Tento příklad poskytuje sadu testů v členovi, který ověřuje operace CRUD (vytváření, čtení, aktualizace a odstraňování) u uživatelů a skupin, filtrování, aktualizace členství ve skupinách a zakázání uživatelů.

Koncové body jsou v `{host}/scim/` adresáři a můžete k jejich interakci použít standardní požadavky HTTP. Postup změny `/scim/` trasy najdete v tématu *ControllerConstant. cs* v   >    >  **řadičích** ScimReferenceApi AzureADProvisioningSCIMreference.

> [!NOTE]
> Koncové body HTTP můžete použít pouze pro místní testy. Služba zřizování Azure AD vyžaduje, aby koncový bod podporoval protokol HTTPS.

1. Stáhněte si [post](https://www.getpostman.com/downloads/) a spusťte aplikaci.
1. Zkopírujte a vložte tento odkaz do metody post pro import kolekce testů: `https://aka.ms/ProvisioningPostman` .

    ![Snímek obrazovky, který ukazuje import kolekce testů v poli post.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Vytvořte testovací prostředí, které má tyto proměnné:

   |Prostředí|Proměnná|Hodnota|
   |-|-|-|
   |Spustit projekt místně pomocí IIS Express|||
   ||**Server**|`localhost`|
   ||**Port**|`:44359`*(nezapomeňte na **`:`** )*|
   ||**API**|`scim`|
   |Spustit projekt místně pomocí Kestrel|||
   ||**Server**|`localhost`|
   ||**Port**|`:5001`*(nezapomeňte na **`:`** )*|
   ||**API**|`scim`|
   |Hostování koncového bodu v Azure|||
   ||**Server**|*(zadejte adresu URL SCIM)*|
   ||**Port**|*(ponechte prázdné)*|
   ||**API**|`scim`|

1. Použijte **Get Key** z kolekce post a odešlete požadavek **Get** na koncový bod tokenu a načtěte token zabezpečení, který bude uložen v proměnné **tokenu** pro následné požadavky.

   ![Snímek obrazovky, který zobrazuje složku pro získání klíčů pro odeslání](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Aby byl SCIM koncový bod zabezpečený, budete před připojením potřebovat token zabezpečení. Kurz používá `{host}/scim/token` ke generování tokenu podepsaného svým držitelem koncový bod.

A to je vše! Nyní můžete spustit kolekci **post** a otestovat funkci koncového bodu SCIM.

## <a name="next-steps"></a>Další kroky

Informace o vývoji koncového bodu uživatele a skupiny kompatibilního s SCIM s interoperabilitou pro klienta najdete v tématu [implementace klientů SCIM](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Kurz: vývoj a plánování zřizování pro koncový bod SCIM](use-scim-to-provision-users-and-groups.md) 
>  [Kurz: Konfigurace zřizování pro aplikaci Galerie](configure-automatic-user-provisioning-portal.md)