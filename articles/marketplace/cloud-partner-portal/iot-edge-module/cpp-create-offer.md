---
title: Vytvoření nabídky modulu Azure IoT Edge | Azure Marketplace
description: Jak publikovat nový modul IoT Edge pro Marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d8f4300f45fc626c104055f987d123301ba8f39d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148066"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Vytvoření nové nabídky modulu IoT Edge s využitím portál partnerů cloudu

>[!Important]
>Od 13. dubna 2020 začneme přesouvat správu nabídek IoT Edge modulů do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Podle pokynů v tématu [vytvoření IoT Edge modulu nabídky](https://docs.microsoft.com//azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) můžete spravovat migrované nabídky.

Tento článek popisuje, jak vytvořit a publikovat položku nabídky IoT Edge modulu pro Azure Marketplace. Každá nabídka se zobrazí jako vlastní entita v Azure Marketplace a je spojena s jednou nebo více SKU.  Nabídka modulu IoT Edge se skládá z následujících skupin prostředků a podpůrných služeb:

|  **Skupina prostředků**   |  **Popis**  |
|  ---------------   |  ---------------  |
|    SKU            |  Nejmenší jednotka v nabídce k nasazení. K jedné nabídce (třídě produktu) může být přidruženo několik SKU přidružených k této nabídce. Můžete použít SKU k odlišení podporovaných funkcí a modelů fakturace. |
|  Marketplace       | Obsahuje prostředky a specifikace pro marketing, právní a vedoucí.  <ul><li> Marketingové prostředky zahrnují název nabídky, popis a loga.</li> <li> Právní prostředky zahrnují zásady ochrany osobních údajů, podmínek použití a další právní dokumentaci.</li>  <li> Zásady správy potenciálních zákazníků vám umožní určit, jak se mají zpracovávat potenciální zákazníky z portálu Azure Marketplace portál pro koncové uživatele.</li> </ul> |
| Podpora            | Obsahuje informace o kontaktech a zásadách podpory |


## <a name="new-offer-form"></a>Formulář nové nabídky 

Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/)a v levém řádku nabídek vyberte **+ Nová nabídka** . V nabídce nová nabídka vyberte **IoT Edge moduly** pro zobrazení **nového formuláře nabídky** a spusťte proces definování prostředků pro novou nabídku modulu IoT Edge. 

![Výběr uživatelského rozhraní nabídky modulu New IoT Edge](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Další kroky

**Nová stránka nabídky** pro typ nabídky IoT Edge modul poskytuje sadu karet a polí formuláře, které použijete k vytvoření nové nabídky. V následujících článcích se dozvíte, jak pomocí karty definovat skupiny assetů a podpůrné služby pro novou nabídku modulu IoT Edge.

- [Karta nastavení nabídky](./cpp-offer-settings-tab.md)
- [Karta Skladové položky](./cpp-skus-tab.md)
- [Karta Marketplace](./cpp-marketplace-tab.md)
- [Karta Podpora](./cpp-support-tab.md)
