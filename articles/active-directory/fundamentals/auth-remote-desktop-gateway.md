---
title: Brána vzdálené plochy služby s Azure Active Directory
description: Pokyny pro architekturu při dosahování tohoto vzoru ověřování
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff099d41970ad4d78d5c6035a60f448f29ab24b1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114198"
---
# <a name="remote-desktop-gateway-services"></a>Služby Brána vzdálené plochy

Standardní nasazení vzdálené plochy (RDS) zahrnuje různé [služby role vzdálené plochy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) běžící na Windows serveru. Proxy aplikace služby RDS s Azure Active Directory (Azure AD) má trvalé odchozí připojení ze serveru, na kterém je spuštěná služba konektoru. Další nasazení ponechají otevřená příchozí připojení prostřednictvím nástroje pro vyrovnávání zatížení. Tento vzor ověřování vám umožní nabízet více typů aplikací publikováním místních aplikací prostřednictvím služby Vzdálená plocha. Zároveň se tím snižuje plocha pro útok na nasazení pomocí Azure Proxy aplikací služby AD.

## <a name="use-when"></a>Kdy použít

Musíte poskytnout vzdálený přístup a chránit nasazení služby Vzdálená plocha pomocí předběžného ověření.

![Diagram architektury](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel**: přistupuje k programu RDS Obsluhovánému proxy aplikací.

* **Webový prohlížeč**: komponenta, s jakou uživatel pracuje, aby mohla přistupovat k externí adrese URL aplikace.

* **Azure AD**: ověřuje uživatele. 

* **Služba proxy aplikací**: slouží jako reverzní proxy server k přeposílání žádosti od uživatele do služby RDS. Proxy aplikací může taky vyhovět všem zásadám podmíněného přístupu. 

* **Vzdálená plocha**: slouží jako platforma pro jednotlivé virtualizované aplikace, zajišťuje zabezpečený mobilní a vzdálená desktopový přístup a poskytuje koncovým uživatelům možnost spouštět své aplikace a plochy z cloudu. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Implementace služby Brána vzdálené plochy Services s využitím Azure AD

* [Publikování vzdálené plochy pomocí Azure Proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikací v Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
