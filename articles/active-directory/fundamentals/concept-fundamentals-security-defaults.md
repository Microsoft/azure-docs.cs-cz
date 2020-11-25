---
title: Výchozí nastavení zabezpečení Azure Active Directory
description: Výchozí zásady zabezpečení, které vám pomůžou chránit organizace před běžnými útoky ve službě Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c26cbf55c1e3883605d4c65659511af20cf02c7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996674"
---
# <a name="what-are-security-defaults"></a>Co jsou výchozí nastavení zabezpečení?

Správa zabezpečení může být obtížné díky běžným útokům souvisejícím s identitou, jako je například postřik hesla, přehrávání a útoky phishing. Výchozí hodnoty zabezpečení usnadňují ochranu vaší organizace před těmito útoky pomocí předem nakonfigurovaných nastavení zabezpečení:

- Vyžaduje se, aby se všichni uživatelé zaregistrovali Multi-Factor Authentication služby Azure AD.
- Vyžadování správců k provádění vícefaktorového ověřování.
- Blokování protokolů pro ověřování starší verze.
- Vyžaduje, aby uživatelé v případě potřeby prováděli službu Multi-Factor Authentication.
- Ochrana privilegovaných aktivit, jako je přístup k Azure Portal.

![Snímek obrazovky Azure Portal s přepínačem pro povolení výchozích hodnot zabezpečení](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Další podrobnosti o tom, proč jsou k dispozici výchozí hodnoty zabezpečení, najdete v blogovém příspěvku Alex Weinert, [představení výchozích hodnot zabezpečení](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="availability"></a>Dostupnost

Microsoft zpřístupňuje výchozí nastavení zabezpečení všem uživatelům. Cílem je zajistit, aby měly všechny organizace základní úroveň zabezpečení povoleno bez dalších poplatků. Výchozí nastavení zabezpečení můžete zapnout v Azure Portal. Pokud se tenant vytvořil v nebo po 22. říjnu 2019, je možné, že ve vašem tenantovi jsou už povolené výchozí hodnoty zabezpečení. V zájmu ochrany všech našich uživatelů se ve všech nově vytvořených tenantůch zavádějí výchozí hodnoty zabezpečení.

### <a name="whos-it-for"></a>Kdo je pro?

- Pokud jste organizace, která chce zvýšit zabezpečení stav, ale nevíte, jak nebo kde začít, výchozí nastavení zabezpečení je pro vás.
- Pokud jste organizace s využitím bezplatné úrovně Azure Active Directory licencování, výchozí nastavení zabezpečení je pro vás.

### <a name="who-should-use-conditional-access"></a>Kdo má používat podmíněný přístup?

- Pokud jste v organizaci aktuálně používali zásady podmíněného přístupu, které slouží ke spojování signálů, k rozhodování a vystavování zásad organizace, pro vás nejspíš neplatí výchozí nastavení zabezpečení. 
- Pokud jste organizace s licencemi Azure Active Directory Premium, výchozí nastavení zabezpečení pro vás pravděpodobně nejsou správná.
- Pokud má vaše organizace složité požadavky na zabezpečení, měli byste zvážit podmíněný přístup.

## <a name="policies-enforced"></a>Zásady se vynutily

### <a name="unified-multi-factor-authentication-registration"></a>Registrace sjednocené Multi-Factor Authentication

Všichni uživatelé ve vašem tenantovi musí zaregistrovat službu Multi-Factor Authentication (MFA) ve formě Multi-Factor Authentication služby Azure AD. Uživatelé mají po 14 dnech registraci k Azure AD Multi-Factor Authentication pomocí aplikace Microsoft Authenticator. Po uplynutí 14 dnů se uživatel nebude moct přihlásit, dokud se nedokončí registrace. Po dobu 14 dní uživatele začíná po prvním úspěšném interaktivním přihlášení po povolení výchozích hodnot zabezpečení.

### <a name="protecting-administrators"></a>Ochrana správců

Uživatelé s privilegovaným přístupem mají větší přístup k vašemu prostředí. Vzhledem k napájení těchto účtů byste je měli považovat za zvláštní péči. Jednou z běžných metod, jak zlepšit ochranu privilegovaných účtů, je vyžadovat pro přihlášení silnější formu ověření účtu. V Azure AD můžete získat silnější ověření účtu tím, že budete vyžadovat vícefaktorové ověřování.

Po dokončení registrace ve službě Azure AD Multi-Factor Authentication bude nutné při každém přihlášení provést následující devět rolí správce Azure AD:

- Globální správce
- Správce SharePointu
- Správce Exchange
- Správce podmíněného přístupu
- Správce zabezpečení
- Správce helpdesku
- Správce fakturace
- Správce uživatelů
- Správce ověřování

### <a name="protecting-all-users"></a>Ochrana všech uživatelů

Doporučujeme, abyste si myslíte, že účty správců jsou jedinými účty, které vyžadují další vrstvy ověřování. Správci mají rozsáhlý přístup k citlivým informacím a můžou provádět změny nastavení pro celé předplatné. Ale útočníci často cílí na koncové uživatele. 

Po získání přístupu pro tyto útočníky můžou požádat o přístup k privilegovaným informacím jménem původního držitele účtu. Můžou si dokonce stáhnout celý adresář a udělat tak útok útoku phishing na celou organizaci. 

Jednou z běžných metod pro zlepšení ochrany pro všechny uživatele je vyžadovat silnější formu ověření účtu, například Multi-Factor Authentication pro každého. Jakmile se uživatelé dokončí Multi-Factor Authentication registraci, budou vyzváni k dalšímu ověřování, kdykoli to bude nutné. Tato funkce chrání všechny aplikace zaregistrované ve službě Azure AD, včetně aplikací SaaS.

### <a name="blocking-legacy-authentication"></a>Blokování starších verzí ověřování

Aby měli uživatelé snadný přístup k vašim cloudovým aplikacím, Azure AD podporuje nejrůznější ověřovací protokoly, včetně starší verze ověřování. *Starší verze ověřování* je termín, který odkazuje na žádost o ověření, kterou provedla:

- Klienti, kteří nepoužívají moderní ověřování (například klienta Office 2010).
- Každý klient používající starší e-mailové protokoly, jako jsou IMAP, SMTP nebo POP3.

V současné době většina neúspěšných pokusů o přihlášení pocházela ze staršího ověřování. Starší verze ověřování nepodporuje Multi-Factor Authentication. I v případě, že máte ve svém adresáři povolené zásady Multi-Factor Authentication, útočník se může ověřit pomocí staršího protokolu a Multi-Factor Authentication obcházení. 

Po povolení výchozích hodnot zabezpečení ve vašem tenantovi budou všechny požadavky na ověření provedené starším protokolem blokované. Výchozí hodnoty zabezpečení blokují Exchange Active Sync základní ověřování.

> [!WARNING]
> Než povolíte výchozí nastavení zabezpečení, zajistěte, aby vaši správci nepoužívali starší ověřovací protokoly. Další informace najdete v tématu [Jak přejít pryč ze starší verze ověřování](concept-fundamentals-block-legacy-authentication.md).

- [Jak nastavit multifunkční zařízení nebo aplikaci pro odesílání e-mailů pomocí Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Ochrana privilegovaných akcí

Organizace používají různé služby Azure spravované prostřednictvím rozhraní Azure Resource Manager API, včetně:

- portál Azure 
- Azure PowerShell 
- Azure CLI

Použití Azure Resource Manager ke správě služeb je vysoce privilegovaná akce. Azure Resource Manager může měnit konfigurace v rámci tenanta, jako je například nastavení služby a fakturace předplatného. Jednotné vícefaktorové ověřování je zranitelné vůči nejrůznějším útokům, jako je útok phishing a heslo. 

Je důležité ověřit identitu uživatelů, kteří chtějí získat přístup k Azure Resource Manager a aktualizovat konfigurace. Než povolíte přístup, ověříte jejich identitu tím, že budete vyžadovat další ověřování.

Po povolení výchozích hodnot zabezpečení ve vašem tenantovi bude nutné, aby každý uživatel, který přistupuje k Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI, dokončil další ověřování. Tato zásada platí pro všechny uživatele, kteří přistupují k Azure Resource Manager, ať už se jedná o správce nebo uživatele. 

> [!NOTE]
> Ve výchozím nastavení je ve výchozím nastavení zakázáno moderní ověřování klientů služby Exchange Online ve více než 2017. Abyste se vyhnuli možnosti přihlašovací smyčky při ověřování prostřednictvím těchto tenantů, musíte [Povolit moderní ověřování](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Účet synchronizace Azure AD Connect je vyloučený z výchozích hodnot zabezpečení a nebude vyzván k registraci nebo provedení vícefaktorového ověřování. Organizace by tento účet neměli používat pro jiné účely.

## <a name="deployment-considerations"></a>Aspekty nasazování

K nasazení výchozích hodnot zabezpečení se vztahují následující další požadavky.

### <a name="authentication-methods"></a>Metody ověřování

Tato bezplatná výchozí nastavení zabezpečení umožňují registraci a používání služby Azure AD Multi-Factor Authentication **jenom pomocí Microsoft Authenticator aplikace**. Podmíněný přístup umožňuje použití libovolné metody ověřování, kterou správce zvolí k povolení.

| Metoda | Výchozí nastavení zabezpečení | Podmíněný přístup |
| --- | --- | --- |
| Oznámení prostřednictvím mobilní aplikace | X | X |
| Ověřovací kód z mobilní aplikace nebo hardwarového tokenu | × * * | X |
| Textová zpráva na telefon |   | X |
| Zavolat na telefon |   | X |
| Hesla aplikací |   | X * * _ |

- _ * Uživatelé můžou použít ověřovací kódy z aplikace Microsoft Authenticator, ale můžou se zaregistrovat jenom pomocí možnosti oznámení.
- * * _ Hesla aplikací jsou k dispozici pouze v případě MFA pro jednotlivé uživatele se staršími scénáři ověřování, pokud jsou povolena správci.

### <a name="disabled-mfa-status"></a>Stav zakázaného MFA

Pokud je vaše organizace předchozím uživatelem Multi-Factor Authentication Azure AD, který je založený na uživatelích, nebudete budíkem, aby neviděli uživatele v *Enabled* případě, že se podíváte na **stránku stavu multi** -Factor auth. **Disabled (zakázáno** ) je odpovídající stav pro uživatele, kteří používají výchozí hodnoty zabezpečení nebo Multi-Factor Authentication Azure AD založené na podmíněném přístupu.

### <a name="conditional-access"></a>Podmíněný přístup

Podmíněný přístup můžete použít ke konfiguraci zásad, které se podobají výchozím hodnotám zabezpečení, ale s větší členitosti včetně vylučování uživatelů, které nejsou dostupné ve výchozím nastavení zabezpečení. Pokud používáte podmíněný přístup a ve vašem prostředí máte povolené zásady podmíněného přístupu, výchozí nastavení zabezpečení nebude k dispozici. Pokud máte licenci, která poskytuje podmíněný přístup, ale nemáte ve svém prostředí povolené žádné zásady podmíněného přístupu, budete mít k dispozici výchozí nastavení zabezpečení, dokud nepovolíte Zásady podmíněného přístupu. Další informace o licencování Azure AD najdete na stránce s [cenami služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Zpráva s upozorněním, že je možné použít výchozí nastavení zabezpečení nebo podmíněný přístup ne obojí](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Tady jsou podrobné návody, jak pomocí podmíněného přístupu nakonfigurovat ekvivalentní zásady na tyto zásady povolené ve výchozím nastavení zabezpečení:

- [Vyžadování MFA pro správce](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Vyžadování MFA pro správu Azure](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blokování starší verze ověřování](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Vyžadování MFA pro všechny uživatele](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Vyžadovat registraci Azure AD MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – vyžaduje Azure AD Identity Protection část Azure AD Premium P2.

## <a name="enabling-security-defaults"></a>Povolení výchozích hodnot zabezpečení

Povolení výchozích hodnot zabezpečení v adresáři:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)   jako správce zabezpečení, správce podmíněného přístupu nebo globální správce.
1. Přejděte na **Azure Active Directory**   >  **vlastnosti**.
1. Vyberte **Spravovat výchozí nastavení zabezpečení**.
1. Nastavte přepínač **Povolit výchozí hodnoty zabezpečení** na **Ano**.
1. Vyberte **Uložit**.

## <a name="disabling-security-defaults"></a>Zakazování výchozích hodnot zabezpečení

Organizace, které se rozhodnou implementovat zásady podmíněného přístupu, které nahrazují výchozí hodnoty zabezpečení, musí zakázat výchozí nastavení zabezpečení. 

![Zpráva upozornění zakázat výchozí nastavení zabezpečení pro povolení podmíněného přístupu](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Zakázání výchozích hodnot zabezpečení v adresáři:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)   jako správce zabezpečení, správce podmíněného přístupu nebo globální správce.
1. Přejděte na **Azure Active Directory**   >  **vlastnosti**.
1. Vyberte **Spravovat výchozí nastavení zabezpečení**.
1. Nastavte přepínač **Povolit výchozí hodnoty zabezpečení** na **ne**.
1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Běžné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md)