---
title: Konfigurace Azure Firewall pravidel aplikací s plně kvalifikovanými názvy domén SQL
description: V tomto článku se dozvíte, jak nakonfigurovat plně kvalifikované názvy domény SQL v Azure Firewall pravidlech aplikací.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/18/2020
ms.author: victorh
ms.openlocfilehash: 8b67574f435681d8071eda1ad954dcafb5124cbf
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655100"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurace Azure Firewall pravidel aplikací s plně kvalifikovanými názvy domén SQL

Teď můžete nakonfigurovat Azure Firewall pravidla aplikací s plně kvalifikovanými názvy domén SQL. To umožňuje omezit přístup z virtuálních sítí pouze na zadané instance systému SQL Server.

Pomocí plně kvalifikovaných názvů domén SQL můžete filtrovat provoz:

- Z vaší virtuální sítě na Azure SQL Database nebo Azure SQL Data Warehouse. Příklad: Povolte přístup jenom k *SQL-Server1.Database.Windows.NET*.
- Z místního prostředí do Azure SQL Managed Instances nebo SQL IaaS spuštěné v virtuální sítě.
- Od paprsku po paprsky až po Azure SQL Managed Instances nebo SQL IaaS spuštěné v virtuální sítě.

Filtrování plně kvalifikovaného názvu domény SQL je podporováno pouze v [režimu proxy serveru](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Pokud používáte SQL ve výchozím režimu přesměrování, můžete přístup filtrovat pomocí značky služby SQL jako součást [pravidel sítě](overview.md#network-traffic-filtering-rules).
Pokud používáte jiné než výchozí porty pro přenos SQL IaaS, můžete tyto porty nakonfigurovat v pravidlech aplikací brány firewall.

## <a name="configure-using-azure-cli"></a>Konfigurace pomocí Azure CLI

1. Nasaďte [Azure firewall pomocí Azure CLI](deploy-cli.md).
2. Pokud filtrujete provoz na Azure SQL Database, SQL Data Warehouse nebo spravované instance SQL, ujistěte se, že je režim připojení SQL nastavený na **proxy**. Pokud se chcete dozvědět, jak přepnout do režimu připojení SQL, přečtěte si téma [nastavení připojení Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > Režim *proxy serveru* SQL může mít za následek větší latenci v porovnání s *přesměrování*. Pokud chcete pokračovat v používání režimu přesměrování, který je ve výchozím nastavení pro klienty připojující se v rámci Azure, můžete přístup filtrovat pomocí [značky služby](service-tags.md) SQL v [síťových pravidlech](tutorial-firewall-deploy-portal.md#configure-a-network-rule)brány firewall.

3. Konfigurace pravidla aplikace s plně kvalifikovaným názvem domény SQL pro povolení přístupu k SQL serveru:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurace prostřednictvím portálu Azure Portal
1. Nasaďte [Azure firewall pomocí Azure CLI](deploy-cli.md).
2. Pokud filtrujete provoz na Azure SQL Database, SQL Data Warehouse nebo spravované instance SQL, ujistěte se, že je režim připojení SQL nastavený na **proxy**. Pokud se chcete dozvědět, jak přepnout do režimu připojení SQL, přečtěte si téma [nastavení připojení Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > Režim *proxy serveru* SQL může mít za následek větší latenci v porovnání s *přesměrování*. Pokud chcete pokračovat v používání režimu přesměrování, který je ve výchozím nastavení pro klienty připojující se v rámci Azure, můžete přístup filtrovat pomocí [značky služby](service-tags.md) SQL v [síťových pravidlech](tutorial-firewall-deploy-portal.md#configure-a-network-rule)brány firewall.
3. Přidejte pravidlo aplikace s příslušným protokolem, portem a plně kvalifikovaným názvem domény SQL a pak vyberte **Uložit**.
   ![pravidlo aplikace s plně kvalifikovaným názvem domény SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Přístup k SQL z virtuálního počítače ve virtuální síti, která filtruje přenos přes bránu firewall. 
5. Ověřte, že je povolený provoz [Azure firewall protokoly](log-analytics-samples.md) .

## <a name="next-steps"></a>Další kroky

Další informace o proxy serveru SQL a režimech přesměrování najdete v tématu [Architektura připojení Azure SQL Database](../sql-database/sql-database-connectivity-architecture.md).