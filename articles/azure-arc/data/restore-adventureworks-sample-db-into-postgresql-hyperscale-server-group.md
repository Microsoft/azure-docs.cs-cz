---
title: Obnovení ukázkové databáze AdventureWorks do PostgreSQL s povoleným rozšířením Azure ARC
description: Obnovení ukázkové databáze AdventureWorks do PostgreSQL s povoleným rozšířením Azure ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b51c5ca2295671a30fa6c0aee8d313c4c333900
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935955"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Obnovení ukázkové databáze AdventureWorks do PostgreSQL s povoleným rozšířením Azure ARC

[AdventureWorks](/sql/samples/adventureworks-install-configure) je ukázková databáze obsahující databázi OLTP, která se používá v kurzech, a příklady. Společnost Microsoft je poskytována a spravována jako součást [úložiště GitHub SQL Server Samples](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Open source projekt převedl databázi AdventureWorks, aby byla kompatibilní s PostgreSQLem s povoleným rozšířením Azure ARC.
- [Původní projekt](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Sledujte na projektu, který předem převede soubory CSV, aby byly kompatibilní s PostgreSQL.](https://github.com/NorfolkDataSci/adventure-works-postgres)

Tento dokument popisuje jednoduchý proces, který načte ukázkovou databázi AdventureWorks do PostgreSQL skupiny serverů s vlastním škálováním.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Stažení záložního souboru AdventureWorks

Stáhněte si soubor AdventureWorks. SQL do kontejneru PostgreSQL skupiny serverů s vlastním škálováním. V tomto příkladu použijeme `kubectl exec` příkaz pro vzdálené spuštění příkazu v kontejneru skupiny serverů PostgreSQL s profilem a Stáhněte si ho do kontejneru. Tento soubor můžete stáhnout z libovolného umístění přístupného pomocí `curl` . Tuto metodu použijte, pokud máte další soubory zálohování databáze, které chcete načíst do kontejneru PostgreSQL Server Group. Jakmile je v kontejneru skupiny serverů PostgreSQL s daty, je snadné vytvořit databázi, schéma a naplnit data.

Spusťte příkaz podobný tomuto: ke stažení souborů nahraďte hodnotu názvu pod a názvem oboru názvů před spuštěním:

> [!NOTE]
>  Aby váš kontejner mohl stáhnout soubor z GitHubu, bude nutné, abyste měli k dispozici připojení k internetu přes 443.

> [!NOTE]
>  Použijte název pod uzlu koordinátora Postgres skupiny serverů s rozšířením. Jeho název je <server group name> -0.  Pokud si nejste jisti názvem pod, spusťte příkaz. `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/master/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>Krok 2: obnovení databáze AdventureWorks

Podobně můžete spustit příkaz kubectl exec pro použití nástroje psql CLI, který je součástí kontejnerů skupiny serverů PostgreSQL s možností škálování pro vytvoření a načtení databáze.

Spusťte příkaz podobný tomuto, abyste vytvořili prázdnou databázi, a to tak, že před spuštěním nasadíte hodnotu názvu pod a název oboru názvů.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Pak spusťte příkaz podobný tomuto, aby obnovil databázi, která nahradí hodnotu názvu pod a název oboru názvů před spuštěním.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Poznámka: neuvidíte, že v případě, že budete škálovat PostgreSQL a Vy horizontálních oddílů nebo distribuujete data/tabulky v pracovních uzlech vaší skupiny serverů s technologií PostgreSQL pro škálování na více instancí, se vám nezobrazuje žádné výhody výkonu provozu na Azure ARC s povoleným rozšířením. Viz [navrhovaný další postup](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Navrhovaný další postup
- Přečtěte si téma koncepty a návody Azure Database for PostgreSQLho škálování pro distribuci vašich dat napříč několika PostgreSQL uzly s vlastním škálováním a využijte výhod Azure Database for PostgreSQL vysokého měřítka. :
    * [Uzly a tabulky](../../postgresql/concepts-hyperscale-nodes.md)
    * [Určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md)
    * [Volba distribučního sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Společné umístění tabulek](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuce a úprava tabulek](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Návrh databáze s více klienty](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Návrh řídicího panelu pro analýzu v reálném čase](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* V dokumentech výše přeskočte oddíly, **které se přihlásí k Azure Portal**, & **Vytvoření Azure Database for PostgreSQL-Citus (škálování)**. Implementujte zbývající kroky v nasazení ARC Azure. Tyto části jsou specifické pro Azure Database for PostgreSQL Citus (PaaS), které nabízíme jako službu v cloudu Azure, ale ostatní části dokumentů jsou přímo použitelné pro PostgreSQL škálování na úrovni Azure ARC.

- [Horizontální navýšení kapacity Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním](scale-out-postgresql-hyperscale-server-group.md)
