---
title: Simulace zjišťování rizik v Azure AD Identity Protection
description: Naučte se simulovat detekci rizik v Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbcc2351b7ead3714cfd21aa30b031cec75074a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84463752"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulace zjišťování rizik v Identity Protection

Správci mohou chtít simulovat riziko ve svém prostředí, aby mohli provádět následující položky:

- Naplňte data v prostředí Identity Protection tím, že simulujete detekci rizik a ohrožení zabezpečení.
- Nastavte zásady podmíněného přístupu na základě rizika a otestujte dopad těchto zásad.

Tento článek popisuje kroky pro simulaci následujících typů detekce rizik:

- Anonymní IP adresa (Snadná)
- Neznámou vlastnost přihlášení (střední)
- Neobvyklá cestování (obtížné)

Jiné zjišťování rizik se nedají simulovat zabezpečeným způsobem.

Další informace o jednotlivých detekcích rizik najdete v článku [co je to riziko](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Anonymní IP adresa

Dokončení následujícího postupu vyžaduje, abyste použili:

- [Prohlížeč mandátu](https://www.torproject.org/projects/torbrowser.html.en) pro simulaci anonymních IP adres. Je možné, že budete muset virtuální počítač použít, pokud vaše organizace omezuje používání prohlížeče pro prostředí.
- Testovací účet, který ještě není zaregistrovaný pro Azure Multi-Factor Authentication.

**Chcete-li simulovat přihlášení z anonymní IP adresy, proveďte následující kroky**:

1. V [prohlížeči](https://www.torproject.org/projects/torbrowser.html.en)systému pro práci přejděte na adresu [https://myapps.microsoft.com](https://myapps.microsoft.com) .   
2. Zadejte přihlašovací údaje účtu, který se má zobrazit v sestavě **přihlášení z anonymních IP adres** .

Přihlašování se zobrazuje na řídicím panelu Identity Protection během 10-15 minut. 

## <a name="unfamiliar-sign-in-properties"></a>Neznámé vlastnosti přihlášení

Abyste mohli simulovat neznámou polohu, musíte se přihlásit z umístění a zařízení, ke kterému váš zkušební účet ještě nebyl přihlášený.

Následující postup používá nově vytvořenou:

- Připojení k síti VPN pro simulaci nového umístění.
- Virtuální počítač, abyste mohli simulovat nové zařízení.

Provedení následujícího postupu vyžaduje, abyste použili uživatelský účet, který má:

- Aspoň 30 dní na historii přihlášení.
- Azure Multi-Factor Authentication povolen.

**Chcete-li simulovat přihlášení z neznámého umístění, proveďte následující kroky**:

1. Když se přihlašujete pomocí testovacího účtu, selže výzva služby Multi-Factor Authentication (MFA), protože neprojde dotaz MFA.
2. Pomocí nové sítě VPN přejděte na adresu [https://myapps.microsoft.com](https://myapps.microsoft.com) a zadejte přihlašovací údaje svého testovacího účtu.

Přihlašování se zobrazuje na řídicím panelu Identity Protection během 10-15 minut.

## <a name="atypical-travel"></a>Neobvyklá cesta

Simulace netypických cestovních cest je obtížné, protože algoritmus využívá Machine Learning k pleveli falešně pozitivních hodnot, jako je například neobvyklá cesta ze známých zařízení, nebo přihlášení z sítí VPN, které používají jiní uživatelé v adresáři. Kromě toho algoritmus vyžaduje historii přihlášení 14 dní a 10 přihlášení uživatele, než začne generovat detekci rizik. Z důvodu komplexních modelů strojového učení a výše uvedených pravidel existuje možnost, že následující kroky nebudou mít za následek detekci rizik. Tyto kroky můžete chtít replikovat pro více účtů Azure AD, abyste mohli simulovat toto zjišťování.

**Chcete-li simulovat detekci netypických cest na cestách, proveďte následující kroky**:

1. V prohlížeči použijte standardní prohlížeč a přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. Zadejte přihlašovací údaje účtu, pro který chcete vygenerovat kontrolu neobvyklých rizik pro služební vztah.
3. Změňte svého uživatelského agenta. Uživatelský agent můžete změnit v Microsoft Edge z Vývojářské nástroje (F12).
4. Změňte IP adresu. IP adresu můžete změnit pomocí sítě VPN, samoobslužného doplňku nebo vytvořením nového virtuálního počítače v Azure v jiném datovém centru.
5. Přihlaste se [https://myapps.microsoft.com](https://myapps.microsoft.com) pomocí stejných přihlašovacích údajů jako před a během několika minut po předchozím přihlášení.

Přihlašování se zobrazuje na řídicím panelu Identity Protection během 2-4 hodin.

## <a name="testing-risk-policies"></a>Testování rizikových zásad

V této části najdete postup testování uživatelů a zásad rizik přihlašování vytvořených v článku [Postupy: konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Zásady rizik uživatelů

Chcete-li otestovat zásady zabezpečení rizik uživatelů, proveďte následující kroky:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory**  >  **Security**  >  **Přehled**zabezpečení Azure Active Directory.
1. Vyberte **Konfigurovat zásady rizik uživatelů**.
   1. V části **přiřazení**
      1. **Uživatelé** – zvolte možnost **Všichni uživatelé** nebo **Vyberte jednotlivce a skupiny,** Pokud chcete omezit zavedení.
         1. Volitelně můžete vybrat možnost vyloučení uživatelů ze zásad.
      1. **Podmínky**  -  **Riziko uživatele** V doporučení Microsoftu je tato možnost nastavená na hodnotu **Vysoká**.
   1. Pod **ovládacími prvky**
      1. **Přístup** – doporučení Microsoftu je **Povolení přístupu** a **vyžadování změny hesla**.
   1. **Vyhovět zásadám**  -  **Vypnuto**
   1. **Uložit** – Tato akce vás vrátí na stránku **Přehled** .
1. Zvyšte riziko pro uživatele testovacího účtu, a to například simulací některého z detekcí rizika několikrát.
1. Počkejte pár minut a pak ověřte, že riziko pro vašeho uživatele má vyšší oprávnění. Pokud ne, simulovat pro uživatele více detekcí rizik.
1. Vraťte se k vašim rizikovým zásadám a nastavte **zásadu Vynutilit** na **zapnuto** a **uložte** změnu zásad.
1. Můžete teď otestovat podmíněný přístup na základě rizik uživatelů přihlášením pomocí uživatele se zvýšenou úrovní rizika.

### <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení rizik přihlašování

Chcete-li otestovat rizikové zásady přihlašování, proveďte následující kroky:

1. Přejděte na [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory**  >  **Security**  >  **Přehled**zabezpečení Azure Active Directory.
1. Vyberte **Konfigurovat zásady rizik přihlašování**.
   1. V části **přiřazení**
      1. **Uživatelé** – zvolte možnost **Všichni uživatelé** nebo **Vyberte jednotlivce a skupiny,** Pokud chcete omezit zavedení.
         1. Volitelně můžete vybrat možnost vyloučení uživatelů ze zásad.
      1. **Podmínky**  -  **Riziko přihlášení** Doporučení Microsoftu je nastavit tuto možnost na **střední a vyšší**.
   1. Pod **ovládacími prvky**
      1. **Přístup** – doporučení Microsoftu je **Povolení přístupu** a **vyžadování služby Multi-Factor Authentication**.
   1. **Vyhovět zásadám**  -  **Zapnuto**
   1. **Uložit** – Tato akce vás vrátí na stránku **Přehled** .
1. Nyní můžete otestovat podmíněný přístup na základě rizik přihlašování pomocí rizikové relace (například pomocí prohlížeče služby 

## <a name="next-steps"></a>Další kroky

- [Co je riziko?](concept-identity-protection-risks.md)

- [Postupy: konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
