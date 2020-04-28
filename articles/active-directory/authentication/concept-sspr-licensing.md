---
title: Samoobslužné resetování hesla k licenci – Azure Active Directory
description: Přečtěte si o rozdílech Azure Active Directory licenčních požadavcích na Samoobslužné resetování hesla
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393064"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licenční požadavky pro Azure Active Directory Samoobslužné resetování hesla

Pokud se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci, aby se snížila pravděpodobnost volání helpdesku a ztráty produktivity, můžou být pro Samoobslužné resetování hesla (SSPR) povolené uživatelské účty v Azure Active Directory (Azure AD). K funkcím, které tvoří SSPR, patří změna hesla, resetování, odemčení a zpětný zápis do místního adresáře. Základní funkce SSPR jsou k dispozici pro Office 365 a všechny uživatele Azure AD bez jakýchkoli nákladů.

Tento článek podrobně popisuje různé způsoby, kterými se dá Samoobslužné resetování hesla licencovat a používat. Konkrétní podrobnosti o cenách a fakturaci najdete na [stránce s cenami služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porovnání edicí a funkcí

SSPR má licenci na uživatele. Aby bylo možné zajistit dodržování předpisů, je nutné, aby organizace přiřadily příslušné licence svým uživatelům.

Následující tabulka popisuje různé scénáře SSPR pro změnu hesla, resetování nebo místní zpětný zápis a které skladové položky tuto funkci poskytují.

| Funkce | Azure AD Free | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 nebo P2 |
| --- |:---:|:---:|:---:|:---:|
| **Změna hesla jenom pro Cloud uživatele**<br />Když uživatel ve službě Azure AD zná heslo a chce ho změnit na něco nového. | ● | ● | ● | ● |
| **Resetování hesla uživatele jenom pro Cloud**<br />Když uživatel v Azure AD zapomněl heslo a potřebuje ho resetovat. | | ● | ● | ● |
| **Změna nebo resetování hesla hybridního uživatele pomocí zpětného zápisu Prem**<br />Když uživatel v Azure AD, který je synchronizovaný z místního adresáře pomocí Azure AD Connect, chce změnit nebo resetovat heslo a zapsat nové heslo zpátky do Prem. | | | ● | ● |

> [!WARNING]
> Samostatné plány licencování Office 365 nepodporují SSPR s místním zpětným zápisem. Tyto plány licencování Office 365 vyžadují Azure AD Premium P1, Premium P2 nebo Microsoft 365 Business, aby tato funkce fungovala.

Další informace o licencování, včetně nákladů, najdete na následujících stránkách:

* [Ceny Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funkce a možnosti](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Povolení licencování skupin nebo uživatelů

Azure AD podporuje licencování na základě skupin. Správci můžou hromadně přiřazovat licence skupině uživatelů, ale nepřiřazovat je po jednom. Další informace najdete v tématu [přiřazení, ověření a řešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Některé služby společnosti Microsoft nejsou k dispozici ve všech umístěních. Před přiřazením licence uživateli musí správce zadat pro uživatele vlastnost **umístění používání** . Přiřazení licencí se dá udělat v části**Nastavení** **profilu** >  **uživatele** > v Azure Portal. *Když použijete přiřazení skupinové licence, všichni uživatelé bez zadaného umístění pro použití zdědí umístění adresáře.*

## <a name="next-steps"></a>Další kroky

Pokud chcete začít pracovat s SSPR, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: povolení samoobslužného resetování hesla (SSPR)](tutorial-enable-sspr.md)
