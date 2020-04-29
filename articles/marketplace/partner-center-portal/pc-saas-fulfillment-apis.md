---
title: Rozhraní API pro plnění SaaS | Azure Marketplace
description: Zavádí verze rozhraní API pro plnění, které vám umožní integrovat nabídky SaaS s Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275726"
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
