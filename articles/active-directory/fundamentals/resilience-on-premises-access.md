---
title: Odolnost sestavení v přístupu aplikace s proxy aplikací
description: Průvodce architekty a správci IT, kteří používají proxy aplikace pro odolný přístup k místním aplikacím
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8bfc3fb239f30911eddf0aa27496a465e36c486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919418"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Odolnost sestavení v přístupu aplikace s proxy aplikací

Proxy aplikací je funkce služby Azure AD, která uživatelům umožňuje přístup k místním webovým aplikacím ze vzdáleného klienta. Proxy aplikace zahrnuje službu proxy aplikací v cloudu a konektory proxy aplikací, které běží na místním serveru. 

Uživatelé přistupují k místním prostředkům prostřednictvím adresy URL publikované prostřednictvím proxy aplikací. Budou přesměrovány na přihlašovací stránku služby Azure AD. Služba proxy aplikací ve službě Azure AD pak pošle token konektoru proxy aplikací v podnikové síti, který předá token do místní služby Active Directory. ověřený uživatel pak může získat přístup k místnímu prostředku. V následujícím diagramu se [konektory](../manage-apps/application-proxy-connectors.md) zobrazují ve [skupině konektorů](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> Když publikujete aplikace prostřednictvím proxy aplikace, musíte implementovat [plánování kapacity a odpovídající redundanci pro konektory proxy aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning).

![Diagram architektury aplikace y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Návody implementovat aplikační proxy?

Postup implementace vzdáleného přístupu se službou Azure Proxy aplikací služby AD najdete v následujících zdrojích informací.

* [Plánování nasazení proxy aplikací](../manage-apps/application-proxy-deployment-plan.md)

* [Osvědčené postupy pro vysokou dostupnost a vyrovnávání zatížení](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Konfigurace proxy serverů](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Návrh odolné strategie řízení přístupu](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Další kroky
Prostředky odolnosti pro správce a architekty
 
* [Odolnost sestavení se správou přihlašovacích údajů](resilience-in-credentials.md)

* [Odolnost sestavení se stavy zařízení](resilience-with-device-states.md)

* [Odolnost sestavení pomocí vyhodnocení průběžného přístupu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Odolnost sestavení při ověřování externích uživatelů](resilience-b2b-authentication.md)

* [Odolnost sestavení v hybridním ověřování](resilience-in-hybrid.md)

Prostředky odolnosti pro vývojáře

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)
