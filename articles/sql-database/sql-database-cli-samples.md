---
title: Ukázkové skripty Azure CLI pro službu SQL Database | Microsoft Docs
description: Ukázkové skripty Azure CLI pro vytváření a správu serverů služby Azure SQL Database, elastických fondů, databází a bran firewall.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: 3b98214da259e9e429c938f8ca7963c9f525e862
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560817"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Ukázky v Azure CLI pro službu Azure SQL Database

Azure SQL Database lze konfigurovat pomocí <a href="/cli/azure">rozhraní příkazového řádku Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="single-database--elastic-pools"></a>Izolované databáze a elastické fondy

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure SQL Database.

| |  |
|---|---|
|**Vytvoření izolované databáze a elastického fondu**||
| [Vytvoření izolované databáze a konfigurace pravidla brány firewall](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento skript rozhraní příkazového řádku vytvoří izolovanou databázi SQL Azure a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento ukázkový skript rozhraní příkazového řádku vytvoří elastické fondy SQL, přesune databáze SQL Azure ve fondu a změní velikosti výpočetních prostředků.|
|**Škálování izolované databáze a elastického fondu**||
| [Škálování izolované databáze](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento ukázkový skript rozhraní příkazového řádku škáluje izolovanou databázi SQL Azure na jinou velikost výpočetního prostředku po provedení dotazu na informace o velikosti databáze. |
| [Škálování elastického fondu](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento skript rozhraní příkazového řádku škáluje elastický fond SQL na jinou velikost výpočetního prostředku.  |
|||

Další informace o [jediné databáze rozhraní API Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>MI

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure SQL Database – Managed Instance.

| |  |
|---|---|
| [Vytvoření spravované instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/) | Tento skript rozhraní příkazového řádku ukazuje, jak vytvoříte Managed Instance. |
| [Aktualizovat spravované Instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/modify-azure-sql-database-managed-instance-using-azure-cli/) | Tento skript rozhraní příkazového řádku ukazuje, jak aktualizovat Managed Instance. |
| [Přesunutí databáze do jiné mi](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Tento skript rozhraní příkazového řádku ukazuje, jak obnovit zálohu databáze z jedné instance. |
|||

Další informace o [spravované Instance Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) a najít [Další příklady v tomto článku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
