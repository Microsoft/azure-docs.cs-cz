---
title: Aktualizace existující nabídky kontejnerů Azure | Azure Marketplace
description: Jak aktualizovat existující nabídku kontejnerů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 74f97b082c07e17a59a1615c4b1245434c497ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279942"
---
# <a name="update-an-existing-container-offer"></a>Aktualizace existující nabídky kontejneru

Tento článek prochází různé aspekty aktualizace nabídky kontejneru na [portálu cloudových partnerů](https://cloudpartner.azure.com/).

Nabídku můžete aktualizovat několika důvody, například:

-  Přidání nové verze image kontejneru do existujících sku.
-  Přidání nových skum.
-  Aktualizace metadat marketplace pro nabídku nebo jednotlivé skum.

Portál poskytuje funkce **Porovnání** a **Historie,** které vám pomohou s těmito úpravami.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Nepovolené změny nabídky kontejneru nebo skladové položky

Existují atributy nabídky kontejneru nebo skladové položky, které nelze změnit po nabídce je aktivní na Azure Marketplace. Nelze změnit následující nastavení:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **ID skladové položky** existujících skladových částek
-  Značky verzí, například:`1.0.1`
-  Změny fakturačního/licenčního modelu stávajících skutých.

## <a name="common-update-operations"></a>Běžné operace aktualizace

Následující operace aktualizace jsou běžné.

### <a name="update-container-image-version-for-a-sku"></a>Aktualizace verze bitové kopie kontejneru pro skladovou položku

Je běžné, že image kontejneru je pravidelně aktualizována opravami zabezpečení, dalšími funkcemi a tak dále. V tomto scénáři chcete aktualizovat image kontejneru, na kterou vaše skladová položka odkazuje, pomocí následujících kroků:

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **Všechny nabídky**najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **Skladové položky** vyberte skladovou položku přidruženou k bitové kopii kontejneru, kterou chcete aktualizovat.
4. V části **Image kontejneru**vyberte **+ Nová verze obrázku** a přidejte novou image kontejneru.
5. Zadejte nové **verze image kontejneru**. Verze bitové kopie musí dodržovat stejná pravidla pro značky jako předchozí verze. Značky verze by měly být ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla. Ověřte, zda je nová verze, kterou zadáte, větší než všechny předchozí verze.
6. Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali novou verzi image kontejneru na Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidání nové skladové položky

Pomocí následujících kroků můžete zpřístupnit novou skladovou položku pro vaši nabídku:

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **Všechny nabídky**najděte nabídku, kterou chcete aktualizovat.
3. Na kartě **Skladové položky** vyberte **Přidat novou skladovou položku** a ve vyskakovacím okně zadejte **ID skladové** položky.
4. Znovu publikovat kontejner pomocí kroků popsaných v [nabídce publikovat kontejner](./cpp-publish-offer.md).
5. Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali novou skladovou položku.

### <a name="update-offer-marketplace-metadata"></a>Aktualizovat metadata tržiště

Pomocí následujících kroků aktualizujte metadata webu marketplace přidružená k vaší nabídce. (Například: název společnosti, loga atd.)

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **Všechny nabídky**najdete nabídku, kterou chcete aktualizovat.
3. Přejděte na kartu **Marketplace.** [Publish container offer](./cpp-publish-offer.md)
4. Vyberte **Publikovat,** chcete-li spustit pracovní postup, abyste publikovali změny.

## <a name="compare-feature"></a>Funkce Porovnat

Když provedete změny publikované nabídky, můžete pomocí funkce **Porovnat** auditovat provedené změny.

### <a name="to-use-the-compare-feature"></a>Použití funkce Porovnat:

1. Kdykoli v procesu úprav vyberte porovnat pro vaši nabídku.
2. Podívejte se na souběžné verze marketingových prostředků a metadat.


## <a name="history-of-publishing-actions"></a>Historie publikování akcí

Chcete-li zobrazit historickou publikační aktivitu, vyberte kartu **Historie** na levém panelu nabídek cloudového partnerského portálu. Můžete vidět akce s časovým razítkem provedené během životnosti nabídek Azure Marketplace.
