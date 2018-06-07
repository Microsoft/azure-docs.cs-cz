---
title: 'Kurz služby Azure Analysis Services – Lekce 3: Označení jako tabulky kalendářních dat | Dokumentace Microsoftu'
description: Popisuje, jak označit tabulku kalendářních dat v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 837543b6e9af714189e1d79eef483caabc2d32ad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597310"
---
# <a name="mark-as-date-table"></a>Označit jako tabulku kalendářních dat

V Lekci 2: Získání dat jste naimportovali tabulku dimenzí s názvem DimDate. Přestože ve vašem modelu je tato tabulka pojmenovaná DimDate, je možné ji také označit jako *tabulku kalendářních dat*, protože obsahuje údaje o datu a čase.  
  
Při každém použití funkcí časového měřítka DAX, třeba při pozdějším vytvoření měření, musíte zadat vlastnosti, které zahrnují *tabulku kalendářních dat* a jedinečný identifikátor – *sloupec Date* v této tabulce.
  
V této lekci označíte tabulku DimDate jako *tabulku kalendářních dat* a sloupec Date (v tabulce kalendářních dat) jako *sloupec Date* (jedinečný identifikátor).  

Než označíte tabulku a sloupec kalendářních dat, je vhodná doba provést drobnou údržbu, která zajistí lepší srozumitelnost vašeho modelu. Všimněte si, že tabulka DimDate obsahuje sloupec s názvem **FullDateAlternateKey**. Tento sloupec obsahuje jeden řádek pro každý den každého kalendářního roce zahrnutého v tabulce. Tento sloupec používáte v řadě vzorců měření a v sestavách. Ale FullDateAlternateKey skutečně není dobrý identifikátor pro tento sloupec. Přejmenujete ho na **Date** a usnadníte tak jeho identifikaci a použití ve vzorcích. Je vhodné přejmenovat objekty, jako jsou tabulky a sloupce, aby se usnadnila jejich identifikace v SSDT a klientských aplikacích pro vytváření sestav, jako je Power BI a Excel. 
  
Odhadovaný čas dokončení této lekce: **3 minuty**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabulkového modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 2: Získání dat](../tutorials/aas-lesson-2-get-data.md). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>Přejmenování sloupce FullDateAlternateKey

1.  V návrháři modelů klikněte na tabulku **DimDate**.

2.  Dvakrát klikněte na hlavičku sloupce **FullDateAlternateKey** a přejmenujte ho na **Date**.

  
### <a name="to-set-mark-as-date-table"></a>Nastavení Označit jako tabulku kalendářních dat  
  
1.  Vyberte sloupec **Date** a potom v okně **Vlastnosti** v části **Typ dat** ověřte, že je vybraná možnost **Datum**.  
  
2.  Klikněte do nabídky **Tabulka**, potom klikněte na **Datum** a nakonec klikněte na **Označit jako tabulku kalendářních dat**.  
  
3.  V dialogovém okně **Označit jako tabulku kalendářních dat** v seznamu **Datum** vyberte sloupec **Date** jako jedinečný identifikátor. Ve výchozím nastavení je obvykle vybraný. Klikněte na **OK**. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>Co dále?
[Lekce 4: Vytvoření relací](../tutorials/aas-lesson-4-create-relationships.md)
  
