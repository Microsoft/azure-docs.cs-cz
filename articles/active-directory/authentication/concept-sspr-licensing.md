---
title: Licence Azure Active Directory hesla pomocí samoobslužné služby
description: Azure AD samoobslužné resetování hesla licenčním požadavkům
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: b558a494a87b9d176888f6e21d3153d921cd590f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422129"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Resetovat licenčních požadavcích pro hesla pomocí samoobslužné služby Azure AD

Azure Active Directory (Azure AD) je k dispozici ve čtyřech edicích: Free, Basic, Premium P1 a Premium P2. Existuje několik různých funkcí, které se samoobslužné resetování hesla, včetně změn, resetovat, odemkněte a zpětný zápis, které jsou k dispozici v různých edicích služby Azure AD. V tomto článku se pokusí vysvětlují rozdíly. Další informace o funkcích v každé edici Azure AD můžete najít na [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porovnání edicí a funkce

Hesel Azure AD samoobslužné resetování je licencovaný podle počtu uživatelů, bude zajišťovat dodržování nutí organizace, přiřadit uživatelům příslušnou licenci.

* Samoobslužná změna hesel pro cloudové uživatele
   * Jsem **výhradně cloudový uživatel** a vím svoje heslo.
      * Chci **změnit** heslo na něco nového.
   * Tato funkce je zahrnut ve všech edicích služby Azure AD.

* Samoobslužné resetování hesla pro cloudové uživatele
   * Jsem **výhradně cloudový uživatel** a zapomněli jste heslo.
      * Chci **resetování** heslo na něco, co mi vědět.
   * Tato funkce je zahrnutá v Azure AD Basic, Premium P1 nebo P2 nebo Microsoft 365 Business.

* Samoobslužná služba heslo resetování/změna/odemknutí **s místní zpětný zápis**
   * Jsem **hybridní uživatele** uživatelským účtem místní služby Active Directory se synchronizuje s účtem služby Azure AD pomocí služby Azure AD Connect. Chci změnit heslo, jste zapomněli heslo nebo byl uzamčen.
      * Chci změnit heslo, nebo ho resetovat do něco vědět nebo odemknout svůj účet **a** jste, že změny synchronizovat zpět do místní služby Active Directory.
   * Tato funkce je zahrnutá v Azure AD Premium P1 nebo P2 nebo Microsoft 365 Business.

> [!WARNING]
> Office 365 samostatné licenční plány *nepodporují "Samoobslužné heslo resetování/změna/odemknutí přes místní zpětný zápis"* a vyžadovat, která zahrnuje Microsoft 365 Business, Azure AD Premium P1 a Premium P2 pro tento plán funkce pro práci.
>

Další licenční informace, včetně nákladů, najdete na následujících stránkách:

* [Azure Active Directory, ceny za Web](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funkce a možnosti](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Popis služby Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Povolit skupiny nebo licencování na základě uživatele

Azure AD teď podporuje licencování na základě skupiny. Správci mohou přiřadit hromadných licencí ke skupině uživatelů, a ne přiřazení postupně po jednom. Další informace najdete v tématu [přiřadit zkontrolujte a vyřešte problémy s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Některé služby Microsoft nejsou dostupné ve všech umístěních. Předtím, než je možné přiřadit licence pro uživatele, musí správce zadat **místo využívání** vlastnost na uživatele. Přiřazení licencí můžete udělat v rámci **uživatele** > **profilu** > **nastavení** části webu Azure Portal. *Při použití přiřazení licence skupině dědí všechny uživatele bez zadaného místa využití umístění adresáře.*

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
