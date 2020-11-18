---
title: Toky uživatelů v Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Přečtěte si další informace o rozšiřitelném rozhraní zásad Azure Active Directory B2C a o tom, jak vytvářet různé toky uživatelů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06253b571fd71623501c27fd5b0d9d4013727fc2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840185"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Toky uživatelů v Azure Active Directory B2C

Aby vám pomohlo nastavit nejběžnější úlohy identit pro vaše aplikace, Azure AD B2C portál obsahuje předdefinované a konfigurovatelné zásady nazývané **uživatelské toky**. Tok uživatele umožňuje určit, jak uživatelé budou pracovat s vaší aplikací, když provádějí věci, jako je přihlášení, registrace, úprava profilu nebo resetování hesla. Pomocí uživatelských toků můžete řídit následující možnosti:

- Typy účtů používané pro přihlašování, jako jsou účty sociálních sítí, jako je například Facebook nebo místní účet
- Atributy, které se mají shromáždit od příjemce, jako je křestní jméno, PSČ a velikost bot
- Multi-Factor Authentication Azure AD
- Přizpůsobení uživatelského rozhraní
- Informace, které aplikace obdrží jako deklarace identity v tokenu

V tenantovi můžete vytvořit mnoho uživatelských toků různých typů a podle potřeby je používat ve svých aplikacích. Toky uživatelů se dají znovu použít napříč aplikacemi. Tato flexibilita umožňuje definovat a upravovat prostředí identity s minimálními nebo žádnými změnami kódu. Vaše aplikace spustí tok uživatele pomocí standardní žádosti o ověření protokolu HTTP, která obsahuje parametr toku uživatele. Přizpůsobený [token](tokens-overview.md) se přijímá jako odpověď.

Následující příklady znázorňují parametr řetězce dotazu "p", který určuje tok uživatele, který se má použít:

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Verze toku uživatele

Azure AD B2C obsahuje několik typů uživatelských toků:

- **Zaregistrujte se a přihlaste se a přihlaste** se pomocí jediné konfigurace. Uživatelé mají v závislosti na kontextu správnou cestu. K dispozici jsou také samostatné toky **registrace** nebo **přihlašování** uživatelů. Obecně doporučujeme, abyste použili kombinaci registrace a přihlášení uživatele.
- **Úpravy profilu** – umožňuje uživatelům upravovat informace o profilu.
- **Resetování hesla** – umožňuje nakonfigurovat, jestli a jak můžou uživatelé resetovat heslo.

Většina typů uživatelských toků má jak **doporučenou** verzi, tak i **standardní** verzi. Podrobnosti najdete v tématu [verze toku uživatele](user-flow-versions.md).

> [!IMPORTANT]
> Pokud jste s toky uživatelů v Azure AD B2C pracovali předem, všimnete si, že jsme změnili způsob, jakým odkazujeme na verze uživatelského toku. Dříve jsme nabídli verze V1 (připravené pro produkční prostředí) a verze V 1.1 a v2 (Preview). Nyní jsme konsoliduje toky uživatelů do dvou verzí:
>
>- **Doporučené** toky pro uživatele jsou nové verze Preview toků uživatelů. Jsou důkladně testovány a kombinovány se všemi funkcemi starších verzí **v2** a **v 1.1** . Až dál, budou se udržovat a aktualizovat nové doporučené toky uživatelů. Po přechodu na tyto nové doporučené toky uživatelů budete mít přístup k novým funkcím, jakmile jsou vydány.
>- **Standardní** uživatelské toky, dříve označované jako **v1**, jsou všeobecně dostupné, uživatelské toky připravené pro produkční prostředí. Pokud jsou toky uživatelů klíčové a závisí na vysoce stabilních verzích, můžete pokračovat v používání standardních uživatelských toků a realizovat, že tyto verze nebudou zachované a aktualizované.
>
>Všechny toky pro uživatele starší verze Preview (V 1.1 a v2) jsou na cestě k vyřazení od **1. srpna 2021**. Pokud je to možné, důrazně doporučujeme [přepínat na nové **Doporučené** uživatelské toky](user-flow-versions.md#how-to-switch-to-a-new-recommended-user-flow) , abyste mohli vždycky využít nejnovější funkce a aktualizace.

## <a name="linking-user-flows"></a>Propojení toků uživatelů

Tok uživatelů **registrace nebo přihlašování** pomocí místních účtů zahrnuje **zapomenuté heslo** a odkaz na první stránce prostředí. Kliknutím na tento odkaz se automaticky neaktivuje tok uživatele resetování hesla.

Místo toho se kód chyby `AADB2C90118` vrátí do vaší aplikace. Vaše aplikace potřebuje zpracovat tento kód chyby spuštěním konkrétního toku uživatele, který resetuje heslo. Příklad zobrazíte tak, že se podíváte na [jednoduchý vzorek ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , který ukazuje propojení uživatelských toků.

## <a name="email-address-storage"></a>Úložiště e-mailových adres

E-mailová adresa může být vyžadována jako součást toku uživatele. Pokud se uživatel ověří pomocí zprostředkovatele sociální identity, e-mailová adresa se uloží do vlastnosti **otherMails** . Pokud je místní účet založen na uživatelském jménu, bude e-mailová adresa uložena ve vlastnosti detail silného ověřování. Pokud je místní účet založen na e-mailové adrese, uloží se tato e-mailová adresa do vlastnosti **signInNames** .

Tato e-mailová adresa není v žádném z těchto případů ověřena. Správce klienta může zakázat ověřování e-mailů v základních zásadách pro místní účty. I když je povolené ověřování e-mailových adres, adresy se neověřují, pokud pocházejí od poskytovatele sociální identity a nezměnily se.

Prostřednictvím rozhraní Microsoft Graph API se zveřejňují jenom vlastnosti **otherMails** a **signInNames** . E-mailová adresa v vlastnosti detail silného ověřování není k dispozici.

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit toky doporučeného uživatele, postupujte podle pokynů v tématu [kurz: vytvoření toku uživatele](tutorial-create-user-flows.md).
