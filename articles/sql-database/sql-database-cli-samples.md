---
title: Ukázkové skripty Azure CLI pro službu SQL Database | Microsoft Docs
description: Ukázkové skripty Azure CLI pro vytváření a správu serverů služby Azure SQL Database, elastických fondů, databází a bran firewall.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 5ecd5ee4a053d3ebb550b6f2387a0e915b3c2c23
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569404"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Ukázky v Azure CLI pro službu Azure SQL Database

Azure SQL Database můžete nakonfigurovat pomocí <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-pools"></a>Elastické fondy & jedné databáze

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro službu Azure SQL Database.

| |  |
|---|---|
|**Vytvoření izolované databáze a elastického fondu**||
| [Vytvoření izolované databáze a konfigurace pravidla brány firewall](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento skript rozhraní příkazového řádku vytvoří izolovanou databázi Azure SQL a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento ukázkový skript rozhraní příkazového řádku vytvoří elastické fondy SQL, přesune databáze Azure SQL ve fondu a změní velikost výpočetních prostředků.|
|**Škálování izolované databáze a elastického fondu**||
| [Škálování izolované databáze](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento ukázkový skript rozhraní příkazového řádku škáluje izolovanou databázi Azure SQL na jinou velikost výpočetních prostředků po provedení dotazu na informace o velikosti databáze. |
| [Škálování elastického fondu](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Tento skript rozhraní příkazového řádku škáluje elastický fond SQL na jinou velikost výpočetních prostředků.  |
|**Skupiny převzetí služeb při selhání**||
| [Přidat jednu databázi do skupiny převzetí služeb při selhání](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Tento skript rozhraní příkazového řádku vytvoří databázi a skupinu převzetí služeb při selhání přidá databázi do skupiny převzetí služeb při selhání a otestuje převzetí služeb při selhání na sekundární server.|
|||

Přečtěte si další informace o [izolovaná databáze rozhraní příkazového řádku Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>MI

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro Azure SQL Database spravované instance.

| |  |
|---|---|
| [Vytvoření spravované instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Tento skript rozhraní příkazového řádku ukazuje, jak vytvořit spravovanou instanci. |
| [Aktualizace spravované instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Tento skript rozhraní příkazového řádku ukazuje, jak aktualizovat spravovanou instanci. |
| [Přesunutí databáze do jiné spravované instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Tento skript rozhraní příkazového řádku ukazuje, jak obnovit zálohu databáze z jedné instance do druhé. |
|||

Přečtěte si další informace o [rozhraní Azure CLI API spravované instance](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) a [Další příklady najdete tady](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
