---
title: Rozhraní API pro SaaS splnění - Azure Marketplace | Dokumentace Microsoftu
description: Zavádí verze plnění, které nabízí rozhraní API, která vám umožní integrovat vaše SaaS pomocí webu Azure Marketplace.
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
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798723"
---
# <a name="saas-fulfillment-apis"></a>Rozhraní API naplnění SaaS

Rozhraní API pro SaaS splnění povolit nezávislí výrobci softwaru (ISV) pro integraci s Azure Marketplace své aplikace SaaS. Rozhraní API umožňují aplikace nezávislých výrobců softwaru se účastnit všech kanálů commerce povoleno: s přímým přístupem, vedené partnerem (prodejci) a vedla pole.  Jedná se o požadavek na výpis transactable nabídky SaaS na Azure Marketplace.

> [!WARNING]
> Aktuální verze tohoto rozhraní API je verze 2, který má být použit pro všechny nové SaaS nabízí.  Verze 1 z rozhraní API je zastaralá a je udržována pro podporu existující nabídky.


## <a name="business-model-support"></a>Podpora obchodního modelu

Toto rozhraní API podporuje následující funkce obchodního modelu; Můžeš:

* Zadejte více plány pro nabídku. Tyto plány mají různé funkce a může být cenově liší.
* Zadejte nabídku na jednotlivé lokality nebo na základě modelu fakturace uživatele.
* Poskytují měsíční a roční (zaplacené předem) možností fakturace.
* Zadejte privátní ceny zákazníkovi na základě smlouvy vyjednávaný firmy.


## <a name="next-steps"></a>Další postup

Pokud jste tak již neučinili, zaregistrujte si vaši aplikaci SaaS v [webu Azure portal](https://ms.portal.azure.com) jak je vysvětleno v [registrace aplikace Azure AD](./cpp-saas-registration.md).  Později použijte nejnovější verzi tohoto rozhraní pro vývoj: [Verze rozhraní API SaaS splnění 2](./cpp-saas-fulfillment-api-v2.md).
