---
title: Karta nabízejí nastavení virtuálního počítače na portálu partnerů cloudu pro Azure Marketplace | Dokumentace Microsoftu
description: Popisuje na kartě nabídky nastavení použité při vytváření virtuálního počítače Azure Marketplace nabídku.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844317"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta nabízejí nastavení virtuálního počítače

**Nová nabídka** otevře se stránka pro virtuální počítače na první kartě s názvem **nabízejí nastavení**.  Připojený hvězdička (*) na název pole označuje, že je povinný. 

![Nová stránka nabídky virtuálních počítačů](./media/publishvm_004.png)

V **nabízejí nastavení** kartu, je nutné zadat následující požadované pole.

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID nabídky**       | Jedinečný identifikátor (v rámci profilu vydavatele) pro tuto nabídku. Tento identifikátor se nebude zobrazovat v adresách URL produktu, šablon Azure Resource Manageru, a oznámí fakturace. To má maximální délku než 50 znaků, lze pouze obsahovat malé alfanumerické znaky a pomlčky (-), ale nesmí končit spojovníkem. Toto pole nelze změnit po ukončení nabídky za provozu. <br> Například pokud Contoso publikuje nabídka s ID nabídky **ukázkový virtuální počítač**, je přiřazena adresa URL Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Publisher**     | Jedinečný identifikátor vaší organizace na webu Azure Marketplace. Všechny nabídky by měly být přidruženy s vaším ID vydavatele. Tuto hodnotu nelze změnit po uložení nabídky. |
| **Název**          | Zobrazovaný název nabídky. Tento název se zobrazí na webu Azure Marketplace a na portál Cloud Partner. Může mít maximálně 50 znaků. Pokyny v tomto poli se zahrnou rozpoznat název značky pro tento produkt. Pokud to je, jak je na trh, nezahrnujte název vaší organizace. Pokud jsou marketing této nabídky do jiných webových stránek a publikace, ujistěte se, že název je ve všech publikacích přesně stejné. |
|  |  |
 
Klikněte na tlačítko **Uložit** uložte svůj postup. Na další kartě přidáte [SKU](./cpp-skus-tab.md) pro vaši nabídku.
