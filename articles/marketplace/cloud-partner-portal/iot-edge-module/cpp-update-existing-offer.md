---
title: Aktualizace stávající nabídky modulu Azure IoT Edge | Dokumentace Microsoftu
description: Jak aktualizovat stávající nabídky modul IoT Edge na webu Azure Marketplace.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 071837aac9fce405fcc5ef92e044a0265e0e5969
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430814"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aktualizace stávající nabídky modul IoT Edge

Tento článek popisuje postup prostřednictvím různých aspektů nabídky modul IoT Edge v aktualizaci [portál partnerů cloudu](https://cloudpartner.azure.com/) a pak znovu publikovat tuto nabídku.

Tady je několik důvodů, proč můžete chtít aktualizovat vaši nabídku, jako například:

-  Přidává se nová verze bitové kopie modulu IoT Edge do existujících skladových položek.
-  Přidání nové SKU.
-  Aktualizace metadat marketplace pro jednotlivé SKU a nabídky.

Které vám pomohou tyto úpravy, tento portál nabízí **porovnání** a **historie** funkce.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Nedovolené změny nabídka modul IoT Edge nebo SKU

Existují atributy nabídka modul IoT Edge nebo skladovou jednotku, kterou nelze změnit poté, co tato nabídka je za provozu na webu Azure Marketplace. Nelze změnit následující nastavení:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **SKU ID** existujících skladových položek
-  Verze značky, například: `1.0.1`
-  Model fakturace nebo licencí se změní na existující skladové položky

## <a name="common-update-operations"></a>Běžné operace update

Následující operace update jsou společná.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Aktualizace verze bitové kopie modulu IoT Edge pro SKU

Je běžné, že image s modulem IoT Edge pravidelně aktualizovat opravy zabezpečení, další funkce a tak dále. V tomto scénáři budete chtít aktualizovat image s modulem IoT Edge, odkazující na vaši skladovou jednotku s použitím následujících kroků:

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.

3.  V **SKU** kartu, vyberte skladovou Položku přidruženou image s modulem IoT Edge k aktualizaci.

4.  V části **image s modulem Edge**vyberte **+ nová verze Image** přidání nové bitové kopie modulu IoT Edge.

5.  Zadejte nový modul IoT Edge **obrázku verze**. Verze image musí postupujte podle pokynů na stejné značky jako předchozí verze. Označení verze by měla mít formát X.Y.Z, kde X, Y a jsou celá čísla. Ověřte, zda je větší než všechny předchozí verze nové verze, kterou zadáte.

6.  Vyberte **publikovat** spuštění pracovního postupu pro publikování vaší nové verze modulu IoT Edge na webu Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidat novou skladovou Položku

Chcete-li novou skladovou Položku pro danou nabídku dostupná, postupujte následovně: 

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.

3.  V části **SKU** kartu, vyberte možnost **přidat novou skladovou Položku** a zadejte **SKU ID** v místním okně.

4.  Znovu publikujte modul IoT Edge, pomocí kroků popsaných v [publikovat modul IoT Edge na webu Azure Marketplace](./cpp-publish-offer.md).

5.  Vyberte **publikovat** spuštění pracovního postupu pro publikování nové skladové jednotky.


### <a name="update-offer-marketplace-metadata"></a>Aktualizace nabídky marketplace metadat

Použijte následující postup k aktualizaci marketplace metadata související s vaší nabídkou. (Příklad: název společnosti, loga, atd.)

1.  Přihlaste se [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**, najít nabídky, které chcete aktualizovat.

3.  Přejděte **Marketplace** kartu. Postupujte podle pokynů v [publikovat modul IoT Edge na webu Azure Marketplace](./cpp-publish-offer.md) článku provést změny metadat.

4.  Vyberte **publikovat** ke spuštění pracovního postupu publikovat provedené změny.

## <a name="compare-feature"></a>Porovnání funkcí

Pokud provedete změny v publikované nabídky, můžete použít **porovnání** funkce Auditovat změny, které jste udělali. 

**Jak používat funkci porovnání:**

1.  V každém okamžiku procesu úprav, vyberte **porovnání** pro vaši nabídku.

    ![Porovnejte funkce tlačítko](./media/iot-edge-module-compare.png)


2.  Podívejte se na verzích vedle sebe marketingové prostředky a metadata.


## <a name="history-of-publishing-actions"></a>Historie publikování akce

Pokud chcete zobrazit Historický publikační aktivity, vyberte **historie** karty na levé navigační nabídky panelu z cloudu portál pro partnery. Zobrazí se tedy akce prováděné po celou dobu životnosti vaší nabídky na webu Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
