---
title: Vysvětlení cen Azure Data Share
description: Přečtěte si, jak fungují ceny Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88136813"
---
# <a name="understand-azure-data-share-pricing"></a>Vysvětlení cen Azure Data Share

Pro sdílení na základě snímků se poplatky za Azure Data Share za spuštění snímku a snímku datové sady. Tento článek vysvětluje, jak odhadnout snímek datové sady a spouštění snímků pomocí informací historie snímku. V současné době se poskytovatel dat účtuje za snímek datové sady a spuštění snímku.

## <a name="dataset-snapshot"></a>Snímek datové sady

Snímek datové sady je operace přesunu datové sady z jejího zdroje do cíle. Při pořízení snímku pro sdílenou složku je snímek každé datové sady v rámci sdílené složky operací snímku datové sady. Pomocí následujících kroků zobrazte seznam snímků datové sady. 

1. V Azure Portal přejděte do svého prostředku pro sdílení dat.

1. Vyberte sdílenou složku z odeslané sdílené složky nebo přijaté sdílené složky.

1. Klikněte na kartu **Historie** .

1. Klikněte na počáteční čas snímku.
 
    ![Historie snímků](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Historie snímků") 

1. Zobrazení seznamu operací snímku datové sady. Každá položka řádku odpovídá operaci snímku datové sady. V tomto příkladu jsou dva snímky datové sady.

    ![Operace snímku datové sady](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Operace snímku datové sady")

## <a name="snapshot-execution"></a>Spuštění snímku

Spuštění snímku zahrnuje prostředky potřebné k přesunutí datové sady ze zdroje do cíle. Pro každou operaci snímku datové sady se spuštění snímku vypočítá jako počet virtuální jádra vynásobený trváním snímku. Poplatky se účtují po minutách a zaokrouhlují se na ně. Počet vCore se dynamicky vybere na základě páru zdroj-cíl a vzoru dat. Podle následujících pokynů Zobrazte čas spuštění snímku, koncový čas a virtuální jádra, které se používají pro operaci snímku datové sady.

1. V Azure Portal přejděte do svého prostředku pro sdílení dat.

1. Vyberte sdílenou složku z odeslané sdílené složky nebo přijaté sdílené složky.

1. Klikněte na kartu **Historie** .

1. Klikněte na počáteční čas snímku.

    ![Historie snímků](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Historie snímků") 

1. Klikněte na stav operace snímku datové sady.

    ![Stav snímku datové sady](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Stav snímku datové sady")

1. Zobrazte čas zahájení, koncový čas a virtuální jádra, které se použijí pro tuto operaci snímku datové sady. 

    ![Spuštění snímku](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Spuštění snímku")

## <a name="next-steps"></a>Další kroky

- Získat nejnovější informace o cenách – [ceny Azure Data Share](https://azure.microsoft.com/pricing/details/data-share/)
- Pomocí cenové kalkulačky Azure můžete odhadnout náklady – [Cenová Kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)
