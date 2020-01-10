---
title: Architektura virtuální sítě Azure HDInsight
description: Seznamte se s prostředky, které jsou k dispozici při vytváření clusteru HDInsight v Virtual Network Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b3f622b360f565ef5b16d5376cb1aa2498655017
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744747"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architektura virtuální sítě Azure HDInsight

Tento článek popisuje prostředky, které jsou k dispozici při nasazení clusteru HDInsight do vlastního Virtual Network Azure. Tyto informace vám pomůžou připojit místní prostředky ke clusteru HDInsight v Azure. Další informace o virtuálních sítích Azure najdete v tématu [co je Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Typy prostředků v clusterech Azure HDInsight

Clustery Azure HDInsight mají různé typy virtuálních počítačů nebo uzlů. Každý typ uzlu hraje roli v provozu systému. Následující tabulka shrnuje tyto typy uzlů a jejich role v clusteru.

| Typ | Popis |
| --- | --- |
| Hlavní uzel |  Pro všechny typy clusterů kromě Apache Storm hlavní uzly hostují procesy, které spravují provádění distribuované aplikace. Hlavní uzel je také uzel, do kterého můžete přihlédnout a spouštět aplikace, které jsou potom koordinovány pro spouštění v rámci prostředků clusteru. Počet hlavních uzlů je pevně stanoven dvakrát pro všechny typy clusterů. |
| Uzel ZooKeeper | Zookeeper koordinuje úlohy mezi uzly, které provádí zpracování dat. Má také volbu vedoucího hlavního uzlu a sleduje, který hlavní uzel spouští určitou hlavní službu. Počet uzlů ZooKeeper je pevně stanoven na tři. |
| Pracovní uzel | Představuje uzly, které podporují funkci zpracování dat. Pracovní uzly můžete přidat nebo odebrat z clusteru, abyste mohli škálovat výpočetní funkce a spravovat náklady. |
| R Server hraniční uzel | R Server hraniční uzel představuje uzel, ke kterému můžete přihlédnout a spouštět aplikace, které jsou pak koordinovány pro spouštění napříč prostředky clusteru. Hraniční uzel se nepodílí na analýze dat v rámci clusteru. Tento uzel je také hostitelem R Studio serveru, který umožňuje spuštění aplikace R pomocí prohlížeče. |
| Uzel oblasti | Pro typ clusteru HBA uzel region (také označovaný jako datový uzel) spouští server oblasti. Servery oblastí slouží a spravují část dat spravovaných pomocí adaptérů HBA. Uzly oblasti je možné přidat nebo odebrat z clusteru, abyste mohli škálovat výpočetní funkce a spravovat náklady.|
| Uzel Nimbus | Pro typ clusteru neNimbus poskytuje uzel funkce podobné hlavnímu uzlu. Uzel Nimbus přiřazuje úlohy jiným uzlům v clusteru prostřednictvím Zookeeper, který koordinuje spouštění topologií s více průchody. |
| Uzel vedoucího | Pro typ clusteru s více podprocesy provede uzel Správce pokyny poskytované uzlem Nimbus k provedení požadovaného zpracování. |

## <a name="resource-naming-conventions"></a>Zásady vytváření názvů prostředků

Při adresování uzlů v clusteru prosím použijte plně kvalifikované názvy domény (FQDN). Plně kvalifikované názvy domény pro různé typy uzlů v clusteru můžete získat pomocí [rozhraní Ambari API](hdinsight-hadoop-manage-ambari-rest-api.md). 

Tyto plně kvalifikované názvy domén budou mít `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`formuláře.

`<node-type-prefix>` bude *HN* pro hlavních, *dolů* pro uzly pracovních procesů a *zn* pro uzly Zookeeper.

Pokud potřebujete jenom název hostitele, použijte jenom první část plně kvalifikovaného názvu domény: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Základní prostředky virtuální sítě

Následující diagram znázorňuje umístění uzlů HDInsight a síťových prostředků v Azure.

![Diagram entit HDInsight vytvořených ve vlastní virtuální síti Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Výchozí prostředky, které jsou k dispozici při nasazení HDInsight do Azure Virtual Network, zahrnují typy uzlů clusteru uvedené v předchozí tabulce a síťová zařízení, která podporují komunikaci mezi virtuální sítí a externími sítěmi.

Následující tabulka shrnuje devět uzlů clusteru, které se vytvoří při nasazení HDInsight do vlastního Virtual Network Azure.

| Typ prostředku | Číslo k dispozici | Podrobnosti |
| --- | --- | --- |
|Hlavní uzel | dva |    |
|Uzel Zookeeper | tři | |
|Pracovní uzel | dva | Toto číslo se může lišit v závislosti na konfiguraci a škálování clusteru. Pro Apache Kafka je potřeba minimálně tři pracovní uzly.  |
|Uzel brány | dva | Uzly brány jsou virtuální počítače Azure, které jsou vytvořené v Azure, ale nejsou ve vašem předplatném viditelné. Pokud potřebujete tyto uzly restartovat, obraťte se na podporu. |

V rámci virtuální sítě používané se službou HDInsight se automaticky vytvoří tyto síťové prostředky:

| Prostředek sítě | Číslo k dispozici | Podrobnosti |
| --- | --- | --- |
|Nástroj pro vyrovnávání zatížení | tři | |
|Síťová rozhraní | devět | Tato hodnota je založena na běžném clusteru, kde každý uzel má své vlastní síťové rozhraní. Devět rozhraní je pro dva hlavní uzly, tři uzly Zookeeper, dva pracovní uzly a dva uzly brány, které jsou uvedené v předchozí tabulce. |
|Veřejné IP adresy | dva |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Koncové body pro připojení ke službě HDInsight

Ke clusteru HDInsight máte přístup třemi způsoby:

- Koncový bod HTTPS mimo virtuální síť na `CLUSTERNAME.azurehdinsight.net`.
- Koncový bod SSH pro přímé připojení k hlavnímu uzlu na `CLUSTERNAME-ssh.azurehdinsight.net`.
- Koncový bod HTTPS v rámci virtuální sítě `CLUSTERNAME-int.azurehdinsight.net`. Všimněte si, že v této adrese URL je "-int". Tento koncový bod se přeloží na soukromou IP adresu v této virtuální síti a není přístupný z veřejného Internetu.

K těmto třem koncovým bodům se přiřadí nástroj pro vyrovnávání zatížení.

Veřejné IP adresy se také poskytují ke dvěma koncovým bodům, které umožňují připojení mimo virtuální síť.

1. K nástroji pro vyrovnávání zatížení se přiřadí jedna veřejná IP adresa pro plně kvalifikovaný název domény (FQDN), která se použije při připojování ke clusteru z Internetu `CLUSTERNAME.azurehdinsight.net`.
1. Druhá veřejná IP adresa se používá jenom pro název domény `CLUSTERNAME-ssh.azurehdinsight.net`jenom SSH.

## <a name="next-steps"></a>Další kroky

- [Zabezpečení příchozího provozu do clusterů HDInsight ve virtuální síti s privátním koncovým bodem](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
