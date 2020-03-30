---
title: Karta Nastavení nabídky virtuálních strojů na portálu partnerů cloudu pro Azure Marketplace
description: Popisuje kartu Nastavení nabídek používanou při vytváření nabídky virtuálního počítače Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: e5353797aaa7d40e1b6a95685b79cb3fe99f5ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277646"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta Nastavení nabídky virtuálního počítače

Stránka **Nová nabídka** pro virtuální počítače se otevře na první kartě s názvem Nastavení **nabídky**.  

![Stránka Nová nabídka pro virtuální počítače](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Pole Nastavení nabídky

Na kartě **Nastavení nabídky** je nutné zadat následující pole.  Připojená hvězdička (*) k názvu pole označuje, že je požadována. 

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID nabídky\***   | Jedinečný identifikátor (v rámci profilu vydavatele) pro nabídku. Tento identifikátor se zobrazí v adresách URL produktů, šablonách Azure Resource Manageru a v sestavách fakturace. Má maximální délku 50 znaků, může se skládat pouze z malých alfanumerických znaků a pomlček (-), ale nemůže končit pomlčkou. Toto pole nelze změnit po přepychu nabídky do provozu. <br> Pokud například Contoso publikuje nabídku s **ukázkovým virtuálním počítačem**ID nabídky , přiřadí se mu adresa URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` Azure Marketplace . |
| **Vydavatel\***  | Jedinečný identifikátor vaší organizace na Azure Marketplace. Všechny nabídky by měly být spojeny s vaším ID majitele stránek. Tuto hodnotu nelze po uložení nabídky změnit. |
| **Jméno\***       | Zobrazovaný název vaší nabídky. Tento název se zobrazí na Azure Marketplace a na portálu partnerů cloudu. Může mít maximálně 50 znaků. Pokyny zde je zahrnout rozpoznatelné značky pro váš produkt. Neuvádějte zde název vaší organizace, pokud není způsob, jakým je uváděn na trh. Pokud tuto nabídku prodáváte na jiných webech a v publikacích, ujistěte se, že název je ve všech publikacích přesně stejný. |
|   |   |
 
Po zadaných polích klepněte na **tlačítko Uložit.** 


## <a name="next-steps"></a>Další kroky

Na další kartě přidáte do nabídky [soužek.](./cpp-skus-tab.md)
