---
title: Konfigurace spravované instance serveru SQL s podporou ARC Azure
description: Konfigurace spravované instance serveru SQL s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f687923f1185d848c2c4f2f40d949c71a7743d65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936721"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Konfigurace spravované instance serveru SQL s podporou ARC Azure

Tento článek vysvětluje, jak nakonfigurovat spravovanou instanci SQL s podporou ARC Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources-for-azure-arc-enabled-sql-managed-instance"></a>Konfigurace prostředků pro spravovanou instanci SQL s podporou ARC Azure

### <a name="configure-using-azdata"></a>Konfigurace pomocí azdata

Pomocí rozhraní příkazového řádku můžete upravit konfiguraci spravovaných instancí SQL s podporou ARC Azure `azdata` . Spuštěním následujícího příkazu zobrazte možnosti konfigurace. 

```
azdata arc sql mi edit --help
```

V následujícím příkladu se nastaví požadavky a omezení jádra procesoru a paměti.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Chcete-li zobrazit změny provedené ve spravované instanci SQL, můžete použít následující příkazy k zobrazení konfiguračního souboru YAML:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Konfigurovat možnosti serveru

Po uplynutí doby vytvoření můžete nakonfigurovat nastavení konfigurace serveru pro spravovanou instanci SQL s podporou ARC Azure. Tento článek popisuje, jak nakonfigurovat nastavení, jako je povolení nebo zakázání agenta MSSQL, povolení specifických příznaků trasování pro scénáře řešení potíží.

Chcete-li změnit kterékoli z těchto nastavení, postupujte takto:

1. Vytvořte vlastní `mssql-custom.conf` soubor, který obsahuje cílová nastavení. Následující příklad povolí agenta SQL a povolí příznak trasování 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Kopírovat `mssql-custom.conf` soubor do `/var/opt/mssql` `mssql-miaa` kontejneru v poli `master-0` pod. Nahraďte `<namespaceName>` názvem clusteru pro velké objemy dat.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Restartujte instanci SQL Server.  Nahraďte `<namespaceName>` názvem clusteru pro velké objemy dat.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Známá omezení**
- Výše uvedené kroky vyžadují oprávnění správce clusteru Kubernetes.
- Tato změna se může zobrazit v celé verzi Preview.
