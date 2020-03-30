---
title: Odkaz na rozhraní API portálu cloudových partnerů | Azure Marketplace
description: Popis, předpoklady pro použití a seznam operací rozhraní API marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288542"
---
<a name="cloud-partner-portal-api-reference"></a>Odkaz na rozhraní API portálu cloudových partnerů
==================================

Api REST portálu cloudových partnerů umožňují programové načítání a manipulaci s úlohami, nabídkami a profily vydavatelů. Api používají řízení přístupu na základě rolí (RBAC) k vynucení správných oprávnění v době zpracování.

Tento odkaz poskytuje technické podrobnosti pro cloudpartner portál u rest API. Ukázky datové části v tomto dokumentu jsou pouze orientační a mohou se změnit při přidání nových funkcí.


<a name="prerequisites-and-considerations"></a>Předpoklady a úvahy
-------------------------------

Před použitím api byste měli zkontrolovat:

- Požadavky článku se [dozvíte,](./cloud-partner-portal-api-prerequisites.md) jak přidat instanční objekt do vašeho účtu a získat přístupový token služby Azure Active Directory (Azure AD) pro ověřování. 
- Dvě [souběžnosti řízení](./cloud-partner-portal-api-concurrency-control.md).
strategie, které jsou k dispozici pro volání těchto rozhraní API.
- Další [aspekty](./cloud-partner-portal-api-considerations.md)rozhraní API , jako je například správa verzí a zpracování chyb.


<a name="common-tasks"></a>Běžné úkoly
------------
Tento odkaz podrobnosti API provádět následující běžné úkoly.


### <a name="offers"></a>Nabídky

-   [Načíst všechny nabídky](./cloud-partner-portal-api-retrieve-offers.md)
-   [Načtení konkrétní nabídky](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Načtení stavu nabídky](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Vytvoření nabídky](./cloud-partner-portal-api-creating-offer.md)
-   [Publikování nabídky](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Provoz

-   [Načtení operací](./cloud-partner-portal-api-retrieve-operations.md)
-   [Zrušení operací](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikování aplikace

-   [Zveřejnění](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Další úlohy

-   [Nastavení cen pro nabídky virtuálních strojů](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Řešení potíží

-   [Řešení potíží s chybami ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
