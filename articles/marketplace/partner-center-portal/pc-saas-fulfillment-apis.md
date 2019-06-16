---
title: Rozhraní API pro SaaS splnění | Azure Marketplace
description: Zavádí verze plnění, které nabízí rozhraní API, která vám umožní integrovat vaše SaaS pomocí webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 7896ed77d9dbb3358ddb1c809ca342828280f66a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258921"
---
# <a name="saas-fulfillment-apis"></a>Rozhraní API naplnění SaaS

Rozhraní API pro SaaS splnění povolit nezávislí výrobci softwaru (ISV) pro integraci s Azure Marketplace své aplikace SaaS. Rozhraní API umožňují aplikace nezávislých výrobců softwaru se účastnit všech kanálů commerce povoleno: s přímým přístupem, vedené partnerem (prodejci) a vedla pole.  Jedná se o požadavek na výpis transactable nabídky SaaS na Azure Marketplace.

> [!WARNING]
> Aktuální verze tohoto rozhraní API je verze 2, která se má použít pro všechny nové nabídky SaaS.  Verze 1 z rozhraní API je zastaralá a je udržována pro podporu existující nabídky.


## <a name="business-model-support"></a>Podpora obchodního modelu

Toto rozhraní API podporuje následující funkce obchodního modelu; Můžeš:

* Zadejte více plány pro nabídku. Tyto plány mají různé funkce a může být cenově liší.
* Zadejte nabídku na jednotlivé lokality nebo na základě modelu fakturace uživatele.
* Poskytují měsíční a roční (zaplacené předem) možností fakturace.
* Zadejte privátní ceny zákazníkovi na základě smlouvy vyjednávaný firmy.


## <a name="next-steps"></a>Další postup

Pokud jste tak již neučinili, zaregistrujte si vaši aplikaci SaaS v [webu Azure portal](https://ms.portal.azure.com) jak je vysvětleno v [registrace aplikace Azure AD](./pc-saas-registration.md).  Později použijte nejnovější verzi tohoto rozhraní pro vývoj: [Verze rozhraní API SaaS splnění 2](./pc-saas-fulfillment-api-v2.md).
