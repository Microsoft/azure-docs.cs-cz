---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 54e28a9d434500915aa8cc8e07ade3592e5aa96c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185607"
---
# <a name="release-notes"></a>Poznámky k verzi

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky pro Open Source Apache Hadoop a Apache Spark analýzy v Azure.

## <a name="release-date-11072019"></a>Datum vydání: 11/07/2019

Tato verze platí pro HDInsight 3,6 a 4,0.

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v [článku Správa verzí služby HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nové funkce

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight identity broker (HIB) (ve verzi Preview)

Služba HDInsight identity broker (HIB) umožňuje uživatelům přihlásit se k Apache Ambari pomocí služby Multi-Factor Authentication (MFA) a získat požadované lístky protokolu Kerberos bez nutnosti používat hodnoty hash hesla v Azure Active Directory Domain Services (AAD-DS). Aktuálně je HIB k dispozici pouze pro clustery nasazené prostřednictvím šablony ARM.

### <a name="kafka-rest-api-proxy-preview"></a>Proxy Kafka REST API (Preview)

Kafka REST API poskytuje nasazení s vysokou dostupností služby REST proxy s clusterem Kafka prostřednictvím zabezpečeného ověřování AAD a protokolu OAuth jediným kliknutím. 

### <a name="auto-scale"></a>Automatické škálování

Automatické škálování pro Azure HDInsight je teď všeobecně dostupné ve všech oblastech pro typy clusterů Apache Spark a Hadoop. Tato funkce umožňuje spravovat úlohy analýzy velkých objemů dat v cenově výhodnějším a produktivním způsobu. Teď můžete optimalizovat použití clusterů HDInsight a platit jenom za to, co potřebujete.

V závislosti na vašich požadavcích můžete zvolit automatické škálování založené na zatížení a na základě plánu. Díky automatickému škálování na základě zatížení se dá velikost clusteru škálovat nahoru a dolů na základě aktuálních potřeb prostředků, zatímco automatické škálování na základě plánu může měnit velikost clusteru podle předdefinovaného plánu. 

Podpora automatického škálování pro HBA a LLAP úlohy je také Public Preview. Další informace najdete v tématu [Automatické škálování clusterů Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters).

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Urychlené zápisy HDInsight pro Apache HBA 

Zrychlené zápisy s využitím spravovaných disků Azure SSD úrovně Premium zvyšují výkon dopředného protokolování Apache HBase. Další informace najdete v tématu [urychlení zápisů Azure HDInsight pro Apache HBA](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes).

### <a name="custom-ambari-db"></a>Vlastní Ambari DB

HDInsight teď nabízí novou kapacitu, která zákazníkům umožní používat vlastní SQL DB pro Ambari. Zákazníci si teď můžou zvolit správnou databázi SQL DB pro Ambari a snadno ji upgradovat na základě jejich vlastního požadavku na růst firmy. Nasazení se provádí pomocí šablony Azure Resource Manager. Další informace najdete v tématu [Nastavení clusterů HDInsight s vlastní AMBARI DB](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db).

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>Virtuální počítače řady F-Series jsou teď dostupné v HDInsight.

Virtuální počítače řady F-Series jsou vhodné pro zahájení práce s HDInsight s požadavky na lehké zpracování. Za nižší ceníkové sazby za hodinu nabízí řada F-series nejlepší poměr cena–výkon v portfoliu Azure, pokud jde o množství výpočetních jednotek Azure (ACU) na virtuální procesor. Další informace najdete v tématu [Výběr správné velikosti virtuálního počítače pro cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size).

## <a name="deprecation"></a>Vyřazení

### <a name="g-series-virtual-machine-deprecation"></a>Zastaralé virtuální počítače řady G-series
Z této verze už virtuální počítače řady G-series nejsou nabízené v HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>Zastaralá virtuální počítač s Dv1
Z této verze se použití virtuálních počítačů s Dv1 a HDInsight už nepoužívá. Každý požadavek zákazníka na Dv1 se automaticky zpracuje pomocí Dv2. Mezi virtuálními počítači s Dv1 a Dv2 se nevztahují žádné cenové rozdíly.

## <a name="behavior-changes"></a>Změny chování

### <a name="cluster-managed-disk-size-change"></a>Změna velikosti spravovaného disku clusteru
HDInsight poskytuje spravované místo na disku clusteru. Z této verze se velikost spravovaného disku u každého uzlu v novém vytvořeném clusteru změní na 128 GB.

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Od prosince bude HDInsight místo toho používat službu Azure Virtual Machine Scale Sets. Přečtěte si další informace o [službě Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>HBA 2,0 až 2,1
V nadcházející verzi HDInsight 4,0 bude verze HBA upgradována z verze 2,0 na 2,1.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Vyřazení virtuálních počítačů a-Series pro cluster ESP je zastaralé
Virtuální počítače řady A-Series můžou způsobovat problémy s clusterem ESP v důsledku relativně nízké kapacity procesoru a paměti. V nadcházející verzi budou virtuální počítače řady A-Series pro vytváření nových clusterů ESP zastaralé.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi neexistuje žádná změna verze součásti. Aktuální verze komponent pro HDInsight 4,0 AD HDInsight 3,6 najdete [tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
