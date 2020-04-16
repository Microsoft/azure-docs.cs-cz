---
title: Samoobslužné resetování hesla – Azure Active Directory
description: Informace o rozdílu, který je rozdílem samoobslužných licenčních požadavků služby Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393064"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licenční požadavky na samoobslužné resetování hesla služby Azure Active Directory

Chcete-li snížit volání technické podpory a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci, lze povolit uživatelské účty ve službě Azure Active Directory (Azure AD) pro samoobslužné resetování hesla (SSPR). Mezi funkce, které tvoří samoresetování hesla, obnovení, odemknutí a zpětného zápisu do místního adresáře. Základní funkce sspr jsou k dispozici pro Office 365 a všechny uživatele Azure AD zdarma.

Tento článek podrobně popisuje různé způsoby, jak lze licencovat a používat samoobslužné resetování hesla. Konkrétní podrobnosti o cenách a fakturaci najdete na [stránce s cenami Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porovnání edic a funkcí

Sspr je licencován na uživatele. Aby bylo zachováno dodržování předpisů, organizace musí přiřadit příslušnou licenci svým uživatelům.

Následující tabulka popisuje různé scénáře samoobslužného resetování hesla pro změnu hesla, obnovení nebo místní zpětný zápis a které skutové sady poskytují funkci.

| Funkce | Azure AD Free | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 nebo P2 |
| --- |:---:|:---:|:---:|:---:|
| **Změna uživatelského hesla pouze v cloudu**<br />Když uživatel ve službě Azure AD zná své heslo a chce ho změnit na něco nového. | ● | ● | ● | ● |
| **Resetování uživatelského hesla pouze v cloudu**<br />Když uživatel ve službě Azure AD zapomene své heslo a potřebuje ho obnovit. | | ● | ● | ● |
| **Hybridní uživatelská změna nebo resetování hesla pomocí zpětného zápisu**<br />Když uživatel ve službě Azure AD, který je synchronizován z místního adresáře pomocí Služby Azure AD Connect chce změnit nebo obnovit své heslo a také zapsat nové heslo zpět do on-prem. | | | ● | ● |

> [!WARNING]
> Samostatné licenční plány Office 365 nepodporují samoobslužné brány správ s místním zpětným zápisem. Tyto licenční plány Office 365 vyžadují Azure AD Premium P1, Premium P2 nebo Microsoft 365 Business, aby tato funkce fungovala.

Další informace o licencích, včetně nákladů, naleznete na následujících stránkách:

* [Ceny služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funkce a možnosti služby Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Podniková mobilita + zabezpečení](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Povolení skupinových nebo uživatelských licencí

Azure AD podporuje licencování na základě skupiny. Správci mohou hromadně přiřazovat licence skupině uživatelů, nikoli je přiřazovat po jednom. Další informace naleznete v tématu [Přiřazení, ověření a řešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Některé služby společnosti Microsoft nejsou k dispozici ve všech umístěních. Před přiřazením licence uživateli musí správce určit vlastnost **Umístění použití** uživatele. Přiřazení licencí lze provést v části**Nastavení** **profilu** >  **uživatele** > na webu Azure Portal. *Při použití přiřazení skupinové licence zdědí umístění adresáře všichni uživatelé bez zadaného umístění použití.*

## <a name="next-steps"></a>Další kroky

Chcete-li začít s programem SSPR, proveďte následující kurz:

> [!div class="nextstepaction"]
> [Kurz: Povolit samoobslužné resetování hesla (SSPR)](tutorial-enable-sspr.md)
