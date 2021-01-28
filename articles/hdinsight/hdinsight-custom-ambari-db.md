---
title: Vlastní databáze Apache Ambari v Azure HDInsight
description: Naučte se vytvářet clustery HDInsight s vlastní databází Apache Ambari.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: fe38ddc594060c78a2d26e9b25476e38736b4cf7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946047"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Nastavení clusterů HDInsight s vlastní Ambari DB

Apache Ambari zjednodušuje správu a monitorování Apache Hadoopho clusteru. Ambari nabízí snadno použitelné webové uživatelské rozhraní a REST API. Ambari je součástí clusterů HDInsight a používá se k monitorování clusteru a provádění změn konfigurace.

V normálním vytváření clusteru, jak je popsáno v dalších článcích, jako je například [Nastavení clusterů ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md), je Ambari nasazena v [S0 Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) , která je spravována službou HDInsight a není pro uživatele přístupná.

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

- Název databáze nesmí obsahovat spojovníky nebo mezery.
- Musíte mít existující server a databázi Azure SQL DB.
- Databáze, kterou zadáte pro nastavení Ambari, musí být prázdná. Ve výchozím schématu dbo by neměly být žádné tabulky.
- Uživatel, který se používá k připojení k databázi, by měl mít v databázi oprávnění SELECT, CREATE TABLE a INSERT.
- Zapněte možnost [Povolení přístupu ke službám Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) na serveru, kde budete hostovat Ambari.
- V pravidle brány firewall musí být povolená IP adresa pro správu ze služby HDInsight. Seznam IP adres, které se musí přidat do pravidla brány firewall na úrovni serveru, najdete v článku [IP adresy správy HDInsight](hdinsight-management-ip-addresses.md) .

Při hostování databáze Apache Ambari v externí databázi Pamatujte na tyto body:

- Zodpovídáte za další náklady na databázi Azure SQL, která obsahuje Ambari.
- Pravidelně zálohujte svoji vlastní Ambari DB. Azure SQL Database automaticky generuje zálohy, ale časové rámce uchovávání záloh se liší. Další informace najdete v tématu [informace o automatickém zálohování SQL Database](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Nasazení clusterů s vlastní Ambari DB

Pokud chcete vytvořit cluster HDInsight, který používá vaši vlastní externí databázi Ambari, použijte [šablonu pro rychlý Start pro vlastní AMBARI DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Úpravou parametrů v v `azuredeploy.parameters.json` Zadejte informace o novém clusteru a databázi, která bude obsahovat Ambari.

Nasazení můžete zahájit pomocí Azure CLI. Nahraďte `<RESOURCEGROUPNAME>` skupinou prostředků, do které chcete cluster nasadit.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="database-sizing"></a>Velikost databáze

Následující tabulka uvádí pokyny, které úroveň databáze SQL Azure vybere na základě velikosti clusteru HDInsight.

| Počet uzlů pracovního procesu | Požadovaná úroveň databáze |
|---|---|
| <= 4 | S0 |
| >4 && <= 8 | S1 |
| >8 && <= 16 | S2 |
| >16 && <= 32 | S3 |
| >32 && <= 64 | S4 |
| >64 && <= 128 | P2 |
| >128 | Kontaktování podpory |

## <a name="next-steps"></a>Další kroky

- [Použití externích úložišť metadat v Azure HDInsightu](hdinsight-use-external-metadata-stores.md)
