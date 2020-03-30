---
title: Api plnění SaaS | Azure Marketplace
description: Zavádí verze řešení API pro plnění, které vám umožní integrovat nabídky SaaS s Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275726"
---
# <a name="saas-fulfillment-apis"></a>Rozhraní API naplnění SaaS

Api pro plnění SaaS umožňují nezávislým dodavatelům softwaru (ISV) integrovat své aplikace SaaS s Azure Marketplace. Tato rozhraní API umožňují aplikacím isv účastnit se všech kanálů s podporou obchodu: přímých, partnerských (prodejců) a vedených v terénu.  Jsou požadavkem pro výpis obchodovatelných nabídek SaaS na Azure Marketplace.

> [!WARNING]
> Aktuální verze tohoto rozhraní API je verze 2, která by měla být použita pro všechny nové nabídky SaaS.  Verze 1 rozhraní API je zastaralé a je udržována pro podporu existujících nabídek.


## <a name="business-model-support"></a>Podpora obchodního modelu

Toto rozhraní API podporuje následující funkce obchodního modelu; Můžeš:

* Zadejte více plánů pro nabídku. Tyto plány mají různé funkce a mohou být oceněny odlišně.
* Poskytněte nabídku na základě fakturačního modelu pro jednotlivé stránky nebo pro jednotlivé uživatele.
* Poskytněte možnosti měsíční a roční (placené předem) fakturace.
* Poskytněte zákazníkovi soukromé ceny na základě sjednané obchodní smlouvy.


## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili, zaregistrujte svou aplikaci SaaS na [webu Azure Portal,](https://ms.portal.azure.com) jak je vysvětleno v [části Registrace aplikace Azure AD](./pc-saas-registration.md).  Poté použijte nejaktuálnější verzi tohoto rozhraní pro vývoj: [SaaS Fulfillment API verze 2](./pc-saas-fulfillment-api-v2.md).
