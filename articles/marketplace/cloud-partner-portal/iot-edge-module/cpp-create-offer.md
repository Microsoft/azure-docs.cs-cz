---
title: Vytvoření nabídky modulu Azure IoT Edge | Azure Marketplace
description: Jak publikovat nový modul IoT Edge pro Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 112c2ba831ab27faaca755079e9d20e2aebfcf9f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744395"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Vytvoření nové nabídky modulů IoT Edge pomocí portálu partnerů cloudu

>[!Important]
>března 2020 začneme přesouvat správu nabídek modulu IoT Edge do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky modulu IoT Edge](https://aka.ms/AzureCreateIoT) pro správu migrovaných nabídek.

Tento článek popisuje, jak vytvořit a publikovat položku nabídky modulu IoT Edge pro Azure Marketplace. Každá nabídka se na Azure Marketplace zobrazuje jako vlastní entita a je přidružená k jedné nebo více skum.  Nabídka modulu IoT Edge se skládá z následujících skupin prostředků a podpůrných služeb:

|  **Skupina majetku**   |  **Popis**  |
|  ---------------   |  ---------------  |
|    SKU            |  Nejmenší nasaditelná jednotka nabídky. Jedna nabídka (třída produktu) může mít více sku s ku s sebou přidružené k nabídce. Sku lze rozlišovat mezi podporovanými funkcemi a fakturačními modely. |
|  Marketplace       | Obsahuje marketingová, právní a vedoucí aktiva a specifikace správy.  <ul><li> Marketingové prostředky zahrnují název nabídky, popis a loga</li> <li> Právní majetek zahrnuje zásady ochrany osobních údajů, podmínky použití a další právní dokumentaci</li>  <li> Zásady správy potenciálních zákazníků umožňují určit, jak zpracovat zájemce z portálu koncového uživatele Azure Marketplace.</li> </ul> |
| Podpora            | Obsahuje informace o kontaktech podpory a zásadách. |


## <a name="new-offer-form"></a>Formulář nové nabídky 

Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/)a na levém řádku nabídek vyberte **+ nová nabídka.** V nabídce Nová nabídka vyberte **Moduly IoT Edge,** chcete-li zobrazit formulář **Nová nabídka** a spustit proces definování datových zdrojů pro novou nabídku modulu ioT Edge Module. 

![Nový modul IoT Edge nabízí výběr uživatelského rozhraní](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Další kroky

Stránka **Nová nabídka** pro typ nabídky modulu IoT Edge obsahuje sadu karet a polí formulářů, které použijete k vytvoření nové nabídky. Každý z následujících článků vysvětluje, jak pomocí karty definovat skupiny prostředků a podpůrné služby pro novou nabídku modulu IoT Edge.

- [Karta Nastavení nabídky](./cpp-offer-settings-tab.md)
- [Karta Skladové položky](./cpp-skus-tab.md)
- [Karta Marketplace](./cpp-marketplace-tab.md)
- [Karta Podpora](./cpp-support-tab.md)
