---
title: Zotavení po havárii pro sdílenou složku Azure
description: Zotavení po havárii pro sdílenou složku Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218693"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Zotavení po havárii pro sdílenou složku Azure

Tento článek vysvětluje, jak nakonfigurovat prostředí pro zotavení po havárii pro sdílenou složku Azure. Výpadky datového centra Azure jsou v některých případech zřídka, ale můžou to trvat kdekoliv pár minut až hodin. Výpadky datového centra můžou způsobit přerušení prostředí, která se spoléhají na data sdílená poskytovatelem dat. Pomocí kroků popsaných v tomto článku můžou poskytovatelé dat dál sdílet data se svými spotřebiteli dat v případě výpadku datového centra pro primární oblast hostující vaši sdílenou složku dat. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Dosažení provozní kontinuity pro sdílenou složku Azure

Aby bylo možné podobu výpadku datového centra připravit, může mít poskytovatel dat v sekundární oblasti zřízené prostředí pro sdílení dat. K zajištění hladkého převzetí služeb při selhání v případě, že dojde k výpadku datového centra, se dají měřit míry. 

Poskytovatelé dat můžou zřídit sekundární prostředky sdílené složky Azure v další oblasti. Tyto prostředky pro sdílení dat je možné nakonfigurovat tak, aby zahrnovaly sdílené složky a datové sady, které existují v primárním prostředku sdílené složky Azure. Můžou pozvat příjemce dat do sekundární sdílené složky při konfiguraci prostředí DR nebo později (tj. jako součást kroků ručního převzetí služeb při selhání.

Pokud příjemci dat mají aktivní sdílení předplatných v sekundárním prostředí zřízené pro účely zotavení po havárii, můžou jako součást převzetí služeb při selhání Povolit plán snímků. Pokud se uživatelé dat nechtějí přihlásit k odběru sekundární oblasti pro účely zotavení po havárii, můžou se do sekundární sdílené složky pozvat později. 

Příjemci dat můžou mít aktivní sdílení předplatného, které je nečinné pro účely zotavení po havárii, nebo je můžou později pozvat jako součást postupů ručního převzetí služeb při selhání. 

## <a name="related-information"></a>Související informace

- [Provozní kontinuita a zotavení po havárii](../best-practices-availability-paired-regions.md)
- [Integrace vysoké dostupnosti do strategie BCDR](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .