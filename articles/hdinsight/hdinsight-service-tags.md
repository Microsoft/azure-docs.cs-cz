---
title: Značky služeb skupiny zabezpečení sítě (NSG) pro Azure HDInsight
description: Pomocí značek služeb HDInsight povolte příchozí provoz do clusteru z uzlů služby stavu a správy bez přidání IP adres do skupin nsG.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: 34ec05a8362f5947cb61924b19c6b1a52e5d91a4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437677"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Značky služeb NSG pro Azure HDInsight

Značky služeb Azure HDInsight pro skupiny zabezpečení sítě (NSGs) jsou skupiny IP adres pro služby stavu a správy. Tyto skupiny pomáhají minimalizovat složitost při vytváření pravidel zabezpečení. [Značky služeb](../virtual-network/security-overview.md#service-tags) poskytují alternativní metodu umožňující povolit příchozí provoz z konkrétních adres IP bez zadání jednotlivých [adres IP správy](hdinsight-management-ip-addresses.md) ve vašich nevládních souborech.

Služba HDInsight spravuje tyto značky služeb. Nelze vytvořit vlastní výrobní značku ani upravit existující značku. Společnost Microsoft spravuje předpony adres, které odpovídají výrobnímu nebo toku, a automaticky aktualizuje výrobní číslo, jak se adresy mění.

## <a name="get-started-with-service-tags"></a>Začínáme se značkami služeb

Ve skupinách zabezpečení sítě máte dvě možnosti použití značek služeb:

- **Použití jedné globální značky služby HDInsight**: Tato možnost otevře virtuální síť všem IP adresám, které služba HDInsight používá ke sledování clusterů ve všech oblastech. Tato možnost je nejjednodušší metoda, ale nemusí být vhodné, pokud máte omezující požadavky na zabezpečení.

- **Použít více značek místních služeb**: Tato možnost otevře virtuální síť jenom na IP adresy, které HDInsight používá v dané konkrétní oblasti. Pokud však používáte více oblastí, budete muset do virtuální sítě přidat více značek služeb.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Použití jediné globální značky hdinsightu

Nejjednodušší způsob, jak začít používat značky služeb v clusteru `HDInsight` HDInsight, je přidat globální značku do pravidla skupiny nsg.

1. Na [portálu Azure](https://portal.azure.com/)vyberte skupinu zabezpečení sítě.

1. V části **Nastavení**vyberte **Příchozí pravidla zabezpečení**a pak vyberte + **Přidat**.

1. V rozevíracím seznamu **Zdroj** vyberte **Číslo servisu**.

1. V rozevíracím seznamu **Zdrojová značka služby** vyberte **hdinsight**.

    ![Přidání servisní značky z webu Azure Portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Tato značka obsahuje IP adresy služeb stavu a správy pro všechny oblasti, kde je k dispozici HDInsight. Značka zajistí, že váš cluster může komunikovat s potřebnými službami stavu a správy bez ohledu na to, kde je vytvořen.

## <a name="use-regional-hdinsight-service-tags"></a>Použití regionálních značek služeb HDInsight

Pokud možnost globální značky nebude fungovat, protože potřebujete více omezující oprávnění, můžete povolit pouze značky služeb, které jsou použitelné pro vaši oblast. V závislosti na oblasti, kde je vytvořen cluster, může existovat jedna, dvě nebo tři příslušné značky služeb.

Chcete-li zjistit, které značky služeb chcete přidat pro vaši oblast, přečtěte si následující části článku.

### <a name="use-a-single-regional-service-tag"></a>Použití jednoho regionálního servisního tagu

Pokud dáváte přednost použití značek místní služby a cluster se nachází v jedné z oblastí uvedených v této tabulce, stačí do skupiny zabezpečení sítě přidat pouze jednu místní značku služby.

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
| Azure Government | USDoD centrální | HDInsight.USDoDCentral |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDoD východ | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Použití více značek místních služeb

Pokud dáváte přednost použití značek místní chod, ale oblast, kde je vytvořen cluster, nebyla uvedena v předchozí tabulce, je třeba povolit více značek místních služeb. Potřeba používat více než jeden je způsobenrozdíly v uspořádání poskytovatelů zdrojů pro různé regiony.

Zbývající oblasti jsou rozděleny do skupin podle toho, které značky místních služeb používají.

#### <a name="group-1"></a>Skupina 1

Pokud je cluster vytvořen v jedné z oblastí v následující `HDInsight.WestUS` `HDInsight.EastUS` tabulce, povolte značky služeb a kromě uvedené místní značky služby. Oblasti v této části vyžadují tři značky služeb.

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

Klastry v oblastech *Číny Sever* a *Čína – východ* musí povolit dvě značky služeb: `HDInsight.ChinaNorth` a `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Skupina 3

Clustery v oblastech *US Gov Iowa* a US Gov *Virginia* musí povolit dvě značky služeb: `HDInsight.USGovIowa` a `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Skupina 4

Clustery v oblastech *Německo – střed* a Německo – `HDInsight.GermanyNortheast` *severovýchod* musí povolit dvě značky služeb: `HDInsight.GermanyCentral` a .

## <a name="next-steps"></a>Další kroky

- [Skupiny zabezpečení sítě: značky služeb](../virtual-network/security-overview.md#security-rules)
- [Vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md)
