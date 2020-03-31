---
title: Simulace detekce rizik v azure ad identity protection
description: Zjistěte, jak simulovat detekci rizik v identitě Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886932"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulace detekce rizik v ochraně identity

Správci mohou chtít simulovat riziko ve svém prostředí, aby dosáhli následujících položek:

- Naplňte data v prostředí identity Protection simulací detekce rizik a chyb zabezpečení.
- Nastavte zásady podmíněného přístupu založené na rizicích a otestujte dopad těchto zásad.

Tento článek obsahuje postup pro simulaci následujících typů zjišťování rizik:

- Anonymní IP adresa (snadné)
- Neznámé přihlašovací vlastnosti (střední)
- Atypické cestování (obtížné)

Jiné detekce rizik nelze simulovat bezpečným způsobem.

Další informace o každé detekci rizik naleznete v článku [Co je riziko](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Anonymní IP adresa

Dokončení následujícího postupu vyžaduje použití:

- [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) pro simulaci anonymních IP adres. Možná budete muset použít virtuální počítač, pokud vaše organizace omezuje používání prohlížeče Tor.
- Testovací účet, který ještě není registrovaný pro Azure Multi-Factor Authentication.

**Chcete-li simulovat přihlášení z anonymní IP adresy, proveďte následující kroky**:

1. Pomocí [prohlížeče Tor](https://www.torproject.org/projects/torbrowser.html.en)přejděte na . [https://myapps.microsoft.com](https://myapps.microsoft.com)   
2. Zadejte přihlašovací údaje účtu, který se má zobrazit **v sestavě Přihlášení z anonymních IP adres.**

Přihlášení se zobrazí na řídicím panelu identity protection během 10 - 15 minut. 

## <a name="unfamiliar-sign-in-properties"></a>Neznámé přihlašovací vlastnosti

Chcete-li simulovat neznámé umístění, musíte se přihlásit z umístění a zařízení, ze které se testovací účet dosud nepřihlásil.

Níže uvedený postup používá nově vytvořený:

- připojení VPN, pro simulaci nového umístění.
- Virtuální stroj, simulovat nové zařízení.

Dokončení následujícího postupu vyžaduje použití uživatelského účtu, který má:

- Alespoň 30-ti denní přihlašovací historie.
- Vícefaktorové ověřování Azure povoleno.

**Chcete-li simulovat přihlášení z neznámého umístění, proveďte následující kroky**:

1. Při přihlášení pomocí testovacího účtu nesplníte výzvu vícefaktorového ověřování (MFA) tím, že nepředá výzvu mfa.
2. Pomocí nové sítě VPN [https://myapps.microsoft.com](https://myapps.microsoft.com) přejděte na přihlašovací údaje testovacího účtu a zadejte je.

Přihlášení se zobrazí na řídicím panelu identity protection během 10 - 15 minut.

## <a name="atypical-travel"></a>Atypické cestování

Simulace atypické cestovní podmínky je obtížné, protože algoritmus používá strojové učení k vyřazení falešných poplachů, jako je atypické cestování ze známých zařízení nebo přihlášení z virtuálních ny, které používají ostatní uživatelé v adresáři. Kromě toho algoritmus vyžaduje historii přihlášení 14 dny a 10 přihlášení uživatele před zahájením generování detekce rizik. Vzhledem ke složitým modelům strojového učení a nad pravidly existuje pravděpodobnost, že následující kroky nepovedou ke zjištění rizik. Můžete chtít replikovat tyto kroky pro více účtů Azure AD simulovat toto zjišťování.

**Chcete-li simulovat atypickou detekci cestovního rizika, proveďte následující kroky**:

1. Pomocí standardního prohlížeče [https://myapps.microsoft.com](https://myapps.microsoft.com)přejděte na .  
2. Zadejte přihlašovací údaje účtu, pro který chcete vygenerovat atypickou detekci cestovních rizik.
3. Změňte uživatelského agenta. Uživatelského agenta v Microsoft Edge můžete změnit z nástrojů pro vývojáře (F12).
4. Změňte svou IP adresu. Svou IP adresu můžete změnit pomocí VPN, doplňku Tor nebo vytvořením nového virtuálního počítače v Azure v jiném datovém centru.
5. Přihlaste [https://myapps.microsoft.com](https://myapps.microsoft.com) se k používání stejných přihlašovacích údajů jako před a během několika minut po předchozím přihlášení.

Přihlášení se zobrazí na řídicím panelu ochrany identity během 2-4 hodin.

## <a name="testing-risk-policies"></a>Testovací rizikové politiky

Tato část obsahuje postup testování uživatele a zásady rizik přihlášení vytvořené v článku [Postup: Konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Zásady rizika pro uživatele

Chcete-li otestovat zásady zabezpečení rizik uživatelů, proveďte následující kroky:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte na**přehled****zabezpečení** >  **služby Azure Active Directory** > .
1. Vyberte **konfigurovat zásady rizika uživatele**.
   1. V části **Přiřazení**
      1. **Uživatelé** – pokud omezíte zavedení, zvolte **Všichni uživatelé** **nebo Vybrat jednotlivce a skupiny.**
         1. Volitelně můžete vyloučit uživatele ze zásady.
      1. **Podmínky** - **Uživatelské riziko** Microsoft doporučení je nastavit tuto možnost na **vysoké**.
   1. V části **Ovládací prvky**
      1. **Access** – Doporučení společnosti Microsoft je **povolit přístup** a **vyžadovat změnu hesla**.
   1. **Vynutit zásady** - **vypnuto**
   1. **Uložit** – Tato akce vás vrátí na stránku **Přehled.**
1. Zvyšte riziko uživatele testovacího účtu například simulací jedné z detekcí rizik několikrát.
1. Počkejte několik minut a ověřte, zda se riziko pro uživatele zvýšilo. Pokud ne, simulujte více detekcí rizik pro uživatele.
1. Vraťte se do zásad rizik a nastavte **možnost Vynutit zásady** **na Zapnuto** a **Uložit** změnu zásad.
1. Podmíněný přístup založený na rizicích uživatele teď můžete otestovat tak, že se přihlásíte pomocí uživatele se zvýšenou úrovní rizika.

### <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení rizik přihlášení

Chcete-li otestovat zásady rizikpřihlášení, proveďte následující kroky:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte na**přehled****zabezpečení** >  **služby Azure Active Directory** > .
1. Vyberte **Konfigurovat zásady rizika přihlášení**.
   1. V části **Přiřazení**
      1. **Uživatelé** – pokud omezíte zavedení, zvolte **Všichni uživatelé** **nebo Vybrat jednotlivce a skupiny.**
         1. Volitelně můžete vyloučit uživatele ze zásady.
      1. **Podmínky** - **Riziko přihlášení Microsoft** doporučuje nastavit tuto možnost na střední a **vyšší**.
   1. V části **Ovládací prvky**
      1. **Access** – Doporučení společnosti Microsoft je **povolit přístup** a **vyžadovat vícefaktorové ověřování**.
   1. **Vynutit zásady** - **na**
   1. **Uložit** – Tato akce vás vrátí na stránku **Přehled.**
1. Podmíněný přístup založený na přihlašovacích rizicích můžete nyní otestovat tak, že se přihlásíte pomocí rizikové relace (například pomocí prohlížeče Tor). 

## <a name="next-steps"></a>Další kroky

- [Co je riziko?](concept-identity-protection-risks.md)

- [Postup: Konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md)

- [Ochrana identit služby Azure Active Directory](overview-identity-protection.md)
