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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851620"
---
Azure Storage se vám účtuje na základě využití účtu úložiště. Všechny objekty v rámci účtu úložiště se fakturují společně jako skupina. 

Náklady na skladování se počítají podle následujících faktorů: 

* **Oblast** označuje zeměpisnou oblast, ve které je váš účet založen.
* **Typ účtu** odkazuje na typ účtu úložiště, který používáte. 
* **Úroveň přístupu** odkazuje na vzor využití dat, který jste zadali pro váš účet úložiště pro obecné účely v2 nebo objektblo.
* **Kapacita úložiště** označuje, kolik přidělení účtu úložiště používáte k ukládání dat.
* **Replikace** určuje, kolik kopií dat bude udržováno najednou a v jakých umístěních.
* **Transakce** odkazují na všechny operace čtení a zápisu do služby Azure Storage.
* **Odchozí data** odkazuje na všechna data přenesená z oblasti Azure. Když k datům ve vašem účtu úložiště přistupuje aplikace, která není spuštěná ve stejné oblasti, bude se vám účtovat odchozí data. Informace o použití skupin prostředků k seskupení dat a služeb ve stejné oblasti k omezení poplatků za odchozí přenos dat najdete v [tématu Co je skupina prostředků Azure?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Na stránce [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) najdete podrobné informace o cenách na základě typu účtu, kapacity úložiště, replikace a transakcí. Na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/) najdete podrobné informace o sazbách za odchozí data. Představu o nákladech můžete získat v [Cenové kalkulačce pro Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management).

