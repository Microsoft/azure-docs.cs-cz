---
title: Azure AD Connect – předávací ověřování – upgrade auth agenti | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak upgradovat konfiguraci předávacího ověřování služby Azure Active Directory (Azure AD).
services: active-directory
keywords: Předávací ověřování Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 494ccc3b90b8c249ee935087dcf0f0b5264b02ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386741"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Předávací ověřování služby Azure Active Directory: Agenti ověřování preview upgradu

## <a name="overview"></a>Přehled

Tento článek je určen pro zákazníky, kteří používají předávací ověřování Azure AD prostřednictvím verze preview. Nedávno jsme upgradovali (a rebrandovali) software Agent autentizace. Je třeba _ručně_ upgradovat agenty preview ověřování nainstalované na místních serverech. Tento ruční upgrade je pouze jednorázová akce. Všechny budoucí aktualizace agentů ověřování jsou automatické. Důvody pro upgrade jsou následující:

- Verze preview agentů ověřování neobdrží žádné další opravy zabezpečení nebo chyb.
-   Verze preview agentů ověřování nelze nainstalovat na další servery, pro vysokou dostupnost.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrola verzí autentizačních agentů

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1: Kontrola, kde jsou nainstalováni agenti ověřování

Chcete-li zkontrolovat, kde jsou agenti ověřování nainstalováni, postupujte takto:

1. Přihlaste se do [Centra pro správu Služby Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. V levém navigačním panelu vyberte **službu Azure Active Directory.**
3. Vyberte **Azure AD Connect**. 
4. Vyberte **možnost Předávací ověřování**. V tomto okně jsou uvedeny servery, na kterých jsou nainstalováni agenti ověřování.

![Centrum pro správu služby Azure Active Directory – okno předávacího ověřování](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2: Kontrola verzí autentizačních agentů

Chcete-li zkontrolovat verze autentizačních agentů, postupujte na každém serveru uvedeném v předchozím kroku podle následujících pokynů:

1. Přejděte na místním serveru **do Ovládacích panelů -> Programy -> programy a funkce.**
2. Pokud existuje položka pro "**Microsoft Azure AD Connect Authentication Agent**", nemusíte na tomto serveru provádět žádnou akci.
3. Pokud je položka pro "**Microsoft Azure AD Konektor proxy aplikací**", je třeba ručně upgradovat na tomto serveru.

![Předběžná verze agenta ověřování](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Doporučené postupy, které je třeba dodržovat před zahájením upgradu

Před upgradem se ujistěte, že máte k dispozici následující položky:

1. **Vytvoření účtu globálního správce pouze pro cloud**: Neupgradujte, aniž byste měli účet globálního správce pouze pro cloud, který by se používal v nouzových situacích, kdy agenti předávacího ověřování nepracují správně. Přečtěte si [o přidání účtu globálního správce pouze pro cloud](../active-directory-users-create-azure-portal.md). Provedení tohoto kroku je důležité a zajišťuje, že se nedostanete uzamčenz vašeho tenanta.
2.  **Zajistěte vysokou dostupnost**: Pokud jste dříve nedokončili, nainstalujte druhého samostatného agenta ověřování, který poskytuje vysokou dostupnost pro požadavky na přihlášení podle těchto [pokynů](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Upgrade agenta ověřování na serveru Azure AD Connect

Před upgradem agenta ověřování na stejném serveru je třeba upgradovat Azure AD Connect. Postupujte podle následujících kroků na primárních i pracovních serverech Azure AD Connect:

1. **Upgrade Azure AD Connect**: Postupujte podle tohoto [článku](how-to-upgrade-previous-version.md) a upgradujte na nejnovější verzi Azure AD Connect.
2. **Odinstalujte verzi preview agenta ověřování**: Stáhněte [tento skript prostředí PowerShell](https://aka.ms/rmpreviewagent) a spusťte jej jako správce na serveru.
3. **Stáhněte si nejnovější verzi agenta ověřování (verze 1.5.389.0 nebo novější):** Přihlaste se do [centra pro správu Služby Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce svého klienta. Vyberte **Azure Active Directory -> Azure AD Connect -> předávací ověřování -> download agent**. Přijměte [smluvní podmínky](https://aka.ms/authagenteula) a stáhněte si nejnovější verzi agenta pro ověřování. Agenta pro ověřování si můžete stáhnout [také zde](https://aka.ms/getauthagent).
4. **Nainstalujte nejnovější verzi agenta ověřování**: Spusťte spustitelný soubor stažený v kroku 3. Po zobrazení výzvy zadejte pověření globálního správce vašeho klienta.
5. **Ověřte, zda byla nainstalována nejnovější verze**: Jak je uvedeno dříve, přejděte na **Ovládací panely -> Programy -> programy a funkce** a ověřte, zda existuje položka pro**agenta ověřování microsoft azure ad connect**".

>[!NOTE]
>Pokud po dokončení předchozích kroků zaškrtnete okno Předávací ověřování v [Centru pro správu služby Azure Active Directory,](https://aad.portal.azure.com) zobrazí se dvě položky agenta ověřování na server – jedna položka zobrazující agenta ověřování jako **aktivní** a druhá jako **neaktivní**. To _se očekává_. Po několika dnech se automaticky vypustí **položka Neaktivní.**

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Inovace agenta ověřování na jiných serverech

Postupem upgradu agentů ověřování na jiných serverech (kde není nainstalovaný Azure AD Connect):

1. **Odinstalujte verzi preview agenta ověřování**: Stáhněte [tento skript prostředí PowerShell](https://aka.ms/rmpreviewagent) a spusťte jej jako správce na serveru.
2. **Stáhněte si nejnovější verzi agenta ověřování (verze 1.5.389.0 nebo novější):** Přihlaste se do [centra pro správu Služby Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce svého klienta. Vyberte **Azure Active Directory -> Azure AD Connect -> předávací ověřování -> download agent**. Přijměte smluvní podmínky a stáhněte si nejnovější verzi.
3. **Nainstalujte nejnovější verzi agenta ověřování**: Spusťte spustitelný soubor stažený v kroku 2. Po zobrazení výzvy zadejte pověření globálního správce vašeho klienta.
4. **Ověřte, zda byla nainstalována nejnovější verze**: Jak je uvedeno dříve, přejděte na **Ovládací panely -> programy - > programy a funkce** a ověřte, zda existuje položka nazvaná Agent pro ověřování připojení **služby Microsoft Azure AD**.

>[!NOTE]
>Pokud po dokončení předchozích kroků zaškrtnete okno Předávací ověřování v [Centru pro správu služby Azure Active Directory,](https://aad.portal.azure.com) zobrazí se dvě položky agenta ověřování na server – jedna položka zobrazující agenta ověřování jako **aktivní** a druhá jako **neaktivní**. To _se očekává_. Po několika dnech se automaticky vypustí **položka Neaktivní.**

## <a name="next-steps"></a>Další kroky
- [**Poradce při potížích**](tshoot-connect-pass-through-authentication.md) – Přečtěte si, jak vyřešit běžné problémy s funkcí.
