---
title: Rozhraní API pro plnění SaaS | Azure Marketplace
description: Zavádí verze rozhraní API pro plnění, které vám umožní integrovat nabídky SaaS s Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: ebfc278d09c244970df5807df1505295fe7016c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819122"
---
# <a name="saas-fulfillment-apis"></a>Rozhraní API naplnění SaaS

Rozhraní API pro plnění SaaS umožňují nezávislým dodavatelům softwaru integrovat své aplikace SaaS s Azure Marketplace. Tato rozhraní API umožňují aplikacím ISV zapojit se do všech kanálů s povoleným obchodováním: přímé, partner – LED (prodejce) a indikátor LED pro pole.  Jsou vyžadovány pro výpis SaaSch nabídek s transakčními příkazy na Azure Marketplace.

> [!WARNING]
> Aktuální verze tohoto rozhraní API je verze 2, která by se měla používat pro všechny nové nabídky SaaS.  Verze 1 rozhraní API je zastaralá a udržuje se tak, aby podporovala stávající nabídky.


## <a name="business-model-support"></a>Podpora obchodního modelu

Toto rozhraní API podporuje následující funkce obchodního modelu; Můžeš:

* Zadejte pro nabídku více plánů. Tyto plány mají různé funkce a můžou být cenově odlišné.
* Poskytněte nabídku pro jednotlivé weby nebo model fakturace podle uživatele.
* Poskytněte možnosti fakturace na měsíc a roční (placené předem).
* Poskytněte zákazníkům soukromé ceny na základě sjednané obchodní smlouvy.


## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, zaregistrujte svou aplikaci SaaS do [Azure Portal](https://ms.portal.azure.com) , jak je vysvětleno v tématu [Registrace aplikace Azure AD](./pc-saas-registration.md).  Potom pro vývoj použijte nejaktuálnější verzi tohoto rozhraní: [SaaS API pro splnění verze 2](./pc-saas-fulfillment-api-v2.md).
