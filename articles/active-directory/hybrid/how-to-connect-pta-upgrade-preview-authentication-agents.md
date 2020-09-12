---
title: Azure AD Connect-předávací ověřování – agenti ověřování upgradu | Microsoft Docs
description: Tento článek popisuje, jak upgradovat konfiguraci předávacího ověřování pro Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0507f7751305af5e626cbd7dd6e0dfd1a63a74
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279478"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory předávací ověřování: Upgrade agentů ověřování verze Preview

## <a name="overview"></a>Přehled

Tento článek platí pro zákazníky, kteří používají předávací ověřování Azure AD prostřednictvím verze Preview. Nedávno jsme upgradovali (a přepracovali) software ověřovacího agenta. Musíte _ručně_ upgradovat agenty ověřování verze Preview nainstalované na místních serverech. Tento ruční upgrade je pouze jednorázová akce. Všechny budoucí aktualizace ověřovacích agentů jsou automaticky. Důvody pro upgrade jsou následující:

- Verze Preview agentů pro ověřování nebudou dostávat žádné další opravy zabezpečení nebo chyby.
-   Verze Preview agentů ověřování nelze instalovat na další servery pro zajištění vysoké dostupnosti.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrolovat verze agentů ověřování

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1: ověření instalace agentů ověřování

Postupujte podle těchto kroků a ověřte, kde jsou nainstalováni agenti ověřování:

1. Přihlaste se k [centru pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta.
2. V levém navigačním panelu vyberte **Azure Active Directory** .
3. Vyberte **Azure AD Connect**. 
4. Vyberte **předávací ověřování**. V tomto okně jsou uvedené servery, na kterých jsou nainstalované vaše agenti ověřování.

![Okno pro správu Azure Active Directory – okno předávacího ověřování](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2: ověření verzí agentů ověřování

Pokud chcete ověřit verze vašich ověřovacích agentů, na každém serveru, který jste zjistili v předchozím kroku, postupujte podle těchto pokynů:

1. Přejít na **Ovládací panely – > programy – > programy a funkce** na místním serveru.
2. Pokud existuje položka pro "**Microsoft Azure AD Connect Authentication agent**", nemusíte na tomto serveru provádět žádné akce.
3. Pokud existuje položka pro**Microsoft Azure AD konektor proxy aplikací**, musíte na tomto serveru ručně upgradovat.

![Verze Preview ověřovacího agenta](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Osvědčené postupy, které je potřeba provést před zahájením upgradu

Před upgradem se ujistěte, že máte následující položky:

1. **Vytvořit účet globálního správce jenom pro Cloud**: neupgradovat bez použití cloudového globálního účtu správce, který by se měl používat v mimořádných situacích, kdy ověřovací agenti, kteří prošli ověřováním, nefungují správně. Seznamte [se s přidáním účtu globálního správce jenom pro Cloud](../fundamentals/add-users-azure-active-directory.md). Provedení tohoto kroku je kritické a zajistí, že se nezamknete ze svého tenanta.
2.  **Zajištění vysoké dostupnosti**: Pokud není dřív dokončená, nainstalujte druhého samostatného ověřovacího agenta, který zajistí vysokou dostupnost žádostí o přihlášení, a to pomocí těchto [pokynů](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Upgrade ověřovacího agenta na serveru Azure AD Connect

Před upgradem ověřovacího agenta na stejném serveru je potřeba upgradovat Azure AD Connect. Postupujte podle těchto kroků na primárních i pracovních Azure AD Connect serverech:

1. **Upgrade Azure AD Connect**: postupujte podle tohoto [článku](how-to-upgrade-previous-version.md) a upgradujte na nejnovější verzi Azure AD Connect.
2. **Odinstalujte verzi Preview ověřovacího agenta**: Stáhněte si [Tento skript PowerShellu](https://aka.ms/rmpreviewagent) a spusťte ho jako správce na serveru.
3. **Stáhněte si nejnovější verzi ověřovacího agenta (verze 1.5.389.0 nebo novější)**: Přihlaste se k [centru pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta. Vyberte možnost **Azure Active Directory-> Azure AD Connect-> předávací ověřování-> stáhnout agenta**. Přijměte [podmínky služby](https://aka.ms/authagenteula) a Stáhněte si nejnovější verzi ověřovacího agenta. Ověřovacího agenta můžete také stáhnout z [tohoto umístění](https://aka.ms/getauthagent).
4. **Instalace nejnovější verze ověřovacího agenta**: Spusťte spustitelný soubor stažený v kroku 3. Po zobrazení výzvy zadejte přihlašovací údaje globálního správce vašeho tenanta.
5. **Ověřte, že je nainstalovaná nejnovější verze**: jak je znázorněno v části **ovládací panely – > programy – > programy a funkce** a ověřte, jestli je pro**agenta "Microsoft Azure AD Connect Authentication**" uvedena položka.

>[!NOTE]
>Pokud po dokončení předchozích kroků přejdete do okna předávací ověřování v [centru pro správu Azure Active Directory](https://aad.portal.azure.com) , zobrazí se dvě položky ověřovacího agenta na server – jedna položka, která zobrazuje ověřovacího agenta jako **aktivní** , a druhý jako **neaktivní**. Toto je _očekávané_. **Neaktivní** položka je automaticky vyřazena po několika dnech.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Upgrade ověřovacího agenta na jiných serverech

Pomocí těchto kroků můžete upgradovat agenty ověřování na jiných serverech (kde Azure AD Connect není nainstalovaný):

1. **Odinstalujte verzi Preview ověřovacího agenta**: Stáhněte si [Tento skript PowerShellu](https://aka.ms/rmpreviewagent) a spusťte ho jako správce na serveru.
2. **Stáhněte si nejnovější verzi ověřovacího agenta (verze 1.5.389.0 nebo novější)**: Přihlaste se k [centru pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta. Vyberte možnost **Azure Active Directory-> Azure AD Connect-> předávací ověřování-> stáhnout agenta**. Přijměte podmínky služby a Stáhněte si nejnovější verzi.
3. **Instalace nejnovější verze ověřovacího agenta**: Spusťte spustitelný soubor stažený v kroku 2. Po zobrazení výzvy zadejte přihlašovací údaje globálního správce vašeho tenanta.
4. **Ověřte, že je nainstalovaná nejnovější verze**: jak je znázorněno v části **ovládací panely – > programy – > programy a funkce** a ověřte, že se nachází položka s názvem **Microsoft Azure AD připojit ověřovací agent**.

>[!NOTE]
>Pokud po dokončení předchozích kroků přejdete do okna předávací ověřování v [centru pro správu Azure Active Directory](https://aad.portal.azure.com) , zobrazí se dvě položky ověřovacího agenta na server – jedna položka, která zobrazuje ověřovacího agenta jako **aktivní** , a druhý jako **neaktivní**. Toto je _očekávané_. **Neaktivní** položka je automaticky vyřazena po několika dnech.

## <a name="next-steps"></a>Další kroky
- [**Řešení potíží**](tshoot-connect-pass-through-authentication.md) – Naučte se řešit běžné problémy s touto funkcí.