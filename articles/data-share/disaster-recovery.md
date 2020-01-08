---
title: Zotavení po havárii pro sdílenou složku Azure
description: Zotavení po havárii pro sdílenou složku Azure
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483177"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Zotavení po havárii pro sdílenou složku Azure

V tomto článku se dozvíte, jak nakonfigurovat prostředí pro zotavení po havárii pro Azure Data Share. Výpadky datového centra Azure jsou v některých případech zřídka, ale můžou to trvat kdekoliv pár minut až hodin. Výpadky datového centra můžou způsobit přerušení prostředí, která se spoléhají na data sdílená poskytovatelem dat. Pomocí kroků popsaných v tomto článku můžou poskytovatelé dat dál sdílet data s uživateli s daty v případě výpadku datového centra pro primární oblast, která hostuje vaši sdílenou složku vašich dat. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Dosažení provozní kontinuity pro sdílenou složku Azure

Aby bylo možné podobu výpadku datového centra připravit, může mít poskytovatel dat v sekundární oblasti zřízené prostředí pro sdílení dat. Existují míry, které je možné provést, což zajistí hladké převzetí služeb při selhání v případě, že dojde k výpadku datového centra. 

Poskytovatelé dat můžou zřídit sekundární prostředky sdílené složky Azure v další oblasti. Tyto prostředky sdílení dat je možné nakonfigurovat tak, aby zahrnovaly datové sady, které existují v prostředí primárního sdílení dat. Příjemci dat se dají do sdílené složky přidat při konfiguraci prostředí DR nebo v pozdějším časovém okamžiku (tj. jako součást kroků ručního převzetí služeb při selhání.

Pokud příjemci dat mají aktivní předplatné sdílení v sekundárním prostředí zřízené pro účely zotavení po havárii, můžou jako součást převzetí služeb při selhání Povolit plán snímků. Pokud se spotřebitelé dat nechtějí přihlašovat k odběru sekundární oblasti pro účely zotavení po havárii, můžou se k sekundární sdílené složce dat přizvat později v pozdějším okamžiku. 

Příjemci dat můžou mít aktivní sdílení předplatného, které je nečinné pro účely zotavení po havárii, nebo je můžou přidat v pozdějším okamžiku v rámci postupu ručního převzetí služeb při selhání. 

## <a name="related-information"></a>Související informace

- [Provozní kontinuita a zotavení po havárii](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Sestavení vysoké dostupnosti do strategie BCDR](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .




