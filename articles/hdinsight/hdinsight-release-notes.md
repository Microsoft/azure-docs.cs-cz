---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 091ca4d632d89405d85c66e264aff9867979fcd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905232"
---
# <a name="release-notes"></a>Poznámky k verzi

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

## <a name="release-date-01092020"></a>Datum vydání: 01/09/2020

Tato verze platí pro HDInsight 3,6 a 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte prosím, až bude verze ve vaší oblasti v průběhu několika dnů živá.

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v [článku Správa verzí služby HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nové funkce
### <a name="tls-12-enforcement"></a>Vynucení protokolu TLS 1.2
Protokol TLS (Transport Layer Security) a SSL (Secure Sockets Layer) (SSL) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti. Přečtěte si další informace o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight používá TLS 1,2 u veřejných koncových bodů HTTPs, ale TLS 1,1 se pořád podporuje kvůli zpětné kompatibilitě. 

V této verzi se zákazníci můžou rozhodnout k TLS 1,2 jenom pro všechna připojení prostřednictvím koncového bodu veřejného clusteru. V rámci této podpory je zavedena nová vlastnost **minSupportedTlsVersion** , která se dá zadat při vytváření clusteru. Pokud vlastnost není nastavená, cluster stále podporuje TLS 1,0, 1,1 a 1,2, což je stejné jako dnešní chování. Zákazníci mohou tuto vlastnost nastavit na hodnotu "1,2", což znamená, že cluster podporuje pouze TLS 1,2 a vyšší. Další informace najdete v tématu [Naplánování zabezpečení virtuální sítě – Transport Layer Security](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security).

### <a name="bring-your-own-key-for-disk-encryption"></a>Přineste si vlastní klíč pro šifrování disků
Všechny spravované disky v HDInsight jsou chráněné pomocí šifrování služby Azure Storage (SSE). Data na těchto discích jsou ve výchozím nastavení šifrována pomocí klíčů spravovaných Microsoftem. Od této verze můžete Bring Your Own Key (BYOK) pro šifrování disků a spravovat je pomocí Azure Key Vault. Šifrování BYOK je při vytváření clusteru jedním krokem konfigurace bez dalších poplatků. Stačí zaregistrovat HDInsight jako spravovanou identitu pomocí Azure Key Vault a přidat šifrovací klíč při vytváření clusteru. Další informace najdete v tématu [šifrování klíčového disku spravovaného zákazníkem](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

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

## <a name="known-issues"></a>Známé problémy

Od 29. ledna 2020 existuje aktivní problém, ve kterém se při pokusu o použití poznámkového bloku Jupyter může zobrazit chyba. Problém můžete vyřešit pomocí následujících kroků. Můžete se také podívat na tento příspěvek na [MSDN](https://social.msdn.microsoft.com/Forums/en-us/8c763fb4-79a9-496f-a75c-44a125e934ac/hdinshight-create-not-create-jupyter-notebook?forum=hdinsight) nebo tento [příspěvek na StackOverflow](https://stackoverflow.com/questions/59687614/azure-hdinsight-jupyter-notebook-not-working/59831103) pro aktuální informace nebo si klást další otázky. Tato stránka bude aktualizována, jakmile bude problém vyřešen.

**Vyskytl**

* ValueError: nejde převést Poznámkový blok na verzi V5, protože tato verze neexistuje.
* Chyba při načítání poznámkového bloku při načítání tohoto poznámkového bloku došlo k neznámé chybě. Tato verze může načíst formáty poznámkových bloků v4 nebo starší.

**Příčina** 

Soubor _version. py v clusteru se aktualizoval na 5. x. x místo 4.4. x. # # nebo Ambari se musí restartovat.

**Řešení**

Pokud vytvoříte nový Poznámkový blok Jupyter a dostanete jednu z výše uvedených chyb, proveďte následující kroky k vyřešení problému.

1. Otevřete Ambari ve webovém prohlížeči tak, že na `https://CLUSTERNAME.azurehdinsight.net`, kde název_clusteru je název vašeho clusteru.
1. V Ambari nabídce vlevo klikněte na **Jupyter**a pak na **Akce služby**klikněte na **zastavit**.
1. Spusťte SSH do clusteru hlavnímu uzlu, kde je spuštěná služba Jupyter.
1. Otevřete následující soubor/usr/bin/Anaconda/lib/python2.7/site-Packages/nbformat/_version. py v režimu sudo.
1. Ověřte hodnotu version_info.
1. Pokud je hodnota version_info nastavena na: 

    version_info = (5, 0, 3)

    Pak položku upravte na: 
    
    version_info = (4, 4, 0)

    A uložte soubor. 

    Pokud je už version_info nastavené na (4, 4, 0), pak pokračujte dalším krokem, protože se musí restartovat jenom Ambari, takže se nevyžadují žádné další změny.
1. Vraťte se na Ambari a v **akci služby**klikněte na **restartovat vše**.
