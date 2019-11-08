---
title: Nastavení nabídky aplikace Azure | Azure Marketplace
description: Nakonfigurujte nastavení nabídky pro nabídku aplikace Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: c5e00f77ffeec61fb4fee4b4e8eb72b7c7ed1077
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827530"
---
# <a name="azure-application-offer-settings-tab"></a>Karta nastavení nabídky aplikací Azure

Tento článek popisuje, jak nakonfigurovat nastavení nabídky pro aplikaci Azure.

**Aplikace Azure > novou stránku nabídky** se otevře s fokusem na kartě **nastavení nabídky** . Hvězdička (*), která je připojena k názvu pole, označuje, že je požadováno.

![Formulář nabídky identity](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Nastavení identity nabídky

V části **Nabídka identity**musíte zadat informace pro pole popsaná v následující tabulce.  

|    Pole         |       Popis                                                            |
|  ---------       |     ---------------                                                          |
| **ID nabídky\***       | Jedinečný identifikátor (v rámci profilu vydavatele) pro nabídku. Tento identifikátor bude viditelný v adresách URL produktů a sestavách Insights. Má maximální délku 50 znaků a může používat malé alfanumerické znaky a pomlčky (-). (Identifikátor nemůže končit spojovníkem.) **Poznámka:** Po živé nabídce nelze toto pole změnit. <br> Pokud například contoso zveřejňuje nabídku s ID nabídky **-Container**, je mu přiřazená Azure Marketplace adresa URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID vydavatele\***     | Jedinečný identifikátor vaší organizace v Azure Marketplace. Všechny vaše nabídky by se měly přidružit k vašemu vydavateli s vaším ID. Tuto hodnotu nelze po uložení nabídky změnit. |
| **Název\***          | Zobrazovaný název vaší nabídky Tento název se zobrazí v Azure Marketplace a v portál partnerů cloudu. Může mít maximálně 50 znaků. Pro váš produkt doporučujeme použít rozpoznatelný název značky. Nepoužívejte název vaší organizace, pokud to nezpůsobuje uvedení produktu na trh. Pokud tuto nabídku zadáte na jiné weby a publikace, ujistěte se, že je název přesně stejný ve všech publikacích. |
|  |  |

Vyberte **Uložit** a uložte nastavení nabídky.

## <a name="next-steps"></a>Další kroky

Použijte kartu [SKU](./cpp-skus-tab.md) ke konfiguraci SKU pro vaši nabídku.