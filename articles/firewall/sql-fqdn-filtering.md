---
title: Konfigurace pravidel aplikace Brána Firewall služby Azure s SQL plně kvalifikovaných názvů domén
description: V tomto článku se dozvíte, jak nakonfigurovat SQL plně kvalifikovaných názvů domén v pravidlech brány Firewall Azure aplikace.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786585"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurace pravidel aplikace Brána Firewall služby Azure s SQL plně kvalifikovaných názvů domén

> [!IMPORTANT]
> Azure application pravidla brány Firewall pomocí plně kvalifikovaných názvů domén SQL je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Teď můžete nakonfigurovat pravidla brány Firewall Azure aplikace s SQL plně kvalifikovaných názvů domény. To umožňuje omezit přístup z vašich virtuálních sítí na pouze Zadaná instance systému SQL server.

S SQL plně kvalifikované názvy domény můžete filtrovat provoz:

- Z vaší virtuální sítě k Azure SQL Database nebo službu Azure SQL Data Warehouse. Příklad: Povolit jenom přístup k *sql server1.database.windows.net*.
- Z místních do spravované instance SQL Azure nebo SQL IaaS spuštěná ve virtuálních sítích.
- Z paprsku k paprsek do spravované instance SQL Azure nebo SQL IaaS spuštěná ve virtuálních sítích.

Ve verzi public preview, je podporován SQL, plně kvalifikovaný název domény filtrování [režim proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) jenom (port 1433). Pokud používáte SQL ve výchozím režimu přesměrování, můžete filtrovat přístupu pomocí značka služby SQL jako součást [pravidla síťových](overview.md#network-traffic-filtering-rules).
Pokud používáte jiné než výchozí čísla portů pro provoz SQL IaaS, můžete nakonfigurovat tyto porty v pravidlech brány firewall aplikací.

> [!NOTE]
> Pravidla aplikací s plně kvalifikované názvy domény SQL je teď dostupná v všech oblastech prostřednictvím rozhraní příkazového řádku Azure, rozhraní REST a šablony. Portálu uživatelského rozhraní je přidáte do oblastí postupně a budou k dispozici ve všech oblastech po dokončení tohoto uvedení.

## <a name="configure-using-azure-cli"></a>Konfigurace pomocí rozhraní příkazového řádku Azure

1. Nasazení [Brána Firewall služby Azure pomocí Azure CLI](deploy-cli.md).
2. Pokud můžete filtrovat provoz do Azure SQL Database, SQL Data Warehouse nebo spravované Instance SQL, zkontrolujte režim připojení SQL je nastavená na **Proxy**. Zjistěte, jak přepnout režim připojení SQL, najdete v článku [architektura připojení SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* režimu může způsobit další latenci v porovnání s *přesměrování*. Pokud chcete dál používat režim přesměrování, což je výchozí nastavení pro klienty připojující se v rámci Azure, můžete filtrovat přístupu pomocí SQL [značka služby](service-tags.md) v bráně firewall [pravidla síťových](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Konfigurace pravidla aplikace s SQL plně kvalifikovaný název domény chcete povolit přístup k systému SQL server:

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurovat pomocí webu Azure portal
1. Nasazení [Brána Firewall služby Azure pomocí Azure CLI](deploy-cli.md).
2. Pokud můžete filtrovat provoz do Azure SQL Database, SQL Data Warehouse nebo spravované Instance SQL, zkontrolujte režim připojení SQL je nastavená na **Proxy**. Zjistěte, jak přepnout režim připojení SQL, najdete v článku [architektura připojení SQL Azure](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* režimu může způsobit další latenci v porovnání s *přesměrování*. Pokud chcete dál používat režim přesměrování, což je výchozí nastavení pro klienty připojující se v rámci Azure, můžete filtrovat přístupu pomocí SQL [značka služby](service-tags.md) v bráně firewall [pravidla síťových](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Přidejte pravidlo brány application s odpovídající protokol, port a plně kvalifikovaný název domény SQL a potom vyberte **Uložit**.
   ![pravidlo brány Application s plně kvalifikovaný název domény SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Přístup SQL z virtuálního počítače ve virtuální síti, která filtruje provoz přes bránu firewall. 
5. Ověřit, zda [protokoly brány Firewall Azure](log-analytics-samples.md) Zobrazit provoz je povolený.

## <a name="next-steps"></a>Další postup

Další informace o proxy serveru SQL a přesměrovat režimech najdete v tématu [architektura připojení k databázi Azure SQL](../sql-database/sql-database-connectivity-architecture.md).