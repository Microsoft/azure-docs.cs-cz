---
title: Definice souborů cookie
titleSuffix: Azure AD B2C
description: Poskytuje definice souborů cookie používaných ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189510"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definice souborů cookie pro Azure AD B2C

Následující části obsahují informace o souborech cookie používaných ve službě Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>Stejné stránky

Služba Microsoft Azure AD B2C je kompatibilní s konfiguracemi `SameSite=None` prohlížeče `Secure` SameSite, včetně podpory s atributem.

Pro zajištění přístupu k webům zavedou webové prohlížeče nový model podle výchozích nastavení, který předpokládá, že všechny soubory cookie by měly být chráněny před externím přístupem, pokud není uvedeno jinak. Prohlížeč Chrome je první, kdo implementuje tuto změnu, počínaje [Chrome 80 v únoru 2020](https://www.chromium.org/updates/same-site). Další informace o přípravě na změnu v Chromu najdete v [tématu Vývojáři: Připravte se na nový samesite=žádný; Zabezpečte nastavení souborů cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) na blogu chromu.

Vývojáři musí použít nové `SameSite=None`nastavení souborů cookie , k označení souborů cookie pro přístup mezi sítěmi. Pokud `SameSite=None` je atribut přítomen, `Secure` musí být použit další atribut, aby byly soubory cookie mezi webovými servery přístupné pouze přes připojení HTTPS. Ověřte a otestujte všechny aplikace, včetně těch aplikací, které používají Azure AD B2C.

Další informace naleznete v tématu:

* [Zpracování změn souborů cookie SameSite v prohlížeči Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Vliv na weby zákazníků a služby a produkty společnosti Microsoft v Chromu verze 80 nebo novějším](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Soubory cookie

V následující tabulce jsou uvedeny soubory cookie používané v Azure AD B2C.

| Name (Název) | Domain (Doména) | Konec platnosti | Účel |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Ukončení [relace prohlížeče](session-behavior.md) | Obsahuje data členství uživatele napříč klienty. Klienti, kterých je uživatel členem a úroveň členství (Správce nebo Uživatel). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Slouží k směrování požadavků do příslušné výrobní instance. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Používá se pro sledování transakcí (počet žádostí o ověření pro Azure AD B2C) a aktuální transakce. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Používá se pro udržování relace s přisychávání. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, značková doména | Konec [relace prohlížeče](session-behavior.md), úspěšné ověření | Používá se pro udržování stavu požadavku. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Token padělání požadavků mezi lokalitami používaný pro ochranu CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Používá se pro směrování sítě Azure AD B2C. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Kontext |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Používá se pro ukládání dat členství pro klienta poskytovatele prostředků. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, značková doména | Ukončení [relace prohlížeče](session-behavior.md) | Používá se pro ukládání relé cookie. |
