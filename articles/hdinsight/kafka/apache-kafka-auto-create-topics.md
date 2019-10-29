---
title: Povolit automatické vytváření témat v Apache Kafka – Azure HDInsight
description: Naučte se, jak nakonfigurovat Apache Kafka ve službě HDInsight pro automatické vytváření témat. Kafka můžete nakonfigurovat nastavením auto. Create. témats. Enable na hodnotu true prostřednictvím Ambari nebo během vytváření clusteru prostřednictvím šablon PowerShellu nebo Správce prostředků.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 1bcb433230af856e92c7e418fc81b35bea549ddf
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987988"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Postup konfigurace Apache Kafka v HDInsight pro automatické vytváření témat

Ve výchozím nastavení [Apache Kafka](https://kafka.apache.org/) ve službě HDInsight nepovoluje automatické vytváření témat. Pomocí [Apache Ambari](https://ambari.apache.org/)můžete povolit automatické vytváření témat pro existující clustery. Můžete také povolit automatické vytváření témat při vytváření nového clusteru Kafka pomocí šablony Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Webové uživatelské rozhraní Apache Ambari

Pokud chcete povolit automatické vytváření témat pro existující cluster prostřednictvím webového uživatelského rozhraní Ambari, použijte následující postup:

1. Z [Azure Portal](https://portal.azure.com)vyberte svůj cluster Kafka.

1. Z **řídicích panelů clusteru**vyberte **Ambari Home (domů**).

    ![Obrázek portálu s vybraným řídicím panelem clusteru](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Po zobrazení výzvy proveďte ověření pomocí přihlašovacích údajů přihlášení (admin) pro cluster. Případně se můžete připojit k Amabri přímo z `https://CLUSTERNAME.azurehdinsight.net/`, kde `CLUSTERNAME` je název vašeho clusteru Kafka.

1. V seznamu na levé straně stránky vyberte službu Kafka.

    ![Karta seznam služeb Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. V polovině stránky vyberte konfigurace.

    ![Karta konfigurace služby Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. Do pole Filtr zadejte hodnotu `auto.create`.

    ![Pole vyhledávacího filtru Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Tím se vyfiltruje seznam vlastností a zobrazí se nastavení `auto.create.topics.enable`.

1. Změňte hodnotu `auto.create.topics.enable` na `true`a pak vyberte **Save (Uložit**). Přidejte poznámku a pak znovu vyberte **Uložit** .

    ![Obrázek automatického vytváření. Create. témata. povolit položku](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Vyberte službu Kafka, vyberte __restartovat__a pak vyberte __restartovat všechny ovlivněné__. Po zobrazení výzvy vyberte __Potvrdit restartování vše__.

    ![Ambari restartování Apache All ovlivnilo](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Hodnoty Ambari můžete nastavit také pomocí REST API Ambari. To je všeobecně obtížnější, protože je třeba provést více volání REST pro načtení aktuální konfigurace, její úpravy atd. Další informace najdete v tématu [Správa clusterů HDInsight pomocí dokumentu REST API Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Šablony Správce prostředků

Při vytváření clusteru Kafka pomocí šablony Azure Resource Manager můžete `auto.create.topics.enable` nastavit přímo tak, že ho přidáte do `kafka-broker`. Následující fragment kódu JSON ukazuje, jak nastavit tuto hodnotu na `true`:

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

V tomto dokumentu jste zjistili, jak povolit automatické vytváření témat pro Apache Kafka ve službě HDInsight. Další informace o práci s Kafka najdete na následujících odkazech:

* [Analyzovat protokoly Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi Apache Kafka clustery](apache-kafka-mirroring.md)
