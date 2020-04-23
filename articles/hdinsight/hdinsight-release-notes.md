---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k vydání pro Azure HDInsight. Získejte tipy a podrobnosti pro vývoj hadoopu, spark, r server, hive a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: f8331b64792781a89428f8c5070375c31c0da48d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084885"
---
# <a name="release-notes"></a>Poznámky k verzi

Tento článek obsahuje informace o **nejnovějších** aktualizacích verze Azure HDInsight. Informace o dřívějších verzích naleznete v [tématu ARCHIV POZNÁMEk k verzi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb mezi podnikovými zákazníky pro open source analýzy v Azure.

## <a name="release-date-01092020"></a>Datum vydání: 01/09/2020

Tato verze platí pro HDInsight 3.6 a 4.0. Verze HDInsight je k dispozici pro všechny oblasti během několika dní. Zde uvedené datum vydání označuje datum vydání první oblasti. Pokud níže uvedené změny nevidíte, počkejte prosím, než bude verze ve vaší oblasti za několik dní aktivní.

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace naleznete v [článku správy verzí HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nové funkce
### <a name="tls-12-enforcement"></a>Vynucení protokolu TLS 1.2
Zabezpečení transportní vrstvy (TLS) a SSL (Secure Sockets Layer) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti. Další informace o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight používá TLS 1.2 na veřejné koncové body HTTPs, ale TLS 1.1 je stále podporována pro zpětnou kompatibilitu. 

V této verzi se zákazníci mohou přihlásit do TLS 1.2 pouze pro všechna připojení prostřednictvím koncového bodu veřejného clusteru. Pro podporu tohoto, nová vlastnost **minSupportedTlsVersion** je zavedena a lze zadat během vytváření clusteru. Pokud vlastnost není nastavena, cluster stále podporuje TLS 1.0, 1.1 a 1.2, což je stejné jako dnešní chování. Zákazníci mohou nastavit hodnotu této vlastnosti na "1.2", což znamená, že cluster podporuje pouze TLS 1.2 a vyšší. Další informace naleznete v tématu [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Přineste si vlastní klíč pro šifrování disku
Všechny spravované disky ve službě HDInsight jsou chráněné šifrováním služby Azure Storage Service (SSE). Data na těchto discích jsou ve výchozím nastavení šifrována klíči spravovanými společností Microsoft. Počínaje touto verzí můžete přinést vlastní klíč (BYOK) pro šifrování disku a spravovat jej pomocí Azure Key Vault. Šifrování BYOK je konfigurace v jednom kroku během vytváření clusteru bez dalších nákladů. Stačí zaregistrovat HDInsight jako spravovanou identitu pomocí služby Azure Key Vault a při vytváření clusteru přidat šifrovací klíč. Další informace naleznete v [tématu Customer-managed key disk encryption](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Vyřazení
Žádné vyřazení pro tuto verzi. Chcete-li se připravit na nadcházející vyřazení, podívejte [se na nadcházející změny](#upcoming-changes).

## <a name="behavior-changes"></a>Změny chování
Žádné změny chování pro tuto verzi. Pokud se chcete připravit na nadcházející změny, [přečtěte si informace o nadcházejících změnách](#upcoming-changes).

## <a name="upcoming-changes"></a>Nadcházející změny
Následující změny se stanou v nadcházejících verzích. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Pro hlavní uzel je vyžadován minimální 4jádrový virtuální virtuální mk. 
Minimální 4-jádrový virtuální virtuální ms je vyžadován pro hlavní uzel k zajištění vysoké dostupnosti a spolehlivosti clusterů HDInsight. dubna 2020 si zákazníci můžou jako hlavní uzel pro nové clustery HDInsight vybrat jenom 4jádrový nebo vyšší virtuální uzel. Existující clustery budou nadále fungovat podle očekávání. 

### <a name="esp-spark-cluster-node-size-change"></a>Změna velikosti uzlu clusteru ESP Spark 
V nadcházející verzi se minimální povolená velikost uzlu pro cluster ESP Spark změní na Standard_D13_V2. Virtuální počítače řady A mohou způsobit problémy clusteru ESP z důvodu relativně nízké kapacity procesoru a paměti. Virtuální chodřady A se pro vytváření nových clusterů ESP zastaralá.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na škálovací sady virtuálních strojů Azure
HDInsight teď používá virtuální počítače Azure ke zřizování clusteru. V nadcházející verzi hdinsight bude místo toho používat škálovací sady virtuálních strojů Azure. Další informace o škálovacích sadách virtuálních strojů Azure.

### <a name="hbase-20-to-21"></a>HBase 2,0 až 2,1
V nadcházející verzi HDInsight 4.0 bude verze HBase upgradována z verze 2.0 na 2.1.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight pokračuje ve zlepšování spolehlivosti a výkonu clusteru. 

## <a name="component-version-change"></a>Změna verze komponenty
Pro tuto verzi nebyla žádná změna verze komponenty. Aktuální verze komponent pro HDInsight 4.0 ad HDInsight 3.6 najdete zde.

