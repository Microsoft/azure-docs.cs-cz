---
title: Povolit automatické tématu Vytvoření v Apache Kafka - Azure HDInsight | Microsoft Docs
description: Naučte se konfigurovat Apache Kafka v HDInsight pro automatické vytvoření témata. Kafka můžete nakonfigurovat nastavení auto.create.topics.enable na hodnotu true pomocí Ambari nebo při vytváření clusteru pomocí prostředí PowerShell nebo správce prostředků šablony.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: larryfr
ms.openlocfilehash: fa5dd7533259c794671cd16231fd3f530173bfa3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Postup konfigurace Apache Kafka v HDInsight pro automatické vytvoření témata

Ve výchozím nastavení Kafka v HDInsight neumožňuje vytvoření automatického tématu. Můžete povolit automatické vytváření tématu pro stávajících clusterů pomocí nástroje Ambari. Můžete také povolit automatické vytváření tématu při vytváření nového clusteru Kafka pomocí šablony Azure Resource Manager.

## <a name="ambari-web-ui"></a>Ambari Web UI

Pokud chcete povolit automatické tématu Vytvoření u stávajícího clusteru prostřednictvím webového uživatelského rozhraní Ambari, použijte následující kroky:

1. Z [portál Azure](https://portal.azure.com), vyberte cluster, Kafka.

2. Z __clusteru přehled__, vyberte __řídicí panel clusteru__. 

    ![Obrázek portálu s vybraný řídicí panel clusteru](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Potom vyberte __řídicí panel clusteru HDInsight__. Po zobrazení výzvy, proveďte ověření pomocí přihlašovacích (správce) pro cluster.

    ![Obrázek položky řídicí panel clusteru HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Vyberte službu Kafka ze seznamu na levé straně stránky.

    ![Seznam služeb](./media/apache-kafka-auto-create-topics/service-list.png)

4. Vyberte konfigurací uprostřed stránky.

    ![Karta Konfigurace služby](./media/apache-kafka-auto-create-topics/service-config.png)

5. Do pole filtru zadejte hodnotu `auto.create`. 

    ![Obrázek pole filtru](./media/apache-kafka-auto-create-topics/filter.png)

    Tím se odfiltrují seznam vlastností a zobrazí `auto.create.topics.enable` nastavení.

6. Změňte hodnotu `auto.create.topics.enable` k `true`a potom vyberte Uložit. Přidat poznámku a pak vyberte uložit znovu.

    ![Obrázek položky auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Vyberte službu Kafka, vyberte __restartujte__a potom vyberte __restartujte všechny zasažené__. Po zobrazení výzvy vyberte __potvrdit restartujte__.

    ![Obrázek výběru restartování](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> Můžete také nastavit hodnoty Ambari přes rozhraní Ambari REST API. Toto je obecně obtížnější, jako je nutné provést několik volání REST načíst aktuální konfiguraci, upravit jej a podobně. Další informace najdete v tématu [Správa clusterů HDInsight pomocí Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentu.

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Při vytváření clusteru Kafka pomocí šablony Azure Resource Manager, můžete přímo nastavit `auto.create.topics.enable` tak, že ho přidáte `kafka-broker`. Následující fragment kódu JSON ukazuje, jak nastavit hodnotu `true`:

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

V tomto dokumentu jste zjistili, jak můžete povolit automatické tématu vytváření pro Kafka v HDInsight. Další informace o práci s Kafka najdete v následujících tématech:

* [Analýza protokolů Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Kafka](apache-kafka-mirroring.md)