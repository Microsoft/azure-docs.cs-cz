---
title: Referenční informace k rozhraní API pro portál partnerů cloudu | Azure Marketplace
description: Popis, předpoklady k použití a seznam operací rozhraní API Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819628"
---
<a name="cloud-partner-portal-api-reference"></a>Reference k rozhraní API portál partnerů cloudu
==================================

Rozhraní REST API pro portál partnerů cloudu umožňují programové načítání a manipulaci s pracovními postupy, nabídkami a profily vydavatelů. Rozhraní API používají řízení přístupu na základě role (RBAC) k prosazování správných oprávnění v době zpracování.

Tento odkaz poskytuje technické podrobnosti o portál partnerů cloudu rozhraní REST API. Ukázky datové části v tomto dokumentu jsou jenom pro referenci a můžou se změnit, protože se přidávají nové funkce.


<a name="prerequisites-and-considerations"></a>Předpoklady a požadavky
-------------------------------

Před použitím rozhraní API byste si měli projít:

- V článku [požadavky](./cloud-partner-portal-api-prerequisites.md) se dozvíte, jak přidat instanční objekt k vašemu účtu a získat přístupový token Azure Active Directory (Azure AD) pro ověřování. 
- Druhá [Kontrola souběžnosti](./cloud-partner-portal-api-concurrency-control.md).
strategie, které jsou k dispozici pro volání těchto rozhraní API.
- Další [požadavky](./cloud-partner-portal-api-considerations.md)rozhraní API, jako je například Správa verzí a zpracování chyb.


<a name="common-tasks"></a>Běžné úkoly
------------
Tato reference odkazuje na rozhraní API, aby prováděla následující běžné úlohy.


### <a name="offers"></a>Nabídky

-   [Načíst všechny nabídky](./cloud-partner-portal-api-retrieve-offers.md)
-   [Načíst konkrétní nabídku](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Načtení stavu nabídky](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Vytvoření nabídky](./cloud-partner-portal-api-creating-offer.md)
-   [Publikování nabídky](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Provoz

-   [Načtení operací](./cloud-partner-portal-api-retrieve-operations.md)
-   [Zrušení operací](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikování aplikace

-   [Zveřejnění](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Další úlohy

-   [Nastavení cen pro nabídky virtuálních počítačů](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Řešení potíží

-   [Řešení potíží s chybami ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
