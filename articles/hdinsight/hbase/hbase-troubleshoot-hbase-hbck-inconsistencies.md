---
title: HBA hbck vrátí nekonzistence ve službě Azure HDInsight.
description: HBA hbck vrátí nekonzistence ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: cbe4231bbecdf279c637cd334336437a020188d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936988"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scénář: `hbase hbck` příkaz vrátí nekonzistence ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problém: oblast není v `hbase:meta`

Oblast XXX na HDFS, ale není uvedená v `hbase:meta` systému nebo nasazená na žádném serveru oblasti.

### <a name="cause"></a>Příčina

Se liší.

### <a name="resolution"></a>Řešení

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

Oblast XXX není nasazená na žádném RegionServer. To znamená, že oblast je v `hbase:meta` , ale je v režimu offline.

### <a name="cause"></a>Příčina

Se liší.

### <a name="resolution"></a>Řešení

Doveďte oblasti online spuštěním:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problém: regiony mají stejné počáteční a koncové klíče.

### <a name="cause"></a>Příčina

Se liší.

### <a name="resolution"></a>Řešení

Ručně sloučí tyto překryté oblasti. V části přejít na HMaster web UI tabulka webové uživatelské rozhraní vyberte odkaz tabulka, který má problém. Zobrazí se počáteční klíč/koncový klíč každé oblasti patřící do této tabulky. Pak tyto překryté oblasti slučte. V prostředí HBA Shell `merge_region 'xxxxxxxx','yyyyyyy', true` . Například:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

V tomto scénáři je potřeba sloučit oblast a RegionC a získat v oblasti stejný rozsah klíčů jako RegionB a potom sloučit RegionB a v oblasti. xxxxxxx a Yyyyyy jsou řetězcem hash na konci každého názvu oblasti. Dejte pozor, abyste nesloučili dvě nesouvislé oblasti. Po každém sloučení, jako je sloučení a a C, se v rámci jednotlivých adaptérů spustí komprimace v oblasti. Počkejte na dokončení komprimace a potom proveďte další sloučení s oblastí. Stav komprimace můžete najít na stránce serveru této oblasti v části HBA HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Problém: nejde načíst. `.regioninfo`

Nelze načíst `.regioninfo` pro oblast `/hbase/data/default/tablex/regiony` .

### <a name="cause"></a>Příčina

Nejpravděpodobnější příčinou částečného odstranění oblasti při selhání RegionServer nebo restartování virtuálního počítače. V současné době je Azure Storage plochý systém souborů BLOB a některé operace se soubory nejsou atomické.

### <a name="resolution"></a>Řešení

Ručně vyčistit tyto zbývající soubory a složky:

1. Spusťte `hdfs dfs -ls /hbase/data/default/tablex/regiony` , chcete-li zjistit, které složky nebo soubory jsou pořád pod ní.

1. Provést `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` odstranění všech podřízených souborů nebo složek

1. Proveďte `hdfs dfs -rmr /hbase/data/default/tablex/regiony` odstranění složky oblasti.

---

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).