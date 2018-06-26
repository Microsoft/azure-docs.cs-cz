---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: bfdc89d9bc5d5a07c04e857c1a46e4b988c125ab
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943551"
---
## <a name="test-your-code"></a>Otestujte svůj kód

Chcete-li otestovat aplikaci v sadě Visual Studio, stiskněte **F5** ke spuštění projektu. Prohlížeči se otevře http://<span></span>localhost: {port} umístění a zobrazí **přihlásit pomocí Microsoft** tlačítko. Kliknutím na tlačítko Spustit proces přihlášení.

Pokud jste připravení spustit test, použít účet Microsoft Azure Active Directory (Azure AD) (pracovní nebo školní účet) nebo osobní účet Microsoft (<span>za provozu.</span> com nebo <span>aplikace outlook.</span> com) pro přihlášení.

![Přihlaste se pomocí Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Přihlaste se ke svému účtu Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Zobrazení výsledků aplikace
Po přihlášení se uživatel přesměruje na domovskou stránku vašeho webu. Domovská stránka je adresu URL HTTPS, který je uveden v informace o registraci aplikace v portálu pro registraci aplikace společnosti Microsoft. Domovská stránka obsahuje uvítací zprávy *"Hello \<uživatele >,"* odkaz Odhlásit se a odkaz na zobrazení deklaracích identity uživatele. Přejde na odkaz pro deklarace identity uživatele k *deklarace identity* řadiče, kterou jste vytvořili dříve.

### <a name="browse-to-see-the-users-claims"></a>Procházet zobrazíte deklaracích identity uživatele
Informace o deklaracích identity uživatele, vyberte odkaz a přejděte do zobrazení řadiče, které je k dispozici pouze pro ověřené uživatele.

#### <a name="view-the-claims-results"></a>Zobrazení výsledků deklarace identity
Po přejití řadiče zobrazení, měli byste vidět tabulku, která obsahuje základní vlastnosti pro uživatele:

|Vlastnost |Hodnota |Popis |
|---|---|---|
|**Název** |Jméno a příjmení uživatele | Jméno a příjmení uživatele.
|**Uživatelské jméno** |Uživatel<span>@domain.com</span> | Uživatelské jméno, který se používá k identifikaci uživatele.
|**Předmět** |Předmět |Řetězec, který jednoznačně identifikuje uživatele na webu.|
|**ID klienta** |Guid | A **guid** který jedinečně reprezentuje uživatele organizaci Azure AD.|

Kromě toho měli byste vidět tabulku všechny deklarace identity, které jsou v žádosti o ověření. Další informace najdete v tématu [seznam deklarací identity, které jsou v tokenu ID služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Test přístupu k metodě, která má atribut autorizovat (volitelné)
K otestování přístupu jako anonymní uživatel na řadič chráněný pomocí `Authorize` atribut, postupujte takto:
1. Vyberte odkaz Odhlásit uživatele a dokončete proces přihlášení.
2. V prohlížeči zadejte http://<span></span>localhost: {port} / deklarace identity pro přístup k řadiči, která je chráněná pomocí `Authorize` atribut.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Očekávané výsledky po přístup k chráněné řadiče
Se zobrazí výzva k ověření použít chráněné řadiče zobrazení.

## <a name="advanced-options"></a>Upřesnit možnosti

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Chránit celého webu
K ochraně celého webu, v **Global.asax** soubor, přidejte `AuthorizeAttribute` atribut `GlobalFilters` filtrovat v `Application_Start` metoda:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Omezit, kdo může přihlásit k aplikaci
Ve výchozím nastavení při sestavování aplikace vytvořené v této příručce, aplikace bude přijímat přihlášení osobní účty (včetně live.com, outlook.com a dalších) a také pracovní a školní účty z jakékoli společnost nebo organizace, který se má integrovat s Azure Active Directory. Toto je doporučená možnost pro SaaS aplikace.

Pokud chcete omezit přístup přihlášení uživatelů pro vaši aplikaci, jsou k dispozici několik možností:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Možnost 1: Omezte uživatele z instance služby Active Directory pouze jedna organizace pro přihlášení k aplikaci (single klienta)

Tato možnost je běžný scénář pro *obchodních aplikací*: Pokud chcete, aby aplikace tak, aby přijímal přihlášení pouze z účtů, které patří ke konkrétní instanci Azure Active Directory (včetně *účet hosta*této instance) proveďte následující:

1. V **web.config** souboru, změňte hodnotu `Tenant` parametr z `Common` k názvu klienta organizace, například `contoso.onmicrosoft.com`.
2. Ve vaší [třídy pro spuštění OWIN](#configure-the-authentication-pipeline), nastavte `ValidateIssuer` argument `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Možnost 2: Omezení přístupu k aplikaci pro uživatele v konkrétní seznam organizací

Můžete omezit přístup k přihlášení do pouze uživatelské účty, které jsou v organizaci Azure AD, která je v seznamu povolených organizací:
1. Ve vaší [třídy pro spuštění OWIN](#configure-the-authentication-pipeline), nastavte `ValidateIssuer` argument `true`.
2. Nastavte hodnotu `ValidIssuers` parametru do seznamu povolených organizací.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Možnost 3: Používáte vlastní metodu k ověření vystavitele
Můžete implementovat vlastní metodu pro ověření vystavitelů pomocí **IssuerValidator** parametr. Další informace o tom, jak tento parametr použijte, přečtěte si informace o [parametry tokenvalidationparameters třída](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) na webu MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
