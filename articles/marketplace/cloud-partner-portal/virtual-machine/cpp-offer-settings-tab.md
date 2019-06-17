---
title: Karta nabízejí nastavení virtuálního počítače na portálu partnerů cloudu pro Azure Marketplace
description: Popisuje na kartě nabídky nastavení použité při vytváření virtuálního počítače Azure Marketplace nabídku.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938159"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta nabízejí nastavení virtuálního počítače

**Nová nabídka** otevře se stránka pro virtuální počítače na první kartě s názvem **nabízejí nastavení**.  

![Nová stránka nabídky virtuálních počítačů](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Nabídka nastavení pole

V **nabízejí nastavení** kartu, je nutné zadat následující pole.  Připojený hvězdička (*) na název pole označuje, že je povinný. 

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID nabídky\***   | Jedinečný identifikátor (v rámci profilu vydavatele) pro tuto nabídku. Tento identifikátor se nebude zobrazovat v adresách URL produktu, šablon Azure Resource Manageru, a oznámí fakturace. To má maximální délku než 50 znaků, lze pouze obsahovat malé alfanumerické znaky a pomlčky (-), ale nesmí končit spojovníkem. Toto pole nelze změnit po ukončení nabídky za provozu. <br> Například pokud Contoso publikuje nabídka s ID nabídky **ukázkový virtuální počítač**, je přiřazena adresa URL Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Publisher\***  | Jedinečný identifikátor vaší organizace na webu Azure Marketplace. Všechny nabídky by měly být přidruženy s vaším ID vydavatele. Tuto hodnotu nelze změnit po uložení nabídky. |
| **Název\***       | Zobrazovaný název nabídky. Tento název se zobrazí na webu Azure Marketplace a na portál Cloud Partner. Může mít maximálně 50 znaků. Pokyny v tomto poli se zahrnou rozpoznat název značky pro tento produkt. Pokud to je, jak je na trh, nezahrnujte název vaší organizace. Pokud jsou marketing této nabídky do jiných webových stránek a publikace, ujistěte se, že název je ve všech publikacích přesně stejné. |
|   |   |
 
Klikněte na tlačítko **Uložit** poté, co jste zadali všechna pole. 


## <a name="next-steps"></a>Další postup

Na další kartě přidáte [SKU](./cpp-skus-tab.md) pro vaši nabídku.
