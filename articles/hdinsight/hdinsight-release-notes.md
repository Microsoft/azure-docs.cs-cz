---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 37b49b3fbe91d199b13f548e8aaf72a6a2f0f848
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895297"
---
# <a name="release-notes"></a>Poznámky k verzi

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

## <a name="release-date-01092019"></a>Datum vydání: 01/09/2019

Tato verze platí pro HDInsight 3,6 a 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte prosím, až bude verze ve vaší oblasti v průběhu několika dnů živá.

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v [článku Správa verzí služby HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nové funkce
### <a name="tls-12-enforcement"></a>Vynucení protokolu TLS 1.2
Protokol TLS (Transport Layer Security) a SSL (Secure Sockets Layer) (SSL) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti. Přečtěte si další informace o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight používá TLS 1,2 u veřejných koncových bodů HTTPs, ale TLS 1,1 se pořád podporuje kvůli zpětné kompatibilitě. 

Z této verze můžou zákazníci pro všechna připojení prostřednictvím TLS 1,2 souhlasit s protokolem TLS 1,2 enformence. Nová vlastnost **minSupportedTlsVersion** se zavádí prostřednictvím šablony Azure Resource Manager pro vytvoření clusteru. Pokud vlastnost není nastavená, cluster stále podporuje 1,0, 1,1 a 1,2, stejně jako dnešní chování. Zákazníci mohou tuto vlastnost nastavit na hodnotu "1,2", což znamená, že cluster podporuje pouze TLS 1,2 a vyšší. 

### <a name="bring-your-own-key-for-disk-encryption"></a>Přineste si vlastní klíč pro šifrování disků
Všechny spravované disky v HDInsight jsou chráněné pomocí šifrování služby Azure Storage (SSE). Data na těchto discích jsou ve výchozím nastavení šifrována pomocí klíčů spravovaných Microsoftem. Od této verze můžete Bring Your Own Key (BYOK) pro šifrování disků a spravovat je pomocí Azure Key Vault. Šifrování BYOK je při vytváření clusteru jedním krokem konfigurace bez dalších poplatků. Stačí zaregistrovat HDInsight jako spravovanou identitu pomocí Azure Key Vault a přidat šifrovací klíč při vytváření clusteru. 

## <a name="deprecation"></a>Vyřazení
Pro tuto verzi nejsou žádné zastaralosti. Chcete-li se připravit na nadcházející zastaralost, Projděte si [nadcházející změny](#upcoming-changes).

## <a name="behavior-changes"></a>Změny chování
V této verzi nejsou žádné změny chování. Příprava na nadcházející změny najdete v tématu [nadcházející změny](#upcoming-changes).

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Pro hlavní uzel se vyžaduje minimální virtuální počítač se 4 jádry. 
Hlavní uzel vyžaduje minimálně 4 jádro virtuálního počítače, aby se zajistila vysoká dostupnost a spolehlivost clusterů HDInsight. Od 6. dubna 2020 můžou zákazníci jako hlavní uzel pro nové clustery HDInsight vybrat jenom 4 Core nebo novější virtuální počítač. Existující clustery budou i nadále fungovat podle očekávání. 

### <a name="esp-spark-cluster-node-size-change"></a>Změna velikosti uzlu clusteru pro Spark Spark 
V nadcházející verzi se minimální povolená velikost uzlu pro cluster ESP Spark změní na Standard_D13_V2. Virtuální počítače řady A-Series můžou způsobovat problémy s clusterem ESP z důvodu relativně nízké kapacity procesoru a paměti. Virtuální počítače řady A-Series budou zastaralé pro vytváření nových clusterů ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. V nadcházející verzi bude HDInsight místo toho používat službu Azure Virtual Machine Scale Sets. Přečtěte si další informace o službě Azure Virtual Machine Scale Sets.

### <a name="hbase-20-to-21"></a>HBA 2,0 až 2,1
V nadcházející verzi HDInsight 4,0 bude verze HBA upgradována z verze 2,0 na 2,1.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. Aktuální verze komponent pro HDInsight 4,0 AD HDInsight 3,6 najdete tady.
