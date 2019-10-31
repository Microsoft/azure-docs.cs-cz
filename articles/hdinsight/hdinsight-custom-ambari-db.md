---
title: Vlastní databáze Apache Ambari v Azure HDInsight
description: Naučte se vytvářet clustery HDInsight s vlastní databází Apache Ambari.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: 8064fd5369e55ea223a697d30d7643ff5407cf76
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73066000"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Nastavení clusterů HDInsight s vlastní Ambari DB

Apache Ambari zjednodušuje správu a monitorování Apache Hadoopho clusteru. Ambari nabízí snadno použitelné webové uživatelské rozhraní a REST API. Ambari je součástí clusterů HDInsight a používá se k monitorování clusteru a provádění změn konfigurace.

V normálním vytváření clusteru, jak je popsáno v dalších článcích, jako je například [Nastavení clusterů ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md), je Ambari nasazena v [S0 databázi SQL Azure](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) spravované službou HDInsight a není přístupná pro uživatele.

Funkce vlastní Ambari DB umožňuje nasadit nový cluster a nastavit Ambari v externí databázi, kterou spravujete. Nasazení se provádí pomocí šablony Azure Resource Manager. Tato funkce má následující výhody:

- Přizpůsobení – zvolíte velikost a kapacitu zpracování databáze. Pokud máte velké clustery zpracování náročných úloh, může být databáze Ambari s nižšími specifikacemi kritickým bodem pro operace správy.
- Flexibilita – databázi můžete škálovat podle potřeby, aby vyhovovala vašim požadavkům.
- Řízení – zálohování a zabezpečení databáze můžete spravovat způsobem, který vyhovuje vašim potřebám vaší organizace.

Zbývající část tohoto článku popisuje následující body:

- požadavky na používání vlastní funkce Ambari DB
- kroky nezbytné ke zřízení clusterů HDInsight s využitím vlastní externí databáze pro Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Požadavky na vlastní Ambari DB

Můžete nasadit vlastní Ambari DB se všemi typy a verzemi clusteru. Víc clusterů nemůže používat stejnou Ambari DB.

Vlastní Ambari DB má následující požadavky:

- Musíte mít existující server a databázi Azure SQL DB.
- Databáze, kterou zadáte pro nastavení Ambari, musí být prázdná. Ve výchozím schématu dbo by neměly být žádné tabulky.
- Uživatel, který se používá k připojení k databázi, by měl mít v databázi oprávnění SELECT, CREATE TABLE a INSERT.
- Zapněte možnost [Povolení přístupu ke službám Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) na serveru Azure SQL, na kterém budete hostovat Ambari.
- V SQL Server musí být povolené IP adresy pro správu ze služby HDInsight. Seznam IP adres, které je nutné přidat do brány firewall systému SQL Server, najdete v části [IP adresy správy HDInsight](hdinsight-management-ip-addresses.md) .

Při hostování databáze Apache Ambari v externí databázi Pamatujte na tyto body:

- Zodpovídáte za další náklady na databázi Azure SQL, která obsahuje Ambari.
- Pravidelně zálohujte svoji vlastní Ambari DB. Azure SQL Database automaticky generuje zálohy, ale časové rámce uchovávání záloh se liší. Další informace najdete v tématu [informace o automatickém zálohování SQL Database](../sql-database/sql-database-automated-backups.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Nasazení clusterů s vlastní Ambari DB

Pokud chcete vytvořit cluster HDInsight, který používá vaši vlastní externí databázi Ambari, použijte [šablonu pro rychlý Start pro vlastní AMBARI DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Úpravou parametrů v `azuredeploy.parameters.json` zadejte informace o novém clusteru a databázi, která bude obsahovat Ambari.

Nasazení můžete zahájit pomocí Azure CLI. Místo `<RESOURCEGROUPNAME>` nahraďte skupinou prostředků, do které chcete cluster nasadit.

```azure-cli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Další kroky

- [Použití externích úložišť metadat v Azure HDInsightu](hdinsight-use-external-metadata-stores.md)