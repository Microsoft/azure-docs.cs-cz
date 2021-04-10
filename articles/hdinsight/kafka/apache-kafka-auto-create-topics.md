---
title: Povolit automatické vytváření témat v Apache Kafka – Azure HDInsight
description: Naučte se, jak nakonfigurovat Apache Kafka ve službě HDInsight pro automatické vytváření témat. Kafka můžete nakonfigurovat nastavením `auto.create.topics.enable` na hodnotu true prostřednictvím Ambari. Nebo během vytváření clusteru prostřednictvím šablon PowerShellu nebo Správce prostředků.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 3766d41959383d802e50aafbf59b9841d1c8d74e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870683"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Postup konfigurace Apache Kafka v HDInsight pro automatické vytváření témat

Ve výchozím nastavení Apache Kafka ve službě HDInsight nepovoluje automatické vytváření témat. Pomocí Apache Ambari můžete povolit automatické vytváření témat pro existující clustery. Můžete také povolit automatické vytváření témat při vytváření nového clusteru Kafka pomocí šablony Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Webové uživatelské rozhraní Apache Ambari

Pokud chcete povolit automatické vytváření témat pro existující cluster prostřednictvím webového uživatelského rozhraní Ambari, použijte následující postup:

1. Z [Azure Portal](https://portal.azure.com)vyberte svůj cluster Kafka.

1. Z **řídicích panelů clusteru** vyberte **Ambari Home (domů**).

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="Obrázek portálu s vybraným řídicím panelem clusteru" border="true":::

    Po zobrazení výzvy proveďte ověření pomocí přihlašovacích údajů přihlášení (admin) pro cluster. Místo toho se můžete připojit k Amabri přímo z `https://CLUSTERNAME.azurehdinsight.net/` místa, kde `CLUSTERNAME` je název vašeho clusteru Kafka.

1. V seznamu na levé straně stránky vyberte službu Kafka.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Karta seznam služeb Apache Ambari" border="true":::

1. V polovině stránky vyberte konfigurace.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Karta konfigurace služby Apache Ambari" border="true":::

1. Do pole Filtr zadejte hodnotu `auto.create` .

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Pole vyhledávacího filtru Apache Ambari" border="true":::

    Toto nastavení filtruje seznam vlastností a zobrazí `auto.create.topics.enable` nastavení.

1. Změňte hodnotu `auto.create.topics.enable` na `true` a pak vyberte **Save (Uložit**). Přidejte poznámku a pak znovu vyberte **Uložit** .

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="Obrázek automatického vytváření. Create. témata. povolit položku" border="true":::

1. Vyberte službu Kafka, vyberte __restartovat__ a pak vyberte __restartovat všechny ovlivněné__. Po zobrazení výzvy vyberte __Potvrdit restartování vše__.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="&quot;Apache Ambari restartovat všechny ovlivněné&quot;" border="true":::

> [!NOTE]  
> Hodnoty Ambari můžete nastavit také pomocí REST API Ambari. To je všeobecně obtížnější, protože je třeba provést více volání REST pro načtení aktuální konfigurace, její úpravy atd. Další informace najdete v tématu [Správa clusterů HDInsight pomocí dokumentu REST API Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Při vytváření clusteru Kafka pomocí šablony Azure Resource Manager lze přímo nastavit `auto.create.topics.enable` přidáním do `kafka-broker` . Následující fragment kódu JSON ukazuje, jak tuto hodnotu nastavit na `true` :

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

* [Analýza protokolů Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi Apache Kafka clustery](apache-kafka-mirroring.md)
