---
title: Reference k rozhraní API portálu partnerů cloudu | Azure Marketplace
description: Popis, požadavky na použití a seznam operací marketplace rozhraní API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 9626fdb5dbdf720a65217421565fc559a0cee684
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935474"
---
<a name="cloud-partner-portal-api-reference"></a>Reference k portálu rozhraní API cloud Partner
==================================

Rozhraní REST API portál partnerů cloudu povolit programový načítání a manipulaci s úlohami, nabídky a profily vydavatele. Rozhraní API pomocí řízení přístupu na základě role (RBAC) k vynucení správná oprávnění v době zpracování.

Tento odkaz obsahuje podrobné technické informace k rozhraním REST API portál partnerů cloudu. Ukázky datové části v tomto dokumentu slouží jenom jako reference a při přidání nové funkce se můžou změnit.


<a name="prerequisites-and-considerations"></a>Požadavky a důležité informace
-------------------------------

Před použitím rozhraní API, byste měli zkontrolovat:

- [Požadavky](./cloud-partner-portal-api-prerequisites.md) článku se naučíte ke svému účtu přidat hlavní název služby a získání tokenu přístupu Azure Active Directory (Azure AD) pro ověřování. 
- Dva [řízení souběžnosti](./cloud-partner-portal-api-concurrency-control.md).
strategie k dispozici k volání těchto rozhraní API.
- Další rozhraní API [aspekty](./cloud-partner-portal-api-considerations.md), jako je například Správa verzí a zpracování chyb.


<a name="common-tasks"></a>Běžné úkoly
------------
Tento odkaz obsahuje podrobnosti o rozhraní API k provádění následujících běžných úloh.


### <a name="offers"></a>Nabídky

-   [Načíst všechny nabídky](./cloud-partner-portal-api-retrieve-offers.md)
-   [Načtení konkrétní nabídky](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Načtení stavu nabídky](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Vytvoření nabídky](./cloud-partner-portal-api-creating-offer.md)
-   [Publikování nabídky](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operace

-   [Načtení operací](./cloud-partner-portal-api-retrieve-operations.md)
-   [Zrušení operací](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikování aplikace

-   [Zveřejnění](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Další úlohy

-   [Nastavení cenové nabídky se virtuální počítač](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Řešení potíží

-   [Řešení potíží s chybami ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
