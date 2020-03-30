---
title: Zotavení po havárii pro Azure Data Share
description: Zotavení po havárii pro Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483177"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Zotavení po havárii pro Azure Data Share

V tomto článku si projdeme, jak nakonfigurovat prostředí pro zotavení po havárii pro Azure Data Share. Výpadky datového centra Azure jsou vzácné, ale může trvat kdekoli od několika minut až po hodiny. Výpadky datového centra mohou způsobit narušení prostředí, která spoléhají na sdílení dat poskytovatelem dat. Podle kroků popsaných v tomto článku mohou poskytovatelé dat pokračovat ve sdílení dat se svými příjemci dat v případě výpadku datového centra pro primární oblast, která hostuje sdílenou složku dat. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Dosažení kontinuity podnikání pro Azure Data Share

Chcete-li být připraveni na výpadek datového centra, poskytovatel dat může mít prostředí pro sdílení dat zřízené v sekundární oblasti. Existují opatření, která mohou být přijata, která zajistí hladké převzetí služeb při selhání v případě, že dojde k výpadku datového centra. 

Zprostředkovatelé dat můžou zřazovat sekundární prostředky Azure Data Share v další oblasti. Tyto prostředky sdílení dat lze nakonfigurovat tak, aby zahrnovaly datové sady, které existují v primárním prostředí sdílení dat. Spotřebitelé dat mohou být přidáni do sdílené složky dat při konfiguraci prostředí zotavení po havárii nebo přidáni později (tj. jako součást ručních kroků převzetí služeb při selhání).

Pokud spotřebitelé dat mají aktivní sdílení předplatného v sekundárním prostředí zřízená pro účely zotavení po Havárii, mohou povolit plán snímek jako součást převzetí služeb při selhání. Pokud spotřebitelé dat nechtějí přihlásit k odběru sekundární oblasti pro účely zotavení po Havárii, mohou být pozváni do sekundární sdílené složky dat později v čase. 

Spotřebitelé dat mohou mít buď aktivní sdílení předplatné, které je nečinné pro účely zotavení po Havárii, nebo zprostředkovatelé dat můžete přidat v pozdějším okamžiku v rámci ruční postupy převzetí služeb při selhání. 

## <a name="related-information"></a>Související informace

- [Kontinuita podnikání a zotavení po havárii](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Integrace vysoké dostupnosti do strategie BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak začít sdílet data, [pokračujte ve sdílení dat](share-your-data.md) kurzu.




