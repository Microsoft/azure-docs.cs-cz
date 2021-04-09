---
title: Zajištění odolnosti sestavení v infrastruktuře IAM pomocí Azure Active Directory
description: Příručka pro architekty a správce IT, kteří nevytvářejí odolnost proti výpadkům své infrastruktury IAM.
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
ms.openlocfilehash: 64fe4b8c217ec46cbb6dd046339c3ac65eebb121
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724673"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Odolnost sestavení v infrastruktuře pro správu identit a přístupu

Azure Active Directory je globální cloudový systém pro správu identit a přístupu, který poskytuje důležité služby, jako je například ověřování a autorizace prostředků vaší organizace. Tento dokument obsahuje pokyny pro pochopení, zahrnutí a zmírnění rizika narušení ověřování nebo autorizačních služeb pro prostředky, které spoléhají na Azure Active Directory (Azure AD). 

Sada dokumentů je navržena pro

* Architekti identity

* Vlastníci služby identity

* Týmy pro provoz identity

Podívejte se také na dokumentaci pro [vývojáře aplikací](./resilience-app-development-overview.md) a pro [Azure AD B2C systémy](resilience-b2c.md).

## <a name="what-is-resilience"></a>Co je odolnost proti chybám?

V souvislosti s infrastrukturou identity je odolnost schopnost dlouhodobě rušit narušování služeb, jako je ověřování a autorizace, nebo selhání dalších komponent, s minimálním nebo žádným dopadem na vaše podnikání, uživatele a operace. Dopad přerušení může být vážný a odolnost vyžaduje plánování pečlivé vylaďování.

## <a name="why-worry-about-disruption"></a>Proč se obávat přerušení?

Každé volání systému ověřování je ovlivněno přerušením, pokud se nějaká komponenta volání nezdařila. Pokud dojde k narušení ověřování z důvodu selhání základních součástí, nebudou uživatelé přistupovat ke svým aplikacím. Proto je omezení počtu volání ověřování a počtu závislostí v těchto voláních důležité pro vaši odolnost. Vývojáři aplikací mohou vyhodnotit určitou kontrolu nad tím, jak často jsou tokeny požadovány. Můžete například spolupracovat s vývojáři, abyste zajistili, že používají identity spravované službou Azure AD pro své aplikace bez ohledu na to, kde je to možné. 

V ověřovacím systému založeném na tokenech, jako je Azure AD, musí aplikace uživatele (klient) získat token zabezpečení ze systému identity, aby mohl získat přístup k aplikaci nebo jinému prostředku. Během období platnosti může klient při přístupu k aplikaci použít stejný token několikrát.

Po vypršení platnosti tokenu, který aplikaci prezentuje, aplikace odmítne token a klient musí získat nový token z Azure AD. Získání nového tokenu potenciálně vyžaduje zásah uživatele, například výzvy k zadání přihlašovacích údajů nebo splnění jiných požadavků na ověřovací systém. Omezení frekvence volání ověřování s využitím delších tokenů snižuje nepotřebnou interakci. Je však nutné vyvážit životnost tokenu s rizikem vytvořeným menším počtem vyhodnocení zásad. Další informace o správě životností tokenů najdete v tomto článku o [optimalizaci výzev k opakovanému ověření](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="ways-to-increase-resilience"></a>Způsoby, jak zvýšit odolnost
Následující diagram znázorňuje šest konkrétních způsobů, kterými můžete zvýšit odolnost. Jednotlivé metody jsou podrobně vysvětleny v článcích, které jsou propojeny v dalších krocích tohoto článku.
  
![Diagram znázorňující přehled odolnosti správce](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Další kroky
Prostředky odolnosti pro správce a architekty
 
* [Odolnost sestavení se správou přihlašovacích údajů](resilience-in-credentials.md)

* [Odolnost sestavení se stavy zařízení](resilience-with-device-states.md)

* [Odolnost sestavení pomocí vyhodnocení průběžného přístupu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Odolnost sestavení při ověřování externích uživatelů](resilience-b2b-authentication.md)

* [Odolnost sestavení v hybridním ověřování](resilience-in-hybrid.md)

* [Odolnost sestavení v přístupu aplikace s proxy aplikací](resilience-on-premises-access.md)

Prostředky odolnosti pro vývojáře

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)