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
ms.openlocfilehash: 6ad5bafe66e6e39503d3987aa6ddd9159c532fa0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237321"
---
Účtuje se vám Azure Storage na základě využití účtu úložiště. Všechny objekty v účtu úložiště se účtují dohromady jako skupina. 

Náklady na úložiště se počítají v závislosti na následujících faktorech: oblast/umístění, typ účtu, úroveň přístupu, kapacita úložiště, schéma replikace, transakce úložiště a odchozí přenos dat.

* **Oblast** odkazuje na geografickou oblast, ve které je účet založený.
* **Typ účtu** odkazuje na typ účtu úložiště, který používáte. 
* **Úroveň přístupu** odkazuje na model využití dat, který jste zadali pro účet úložiště pro obecné účely v2 nebo BLOB.
* **Kapacita** úložiště označuje, jak velká část vašeho účtu úložiště používáte k ukládání dat.
* **Replikace** určuje, kolik kopií vašich dat se uchovává najednou a v jakých umístěních.
* **Transakce** odkazují na všechny operace čtení a zápisu na Azure Storage.
* **Výstup dat** odkazuje na všechna data přenesená z oblasti Azure. Když k datům v účtu úložiště přistupovala aplikace, která neběží ve stejné oblasti, účtují se vám poplatky za výstup dat. Informace o použití skupin prostředků k seskupení dat a služeb ve stejné oblasti za účelem omezení nákladů na výstup najdete v tématu [co je skupina prostředků Azure?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Na stránce s [cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/) najdete podrobné informace o cenách na základě typu účtu, kapacity úložiště, replikace a transakcí. [Podrobnosti o cenách přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/) poskytují podrobné informace o cenách pro výstup dat. Pomocí [cenové kalkulačky Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) můžete odhadnout náklady.

