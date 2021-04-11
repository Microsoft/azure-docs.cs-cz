---
title: Samoobslužné resetování hesla k licenci – Azure Active Directory
description: Přečtěte si o rozdílech Azure Active Directory licenčních požadavcích na Samoobslužné resetování hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952367"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licenční požadavky pro Azure Active Directory Samoobslužné resetování hesla

Pokud se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci, aby se snížila pravděpodobnost volání helpdesku a ztráty produktivity, můžou být pro Samoobslužné resetování hesla (SSPR) povolené uživatelské účty v Azure Active Directory (Azure AD). K funkcím, které tvoří SSPR, patří změna hesla, resetování, odemčení a zpětný zápis do místního adresáře. Základní funkce SSPR jsou k dispozici ve Microsoft 365 Business Standard nebo vyšší a všechny Azure AD Premium SKU bez nákladů.

Tento článek podrobně popisuje různé způsoby, kterými se dá Samoobslužné resetování hesla licencovat a používat. Konkrétní podrobnosti o cenách a fakturaci najdete na [stránce s cenami služby Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porovnání edicí a funkcí

SSPR vyžaduje licenci pouze pro tenanta. 

Následující tabulka popisuje různé scénáře SSPR pro změnu hesla, resetování nebo místní zpětný zápis a které skladové položky tuto funkci poskytují.

| Funkce | Azure AD Free | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 nebo P2 |
| --- |:---:|:---:|:---:|:---:|
| **Změna hesla jenom pro Cloud uživatele**<br />Když uživatel ve službě Azure AD zná heslo a chce ho změnit na něco nového. | ● | ● | ● | ● |
| **Resetování hesla uživatele jenom pro Cloud**<br />Když uživatel v Azure AD zapomněl heslo a potřebuje ho resetovat. | | ● | ● | ● |
| **Změna nebo resetování hesla hybridního uživatele pomocí zpětného zápisu Prem**<br />Když uživatel v Azure AD, který je synchronizovaný z místního adresáře pomocí Azure AD Connect, chce změnit nebo resetovat heslo a zapsat nové heslo zpátky do Prem. | | | ● | ● |

> [!WARNING]
> Samostatné Microsoft 365 základní a standardní plány licencování nepodporují SSPR s místním zpětným zápisem. Funkce místního zpětného zápisu vyžaduje Azure AD Premium P1, Premium P2 nebo Microsoft 365 Business Premium.

Další informace o licencování, včetně nákladů, najdete na následujících stránkách:

* [Ceny Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory funkce a možnosti](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Další kroky

Pokud chcete začít pracovat s SSPR, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: povolení samoobslužného resetování hesla (SSPR)](tutorial-enable-sspr.md)