---
title: Aktualizace existující nabídky modulu Azure IoT Edge | Azure Marketplace
description: Jak aktualizovat existující nabídku modulu IoT Edge v Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 17cce766f2d56766a9fcf260416d8bbf3e43d0c5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142253"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aktualizace existující nabídky modulu IoT Edge

>[!Important]
>Od 13. dubna 2020 začneme přesouvat správu nabídek IoT Edge modulů do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Podle pokynů v tématu [vytvoření IoT Edge modulu nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) můžete spravovat migrované nabídky.

Tento článek popisuje různé aspekty aktualizace nabídky IoT Edge modulu v [portál partnerů cloudu](https://cloudpartner.azure.com/) a následném opětovném publikování nabídky.

K dispozici je několik důvodů, proč byste mohli chtít aktualizovat nabídku, například:

-  Přidává se nová verze image modulu IoT Edge do stávajících SKU.
-  Přidávání nových SKU.
-  Aktualizují se metadata Marketplace pro nabídku nebo jednotlivé SKU.

Pro pomoc s těmito úpravami nabízí portál funkce **porovnání** a **Historie** .  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Nepovolené změny nabídky modulu IoT Edge nebo SKU

Existují atributy nabídky modulu IoT Edge nebo SKU, které nelze změnit poté, co je nabídka v Azure Marketplace živá. Nemůžete změnit následující nastavení:

-  **ID nabídky** a **ID vydavatele** nabídky
-  **ID SKU** existujících SKU
-  Značky verze, například:`1.0.1`
-  Fakturační a licenční model se mění ve stávajících SKU

## <a name="common-update-operations"></a>Běžné operace aktualizace

Následující operace aktualizace jsou běžné.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Aktualizace verze image modulu IoT Edge pro SKU

Je běžné, že se image IoT Edge modulu pravidelně aktualizuje pomocí oprav zabezpečení, dalších funkcí a tak dále. V tomto scénáři chcete aktualizovat image modulu IoT Edge, kterou vaše SKU odkazuje, pomocí následujících kroků:

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.

3.  Na kartě **SKU** vyberte skladovou jednotku přidruženou k imagi IoT Edge modulu, která se má aktualizovat.

4.  V části **Obrázek modulu Edge**vyberte **+ Nová verze image** a přidejte novou image modulu IoT Edge.

5.  Zadejte nové **verze imagí**modulu IoT Edge. Verze image musí splňovat stejné pokyny pro značky jako v předchozích verzích. Značky verze by měly mít formát X. Y. Z, kde X, Y a Z jsou celá čísla. Ověřte, že nová verze, kterou zadáte, je větší než všechny předchozí verze.

6.  Vyberte **publikovat** a spusťte pracovní postup, který publikuje novou verzi IoT Edge modulu do Azure Marketplace.

### <a name="add-a-new-sku"></a>Přidat novou SKLADOVOU položku

K dispozici je nová SKU pro vaši nabídku pomocí následujících kroků: 

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V části **všechny nabídky**Najděte nabídku, kterou chcete aktualizovat.

3.  Na kartě **SKU** vyberte **Přidat novou SKU** a v automaticky otevíraném okně zadejte **ID SKU** .

4.  Znovu publikujte modul IoT Edge pomocí postupu popsaného v tématu [publikování modulu IoT Edge a Azure Marketplace](./cpp-publish-offer.md).

5.  Vyberte **publikovat** a spusťte pracovní postup pro publikování nové SKU.


### <a name="update-offer-marketplace-metadata"></a>Aktualizace metadat webu nabídky

Pomocí následujících kroků aktualizujte metadata Marketplace přidružená k vaší nabídce. (Například: název společnosti, loga atd.)

1.  Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).

2.  V nabídce **všechny nabídky**Vyhledejte nabídku, kterou chcete aktualizovat.

3.  Přejít na kartu **Marketplace** . pomocí pokynů v [modulu publikování IoT Edge Azure Marketplace](./cpp-publish-offer.md) článku udělejte změny v metadatech.

4.  Vyberte **publikovat** a spusťte pracovní postup pro publikování změn.

## <a name="compare-feature"></a>Compare – funkce

Když provedete změny v publikované nabídce, můžete pomocí funkce **Compare** auditovat změny, které jste provedli. 

**Použití funkce Compare:**

1.  V jakémkoli okamžiku v procesu úprav vyberte **Porovnat** pro vaši nabídku.

    ![Tlačítko porovnat funkci](./media/iot-edge-module-compare.png)


2.  Podívejte se na souběžné verze materiálů a metadat marketingu.


## <a name="history-of-publishing-actions"></a>Historie akcí publikování

Chcete-li zobrazit historické aktivity publikování, vyberte kartu **Historie** v levém navigačním panelu portál partnerů cloudu. Můžete zobrazit akce s časovým razítkem provedenými během životnosti Azure Marketplace nabídek.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
