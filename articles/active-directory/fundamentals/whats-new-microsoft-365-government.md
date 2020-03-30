---
title: Co je nového pro Azure AD v Microsoftu 365 Government? | Dokumentace Microsoftu
description: Přečtěte si o některých změnách ve službě Azure Active Directory (Azure AD) v cloudové instanci Microsoft 365 Government, což by vás mohlo mít vliv.
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
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425540"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Co je nového pro Azure Active Directory v Microsoft u 365 Government

Provedli jsme některé změny ve službě Azure Active Directory (Azure AD) v cloudové instanci Microsoft 365 Government, která se vztahuje na zákazníky, kteří používají následující služby:

- Microsoft Azure Government

- Microsoft 365 Státní správa – Vysoká GCC

- Microsoft 365 Státní správa – DoD

Tento článek se nevztahuje na zákazníky Microsoft 365 Government – GCC.

## <a name="changes-to-the-initial-domain-name"></a>Změny původního názvu domény

Během počáteční registrace vaší organizace k online službě Microsoft 365 Government jste byli požádáni `<your-domain-name>.onmicrosoft.com`o výběr názvu domény vaší organizace . Pokud již máte název domény s příponou .com, nic se nezmění.

Pokud se však přihlašujete k nové službě Microsoft 365 Government, `.us` budete vyzváni k výběru názvu domény pomocí přípony. Tak, to `<your-domain-name>.onmicrosoft.us`bude .

>[!Note]
>Tato změna se nevztahuje na zákazníky, kteří jsou spravováni poskytovateli cloudových služeb (CSP).

## <a name="changes-to-portal-access"></a>Změny přístupu k portálu

Aktualizovali jsme koncové body portálu pro Microsoft Azure Government, Microsoft 365 Government – GCC High a Microsoft 365 Government – DoD, jak je znázorněno v [tabulce mapování koncového bodu](#endpoint-mapping).

Dříve se zákazníci mohli přihlásit pomocí celosvětových portálů Azure (portal.azure.com) a Office 365 (portal.office.com). Díky této aktualizaci se zákazníci teď musí přihlásit pomocí konkrétních portálů Microsoft Azure Government, Microsoft 365 Government – GCC High a Microsoft 365 Government – DoD.

## <a name="endpoint-mapping"></a>Mapování koncového bodu

V následující tabulce jsou uvedeny koncové body pro všechny zákazníky:

| Name (Název) | Podrobnosti o koncovém bodu |
|------|------------------|
| Portals |Microsoft Azure Government:https://portal.azure.us<p>Microsoft 365 Government – GCC Vysoká:https://portal.office365.us<p>Microsoft 365 Státní správa – DoD:https://portal.apps.mil |
| Koncový bod úřadu Azure Active Directory | https://login.microsoftonline.us |
| Rozhraní Microsoft Graph API pro Microsoft 365 Government – GCC High | https://graph.microsoft.us |
| Rozhraní Microsoft Graph API pro Microsoft 365 Government – DoD | https://dod-graph.microsoft.us |
| Koncové body služeb Azure Government | Podrobnosti najdete v [průvodci vývojáři Azure Government.](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Government – nejvyšší koncové body GCC | Podrobnosti najdete v [tématu Office 365 Us Government GCC High koncové body](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Státní správa – DoD | Podrobnosti najdete v tématu Koncové body dod pro [státní správu pro státní správu office 365.](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích:

- [Co je Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Aktualizace koncového bodu Azure Government AAD Authority](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Koncové body Microsoft Graphu v cloudu vlády USA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 americká vláda GCC vysoké a dod](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
