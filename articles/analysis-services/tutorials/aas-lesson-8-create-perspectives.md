---
title: 'Kurz služby Azure Analysis Services – Lekce 8: Vytvoření perspektiv | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit perspektivy v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e92e86f59058f1f226c67f873530cd9c06b5d5fc
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428330"
---
# <a name="create-perspectives"></a>Vytvoření perspektiv

V této lekci vytvoříte perspektivu Internet Sales. Perspektiva definuje zobrazitelnou podmnožinu modelu poskytující zacílené pohledy specifické pro firmu nebo aplikaci. Když se uživatel k modelu připojí pomocí perspektivy, uvidí jenom objekty modelu (tabulky, sloupce, míry, hierarchie, klíčové ukazatele výkonů) jako pole definovaná v této perspektivě. Další informace najdete v tématu [Perspektivy](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular).
  
Perspektiva Internet Sales, kterou v této lekci vytvoříte, nebude zahrnovat objekt tabulky DimCustomer. Když vytvoříte perspektivu, která vyloučí některé objekty ze zobrazení, budou tyto objekty v modelu dál existovat. Nebudou se ale zobrazovat v seznamu polí klienta sestav. Počítané sloupce a míry, ať už jsou do perspektivy zahnuté či nikoli, můžou dál provádět výpočty z dat vyloučeného objektu.  
  
Cílem této lekce je popsat, jakým způsobem můžete vytvořit perspektivu a seznámit se s nástroji pro vytváření tabulkových modelů. Pokud tento model rozbalíte později, abyste mohli přidat další tabulky, můžete vytvořit další perspektivy pro definování jiných přehledů modelu, např. Inventory and Sales.  
  
Odhadovaný čas dokončení této lekce: **5 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 7: Vytvoření klíčových ukazatelů výkonu](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  
  
## <a name="create-perspectives"></a>Vytvoření perspektiv  
  
#### <a name="to-create-an-internet-sales-perspective"></a>Postup vytvoření perspektivy Internet Sales  
  
1.  Klikněte na nabídku **Model** > **Perspektivy** > **Vytvořit a spravovat**.  
  
2.  V dialogovém okně **Perspektivy** klikněte na **Nová perspektiva**.  
  
3.  Poklikejte na záhlaví sloupce **Nová perspektiva** a pak změňte název na **Internet Sales**.  
  
4.  Vyberte všechny tabulky *kromě* **DimCustomer**.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    V další lekci použijete funkci Analyzovat v aplikaci Excel k otestování této perspektivy. Seznam polí kontingenční tabulky Excelu obsahuje všechny tabulky kromě tabulky DimCustomer.  

## <a name="whats-next"></a>Co dále?
[Lekce 9: Vytvoření hierarchií](../tutorials/aas-lesson-9-create-hierarchies.md)
  
  
  
  
