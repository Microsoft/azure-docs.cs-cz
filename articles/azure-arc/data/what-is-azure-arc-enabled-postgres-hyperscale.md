---
title: Co je rozšíření Azure ARC povolené PostgreSQL.
description: Co je rozšíření Azure ARC povolené PostgreSQL.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 8edf540c3f67a3a8bee075569f0a2588cae18a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390007"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Co je rozšíření Azure ARC povolené PostgreSQL.

PostgreSQL s povoleným rozšířením Azure ARC je jedna z databázových služeb, které jsou dostupné jako součást datových služeb s podporou ARC Azure. Azure ARC umožňuje spouštět místní datové služby Azure, na hraničních zařízeních a ve veřejných cloudech pomocí Kubernetes a infrastruktury podle vašeho výběru. Hodnota umístění datových služeb s povoleným obloukem Azure ARC:
- Vždy aktuální
- Elastické škálování
- Samoobslužné zřizování
- Jednotná správa
- Podpora odpojeného scénáře

Další podrobnosti najdete na adrese:
- [Co jsou datové služby s podporou ARC Azure](overview.md)
- [Režimy připojení a požadavky](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Porovnat řešení

Tato část popisuje, jak se PostgreSQL s využitím rozšíření Azure ARC liší od Azure Database for PostgreSQL s měřítkem (Citus)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL Hyperscale (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database PostgreSQL s měřítkem (Citus)":::

Toto je faktor formy Postgres databázového stroje, který je k dispozici jako služba v Azure (PaaS). Využívá rozšíření Citus, které umožňuje prostředí s technologií škálování. V tomto formuláři je služba spuštěna v datových centrech společnosti Microsoft a je provozována společností Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL s povoleným škálováním Azure ARC

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="PostgreSQL s povoleným škálováním Azure ARC":::

Toto je faktor formuláře pro škálování Postgres databázového stroje, který je dostupný pro datové služby s podporou ARC Azure. Je také napájený pomocí rozšíření Citus, které umožňuje prostředí s technologií škálování. V tomto formuláři poskytují naši zákazníci infrastrukturu, která je hostitelem systémů a pracuje s nimi.

## <a name="next-steps"></a>Další kroky
- **Vyzkoušejte** si to. Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure. 

- **Vytvořte si vlastní.** Pomocí těchto kroků vytvořte vlastní cluster Kubernetes: 
   1. [Instalace klientských nástrojů](install-client-tools.md)
   2. [Vytvoření kontroleru dat ARC Azure](create-data-controller.md)
   3. [Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Další informace o datových službách s podporou ARC Azure](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Přečtěte si o ARC Azure](https://aka.ms/azurearc)
