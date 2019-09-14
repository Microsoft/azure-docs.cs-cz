---
title: Povolit automatické vytváření témat v Apache Kafka – Azure HDInsight
description: Naučte se, jak nakonfigurovat Apache Kafka ve službě HDInsight pro automatické vytváření témat. Kafka můžete nakonfigurovat nastavením auto. Create. témats. Enable na hodnotu true prostřednictvím Ambari nebo během vytváření clusteru prostřednictvím šablon PowerShellu nebo Správce prostředků.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 5d990a1c39495090fed1c78f1ddf5e879490e6c4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960666"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Postup konfigurace Apache Kafka v HDInsight pro automatické vytváření témat

Ve výchozím nastavení [Apache Kafka](https://kafka.apache.org/) ve službě HDInsight nepovoluje automatické vytváření témat. Pomocí [Apache Ambari](https://ambari.apache.org/)můžete povolit automatické vytváření témat pro existující clustery. Můžete také povolit automatické vytváření témat při vytváření nového clusteru Kafka pomocí šablony Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Webové uživatelské rozhraní Apache Ambari

Pokud chcete povolit automatické vytváření témat pro existující cluster prostřednictvím webového uživatelského rozhraní Ambari, použijte následující postup:

1. Z [Azure Portal](https://portal.azure.com)vyberte cluster Kafka.

2. V __přehledu clusteru__vyberte __řídicí panel clusteru__. 

    ![Obrázek portálu s vybraným řídicím panelem clusteru](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Pak vyberte __řídicí panel clusteru HDInsight__. Po zobrazení výzvy proveďte ověření pomocí přihlašovacích údajů přihlášení (admin) pro cluster.

    ![Obrázek položky řídicího panelu clusteru HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. V seznamu na levé straně stránky vyberte službu Kafka.

    ![Seznam služeb](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. V polovině stránky vyberte konfigurace.

    ![Karta konfigurace služby](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. Do pole Filtr zadejte hodnotu `auto.create`. 

    ![Obrázek pole filtru](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Tím se vyfiltruje seznam vlastností a zobrazí `auto.create.topics.enable` se nastavení.

6. Změňte hodnotu `auto.create.topics.enable` na `true`a pak vyberte Save (Uložit). Přidejte poznámku a pak znovu vyberte Uložit.

    ![Obrázek automatického vytváření. Create. témata. povolit položku](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Vyberte službu Kafka, vyberte __restartovat__a pak vyberte __restartovat všechny ovlivněné__. Po zobrazení výzvy vyberte __Potvrdit restartování vše__.

    ![Obrázek výběru pro restartování](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Hodnoty Ambari můžete nastavit také pomocí REST API Ambari. To je všeobecně obtížnější, protože je třeba provést více volání REST pro načtení aktuální konfigurace, její úpravy atd. Další informace najdete v tématu [Správa clusterů HDInsight pomocí dokumentu REST API Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Při vytváření clusteru Kafka pomocí šablony Azure Resource Manager lze přímo nastavit `auto.create.topics.enable` přidáním `kafka-broker`do. Následující fragment kódu JSON ukazuje, jak tuto hodnotu nastavit na `true`:

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
