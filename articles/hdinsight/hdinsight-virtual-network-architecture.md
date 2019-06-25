---
title: Architektura virtuální sítě Azure HDInsight
description: Další prostředky, které jsou dostupné při vytváření clusteru HDInsight ve virtuální síti Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252854"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architektura virtuální sítě Azure HDInsight

Tento článek popisuje prostředky, které jsou k dispozici při nasazování clusteru služby HDInsight do vlastní virtuální sítě Azure. Tyto informace vám pomůže připojení místních prostředků do vašeho clusteru HDInsight v Azure. Další informace ve virtuálních sítích Azure najdete v tématu [co je Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Typy prostředků v clusterech Azure HDInsight

Clustery Azure HDInsight mají různé typy virtuálních počítačů nebo uzly. U každého typu uzlu hraje roli ve fungování systému. Následující tabulka shrnuje tyto typy uzlů a jejich rolí v clusteru.

| Type | Popis |
| --- | --- |
| Hlavní uzel |  Pro všechny typy clusteru, s výjimkou Apache Storm hlavní uzly hostují procesy, které spravují provádění distribuované aplikace. K hlavnímu uzlu je také uzel, který je možné, připojte se přes SSH a spouštět aplikace, které jsou pak koordinovaný ke spuštění napříč prostředky clusteru. Počet hlavních uzlů je pevně nastavena na dvě pro všechny typy clusteru. |
| Uzel zooKeeper | Zookeeper koordinuje úkoly mezi uzly, které provádějí zpracování dat. Také nemá volba lídra nebo hlavního uzlu a uchovává informace o které hlavního uzlu běží konkrétní hlavní služby. Počet uzly ZooKeeper je pevně nastavena na tři. |
| Pracovní uzel | Představuje uzly, které podporují funkce pro zpracování dat. Pracovní uzly lze přidat nebo odebrat z clusteru škálovat výpočetní funkce a spravujte náklady. |
| Hraničnímu uzlu R serveru | Hraničnímu uzlu R serveru představuje uzel, který je možné, připojte se přes SSH a spouštět aplikace, které jsou pak koordinovaný ke spuštění napříč prostředky clusteru. Hraniční uzel není součástí analýzy dat v rámci clusteru. Tento uzel je hostitelem také R Studio Server umožňuje spustit R aplikaci pomocí prohlížeče. |
| Oblast uzlu | Pro typ clusteru HBase uzlu oblasti (také označované jako datový uzel) běží oblastním serveru. Oblastní servery poskytovat a spravovat část data spravovaná přes HBase. Uzly oblasti lze přidat nebo odebrat z clusteru škálovat výpočetní funkce a spravujte náklady.|
| Uzel nimbus | Pro typ clusteru Storm uzel Nimbus poskytuje funkce podobné hlavní uzel. Uzel Nimbus přiřazuje úlohy jiným uzlům v clusteru prostřednictvím nástroje Zookeeper, který koordinuje spuštěné topologie Storm. |
| Dohledové uzly | Pro typ clusteru Storm spustí dohledové uzly podle uzel Nimbus provádět požadované zpracování pokynů. |

## <a name="basic-virtual-network-resources"></a>Prostředky základní virtuální sítě

Následující diagram znázorňuje umístění uzlů HDInsight a síťové prostředky v Azure.

![Diagram vytvořené ve virtuální síti Azure vlastních entit HDInsight](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Výchozí prostředky k dispozici, když HDInsight je nasazený do virtuální sítě Azure zahrnují typy uzlů clusteru, uvedené v předchozí tabulce, jakož i síťová zařízení, které podporují komunikaci mezi virtuální sítí a mimo sítě.

Následující tabulka shrnuje cluster devíti uzlů, které vytvářejí, když HDInsight je nasazený do vlastní virtuální sítě Azure.

| Typ prostředku | Číslo, které jsou k dispozici | Podrobnosti |
| --- | --- | --- |
|Hlavní uzel | dva |    |
|Uzel Zookeeper | tři | |
|Pracovní uzel | dva | Toto číslo se může lišit v závislosti na konfiguraci clusteru a škálování. Pro Apache Kafka se vyžaduje minimálně tři pracovní uzly.  |
|Uzel brány | dva | Uzly brány jsou virtuální počítače s Azure, které se vytvoří v Azure, ale nejsou viditelné v rámci vašeho předplatného. Pokud je potřeba restartovat tyto uzly, obraťte se na podporu. |

K dispozici následující síťovým prostředkům se automaticky vytvoří ve virtuální síti použít s HDInsight:

| Síťové zdroje | Číslo, které jsou k dispozici | Podrobnosti |
| --- | --- | --- |
|Nástroj pro vyrovnávání zatížení | tři | |
|Síťová rozhraní | devět | Tato hodnota je založena na normální cluster, kde má každý uzel vlastní síťové rozhraní. Devět rozhraní jsou dva hlavní uzly, tři uzly zookeeper, dvěma pracovními uzly a dvěma uzly brány uvedených v předchozí tabulce. |
|Veřejné IP adresy | dva |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Koncové body pro připojení k HDInsight

Lze přistoupit ke clusteru HDInsight třemi způsoby:

- Koncový bod HTTPS mimo virtuální síť na `CLUSTERNAME.azurehdinsight.net`.
- Koncový bod SSH pro připojení přímo k hlavnímu uzlu v `CLUSTERNAME-ssh.azurehdinsight.net`.
- Koncový bod HTTPS v rámci virtuální sítě `CLUSTERNAME-int.azurehdinsight.net`. Všimněte si, že "-int" v této adrese URL. Tento koncový bod se přeložit na privátní IP adresu v dané virtuální síti a není přístupný z veřejného Internetu.

Tyto tři koncové body jsou přiřazeny nástroji pro vyrovnávání zatížení.

Veřejné IP adresy jsou také k dispozici na dvou koncových bodů, které umožňují připojení z mimo virtuální síť.

1. Jedna veřejná IP adresa přiřazená plně kvalifikovaný název domény (FQDN) nástroje pro vyrovnávání zatížení mají používat při připojování ke clusteru z Internetu `CLUSTERNAME.azurehdinsight.net`.
1. Druhá veřejná IP adresa se používá pro název domény jen pro SSH `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Další postup

* [Zabezpečení příchozího provozu do clusterů HDInsight ve virtuální síti s privátního koncového bodu](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
