---
title: Agenty Azure AD Connect - předávací ověřování – upgradu auth | Microsoft Docs
description: Tento článek popisuje postup upgradu konfiguraci předávací ověřování Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: c699df83c7032dc6e3d176753d8203fec6977c1b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591986"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure předávací ověřování služby Active Directory: Agenti ověřování upgradu preview

## <a name="overview"></a>Přehled

Tento článek je pro zákazníky používající Azure AD předávací ověřování prostřednictvím preview. Nedávno upgradován (jsme přejmenované) softwaru ověření agenta. Budete muset _ručně_ preview upgradu agentů ověřování nainstalovaný na místní servery. Tento ruční upgrade je jednorázová akce. Všechny budoucí aktualizace agentů ověřování jsou automatické. Důvody k upgradu jsou následující:

- Verze preview ověřování agentů nebude přijímat žádné další zabezpečení nebo opravy chyb.
-   Verze preview ověřování agentů nelze nainstalovat na další servery, pro zajištění vysoké dostupnosti.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrola verze agentů ověřování

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Krok 1: Zkontrolujte, kde jsou nainstalovány agenty ověřování

Postupujte podle těchto kroků ke kontrole, kde jsou nainstalovány agenty ověřování:

1. Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho klienta.
2. Vyberte **Azure Active Directory** na levé straně.
3. Vyberte **Azure AD Connect**. 
4. Vyberte **předávací ověřování**. Toto okno obsahuje seznam serverů, kde jsou nainstalovány agenty ověřování.

![Azure Active Directory Centrum pro správu – okno předávací ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Krok 2: Kontrola verze agentů ověřování

Zkontrolujte verze agentů ověřování, na každém serveru identifikovali v předchozím kroku, postupujte podle těchto pokynů:

1. Přejděte na **ovládací panely -> programy -> programy a funkce** na místním serveru.
2. Pokud je položka pro "**agenta služby Microsoft Azure AD Connect ověřování**", nemusíte provádět žádnou akci na tomto serveru.
3. Pokud je položka pro "**Microsoft Azure AD Application Proxy Connector**", verze 1.5.132.0 nebo starší, je třeba ručně upgradovat na tomto serveru.

![Verze Preview ověřování agenta](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Doporučené postupy před spuštěním upgradu

Před upgradem, ujistěte se, že máte zavedenou následující položky:

1. **Vytvořit účet globálního správce jenom pro cloud**: neupgradujete bez nutnosti výhradně cloudový účet globálního správce používat v případě nouze kde agenty předávací ověřování nefungují správně. Další informace o [přidání účtu globálního správce jenom pro cloud](../active-directory-users-create-azure-portal.md). Tento krok je velmi důležité a zajistí, že nezůstanete z vašeho klienta.
2.  **Zajištění vysoké dostupnosti**: Pokud není dokončen dříve, nainstalovat samostatnou druhé ověření agenta pro zajištění vysoké dostupnosti pro žádostí o přihlášení, použití těchto [pokyny](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Upgrade agenta ověřování na serveru Azure AD Connect

Před upgradem agenta ověřování na stejném serveru, třeba upgradovat Azure AD Connect. Na hlavním i pracovní servery Azure AD Connect, postupujte takto:

1. **Upgrade Azure AD Connect**: následovat po této [článku](./active-directory-aadconnect-upgrade-previous-version.md) a upgradovat na nejnovější verzi Azure AD Connect.
2. **Odinstalujte verzi preview ověřování agenta**: stažení [tento skript prostředí PowerShell](https://aka.ms/rmpreviewagent) a potom ho spusťte jako správce na serveru.
3. **Stáhněte si nejnovější verzi agenta ověřování (verze 1.5.193.0 nebo novější)**: Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho klienta. Vyberte **Azure Active Directory -> Azure AD Connect -> předávací ověřování -> stáhnout agenta**. Přijměte [podmínky služby](https://aka.ms/authagenteula) a stáhnout nejnovější verzi agenta ověřování. Můžete také stáhnout agenta ověřování z [zde](https://aka.ms/getauthagent).
4. **Nainstalujte nejnovější verzi agenta ověřování**: spuštění spustitelného souboru stáhli v kroku 3. Zadejte pověření pro globálního správce vašeho klienta po zobrazení výzvy.
5. **Ověřte, zda byla nainstalována nejnovější verze**: jak je znázorněno před, přejděte na **ovládací panely -> programy -> programy a funkce** a ověřte, zda je položka pro "**Microsoft Azure AD Connect Agent ověřování**".

>[!NOTE]
>Pokud zaškrtnete okně předávací ověřování na [centra pro správu Azure Active Directory](https://aad.portal.azure.com) po dokončení předchozí kroky, uvidíte dvě položky ověřování agenta na server - jednu položku zobrazující Agent ověřování jako **Active** a jiných jako **neaktivní**. Toto je _očekává_. **Neaktivní** položku automaticky vyřazen po několik dní.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Upgrade agenta ověřování na jiných serverech

Použijte následující postup upgradu agentů ověřování na jiných serverech, (kde Azure AD Connect není nainstalovaná):

1. **Odinstalujte verzi preview ověřování agenta**: stažení [tento skript prostředí PowerShell](https://aka.ms/rmpreviewagent) a potom ho spusťte jako správce na serveru.
2. **Stáhněte si nejnovější verzi agenta ověřování (verze 1.5.193.0 nebo novější)**: Přihlaste se k [centra pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce vašeho klienta. Vyberte **Azure Active Directory -> Azure AD Connect -> předávací ověřování -> stáhnout agenta**. Přijměte podmínky služby a stáhnout nejnovější verzi.
3. **Nainstalujte nejnovější verzi agenta ověřování**: spuštění spustitelného souboru stáhli v kroku 2. Zadejte pověření pro globálního správce vašeho klienta po zobrazení výzvy.
4. **Ověřte, zda byla nainstalována nejnovější verze**: jak je znázorněno před, přejděte na **ovládací panely -> programy -> programy a funkce** a ověřte, zda je volána položku **Microsoft Azure AD Connect Agent ověřování**.

>[!NOTE]
>Pokud zaškrtnete okně předávací ověřování na [centra pro správu Azure Active Directory](https://aad.portal.azure.com) po dokončení předchozí kroky, uvidíte dvě položky ověřování agenta na server - jednu položku zobrazující Agent ověřování jako **Active** a jiných jako **neaktivní**. Toto je _očekává_. **Neaktivní** položku automaticky vyřazen po několik dní.

## <a name="next-steps"></a>Další postup
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – zjistěte, jak řešit obvyklé problémy s funkcí.
