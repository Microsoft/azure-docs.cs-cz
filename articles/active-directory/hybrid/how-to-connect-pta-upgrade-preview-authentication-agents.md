---
title: Agenti Azure AD Connect – předávací ověřování - upgradu auth | Dokumentace Microsoftu
description: Tento článek popisuje, jak upgradovat konfigurace předávacího ověřování Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
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
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172246"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Předávací ověřování služby Azure Active Directory: Upgrade agentů ověřování ve verzi preview

## <a name="overview"></a>Přehled

Tento článek je určený pro zákazníky, kteří používají předávacího ověřování Azure AD prostřednictvím ve verzi preview. Jsme nedávno upgradován (a přejmenované) software ověřovacího agenta. Je potřeba _ručně_ upgradu ve verzi preview ověřování agenti nainstalovaní na místních serverech. Tento ruční upgrade je jenom o jednorázovou akci. Všechny budoucí aktualizace agentů ověřování jsou automatické. Důvody pro upgrade jsou následující:

- Ve verzi preview verze agentů ověřování nebude přijímat žádné další zabezpečení nebo opravy chyb.
-   Ve verzi preview verze agentů ověřování nelze nainstalovat na dalších serverů pro zajištění vysoké dostupnosti.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrola verze agentů ověřování

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1: Zkontrolujte nainstalovanou agentů ověřování

Postupujte podle těchto kroků ke kontrole, kde jsou nainstalovaní agenti vaše ověřování:

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. Vyberte **Azure Active Directory** na levém navigačním panelu.
3. Vyberte **služby Azure AD Connect**. 
4. Vyberte **předávací ověřování**. Toto okno obsahuje seznam serverů, kde jsou nainstalovaní agenti vaše ověření.

![Azure Active Directory Centrum pro správu – okno předávací ověřování](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2: Kontrola verze agentů ověřování

Kontrola verze agentů ověřování, na každém serveru identifikován v předchozím kroku, postupujte podle těchto pokynů:

1. Přejděte na **ovládací panely -> programy -> programy a funkce** na místním serveru.
2. Pokud je záznam pro "**agenta služby Microsoft Azure AD Connect ověřování**", není nutné provádět žádnou akci na tomto serveru.
3. Pokud je záznam pro "**Microsoft Azure AD Application Proxy Connector**", je třeba ručně upgradovat na tomto serveru.

![Verze Preview ověřovacího agenta služby](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Doporučené postupy před zahájením upgradu

Před upgradem, ujistěte se, že máte následující položky:

1. **Vytvořit výhradně cloudový účet globálního správce**: Neprovádějte upgrade bez nutnosti výhradně cloudový účet globálního správce pro použití v nouzové situace, kdy agentů předávací ověřování nefungují správně. Další informace o [přidání výhradně cloudový účet globálního správce](../active-directory-users-create-azure-portal.md). Provedením tohoto kroku je velmi důležité a zajišťuje, že nezůstanete zamknutí mimo vašeho tenanta.
2.  **Zajištění vysoké dostupnosti**: Pokud není dokončena dříve, nainstalovat samostatnou druhý ověřovací Agent nebyl pro zajištění vysoké dostupnosti pro žádostí o přihlášení, použití těchto [pokyny](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Upgrade agenta ověřování na serveru služby Azure AD Connect

Před upgradem ověřovacího agenta na stejném serveru, třeba upgradu Azure AD Connect. Na primární a staging serverů služby Azure AD Connect, postupujte takto:

1. **Upgradovat Azure AD Connect**: Použít tento [článku](how-to-upgrade-previous-version.md) a proveďte upgrade na nejnovější verzi Azure AD Connect.
2. **Odinstalovat ověřovacího agenta ve verzi preview**: Stáhněte si [tento skript Powershellu](https://aka.ms/rmpreviewagent) a spustit jako správce na serveru.
3. **Stáhněte si nejnovější verzi agenta ověřování (verze 1.5.389.0 nebo novější)**: Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta. Vyberte **Azure Active Directory -> Azure AD Connect -> předávací ověřování -> stáhnout agenta**. Přijměte [podmínky služby](https://aka.ms/authagenteula) a stáhněte si nejnovější verzi agenta ověřování. Můžete si také stáhnout ověřovacího agenta z [tady](https://aka.ms/getauthagent).
4. **Nainstalujte nejnovější verzi agenta ověřování**: Spusťte spustitelný soubor stáhli v kroku 3. Zadejte přihlašovací údaje globálního správce vašeho tenanta po zobrazení výzvy.
5. **Ověřte, že je nainstalovaná nejnovější verze**: Jak je znázorněno před, přejděte na **ovládací panely -> programy -> programy a funkce** a ověřte, zda je položka pro "**agenta služby Microsoft Azure AD Connect ověřování**".

>[!NOTE]
>Pokud najdete v okně předávací ověřování na [centra pro správu Azure Active Directory](https://aad.portal.azure.com) po dokončení předchozích kroků, uvidíte dvě položky ověřovacího agenta na server – jedna položka zobrazuje ověřování Agent jako **aktivní** a ostatní jako **neaktivní**. Toto je _očekává_. **Neaktivní** položky se automaticky ukončí po několika dnech.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Upgrade agenta ověřování na jiných serverech

Použijte následující postup upgradu agentů ověřování na jiných serverech (kde Azure AD Connect není nainstalovaná):

1. **Odinstalovat ověřovacího agenta ve verzi preview**: Stáhněte si [tento skript Powershellu](https://aka.ms/rmpreviewagent) a spustit jako správce na serveru.
2. **Stáhněte si nejnovější verzi agenta ověřování (verze 1.5.389.0 nebo novější)**: Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho tenanta. Vyberte **Azure Active Directory -> Azure AD Connect -> předávací ověřování -> stáhnout agenta**. Přijměte podmínky služby a stáhnout nejnovější verzi.
3. **Nainstalujte nejnovější verzi agenta ověřování**: Spusťte spustitelný soubor stáhli v kroku 2. Zadejte přihlašovací údaje globálního správce vašeho tenanta po zobrazení výzvy.
4. **Ověřte, že je nainstalovaná nejnovější verze**: Jak je znázorněno před, přejděte na **ovládací panely -> programy -> programy a funkce** a ověřte, zda je položka volá **agenta služby Microsoft Azure AD Connect ověřování**.

>[!NOTE]
>Pokud najdete v okně předávací ověřování na [centra pro správu Azure Active Directory](https://aad.portal.azure.com) po dokončení předchozích kroků, uvidíte dvě položky ověřovacího agenta na server – jedna položka zobrazuje ověřování Agent jako **aktivní** a ostatní jako **neaktivní**. Toto je _očekává_. **Neaktivní** položky se automaticky ukončí po několika dnech.

## <a name="next-steps"></a>Další postup
- [**Řešení potíží s** ](tshoot-connect-pass-through-authentication.md) – zjistěte, jak vyřešit běžné problémy s funkcí.
