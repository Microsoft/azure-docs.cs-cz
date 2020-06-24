---
title: Zabezpečený hybridní přístup k Azure AD | Microsoft Docs
description: Tento článek popisuje Partnerská řešení pro integraci vaší staré aplikace v místním, veřejném cloudu nebo privátního cloudu s Azure AD. Zabezpečte své starší aplikace připojením řadičů pro doručování aplikací nebo sítí k Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 12/18/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295891afbb0136e0b05bcd49f4045e0e8bcff6e5
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763036"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>Zabezpečený hybridní přístup: zabezpečení starších verzí aplikací pomocí řadičů pro doručování aplikací a sítí

Teď můžete chránit svoje místní a cloudové aplikace ověřování pomocí připojení k Azure AD s existujícím řadičem pro doručování aplikací nebo sítí. Tímto způsobem můžete tuto mezeru přemostění a posílit stav zabezpečení napříč všemi aplikacemi pomocí možností Azure AD, jako je Azure AD podmíněný přístup a Azure AD Identity Protection.

Pomocí stávajícího síťového adaptéru a řadiče pro doručování můžete snadno chránit starší verze aplikací, které jsou pro vaše obchodní procesy pořád důležité, ale nemůžete je chránit před službou Azure AD. Je možné, že už máte všechno, co potřebujete, abyste mohli začít chránit tyto aplikace.

![Obrázek ukazující zabezpečený hybridní přístup](media/secure-hybrid-access/secure-hybrid-access.png)

Následující dodavatelé nabízejí předem vytvořená řešení a podrobné pokyny pro integraci se službou Azure AD.

* [Přístup k podnikovým aplikacím Akamai (EAA)](../saas-apps/akamai-tutorial.md)
* [Řadič pro doručování aplikací Citrix (ADC)](../saas-apps/citrix-netscaler-tutorial.md)
* [S funkcí F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)
* [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
