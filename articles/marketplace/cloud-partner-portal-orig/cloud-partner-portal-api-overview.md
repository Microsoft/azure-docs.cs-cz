---
title: Reference k rozhraní API portálu partnerů cloudu | Dokumentace Microsoftu
description: Popis, požadavky na použití a seznam operací marketplace rozhraní API.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 116eb48330381c7560c55ea9535b3c1b7c6a6a70
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809314"
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
-   [Načíst stav nabídky](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Vytvoření nabídky](./cloud-partner-portal-api-creating-offer.md)
-   [Publikování nabídky](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operace

-   [Načtení operací](./cloud-partner-portal-api-retrieve-operations.md)
-   [Zrušení operací](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publikování aplikace

-   [Přechod na živý přenos](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Další úlohy

-   [Nastavení cenové nabídky se virtuální počítač](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Řešení potíží

-   [Řešení chyb ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
