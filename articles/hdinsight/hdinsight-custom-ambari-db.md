---
title: Vlastní databáze Apache Ambari v Azure HDInsight
description: Přečtěte si, jak vytvářet clustery HDInsight pomocí vlastní databáze Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240158"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Nastavení clusterů HDInsight pomocí vlastní databáze Ambari DB

Apache Ambari zjednodušuje správu a monitorování clusteru Apache Hadoop. Ambari poskytuje snadno použitelné webové uživatelské rozhraní a REST API. Ambari je součástí clusterů HDInsight a používá se ke sledování clusteru a provádění změn konfigurace.

Při normálním vytváření clusteru, jak je popsáno v jiných článcích, jako je [nastavení clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md), ambari se nasadí v [databázi S0 Azure SQL,](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) která je spravována HDInsight a není přístupná uživatelům.

Vlastní funkce Ambari DB umožňuje nasadit nový cluster a nastavit Ambari v externí databázi, kterou spravujete. Nasazení se provádí pomocí šablony Azure Resource Manager. Tato funkce má následující výhody:

- Přizpůsobení - zvolíte velikost a kapacitu zpracování databáze. Pokud máte velké clustery, které zpracovávají náročné úlohy, databáze Ambari s nižšími specifikacemi se může stát kritickým bodem pro operace správy.
- Flexibilita - databázi můžete škálovat podle potřeby tak, aby vyhovovala vašim požadavkům.
- Řízení – můžete spravovat zálohy a zabezpečení databáze způsobem, který odpovídá požadavkům vaší organizace.

Zbývající část tohoto článku popisuje následující body:

- požadavky na použití vlastní funkce Ambari DB
- kroky nezbytné pro zřízení clusterů HDInsight pomocí vlastní externí databáze pro Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Vlastní požadavky Ambari DB

Můžete nasadit vlastní Ambari DB se všemi typy clusterů a verzemi. Více clusterů nemůže používat stejnou databázi Ambari DB.

Vlastní Ambari DB má následující další požadavky:

- Musíte mít existující server Azure SQL DB a databázi.
- Databáze, kterou zadáte pro nastavení Ambari, musí být prázdná. Ve výchozím schématu dbo by neměly být žádné tabulky.
- Uživatel, který se používá pro připojení k databázi, by měl mít v databázi oprávnění SELECT, CREATE TABLE a INSERT.
- Zapněte možnost [Povolit přístup ke službám Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) na serveru Azure SQL, kde budete hostovat Ambari.
- Na serveru SQL Server musí být povoleny adresy IP služby pro správu ze služby HDInsight. Seznam adres IP, které musí být přidány do brány firewall serveru SQL, naleznete [v části IP adresy pro správu HDInsight.](hdinsight-management-ip-addresses.md)

Když hostujete Apache Ambari DB v externí databázi, nezapomeňte na následující body:

- Jste zodpovědní za dodatečné náklady na Azure SQL DB, který drží Ambari.
- Pravidelně zálohujte vlastní Ambari DB. Azure SQL Database generuje zálohy automaticky, ale časový rámec uchovávání záloh se liší. Další informace naleznete v tématu [Další informace o automatickém zálohování databáze SQL](../sql-database/sql-database-automated-backups.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Nasazení clusterů s vlastní ambariovou db

Chcete-li vytvořit cluster HDInsight, který používá vlastní externí databázi Ambari, použijte [vlastní šablonu ambari db quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Upravte parametry `azuredeploy.parameters.json` v a určete informace o novém clusteru a databázi, ve které bude ambari.

Nasazení můžete zahájit pomocí příkazového příkazu k příkazu Azure. Nahraďte `<RESOURCEGROUPNAME>` skupinou prostředků, do které chcete cluster nasadit.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Další kroky

- [Použití externích úložišť metadat v Azure HDInsightu](hdinsight-use-external-metadata-stores.md)