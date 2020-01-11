---
title: HBA hbck vrátí nekonzistence ve službě Azure HDInsight.
description: HBA hbck vrátí nekonzistence ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887321"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scénář: příkaz `hbase hbck` vrátí nekonzistence ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problém: oblast není v `hbase:meta`

Oblast XXX na HDFS, ale není uvedená v `hbase:meta` nebo nasazená na žádném serveru oblasti.

### <a name="cause"></a>Příčina

Se liší.

### <a name="resolution"></a>Rozlišení

1. Opravte meta tabulku spuštěním:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Přiřaďte oblasti k RegionServers spuštěním:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problém: oblast je offline.

Oblast XXX není nasazená na žádném RegionServer. To znamená, že oblast je ve `hbase:meta`, ale je offline.

### <a name="cause"></a>Příčina

Se liší.

### <a name="resolution"></a>Rozlišení

Doveďte oblasti online spuštěním:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problém: regiony mají stejné počáteční a koncové klíče.

### <a name="cause"></a>Příčina

Se liší.

### <a name="resolution"></a>Rozlišení

Ručně sloučí tyto překryté oblasti. V části přejít na HMaster web UI tabulka webové uživatelské rozhraní vyberte odkaz tabulka, který má problém. Zobrazí se počáteční klíč/koncový klíč každé oblasti patřící do této tabulky. Pak tyto překryté oblasti slučte. V prostředí HBA proveďte `merge_region 'xxxxxxxx','yyyyyyy', true`. Příklad:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

V tomto scénáři je potřeba sloučit oblast a RegionC a získat v oblasti stejný rozsah klíčů jako RegionB a potom sloučit RegionB a v oblasti. xxxxxxx a Yyyyyy jsou řetězcem hash na konci každého názvu oblasti. Dejte pozor, abyste nesloučili dvě nesouvislé oblasti. Po každém sloučení, jako je sloučení a a C, se v rámci jednotlivých adaptérů spustí komprimace v oblasti. Počkejte na dokončení komprimace a potom proveďte další sloučení s oblastí. Stav komprimace můžete najít na stránce serveru této oblasti v části HBA HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Problém: nejde načíst `.regioninfo`.

Nelze načíst `.regioninfo` pro `/hbase/data/default/tablex/regiony`oblasti.

### <a name="cause"></a>Příčina

Nejpravděpodobnější příčinou částečného odstranění oblasti při selhání RegionServer nebo restartování virtuálního počítače. V současné době je Azure Storage plochý systém souborů BLOB a některé operace se soubory nejsou atomické.

### <a name="resolution"></a>Rozlišení

Ručně vyčistit tyto zbývající soubory a složky:

1. Spusťte `hdfs dfs -ls /hbase/data/default/tablex/regiony` a ověřte, které složky nebo soubory jsou pořád pod ní.

1. Spustí `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` pro odstranění všech podřízených souborů nebo složek.

1. Pro odstranění složky oblasti proveďte `hdfs dfs -rmr /hbase/data/default/tablex/regiony`.

---

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
