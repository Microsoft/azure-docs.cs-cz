---
title: Architektura virtuální sítě Azure HDInsight
description: Seznamte se s prostředky, které jsou k dispozici při vytváření clusteru HDInsight ve virtuální síti Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390207"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architektura virtuální sítě Azure HDInsight

Tento článek vysvětluje prostředky, které jsou k dispozici při nasazení clusteru HDInsight do vlastní virtuální sítě Azure. Tyto informace vám pomůžou připojit místní prostředky k vašemu clusteru HDInsight v Azure. Další informace o virtuálních sítích Azure najdete v tématu [Co je virtuální síť Azure?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Typy prostředků v clusterech Azure HDInsight

Clustery Azure HDInsight mají různé typy virtuálních počítačů nebo uzlů. Každý typ uzlu hraje roli v provozu systému. Následující tabulka shrnuje tyto typy uzlů a jejich role v clusteru.

| Typ | Popis |
| --- | --- |
| Hlavní uzel |  Pro všechny typy clusterů s výjimkou Apache Storm hlavní uzly hostitele procesů, které spravují provádění distribuované aplikace. Hlavní uzel je také uzel, který můžete SSH do a spouštět aplikace, které jsou pak koordinovány ke spuštění napříč prostředky clusteru. Počet hlavních uzlů je stanoven na dva pro všechny typy clusteru. |
| Uzel ZooKeeper | Zookeeper koordinuje úkoly mezi uzly, které provádějí zpracování dat. Provádí také volbu vedoucího hlavního uzlu a sleduje, který hlavní uzel je spuštěn pro konkrétní hlavní službu. Počet uzlů ZooKeeper je stanoven na tři. |
| Uzel pracovníka | Představuje uzly, které podporují funkce zpracování dat. Pracovní uzly lze přidat nebo odebrat z clusteru škálovat výpočetní schopnosti a spravovat náklady. |
| R Uzel hraničního serveru | Hraniční uzel R Serveru představuje uzel, do kterého můžete spouštět ssh, a spouští aplikace, které jsou pak koordinovány tak, aby běžely napříč prostředky clusteru. Hraniční uzel se neúčastní analýzy dat v rámci clusteru. Tento uzel také hostuje server R Studio, který umožňuje spouštět aplikaci R pomocí prohlížeče. |
| Uzel oblasti | Pro typ clusteru HBase uzel oblasti (označovaný také jako datový uzel) spouští server oblasti. Servery oblastí obsluhují a spravují část dat spravovaných společností HBase. Uzly oblasti lze přidat nebo odebrat z clusteru škálovat výpočetní schopnosti a spravovat náklady.|
| Uzel Nimbus | Pro typ clusteru Storm uzel Nimbus poskytuje funkce podobné hlavní uzel. Uzel Nimbus přiřazuje úkoly ostatním uzlům v clusteru prostřednictvím Zookeeper, který koordinuje spuštění topologií Storm. |
| Uzel supervizora | Pro typ clusteru Storm uzel supervizora provede pokyny poskytnuté uzel Nimbus provést zpracování. |

## <a name="resource-naming-conventions"></a>Konvence pro pojmenování prostředků

Při adresování uzlů v clusteru používejte plně kvalifikované názvy domén (FQDN). Pomocí rozhraní API [Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)můžete získat soubory souborů FQDN pro různé typy uzlů v clusteru .

Tyto hlavní protivně laděné `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`soubory budou ve formě .

Bude `<node-type-prefix>` *hn* pro headnodes, *wn* pro pracovní uzly a *zn* pro zookeeper uzly.

Pokud potřebujete pouze název hostitele, použijte pouze první část hlavního názvu celého souboru fQDN:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Základní prostředky virtuální sítě

Následující diagram znázorňuje umístění uzlů HDInsight a síťových prostředků v Azure.

![Diagram entit HDInsight vytvořených ve vlastní virtuální netu Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Výchozí prostředky ve virtuální síti Azure zahrnují typy uzlů clusteru uvedené v předchozí tabulce. A síťová zařízení, která podporují komunikaci mezi virtuální sítí a vnějšími sítěmi.

Následující tabulka shrnuje devět uzlů clusteru vytvořených při nasazení HDInsightu do vlastní virtuální sítě Azure.

| Typ prostředku | Číslo k dispozici | Podrobnosti |
| --- | --- | --- |
|Hlavní uzel | dva |    |
|Uzel Zookeeper | Tři | |
|Uzel pracovníka | dva | Toto číslo se může lišit v závislosti na konfiguraci clusteru a škálování. Pro Apache Kafka jsou potřeba minimálně tři pracovní uzly.  |
|Uzel brány | dva | Uzly brány jsou virtuální počítače Azure, které se vytvářejí v Azure, ale nejsou viditelné ve vašem předplatném. Pokud potřebujete tyto uzly restartovat, obraťte se na podporu. |

Následující síťové prostředky jsou automaticky vytvořeny uvnitř virtuální sítě používané s HDInsight:

| Síťový prostředek | Číslo k dispozici | Podrobnosti |
| --- | --- | --- |
|Nástroj pro vyrovnávání zatížení | Tři | |
|Síťová rozhraní | Devět | Tato hodnota je založena na normálním clusteru, kde každý uzel má své vlastní síťové rozhraní. Devět rozhraní je pro: dva hlavní uzly, tři uzly zookeeper, dva uzly pracovníka a dva uzly brány uvedené v předchozí tabulce. |
|Veřejné IP adresy | dva |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Koncové body pro připojení k HDInsight

Ke svému clusteru HDInsight můžete přistupovat třemi způsoby:

- Koncový bod HTTPS mimo virtuální `CLUSTERNAME.azurehdinsight.net`síť na adrese .
- Koncový bod SSH pro přímé připojení k `CLUSTERNAME-ssh.azurehdinsight.net`headnode at .
- Koncový bod HTTPS v `CLUSTERNAME-int.azurehdinsight.net`rámci virtuální sítě . Všimněte`-int`si " " v této adrese URL. Tento koncový bod se vyřeší na privátní IP adresu v této virtuální síti a není přístupný z veřejného internetu.

Tyto tři koncové body jsou přiřazeny vyrovnávání zatížení.

Veřejné IP adresy jsou také k dispozici dva koncové body, které umožňují připojení mimo virtuální síť.

1. Jeden veřejný IP je přiřazen k balancer pro plně kvalifikovaný název domény (FQDN) použít `CLUSTERNAME.azurehdinsight.net`při připojení ke clusteru z Internetu .
1. Druhá veřejná IP adresa se používá pro `CLUSTERNAME-ssh.azurehdinsight.net`název domény pouze SSH .

## <a name="next-steps"></a>Další kroky

- [Zabezpečení příchozího provozu do clusterů HDInsight ve virtuální síti s privátním koncovým bodem](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
