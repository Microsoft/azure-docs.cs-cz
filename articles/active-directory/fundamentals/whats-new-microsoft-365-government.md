---
title: Co je nového pro Azure AD v Microsoft 365 státní správy? | Microsoft Docs
description: Přečtěte si o některých změnách Azure Active Directory (Azure AD) v instanci cloudu Microsoft 365 státní správy, což vám může ovlivnit.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc6a478bebef0733847ef4744b722ed9e61a6c7f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367944"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Novinky Azure Active Directory v Microsoft 365 státní správě

Provedli jsme některé změny Azure Active Directory (Azure AD) v instanci cloudu Microsoft 365 státní správy, která se vztahuje na zákazníky, kteří používají tyto služby:

- Microsoft Azure Government

- Microsoft 365 státní správy – velká a rychlé v oblasti RSZ

- Microsoft 365 státní správy – DoD

Tento článek se nevztahuje na Microsoft 365 vládní organizace – RSZ pro zákazníky.

## <a name="changes-to-the-initial-domain-name"></a>Změny počátečního názvu domény

Při počáteční registraci vaší organizace pro online službu Microsoft 365 státní správy jste požádáni o výběr názvu domény vaší organizace, `<your-domain-name>.onmicrosoft.com`. Pokud už máte název domény s příponou. com, nic se nezmění.

Pokud si ale zaregistrujete novou Microsoft 365ovou službu pro státní správu, budete požádáni o výběr názvu domény pomocí `.us` přípony. Bude to `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Tato změna se nevztahuje na žádné zákazníky, kteří spravují Poskytovatelé cloudových služeb (CSP).

## <a name="changes-to-portal-access"></a>Změny přístupu na portál

Aktualizovali jsme koncové body portálu pro Microsoft Azure Government, Microsoft 365 vládou – RSZ a Microsoft 365 vládou – DoD, jak je znázorněno v [tabulce mapování koncových bodů](#endpoint-mapping).

Dřív se zákazníci mohli přihlásit pomocí celosvětových portálů Azure (portal.azure.com) a Office 365 (portal.office.com). V této aktualizaci se zákazníci teď musí přihlašovat pomocí konkrétních Microsoft Azure Government, Microsoft 365 vládních úřadů pro státní správu a Microsoft 365.

## <a name="endpoint-mapping"></a>Mapování koncových bodů

V následující tabulce jsou uvedeny koncové body pro všechny zákazníky:

| Název | Podrobnosti koncového bodu |
|------|------------------|
| Portály |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365ská vláda – velká a rychlé v oblasti RSZ: https://portal.office365.us<p>Microsoft 365 státní správa – DoD: https://portal.apps.mil |
| Koncový bod Azure Active Directory autority | https://login.microsoftonline.us |
| Rozhraní API pro Microsoft Graph pro Microsoft 365 státní správu – RSZ – vysoká | https://graph.microsoft.us |
| Microsoft Graph API pro Microsoft 365 vládu – DoD | https://dod-graph.microsoft.us |
| Koncové body služby Azure Government Services | Podrobnosti najdete v tématu [Azure Government příručka pro vývojáře](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) . |
| Microsoft 365 státní správy – horní koncové body pro RSZ | Podrobnosti najdete v části [Office 365 USA – vysoké koncové body pro RSZ](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) . |
| Microsoft 365 státní správy – DoD | Podrobnosti najdete v tématu [koncové body DoD pro státní správu USA pro Office 365](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) . |

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích:

- [Co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Aktualizace koncového bodu autority AAD Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Koncové body Microsoft Graph v cloudu pro státní správu USA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 USA úrovně RSZ a DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)