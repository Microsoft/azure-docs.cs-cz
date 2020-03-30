---
title: hbase hbck vrací nekonzistence v Azure HDInsight
description: hbase hbck vrací nekonzistence v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887321"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scénář: `hbase hbck` příkaz vrátí nekonzistence v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problém: Oblast není v`hbase:meta`

Oblast xxx na HDFS, `hbase:meta` ale není uvedena nebo nasazena na libovolném serveru oblasti.

### <a name="cause"></a>Příčina

Liší.

### <a name="resolution"></a>Řešení

1. Opravte meta tabulku spuštěním:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Přiřaďte oblasti k serverům RegionServers spuštěním:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problém: Oblast je offline

Oblast xxx není nasazena na žádném serveru RegionServer. To znamená, že `hbase:meta`oblast je v , ale offline.

### <a name="cause"></a>Příčina

Liší.

### <a name="resolution"></a>Řešení

Převedení oblastí do režimu online spuštěním:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problém: Oblasti mají stejné počáteční a koncové klíče.

### <a name="cause"></a>Příčina

Liší.

### <a name="resolution"></a>Řešení

Ručně sloučit tyto překrývající se oblasti. Přejděte do části tabulky webového uživatelského portálu HBase HMaster, vyberte odkaz na tabulku, která má problém. Zobrazí se počáteční klíč/koncový klíč každé oblasti, která patří do této tabulky. Potom sloučit tyto překrývající se oblasti. V prostředí HBase `merge_region 'xxxxxxxx','yyyyyyy', true`proveďte . Například:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

V tomto scénáři je třeba sloučit RegionA a RegionC a získat RegionD se stejným rozsahem klíčů jako RegionB, pak sloučit RegionB a RegionD. xxxxxxx a yyyyyy jsou řetězec hash na konci každého názvu oblasti. Dávejte pozor, abyste nesloučili dvě nesouvislé oblasti. Po každém sloučení, jako je sloučení A a C, HBase spustí zhutnění na RegionD. Počkejte na dokončení zhutnění před provedením další sloučení s RegionD. Stav zhutnění najdete na této stránce serveru oblasti v uzlení HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Problém: Nelze načíst`.regioninfo`

Nelze načíst `.regioninfo` pro `/hbase/data/default/tablex/regiony`oblast .

### <a name="cause"></a>Příčina

To je s největší pravděpodobností z důvodu částečné odstranění oblasti při dojde k chybě RegionServer nebo restartování virtuálního počítače. V současné době Azure Storage je plochý systém souborů objektů blob a některé operace se soubory nejsou atomické.

### <a name="resolution"></a>Řešení

Ručně vyčistěte tyto zbývající soubory a složky:

1. Spusťte `hdfs dfs -ls /hbase/data/default/tablex/regiony` a zkontrolujte, jaké složky / soubory jsou stále pod ním.

1. Spustit `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` odstranění všech podřízených souborů/složek

1. Spusťte `hdfs dfs -rmr /hbase/data/default/tablex/regiony` odstranění složky oblasti.

---

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
