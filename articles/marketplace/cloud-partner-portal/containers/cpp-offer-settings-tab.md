---
title: Nastavení nabídky pro Image kontejnerů Azure | Azure Marketplace
description: Nakonfigurujte nastavení nabídky pro kontejner Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 61d9fa535d2bec0a52351ba6199183ea899a0e1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146260"
---
# <a name="container-offer-settings-tab"></a>Karta nastavení nabídky kontejneru

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek Azure Container do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v části [vytvoření kontejneru Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) pro správu migrovaných nabídek.

Zobrazí se stránka **kontejnery > nové nabídky** s fokusem na kartě **nastavení nabídky** . 

![Identita nabídky](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Nastavení identity nabídky

V části **Nabídka identity**musíte zadat informace pro pole popsaná v následující tabulce. Hvězdička (*), která je připojena k názvu pole, označuje, že je požadováno. 

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID nabídky\***       | Jedinečný identifikátor (v rámci profilu vydavatele) pro nabídku. Tento identifikátor bude viditelný v adresách URL produktů a sestavách Insights. Má maximální délku 50 znaků a může používat malé alfanumerické znaky a pomlčky (-). (Identifikátor nemůže končit spojovníkem.) **Poznámka:** Po živé nabídce nelze toto pole změnit. <br> Pokud například contoso zveřejňuje nabídku s ID nabídky **-Container**, je jí přiřazena Azure Marketplace adresa URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID vydavatele\***     | Jedinečný identifikátor vaší organizace v Azure Marketplace. Všechny vaše nabídky by se měly přidružit k vašemu vydavateli s vaším ID. Tuto hodnotu nelze po uložení nabídky změnit. |
| **Název\***          | Zobrazovaný název vaší nabídky Tento název se zobrazí v Azure Marketplace a v portál partnerů cloudu. Může mít maximálně 50 znaků. Pro váš produkt doporučujeme použít rozpoznatelný název značky. Nepoužívejte název vaší organizace, pokud to nezpůsobuje uvedení produktu na trh. Pokud tuto nabídku zadáte na jiné weby a publikace, ujistěte se, že je název přesně stejný ve všech publikacích. |
|  |  |

Vyberte **Uložit** a uložte nastavení nabídky.


## <a name="next-steps"></a>Další kroky

Použijte kartu [SKU](./cpp-skus-tab.md) ke konfiguraci SKU pro vaši nabídku.
