---
title: Sestavování odolné správy identit a přístupu pomocí Azure Active Directory
description: Průvodce architekty, správci IT a vývojáři, kteří zajišťují odolnost proti výpadkům systémů identit.
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
ms.openlocfilehash: 7dfd51b0ed43badbc6a4882f619cb718952b0e85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919417"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Sestavování odolnosti při správě identit a přístupu pomocí Azure Active Directory

Správa identit a přístupu (IAM) je architektura procesů, zásad a technologií, které usnadňují správu identit a k čemu mají přístup. Zahrnuje mnoho komponent, které podporují ověřování a autorizaci uživatelských a dalších účtů v systému.

Odolnost systému IAM je schopnost dlouhodobě rušit narušování systémových komponent a obnovení s minimálním dopadem na vaše podnikání, uživatele, zákazníky a operace. Snížení závislostí, složitosti a jednoduchých bodů selhání a zajištění komplexního zpracování chyb zvýší vaši odolnost.

Přerušení může pocházet z jakékoli součásti systému IAM. Chcete-li vytvořit odolný systém IAM, dojde k přerušení a naplánujte ho. 

Při plánování odolnosti vašeho řešení IAM Vezměte v úvahu tyto prvky: 

* Vaše aplikace, které spoléhají na váš systém IAM.

* Veřejné infrastruktury vaše ověřování volá, včetně telekomunikačních společností, poskytovatelů internetových služeb a poskytovatelů veřejných klíčů.

* Vaše cloudová a místní zprostředkovatelé identity.

* Další služby, které spoléhají na vaše IAM, a rozhraní API, která je spojují.

* Všechny ostatní místní součásti systému.

Bez ohledu na to, jaký zdroj, rozpozná a naplánování pro nepředvídané události, je důležité. Přidání dalších systémů identit a jejich výsledných závislostí a složitosti ale může snížit vaši odolnost, ale nezvyšovat ji.

Pokud chcete v systémech vytvořit větší odolnost, přečtěte si následující články:

* [Odolnost sestavení v infrastruktuře IAM](resilience-in-infrastructure.md)

* [Sestavování odolnosti IAM ve vašich aplikacích](resilience-app-development-overview.md)

* [Odolnost sestavení v systémech CIAM](resilience-b2c.md)
