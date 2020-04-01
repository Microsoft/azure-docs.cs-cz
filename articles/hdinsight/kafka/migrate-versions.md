---
title: Migrace úloh Apache Kafka do Azure HDInsight 4.0
description: Přečtěte si, jak migrovat úlohy Apache Kafka na HDInsight 3.6 na HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437016"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrace úloh Apache Kafka do Azure HDInsight 4.0

Azure HDInsight 4.0 nabízí nejnovější open source komponenty s výraznými vylepšeními výkonu, konektivity a zabezpečení. Tento dokument vysvětluje, jak migrovat úlohy Apache Kafka na HDInsight 3.6 na HDInsight 4.0. Po migraci úloh do HDInsight 4.0 můžete použít mnoho nových funkcí, které nejsou k dispozici na HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Cesty migrace HDInsight 3.6 Kafka

HDInsight 3.6 podporuje dvě verze Kafky: 1.0.0 a 1.1.0. HDInsight 4.0 podporuje verze 1.1.0 a 2.1.0. V závislosti na tom, kterou verzi Kafky a jakou verzi HDInsight chcete spustit, existuje několik podporovaných cest migrace. Tyto cesty jsou vysvětleny níže a znázorněny v následujícím diagramu.

* **Spusťte aplikaci Kafka i HDInsight v nejnovějších verzích (doporučeno):** Migrujte aplikaci HDInsight 3.6 a Kafka 1.0 nebo 1.1.0 na HDInsight 4.0 s Kafka 2.1.0 (cesty D a E níže).
* **Spusťte HDInsight na nejnovější verzi, ale Kafka pouze na novější verzi**: Migrovat HDInsight 3.6 a Kafka 1.0.0 aplikace HDInsight 4.0 s Kafka 1.1.0 (cesta B níže).
* **Spusťte HDInsight na nejnovější verzi, zachovat verzi Kafka**: Migrovat HDInsight 3.6 a Kafka 1.1.0 aplikace HDInsight 4.0 s Kafka 1.1.0 (cesta C níže).
* **Spusťte Kafka na novější verzi, zachovat HDInsight verze**: Migrovat aplikaci Kafka 1.0.0 na 1.1.0 a zůstat na HDInsight 3.6 (cesta Níže). Všimněte si, že tato možnost bude stále vyžadovat nasazení nového clusteru. Inovace verze Kafka v existujícím clusteru není podporována. Po vytvoření clusteru s požadovanou verzí migrujte klienty Kafka a použijte nový cluster.

![Upgrade cesty pro Apache Kafka na 3,6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka verze

### <a name="kafka-110"></a>Kafka 1,1,0
  
Pokud migrujete z Kafky 1.0.0 do 1.1.0, můžete využít následující nové funkce:

* Vylepšení řadiče Kafka urychlují řízené vypnutí, takže můžete restartovat makléře a rychleji se zotavit z problémů. 
* Vylepšení [fetchrequests logiky,](https://issues.apache.org/jira/browse/KAFKA-6254) které umožňují mít více oddílů (a tedy další témata) v clusteru. 
* Kafka Connect podporuje [záhlaví záznamů](https://issues.apache.org/jira/browse/KAFKA-5142) a [regulární výrazy](https://issues.apache.org/jira/browse/KAFKA-3073) pro témata. 

Úplný seznam aktualizací naleznete v tématu [Apache Kafka 1.1 poznámky k verzi](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Pokud migrujete na Kafka 2.1, můžete využít následující funkce:

* Lepší odolnost zprostředkovatele z důvodu vylepšeného replikačního protokolu.
* Nové funkce v rozhraní API klienta KafkaAdminClient.
* Konfigurovatelná správa kvót.
* Podpora komprese Zstandard.

Úplný seznam aktualizací najdete v tématu [Apache Kafka 2.0 poznámky k verzi](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) a Apache [Kafka 2.1 poznámky k verzi](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Kompatibilita s klientem Kafka

Noví makléři Kafka podporují starší klienty. [KIP-35 - Načtení verze protokolu](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) zavedla mechanismus pro dynamické určení funkčnosti zprostředkovatele Kafka a [KIP-97: Vylepšené zásady kompatibility klienta Kafka](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) zavedly nové zásady kompatibility a záruky pro klienta Java. Dříve musel klient Kafka komunikovat s makléřem stejné verze nebo novější verze. Nyní novější verze klientů Java a dalších klientů, kteří podporují `librdkafka` KIP-35, jako je například může vrátit ke starším typům požadavků nebo vyvolat příslušné chyby, pokud funkce není k dispozici.

![Upgrade kompatibility klientů Kafka](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Všimněte si, že to neznamená, že klient podporuje starší makléři.  Další informace naleznete v [tématu Compatibility Matrix](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Obecný proces migrace

Následující pokyny pro migraci předpokládají cluster Apache Kafka 1.0 nebo 1.1.0 nasazený na HDInsight 3.6 v jedné virtuální síti. Stávající makléř má některá témata a aktivně je využíván výrobci a spotřebiteli.

![Aktuální kafka předpokládané prostředí](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Migraci můžete dokončit následujícím postupem:

1. **Nasaďte nový cluster HDInsight 4.0 a klienty pro testování.** Nasaďte nový cluster HDInsight 4.0 Kafka. Pokud lze vybrat více verzí clusteru Kafka, doporučujeme vybrat nejnovější verzi. Po nasazení nastavte některé parametry podle potřeby a vytvořte téma se stejným názvem jako vaše stávající prostředí. Také nastavte TLS a přineste si vlastní klíč (BYOK) šifrování podle potřeby. Pak zkontrolujte, zda funguje správně s novým clusterem.

    ![Nasazení nových clusterů HDInsight 4.0](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Přepněte cluster pro výrobce aplikace a počkejte, dokud všechna data fronty je spotřebována aktuální spotřebitelé.** Když je nový cluster HDInsight 4.0 Kafka připraven, přepněte existující cíl výrobce do nového clusteru. Ponechte ji tak, jak je, dokud existující aplikace Příjemce nespotřebuje všechna data z existujícího clusteru.

    ![Přepnout cluster pro aplikaci pro výrobce](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Přepněte cluster v aplikaci příjemce.** Po potvrzení, že existující aplikace příjemce dokončila využití všech dat z existujícího clusteru, přepněte připojení do nového clusteru.

    ![Přepnutí clusteru v aplikaci pro spotřebitele](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Podle potřeby odeberte staré clustery a otestujte aplikace.** Po dokončení přepínače a správné fungování, odeberte staré HDInsight 3.6 Kafka clusteru a výrobci a spotřebitelé používají v testu podle potřeby.

## <a name="next-steps"></a>Další kroky

* [Optimalizace výkonu pro clustery Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Úvodní příručka: Vytvoření clusteru Apache Kafka v Azure HDInsight pomocí portálu Azure](apache-kafka-get-started.md)
