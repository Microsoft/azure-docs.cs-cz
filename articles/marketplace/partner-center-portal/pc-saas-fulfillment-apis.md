---
title: Rozhraní API pro plnění SaaS na komerčním webu Microsoft Marketplace
description: Úvod k rozhraním API pro splnění, které vám umožní integrovat nabídky SaaS do Microsoft AppSource a Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858087"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>Rozhraní API pro plnění SaaS na komerčním webu Microsoft Marketplace

Rozhraní API pro plnění SaaS umožňují nezávislým dodavatelům softwaru integrovat své aplikace SaaS do Microsoft AppSource a Azure Marketplace. Tato rozhraní API umožňují aplikacím ISV zapojit se do všech kanálů s povoleným obchodováním: přímé, partner – LED (prodejce) a indikátor LED v poli. Jsou vyžadovány k vypsání SaaS nabídek v režimu s podporou transakcí v Microsoft AppSource a Azure Marketplace.

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
