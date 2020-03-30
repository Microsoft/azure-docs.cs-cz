---
title: Konfigurace pravidel aplikací Azure Firewall pomocí sql fqdns
description: V tomto článku se dozvíte, jak nakonfigurovat sql fqdns v pravidlech aplikací Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501497"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurace pravidel aplikací Azure Firewall pomocí sql fqdns

> [!IMPORTANT]
> Pravidla aplikací Azure Firewall s SQL FQDNs je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nyní můžete nakonfigurovat pravidla aplikací Azure Firewall pomocí sql fqdns. To umožňuje omezit přístup z virtuálních sítí pouze na zadané instance serveru SQL.

Pomocí sql fqdn s můžete filtrovat provoz:

- Z vašich virtuálních sítí do azure sql databáze nebo datového skladu Azure SQL. Příklad: Povolit přístup pouze k *sql-server1.database.windows.net*.
- Z místních na Azure SQL spravované instance nebo SQL IaaS běží ve vašich virtuálních sítích.
- Od paprsku k paprsku až po spravované instance Azure SQL nebo SQL IaaS spuštěné ve vašich virtuálních sítích.

Během veřejné ho náhledu sql fqdn filtrování je podporována pouze v [režimu proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (port 1433). Pokud používáte SQL ve výchozím režimu přesměrování, můžete filtrovat přístup pomocí značky služby SQL jako součást [pravidel sítě](overview.md#network-traffic-filtering-rules).
Pokud používáte jiné než výchozí porty pro přenosy SQL IaaS, můžete tyto porty nakonfigurovat v pravidlech aplikace brány firewall.

Pravidla aplikací s SQL FQDNje aktuálně dostupná ve všech oblastech prostřednictvím portálu Azure, rozhraní API Azure, REST a šablon.

## <a name="configure-using-azure-cli"></a>Konfigurace pomocí rozhraní příkazového příkazu Azure

1. Nasazení [brány Azure Firewall pomocí azure cli](deploy-cli.md).
2. Pokud filtrujete provoz do Azure SQL Database, SQL Data Warehouse nebo spravované instance SQL, ujistěte se, že režim připojení SQL je nastavenna na **proxy server**. Informace o přepnutí režimu připojení SQL najdete v [tématu Nastavení připojení Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > Režim *proxy* SQL může mít za následek větší latenci ve srovnání s *přesměrováním*. Pokud chcete pokračovat v používání režimu přesměrování, který je výchozí pro klienty připojující se v rámci Azure, můžete filtrovat přístup pomocí [značky služby](service-tags.md) SQL v [pravidlech sítě](tutorial-firewall-deploy-portal.md#configure-a-network-rule)brány firewall .

3. Konfigurace pravidla aplikace pomocí sítě SQL FQDN pro povolení přístupu k serveru SQL:

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
1. Nasazení [brány Azure Firewall pomocí azure cli](deploy-cli.md).
2. Pokud filtrujete provoz do Azure SQL Database, SQL Data Warehouse nebo spravované instance SQL, ujistěte se, že režim připojení SQL je nastavenna na **proxy server**. Informace o přepnutí režimu připojení SQL najdete v [tématu Nastavení připojení Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > Režim *proxy* SQL může mít za následek větší latenci ve srovnání s *přesměrováním*. Pokud chcete pokračovat v používání režimu přesměrování, který je výchozí pro klienty připojující se v rámci Azure, můžete filtrovat přístup pomocí [značky služby](service-tags.md) SQL v [pravidlech sítě](tutorial-firewall-deploy-portal.md#configure-a-network-rule)brány firewall .
3. Přidejte pravidlo aplikace s příslušným protokolem, portem a sql fqdn a pak vyberte **Uložit**.
   ![pravidlo aplikace s sql fqdn](media/sql-fqdn-filtering/application-rule-sql.png)
4. Přístup k SQL z virtuálního počítače ve virtuální síti, který filtruje provoz přes bránu firewall. 
5. Ověřte, zda [protokoly brány Azure firewall](log-analytics-samples.md) ukazují, že je povolen provoz.

## <a name="next-steps"></a>Další kroky

Informace o režimech proxy SQL a přesměrování najdete v [tématu Architektura připojení k databázi Azure SQL](../sql-database/sql-database-connectivity-architecture.md).