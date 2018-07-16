---
title: Licence samoobslužné resetování hesla – Azure Active Directory
description: Azure AD samoobslužné resetování hesla licenčním požadavkům
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e185b67ae73b86b5f1c3b6cda884de05eb89c6fd
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049080"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Resetovat licenčních požadavcích pro hesla pomocí samoobslužné služby Azure AD

V pořadí pro funkci resetování hesel v Azure Active Directory (Azure AD) můžete *musí mít v organizaci přiřazenu alespoň jednu licenci* pro tohoto uživatele. Řádná licence se požaduje, pokud uživatel přímo nebo nepřímo využívá výhod libovolné funkce, na kterou se tato licence vztahuje.

* **Uživatelů pouze cloudu**: Office 365 placených SKU nebo Azure AD Basic
* **Cloud** nebo **místních uživatelů**: Azure AD Premium P1 nebo P2, Enterprise Mobility + Security (EMS) nebo Microsoft 365

## <a name="licensing-requirements-for-password-writeback"></a>Licenční požadavky pro zpětný zápis hesla

**Samoobslužné služby heslo resetovat/změny/odemknutí přes místní zpětný zápis je Prémiová funkce služby Azure AD**. Další informace o licencích najdete v článku [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

Pokud chcete použít zpětný zápis hesla, musí mít jeden z přiřazené ve svém tenantovi následující licence:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 nebo A3
* Enterprise Mobility + Security E5 nebo A5
* Microsoft 365 E3 nebo A3
* Microsoft 365 E5 nebo A5
* Microsoft 365 F1

> [!WARNING]
> Office 365 samostatné licenční plány *nepodporují zpětný zápis hesla* a vyžadují, abyste měli jeden z předchozích plánů pro tuto funkci pracovat.
>

Další licenční informace, včetně nákladů, najdete na následujících stránkách:

* [Azure Active Directory, ceny za Web](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funkce a možnosti](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

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
