---
title: Aktualizace stávající nabídky kontejnerů Azure | Dokumentace Microsoftu
description: Jak aktualizovat stávající nabídky kontejneru na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 2b568717b6656fb9ae15e9a6dbd27441689c4372
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979693"
---
# <a name="update-an-existing-container-offer"></a>Aktualizace stávající nabídky kontejneru

Tento článek popisuje postup prostřednictvím různých aspektů aktualizaci kontejneru nabídky v [portál partnerů cloudu](https://cloudpartner.azure.com/).

Tady je několik důvodů, proč můžete chtít aktualizovat vaši nabídku, jako například:

-  Přidání nové verze image kontejneru do existujících skladových položek.
-  Přidání nové SKU.
-  Aktualizace metadat marketplace pro jednotlivé SKU a nabídky.

Které vám pomohou tyto úpravy, portál poskytuje **porovnání** a **historie** funkce.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Nedovolené změny kontejneru nabídky nebo SKU

Existují atributy nabídky kontejner nebo skladovou jednotku, kterou nelze změnit poté, co tato nabídka je za provozu na webu Azure Marketplace. Nelze změnit následující nastavení:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **SKU ID** existujících skladových položek
-  Verze značky, například: `1.0.1`
-  Model fakturace nebo licencí se změní na existující skladové položky

## <a name="common-update-operations"></a>Běžné operace update

Následující operace update jsou společná.

### <a name="update-container-image-version-for-a-sku"></a>Aktualizace verze image kontejneru pro SKU

Je běžné, že image kontejneru pravidelně aktualizovat opravy zabezpečení, další funkce a tak dále. V tomto scénáři budete chtít aktualizovat image kontejneru, který odkazuje na skladovou jednotku s použitím následujících kroků:

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. V **SKU** kartu, vyberte skladovou Položku přidruženou image kontejneru se aktualizovat.
4. V části **image kontejneru**vyberte **+ nová verze Image** přidáte novou image kontejneru.
5. Zadejte nový kontejner **obrázku verze**. Verze image musí postupujte podle pokynů na stejné značky jako předchozí verze. Označení verze by měla mít formát X.Y.Z, kde X, Y a jsou celá čísla. Ověřte, zda je větší než všechny předchozí verze nové verze, kterou zadáte.
6. Vyberte **publikovat** spuštění pracovního postupu pro publikování novou verzi image kontejneru na webu Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidat novou skladovou Položku

Chcete-li novou skladovou Položku pro danou nabídku dostupná, postupujte následovně:

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. V části **SKU** kartu, vyberte možnost **přidat novou skladovou Položku** a zadejte **SKU ID** v místním okně.
4. Znovu publikovat pomocí kroků popsaných v kontejneru [nabídky kontejner publikovat](./cpp-publish-offer.md).
5. Vyberte **publikovat** spuštění pracovního postupu pro publikování nové skladové jednotky.

### <a name="update-offer-marketplace-metadata"></a>Aktualizace nabídky marketplace metadat

Použijte následující postup k aktualizaci marketplace metadata související s vaší nabídkou. (Příklad: firemní název, loga a atd.)

1. Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.
3. Přejděte **Marketplace** kartu. Postupujte podle pokynů v [nabídky kontejner publikovat](./cpp-publish-offer.md) nabízejí článku provést změny metadat.
4. Vyberte **publikovat** ke spuštění pracovního postupu publikovat provedené změny.

## <a name="compare-feature"></a>Porovnání funkcí

Když provedete změny v publikované nabídky, můžete **porovnání** funkce Auditovat změny, které jste udělali.

### <a name="to-use-the-compare-feature"></a>Jak používat funkci porovnání:

1. V každém okamžiku procesu úprav vyberte porovnání pro vaši nabídku.
2. Podívejte se na verzích vedle sebe marketingové prostředky a metadata.


## <a name="history-of-publishing-actions"></a>Historie publikování akce

Pokud chcete zobrazit Historický publikační aktivity, vyberte **historie** karty na levé navigační nabídky panelu z cloudu portál pro partnery. Zobrazí se tedy akce prováděné po celou dobu životnosti vaší nabídky na webu Azure Marketplace.