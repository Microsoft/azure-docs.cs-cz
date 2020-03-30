---
title: Značky služeb skupiny zabezpečení sítě (NSG) pro Azure HDInsight
description: Pomocí značek služeb HDInsight povolte příchozí provoz do clusteru z uzlů služby HDInsight pro stav a správu, aniž byste museli explicitně přidávat IP adresy do skupin zabezpečení sítě.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117232"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Značky služeb skupiny zabezpečení sítě (NSG) pro Azure HDInsight

Značky služeb HDInsight pro skupiny zabezpečení sítě (NSG) jsou skupiny IP adres pro služby stavu a správy. Tyto skupiny pomáhají minimalizovat složitost při vytváření pravidel zabezpečení. [Značky služeb](../virtual-network/security-overview.md#service-tags) poskytují alternativní metodu umožňující povolit příchozí přenosy z určitých adres IP bez zadání jednotlivých [adres IP pro správu](hdinsight-management-ip-addresses.md) ve skupinách zabezpečení sítě.

Tyto značky služeb jsou vytvářeny a spravovány službou HDInsight. Nelze vytvořit vlastní výrobní značku ani upravit existující značku. Společnost Microsoft spravuje předpony adres, které odpovídají výrobnímu stavu, a automaticky aktualizuje výrobní číslo, jak se adresy mění.

## <a name="getting-started-with-service-tags"></a>Začínáme se značkami služeb

Ve skupinách zabezpečení sítě máte dvě možnosti použití značek služeb:

1. Použijte jednu značku služby HDInsight – tato možnost otevře vaši virtuální síť všem IP adresám, které služba HDInsight používá ke sledování clusterů ve všech oblastech. Tato možnost je nejjednodušší metoda, ale nemusí být vhodné, pokud máte omezující požadavky na zabezpečení.

1. Použijte více značek místních služeb – tato možnost otevře virtuální síť pouze ip adresám, které HDInsight používá v dané konkrétní oblasti. Pokud však používáte více oblastí, budete muset do virtuální sítě přidat více značek služeb.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Použití jediné globální značky hdinsightu

Nejjednodušší způsob, jak začít používat značky služeb v clusteru `HDInsight` HDInsight, je přidat globální značku do pravidla skupiny zabezpečení sítě.

1. Na [portálu Azure](https://portal.azure.com/)vyberte skupinu zabezpečení sítě.

1. V části **Nastavení**vyberte **Příchozí pravidla zabezpečení**a pak vyberte + **Přidat**.

1. V rozevíracím seznamu **Zdroj** vyberte **Číslo servisu**.

1. V rozevíracím seznamu **Zdrojová značka služby** vyberte **hdinsight**.

    ![Azure Portal přidal značku služby](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Tato značka obsahuje IP adresy služeb stavu a správy pro všechny oblasti, kde je k dispozici HDInsight a zajistí, že váš cluster může komunikovat s potřebnými službami stavu a správy bez ohledu na to, kde je vytvořen.

## <a name="use-regional-hdinsight-service-tags"></a>Použití regionálních značek služeb HDInsight

Pokud možnost jedna nebude fungovat, protože potřebujete více omezující oprávnění, můžete povolit pouze značky služeb platné pro vaši oblast. Příslušné značky služby mohou být jedna, dvě nebo tři značky služby v závislosti na oblasti, kde je vytvořen cluster.

Chcete-li zjistit, které značky služeb chcete přidat pro vaši oblast, přečtěte si následující části dokumentu.

### <a name="use-a-single-regional-service-tag"></a>Použití jednoho regionálního servisního tagu

Pokud dáváte přednost možnosti druhé značky služby a váš cluster se nachází v jedné z oblastí uvedených v této tabulce, stačí do skupiny zabezpečení sítě přidat pouze jednu místní značku služby.

| Země | Region (Oblast) | Značka služby |
| ---- | ---- | ---- |
| Austrálie | Austrálie – východ | HDInsight.AustraliaVýchod |
| &nbsp; | Austrálie – jihovýchod | HDInsight.AustrálieJihovýchod |
| &nbsp; | Austrálie – střed | HDInsight.AustrálieCentrální |
| Čína | Čína východ 2 | HDInsight.ChinaEast2 |
| &nbsp; | Čína Sever 2 | HDInsight.ChinaSever2 |
| Spojené státy | USA – středosever | HDInsight.NorthCentralUS |
| &nbsp; | USA – západ 2 | HDInsight.WestUS2 |
| &nbsp; | USA – středozápad | HDInsight.WestCentralUS |
| Kanada | Kanada – východ | HDInsight.KanadaVýchod |
| Brazílie | Brazílie – jih | HDInsight.BrazílieJižní |
| Jižní Korea | Jižní Korea – střed | HDInsight.KoreaStřední |
| &nbsp; | Jižní Korea – jih | HDInsight.KoreaJižní |
| Indie | Indie – střed | HDInsight.CentralIndie |
| &nbsp; | Indie – jih | HDInsight.Jižní Indie |
| Japonsko | Japonsko – západ | HDInsight.JapanZápad |
| Francie | Francie – střed| HDInsight.FranceCentral |
| UK | Spojené království – jih | HDInsight.UKJižní |
| Azure Government | USDoD centrální   | HDInsight.USDoDCentral |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDoD východ | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Použití více značek místních služeb

Pokud dáváte přednost možnosti značky služby dvě a oblast, kde je vytvořen cluster, nebyla uvedena výše, musíte povolit více značek místních služeb. Potřeba používat více než jeden je způsobenrozdíly v uspořádání poskytovatelů zdrojů pro různé regiony.

Zbývající oblasti jsou rozděleny do skupin podle toho, které značky místních služeb používají.

#### <a name="group-1"></a>Skupina 1

Pokud je váš cluster vytvořen v jedné z oblastí v `HDInsight.WestUS` `HDInsight.EastUS` následující tabulce, povolte značky služeb a kromě uvedené místní značky služby. Oblasti v této části vyžadují tři značky služeb.

Pokud je například cluster vytvořen `East US 2` v oblasti, budete muset do skupiny zabezpečení sítě přidat následující značky služeb:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Země | Region (Oblast) | Značka služby |
| ---- | ---- | ---- |
| Spojené státy | USA – východ 2 | HDInsight.EastUS2 |
| &nbsp; | USA – střed | HDInsight.CentralUS |
| &nbsp; | NorthCentral USA | HDInsight. NorthCentralUS |
| &nbsp; | USA – středojih | HDInsight.SouthCentralUS |
| &nbsp; | USA – východ | HDInsight.EastUS |
| &nbsp; | USA – západ | HDInsight.WestUS |
| Japonsko | Japonsko – východ | HDInsight.JapanEast |
| Evropa | Severní Evropa | HDInsight.NorthEurope |
| &nbsp; | Západní Evropa| HDInsight.WestEurope |
| Asie | Východní Asie | HDInsight.EastAsia |
| &nbsp; | Jihovýchodní Asie | HDInsight.SoutheastAsie |
| Austrálie | Austrálie – východ | HDInsight.AustraliaVýchod |

#### <a name="group-2"></a>Skupina 2

Klastry v oblastech **Číny – sever** a čína – `HDInsight.ChinaEast` **východ**musí povolit dvě značky služeb: `HDInsight.ChinaNorth` a .

#### <a name="group-3"></a>Skupina 3

Clustery v oblastech **US Gov Iowa** a US Gov **Virginia** `HDInsight.USGovIowa` , `HDInsight.USGovVirginia`musí povolit dvě značky služeb: a .

#### <a name="group-4"></a>Skupina 4

Clustery v oblastech **Německo – střed** a **–severovýchod některou**musí povolit dvě značky služeb: `HDInsight.GermanyCentral` a `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Další kroky

- [Skupiny zabezpečení sítě - značky služeb](../virtual-network/security-overview.md#security-rules)
- [Vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md)
