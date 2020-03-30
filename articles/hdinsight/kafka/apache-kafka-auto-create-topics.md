---
title: Povolení automatického vytváření témat v Apache Kafka – Azure HDInsight
description: Přečtěte si, jak nakonfigurovat Apache Kafka na HDInsight automaticky vytvářet témata. Kafka můžete nakonfigurovat nastavením funkce auto.create.topics.enable na hodnotu true prostřednictvím aplikace Ambari nebo při vytváření clusteru prostřednictvím šablon prostředí PowerShell nebo Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242359"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Jak nakonfigurovat Apache Kafka na HDInsight automaticky vytvářet témata

Ve výchozím nastavení [Apache Kafka](https://kafka.apache.org/) na HDInsight neumožňuje automatické vytváření témat. Můžete povolit automatické vytváření témat pro existující clustery pomocí [Apache Ambari](https://ambari.apache.org/). Automatické vytváření témat můžete povolit také při vytváření nového clusteru Kafka pomocí šablony Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Webové uživatelské uzly Apache Ambari

Chcete-li povolit automatické vytváření témat v existujícím clusteru prostřednictvím webového uživatelského uživatelského uživatelského nastavení Ambari, postupujte takto:

1. Na [portálu Azure](https://portal.azure.com)vyberte cluster Kafka.

1. V **řídicích panelech clusteru**vyberte **možnost Ambari home**.

    ![Obrázek portálu s vybranou řídicí deskou clusteru](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Po zobrazení výzvy se ověřte pomocí přihlašovacích (administrativních) přihlašovacích údajů pro cluster. Případně se můžete připojit k Amabri přímo z `https://CLUSTERNAME.azurehdinsight.net/` místa, kde `CLUSTERNAME` je název vašeho clusteru Kafka.

1. Ze seznamu na levé straně stránky vyberte službu Kafka.

    ![Karta Seznam služeb Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Uprostřed stránky vyberte Konfigurace.

    ![Karta konfigurace služby Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. Do pole Filtr zadejte `auto.create`hodnotu .

    ![Pole vyhledávacího filtru Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Tím se vyfiltruje `auto.create.topics.enable` seznam vlastností a zobrazí se nastavení.

1. Změňte hodnotu položky `auto.create.topics.enable` `true`a pak vyberte **uložit**. Přidejte poznámku a pak znovu vyberte **Uložit.**

    ![Obrázek položky auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Vyberte službu Kafka, vyberte __Restartovat__a pak vyberte __Restartovat všechny ovlivněné__. Po zobrazení výzvy vyberte __Potvrdit restart all__.

    ![Apache Ambari restartovat všechny ovlivněny](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Hodnoty Ambari můžete také nastavit prostřednictvím rozhraní Ambari REST API. To je obecně obtížnější, protože je nutné provést více volání REST k načtení aktuální konfigurace, úpravě atd. Další informace najdete v [tématu Správa clusterů HDInsight pomocí dokumentu Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Při vytváření clusteru Kafka pomocí šablony Azure Resource `auto.create.topics.enable` Manager, můžete `kafka-broker`přímo nastavit přidáním v . Následující úryvek JSON ukazuje, jak nastavit `true`tuto hodnotu na :

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak povolit automatické vytváření témat pro Apache Kafka na HDInsight. Další informace o práci s Kafkou najdete v následujících odkazech:

* [Analýza protokolů Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Apache Kafka](apache-kafka-mirroring.md)
