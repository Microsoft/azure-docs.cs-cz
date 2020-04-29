---
title: Aktualizace stávající nabídky kontejnerů Azure | Azure Marketplace
description: Jak aktualizovat existující nabídku kontejneru na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: cc378dcc1d5f777f4e81825e2f99dedd37a87f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143818"
---
# <a name="update-an-existing-container-offer"></a>Aktualizovat existující nabídku kontejneru

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek Azure Container do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v části [vytvoření kontejneru Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) pro správu migrovaných nabídek.

Tento článek popisuje různé aspekty aktualizace nabídky kontejneru v [portál partnerů cloudu](https://cloudpartner.azure.com/).

K dispozici je několik důvodů, proč byste mohli chtít aktualizovat nabídku, například:

-  Přidání nové verze image kontejneru do existujících SKU.
-  Přidávání nových SKU.
-  Aktualizují se metadata Marketplace pro nabídku nebo jednotlivé SKU.

Pro pomoc s těmito úpravami nabízí portál funkce **porovnání** a **Historie** .  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Nepovolené změny nabídky kontejneru nebo SKU

Existují atributy nabídky kontejneru nebo SKU, které nelze změnit poté, co je nabídka v Azure Marketplace živá. Nemůžete změnit následující nastavení:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **ID SKU** existujících SKU
-  Značky verze, například:`1.0.1`
-  Fakturační a licenční model se mění ve stávajících SKU

## <a name="common-update-operations"></a>Běžné operace aktualizace

Následující operace aktualizace jsou běžné.

### <a name="update-container-image-version-for-a-sku"></a>Aktualizace verze image kontejneru pro SKU

Je běžné, že se má image kontejneru pravidelně aktualizovat pomocí oprav zabezpečení, dalších funkcí a tak dále. V tomto scénáři chcete aktualizovat image kontejneru, kterou vaše SKU odkazuje, pomocí následujících kroků:

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **SKU** vyberte SKU přidruženou k imagi kontejneru, která se má aktualizovat.
4. V části **Image kontejneru**vyberte **+ Nová verze image** a přidejte novou image kontejneru.
5. Zadejte nové **verze imagí**kontejneru. Verze image musí splňovat stejné pokyny pro značky jako v předchozích verzích. Značky verze by měly mít formát X. Y. Z, kde X, Y a Z jsou celá čísla. Ověřte, že nová verze, kterou zadáte, je větší než všechny předchozí verze.
6. Vyberte **publikovat** a spusťte pracovní postup, který publikuje novou verzi image kontejneru do Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidat novou SKLADOVOU položku

K dispozici je nová SKU pro vaši nabídku pomocí následujících kroků:

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **SKU** vyberte **Přidat novou SKU** a v automaticky otevíraném okně zadejte **ID SKU** .
4. Znovu publikujte kontejner pomocí postupu popsaného v tématu [publikování nabídky kontejneru](./cpp-publish-offer.md).
5. Vyberte **publikovat** a spusťte pracovní postup pro publikování nové SKU.

### <a name="update-offer-marketplace-metadata"></a>Aktualizace metadat webu nabídky

Pomocí následujících kroků aktualizujte metadata Marketplace přidružená k vaší nabídce. (Například: název společnosti, loga atd.)

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.
3. Přejít na kartu **Marketplace** . pomocí pokynů v článku [publikování nabídky publikovat kontejner](./cpp-publish-offer.md) můžete provádět změny metadat.
4. Vyberte **publikovat** a spusťte pracovní postup pro publikování změn.

## <a name="compare-feature"></a>Compare – funkce

Když provedete změny v publikované nabídce, můžete pomocí funkce **Compare** auditovat změny, které jste provedli.

### <a name="to-use-the-compare-feature"></a>Použití funkce Compare:

1. V jakémkoli okamžiku v procesu úprav vyberte porovnat pro vaši nabídku.
2. Podívejte se na souběžné verze materiálů a metadat marketingu.


## <a name="history-of-publishing-actions"></a>Historie akcí publikování

Chcete-li zobrazit historické aktivity publikování, vyberte kartu **Historie** v levém navigačním panelu portál partnerů cloudu. Můžete zobrazit akce s časovým razítkem provedenými během životnosti Azure Marketplace nabídek.
