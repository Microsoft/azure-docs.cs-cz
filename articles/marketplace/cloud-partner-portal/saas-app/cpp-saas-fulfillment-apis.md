---
title: Rozhraní API pro SaaS splnění | Azure Marketplace
description: Zavádí verze plnění, které nabízí rozhraní API, která vám umožní integrovat vaše SaaS pomocí webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pabutler
ms.openlocfilehash: e7a01af1eba865b0a088b0fa7226273527abd70e
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257409"
---
# <a name="saas-fulfillment-apis"></a>Rozhraní API naplnění SaaS

> [!IMPORTANT] 
> SaaS nabízejí funkce se migroval na [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Všechny nové zdroje musí pomocí partnerského centra pro vytvoření nové nabídky SaaS a správu existující nabídky.  Aktuální vydavatele se nabídky SaaS se batchwise migrují z portál partnerů cloudu do partnerského centra.  Portál partnerů cloudu se zobrazí stavové zprávy k označení, když se konkrétní stávající nabídky migrovaly.
> Další informace najdete v tématu [vytvoření nové nabídky SaaS](../../partner-center-portal/create-new-saas-offer.md).

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
