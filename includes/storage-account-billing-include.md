---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74851620"
---
Účtuje se vám Azure Storage na základě využití účtu úložiště. Všechny objekty v rámci účtu úložiště se fakturují společně jako skupina. 

Náklady na úložiště se počítají v závislosti na následujících faktorech: 

* **Oblast** odkazuje na geografickou oblast, ve které je účet založený.
* **Typ účtu** odkazuje na typ účtu úložiště, který používáte. 
* **Úroveň přístupu** odkazuje na model využití dat, který jste zadali pro účet úložiště pro obecné účely v2 nebo BLOB.
* **Kapacita** úložiště odkazuje na to, kolik z vašich účtů úložiště používáte k ukládání dat.
* **Replikace** určuje, kolik kopií vašich dat se uchovává najednou a v jakých umístěních.
* **Transakce** odkazují na všechny operace čtení a zápisu na Azure Storage.
* **Výstup dat** odkazuje na všechna data přenesená z oblasti Azure. Když k datům v účtu úložiště přistupovala aplikace, která neběží ve stejné oblasti, účtují se vám poplatky za výstup dat. Informace o použití skupin prostředků k seskupení dat a služeb ve stejné oblasti za účelem omezení nákladů na výstup najdete v tématu [co je skupina prostředků Azure?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) najdete podrobné informace o cenách na základě typu účtu, kapacity úložiště, replikace a transakcí. Na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/) najdete podrobné informace o sazbách za odchozí data. Představu o nákladech můžete získat v [Cenové kalkulačce pro Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management).

