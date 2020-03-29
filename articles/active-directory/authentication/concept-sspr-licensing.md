---
title: Samoobslužné resetování hesla – Azure Active Directory
description: Samoobslužné licenční požadavky na resetování hesla Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848557"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licenční požadavky na samoobslužné resetování hesla Azure AD

Azure Active Directory (Azure AD) se dodává v několika edicích: Free, Premium P1 a Premium P2. Existuje několik různých funkcí, které tvoří samoobslužné resetování hesla, včetně změny, resetování, odemknutí a zpětného zápisu, které jsou k dispozici v různých edicích Azure AD. Tento článek se snaží vysvětlit rozdíly. Další podrobnosti o funkcích zahrnutých v každé edici Azure AD najdete na [stránce s cenami služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porovnání edic a funkcí

Samoobslužné resetování hesla Azure AD se licencuje na uživatele, aby byly organizace s dodržováním předpisů povinny přiřadit uživatelům příslušnou licenci.

* Samoobslužná změna hesla pro uživatele cloudu
   * Jsem pouze **cloud uživatel** a vím, že moje heslo.
      * Chtěl bych **změnit** své heslo na něco nového.
   * Tato funkce je součástí všech edicí Azure AD.

* Samoobslužné resetování hesla pro uživatele cloudu
   * Jsem pouze **cloud uživatel** a zapomněli své heslo.
      * Chtěl bych **obnovit** své heslo na něco, co vím.
   * Tato funkce je součástí Azure AD Premium P1 nebo P2, Microsoft 365 Business nebo Office 365.

* Samoobslužné resetování/změna/odemknutí **hesla pomocí místního zpětného zápisu**
   * Jsem hybridní **uživatel** můj místní účet uživatele Služby Active Directory je synchronizován s mým účtem Azure AD pomocí Azure AD Connect. Chtěl bych změnit své heslo, zapomněl jsem heslo nebo byl uzamčen.
      * Chci změnit heslo nebo ho obnovit na něco, co vím, nebo odemknout svůj účet **a** nechat tuto změnu synchronizovat zpět do místní služby Active Directory.
   * Tato funkce je součástí Azure AD Premium P1 nebo P2 nebo Microsoft 365 Business.

> [!WARNING]
> Samostatné licenční plány Office 365 *nepodporují "Samoobslužné resetování/změnu/odemknutí hesla s místním zpětným zápisem"* a vyžadují plán, který zahrnuje Azure AD Premium P1, Premium P2 nebo Microsoft 365 Business, aby tato funkce fungovala.
>

Další informace o licencích, včetně nákladů, naleznete na následujících stránkách:

* [Cenový web Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funkce a možnosti služby Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Popis obchodní služby Microsoft 365](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Povolení skupinových nebo uživatelských licencí

Azure AD teď podporuje skupinové licencování. Správci mohou hromadně přiřazovat licence skupině uživatelů, nikoli je přiřazovat po jednom. Další informace naleznete v tématu [Přiřazení, ověření a řešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Některé služby Microsoft nejsou dostupné ve všech umístěních. Před přiřazením licence uživateli musí správce určit vlastnost **Umístění použití** uživatele. Přiřazení licencí lze provést v části**Nastavení** **profilu** >  **uživatele** > na webu Azure Portal. *Při použití přiřazení skupinové licence zdědí umístění adresáře všichni uživatelé bez zadaného umístění použití.*

## <a name="next-steps"></a>Další kroky

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco rozbité. Jak lze vyřešit probléms sspr?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
