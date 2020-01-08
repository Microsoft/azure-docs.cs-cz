---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435421"
---
# <a name="release-notes"></a>Poznámky k verzi

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky pro Open Source Apache Hadoop a Apache Spark analýzy v Azure.

## <a name="release-date-12172019"></a>Datum vydání: 12/17/2019

Tato verze platí pro HDInsight 3,6 a 4,0.

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v [článku Správa verzí služby HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nové funkce

### <a name="service-tags"></a>Značky služeb
Značky služeb zjednodušují zabezpečení virtuálních počítačů Azure a virtuálních sítí Azure tím, že umožňují snadno omezit síťový přístup ke službám Azure. Pomocí značek služeb ve skupinách zabezpečení sítě (NSG) můžete povolit nebo odepřít provoz do konkrétní služby Azure globálně nebo podle oblasti Azure. Azure poskytuje údržbu IP adres, které jsou základem jednotlivých značek. Značky služby HDInsight pro skupiny zabezpečení sítě (skupin zabezpečení sítě) jsou skupiny IP adres pro služby stavu a správy. Tyto skupiny vám pomůžou minimalizovat složitost pro vytváření pravidel zabezpečení. Zákazníci HDInsight můžou povolit značku služby prostřednictvím webu Azure Portal, PowerShellu a REST API. Další informace najdete v tématu [značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags).

### <a name="custom-ambari-db"></a>Vlastní Ambari DB
HDInsight teď umožňuje používat pro Apache Ambari vlastní SQL DB. Tuto vlastní databázi Ambari DB můžete nakonfigurovat z Azure Portal nebo pomocí šablony Resource Manageru.  Tato funkce umožňuje zvolit správnou databázi SQL pro potřeby zpracování a kapacity. Můžete také snadno upgradovat, aby odpovídaly požadavkům na obchodní růst. Další informace najdete v tématu [Nastavení clusterů HDInsight s vlastní AMBARI DB](hdinsight-custom-ambari-db.md).

![Vlastní Ambari DB](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Vyřazení
Pro tuto verzi nejsou žádné zastaralosti. Chcete-li se připravit na nadcházející zastaralost, Projděte si [nadcházející změny](#upcoming-changes).

## <a name="behavior-changes"></a>Změny chování
V této verzi nejsou žádné změny chování. Příprava na nadcházející změny chování najdete v tématu [nadcházející změny](#upcoming-changes).

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Vynucení protokolu TLS (Transport Layer Security) 1,2
Protokol TLS (Transport Layer Security) a SSL (Secure Sockets Layer) (SSL) jsou kryptografické protokoly, které poskytují zabezpečení komunikace v počítačové síti. Další informace najdete v tématu [zabezpečení transportní vrstvy](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). I když clustery Azure HDInsight přijímají připojení TLS 1,2 k veřejným koncovým bodům HTTPS, TLS 1,1 se pořád podporuje kvůli zpětné kompatibilitě se staršími klienty.

Od další verze se budete moci přihlásit a nakonfigurovat nové clustery HDInsight, aby přijímaly jenom připojení TLS 1,2. 

Později v roce začíná v 6/30/2020. Azure HDInsight vynutila TLS 1,2 nebo novější verze pro všechna připojení HTTPS. Doporučujeme, abyste zajistili, že všichni klienti jsou připraveni na zpracování TLS 1,2 nebo novějších verzí.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Počínaje Feburary 2020 (pozdější datum bude sděleno později) HDInsight použije místo toho službu Azure Virtual Machine Scale Sets. Přečtěte si další informace o [službě Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>Změna velikosti uzlu clusteru pro Spark Spark 
V nadcházející verzi:
- Minimální povolená velikost uzlu pro cluster ESP Spark se změní na Standard_D13_V2. 
- Virtuální počítače řady a-Series budou zastaralé pro vytváření nových clusterů ESP, protože virtuální počítače řady A-Series můžou způsobovat problémy s clusterem ESP z důvodu relativně nízké kapacity procesoru a paměti.

### <a name="hbase-20-to-21"></a>HBA 2,0 až 2,1
V nadcházející verzi HDInsight 4,0 bude verze HBA upgradována z verze 2,0 na 2,1.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Rozšířili jsme podporu HDInsight 3,6 do 31. prosince 2020. Další podrobnosti najdete v [podporovaných verzích HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

Pro HDInsight 4,0 se nezměnila žádná verze součásti.

Apache Zeppelin v HDInsight 3,6:0.7.0--> 0.7.3. 

Nejaktuálnější verze komponent můžete najít z [tohoto dokumentu](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="new-regions"></a>Nové oblasti

### <a name="uae-north"></a>Spojené arabské emiráty – sever
IP adresy pro správu Spojené arabské emiráty sever jsou: `65.52.252.96` a `65.52.252.97`.
