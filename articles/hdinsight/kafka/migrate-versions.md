---
title: Migrace úloh Apache Kafka do Azure HDInsight 4,0
description: Naučte se migrovat Apache Kafka úlohy ve službě HDInsight 3,6 do HDInsight 4,0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: e15ebb13aee0e5dd814688ae77edaded667d54ac
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864121"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrace úloh Apache Kafka do Azure HDInsight 4,0

Azure HDInsight 4,0 nabízí nejnovější Open-Source komponenty se značnými vylepšeními výkonu, připojení a zabezpečení. Tento dokument vysvětluje, jak migrovat Apache Kafka úlohy ve službě HDInsight 3,6 do HDInsight 4,0. Po migraci úloh do HDInsight 4,0 můžete použít řadu nových funkcí, které nejsou dostupné ve službě HDInsight 3,6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Cesty migrace Kafka HDInsight 3,6

HDInsight 3,6 podporuje dvě verze Kafka: 1.0.0 a 1.1.0. HDInsight 4,0 podporuje verze 1.1.0 a 2.1.0. V závislosti na verzi Kafka a verzi HDInsight, kterou chcete spustit, je k dispozici několik podporovaných cest migrace. Tyto cesty jsou vysvětleny níže a jsou znázorněny v následujícím diagramu.

* **Spouštějte oba Kafka i HDInsight na nejnovějších verzích (doporučeno)**: Migrace aplikace HDInsight 3,6 a Kafka 1.0.0 nebo 1.1.0 na HDInsight 4,0 with Kafka 2.1.0 (cesty D a E níže).
* **Spusťte HDInsight na nejnovější verzi, ale Kafka jenom na novější verzi**: migrujte aplikaci HDInsight 3,6 a Kafka 1.0.0 do HDInsight 4,0 s Kafka 1.1.0 (cesta B níže).
* **Spusťte HDInsight na nejnovější verzi, zachovejte verzi Kafka**: migrujte aplikaci HDInsight 3,6 a Kafka 1.1.0 do HDInsight 4,0 s Kafka 1.1.0 (cesta C níže).
* **Spusťte Kafka na novější verzi, zachovejte verzi HDInsight**: migrujte aplikaci Kafka 1.0.0 na 1.1.0 a zůstaňte v HDInsight 3,6 (Path a níže). Všimněte si, že tato možnost bude stále vyžadovat nasazení nového clusteru. Upgrade verze Kafka v existujícím clusteru není podporován. Po vytvoření clusteru s požadovanou verzí migrujte klienty Kafka na použití nového clusteru.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png" alt-text="Cesty upgradu pro Apache Kafka v 3,6" border="false":::

## <a name="apache-kafka-versions"></a>Verze Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
Pokud migrujete z Kafka 1.0.0 na 1.1.0, můžete využít výhod následujících nových funkcí:

* Vylepšení rychlosti řadiče Kafka řízené vypnutím, takže můžete znovu spustit zprostředkovatele a vyřešit problémy rychleji. 
* Vylepšení [logiky FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) , která vám umožní mít v clusteru více oddílů (a tedy i další témata). 
* Kafka Connect podporuje pro témata [záhlaví záznamů](https://issues.apache.org/jira/browse/KAFKA-5142) a [regulární výrazy](https://issues.apache.org/jira/browse/KAFKA-3073) . 

Úplný seznam aktualizací najdete v [poznámkách k verzi Apache Kafka 1,1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Pokud migrujete na Kafka 2,1, můžete využít výhod následujících funkcí:

* Lepší odolnost zprostředkovatele díky vylepšení replikačního protokolu.
* Nové funkce rozhraní KafkaAdminClient API.
* Konfigurovatelná správa kvót
* Podpora pro Zstandard kompresi.

Úplný seznam aktualizací najdete v [poznámkách k verzi Apache Kafka 2,0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) a zpráva k [vydání verze Apache Kafka 2,1](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Kompatibilita klienta Kafka

Noví zprostředkovatelé Kafka podporují starší klienty. [Eskočit-35 – načtená verze protokolu](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) představila mechanizmus pro dynamické určování funkcí služby Kafka Broker a [eskočit-97: vylepšené zásady kompatibility klienta služby RPC pro Kafka](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) zavedly novou zásadu kompatibility a záruky pro klienta Java. Dřív musel klient Kafka komunikovat se zprostředkovatelem stejné verze nebo novější verze. Nyní novější verze klientů Java a dalších klientů, které podporují ESKOČIT-35, například `librdkafka` mohou přejít na starší typy požadavků nebo vyvolat vhodné chyby, pokud nejsou k dispozici funkce.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png" alt-text="Upgrade kompatibility klienta Kafka" border="false":::

Všimněte si, že to neznamená, že klient podporuje starší zprostředkovatele.  Další informace najdete v tématu [věnovaném kompatibilitě](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Obecný proces migrace

Následující pokyny k migraci předpokládají, Apache Kafka cluster 1.0.0 nebo 1.1.0 nasazený v HDInsight 3,6 v jedné virtuální síti. Stávající zprostředkovatel má některá témata a aktivně ho používají producenti a spotřebitelé.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png" alt-text="Aktuální předpokládané prostředí Kafka" border="false":::

K dokončení migrace proveďte následující kroky:

1. **Nasaďte nový cluster HDInsight 4,0 a klienty pro testování.** Nasaďte nový cluster HDInsight 4,0 Kafka. Pokud je možné vybrat více verzí clusteru Kafka, doporučuje se vybrat nejnovější verzi. Po nasazení nastavte některé parametry podle potřeby a vytvořte téma se stejným názvem, jako má vaše existující prostředí. V případě potřeby můžete také nastavit šifrování TLS a vlastní klíč (BYOK). Pak zkontrolujte, jestli funguje s novým clusterem správně.

    :::image type="content" source="./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png" alt-text="Nasazení nových clusterů HDInsight 4,0" border="false":::

1. **Přepněte cluster pro aplikaci producenta a počkejte, až budou všechna data fronty spotřebovaná aktuálními příjemci.** Po přípravě nového clusteru HDInsight 4,0 Kafka přepněte existující cíl pro producenta na nový cluster. Ponechte to tak, jak je, dokud stávající aplikace příjemce nevyužila všechna data z existujícího clusteru.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-producer-app.png" alt-text="Přepnout cluster pro aplikaci producenta" border="false":::

1. **Přepněte cluster na aplikaci příjemce.** Po potvrzení, že stávající aplikace příjemce dokončí využívání všech dat z existujícího clusteru, přepněte připojení k novému clusteru.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png" alt-text="Přepnout cluster na aplikaci příjemce" border="false":::

1. **Odeberte původní cluster a otestujte aplikace podle potřeby.** Jakmile je přepínač dokončen a funguje správně, odeberte starý cluster HDInsight 3,6 Kafka a producenti a příjemce, kteří se v testu použili, podle potřeby.

## <a name="next-steps"></a>Další kroky

* [Optimalizace výkonu pro clustery Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Rychlý Start: Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí Azure Portal](apache-kafka-get-started.md)
