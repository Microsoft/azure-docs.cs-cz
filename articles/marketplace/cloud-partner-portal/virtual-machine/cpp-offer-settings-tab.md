---
title: Karta nastavení nabídky virtuálního počítače v portál partnerů cloudu pro Azure Marketplace
description: Popisuje kartu nastavení nabídky, která se používá při vytváření nabídky virtuálních počítačů s Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: abeadf5f3c0a19212a12256e06602e840c9d1fa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146828"
---
# <a name="virtual-machine-offer-settings-tab"></a>Karta nastavení nabídky virtuálního počítače

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v tématu [Vytvoření virtuálního počítače Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) pro správu migrovaných nabídek.

**Nová stránka nabídky** pro virtuální počítače se otevře na první kartě s názvem **Nabídka nastavení**.  

![Nová stránka nabídky pro virtuální počítače](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Pole nastavení nabídky

Na kartě **nastavení nabídky** musíte zadat následující pole.  Připojená hvězdička (*) v názvu pole označuje, že je požadovaná. 

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID nabídky\***   | Jedinečný identifikátor (v rámci profilu vydavatele) pro nabídku. Tento identifikátor bude viditelný v adresách URL produktů, Azure Resource Manager šablonách a fakturačních sestavách. Maximální délka je 50 znaků, může se skládat jenom z malých alfanumerických znaků a spojovníků (-), ale nemůže končit pomlčkou. Po živé nabídce nelze toto pole změnit. <br> Pokud například contoso zveřejňuje nabídku s ID nabídky **Sample-VM**, přiřadí se Azure Marketplace adresa URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` . |
| **Microsoft\***  | Jedinečný identifikátor vaší organizace v Azure Marketplace. Všechny vaše nabídky by se měly přidružit k vašemu vydavateli s vaším ID. Tuto hodnotu nelze změnit, jakmile se nabídka uloží. |
| **Název\***       | Zobrazovaný název vaší nabídky Tento název se zobrazí v Azure Marketplace a v portál partnerů cloudu. Může mít maximálně 50 znaků. Tady je návod, jak pro svůj produkt zahrnout rozpoznatelný název značky. Sem nezahrnujte název vaší organizace, pokud to nezpůsobuje uvedení na trh. Pokud tuto nabídku zadáte na jiné weby a publikace, ujistěte se, že je název přesně stejný ve všech publikacích. |
|   |   |
 
Po zadání všech polí klikněte na **Uložit** . 


## <a name="next-steps"></a>Další kroky

Na další kartě přidáte [SKU](./cpp-skus-tab.md) do vaší nabídky.
