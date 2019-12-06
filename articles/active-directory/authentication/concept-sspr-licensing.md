---
title: Samoobslužné resetování hesla k licenci – Azure Active Directory
description: Požadavky na licencování samoobslužného resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848557"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licenční požadavky pro Samoobslužné resetování hesla služby Azure AD

Azure Active Directory (Azure AD) se dodává v několika edicích: Free, Premium P1 a Premium P2. K dispozici je několik různých funkcí, které tvoří Samoobslužné resetování hesla, včetně změn, resetování, odemknutí a zpětného zápisu, které jsou k dispozici v různých edicích služby Azure AD. Tento článek se snaží vysvětlit rozdíly. Další podrobnosti o funkcích, které jsou součástí jednotlivých edic Azure AD, najdete na [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porovnání edicí a funkcí

Samoobslužné resetování hesla služby Azure AD je licencováno na uživatele. aby bylo zajištěno, že organizace s dodržováním předpisů přiřadí příslušné licence svým uživatelům.

* Samoobslužná změna hesel pro cloudové uživatele
   * Jsem **pouze cloudový uživatel** a zná heslo.
      * Chci **změnit** heslo na něco nového.
   * Tato funkce je zahrnutá ve všech edicích Azure AD.

* Samoobslužné resetování hesla pro cloudové uživatele
   * Jsem **pouze cloudový uživatel** a zapomněl jsem heslo.
      * Chci **resetovat** heslo na něco, co znáte.
   * Tato funkce je součástí Azure AD Premium P1 nebo P2, Microsoft 365 Business nebo Office 365.

* Samoobslužné resetování hesla, změna/odemknutí **pomocí místního zpětného zápisu**
   * Jsem **hybridní uživatel** , který má místní uživatelský účet služby Active Directory, se synchronizuje s účtem Azure AD pomocí Azure AD Connect. Chci změnit svoje heslo, nechat zapomenuté heslo nebo být zamčené.
      * Chtěl bych změnit heslo nebo ho resetovat na něco, co znáte, nebo odemknout svůj účet **a** nechat si změnu synchronizovat zpátky do místní služby Active Directory.
   * Tato funkce je součástí Azure AD Premium P1 nebo P2 nebo Microsoft 365 Business.

> [!WARNING]
> Samostatné plány licencování pro Office 365 *nepodporují Samoobslužné resetování hesla, změnu/odemknutí pomocí místního zpětného zápisu* a vyžadují, aby tato funkce fungovala v plánu, který zahrnuje Azure AD Premium P1, Premium P2 nebo Microsoft 365 Business.
>

Další informace o licencování, včetně nákladů, najdete na následujících stránkách:

* [Azure Active Directory cenové lokality](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funkce a možnosti](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Popis služby Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Povolení licencování skupin nebo uživatelů

Azure AD teď podporuje licencování na základě skupin. Správci můžou hromadně přiřazovat licence skupině uživatelů, ale nepřiřazovat je po jednom. Další informace najdete v tématu [přiřazení, ověření a řešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Některé služby Microsoft nejsou dostupné ve všech umístěních. Před přiřazením licence uživateli musí správce zadat pro uživatele vlastnost **umístění používání** . Přiřazení licencí se dá udělat v části > **Nastavení** **profilu** **uživatele** > v Azure Portal. *Když použijete přiřazení skupinové licence, všichni uživatelé bez zadaného umístění pro použití zdědí umístění adresáře.*

## <a name="next-steps"></a>Další kroky

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco přerušeno. Návody řešit potíže s SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
