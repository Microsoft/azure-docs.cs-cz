---
title: Značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight
description: Pomocí značek služby HDInsight můžete povolit příchozí provoz do clusteru z uzlů služby stavu HDInsight a služeb správy, aniž byste museli explicitně přidávat IP adresy do skupin zabezpečení sítě.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117232"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight

Značky služby HDInsight pro skupiny zabezpečení sítě (skupin zabezpečení sítě) jsou skupiny IP adres pro služby stavu a správy. Tyto skupiny vám pomůžou minimalizovat složitost pro vytváření pravidel zabezpečení. [Značky služeb](../virtual-network/security-overview.md#service-tags) poskytují alternativní metodu pro povolení příchozího provozu z konkrétních IP adres bez nutnosti ZADÁVAT jednotlivé [IP adresy pro správu](hdinsight-management-ip-addresses.md) ve skupinách zabezpečení sítě.

Tyto značky služby vytváří a spravuje služba HDInsight. Nemůžete vytvořit vlastní značku služby nebo upravit existující značku. Společnost Microsoft spravuje předpony adres, které se shodují se značkou služby, a automaticky aktualizuje značku služby na změny adres.

## <a name="getting-started-with-service-tags"></a>Začínáme s visačkami služeb

Ve skupinách zabezpečení sítě máte dvě možnosti použití značek služeb:

1. Použít jednu značku služby HDInsight – Tato možnost otevře vaši virtuální síť se všemi IP adresami, které služba HDInsight používá pro monitorování clusterů napříč všemi oblastmi. Tato možnost je nejjednodušší způsob, ale nemusí být vhodný, pokud máte omezující požadavky na zabezpečení.

1. Použít více značek regionální služby – Tato možnost otevře virtuální síť pouze s IP adresami, které HDInsight používá v konkrétní oblasti. Pokud ale používáte více oblastí, budete muset do své virtuální sítě přidat několik značek služby.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Použít jednu globální značku služby HDInsight

Nejjednodušší způsob, jak začít používat značky služeb v clusteru HDInsight, je přidat globální značku `HDInsight` k pravidlu skupiny zabezpečení sítě.

1. Z [Azure Portal](https://portal.azure.com/)vyberte skupinu zabezpečení sítě.

1. V části **Nastavení**vyberte **příchozí pravidla zabezpečení**a pak vyberte **+ Přidat**.

1. V rozevíracím seznamu **zdroj** vyberte možnost **značka služby**.

1. V rozevíracím seznamu **tag zdrojové služby** vyberte **HDInsight**.

    ![Azure Portal přidat značku služby](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Tato značka obsahuje IP adresy služeb stavu a správy pro všechny oblasti, kde je HDInsight k dispozici, a zajistí, že cluster bude moci komunikovat s nezbytnými službami pro stav a správu bez ohledu na to, kde je vytvořen.

## <a name="use-regional-hdinsight-service-tags"></a>Použití regionálních značek služby HDInsight

Pokud možnost One nebude fungovat, protože potřebujete více omezující oprávnění, můžete pro vaši oblast použít jenom značky služeb. Příslušné značky služby můžou být jedna, dvě nebo tři značky služby v závislosti na oblasti, ve které se cluster vytváří.

Chcete-li zjistit, které značky služby se mají přidat do vaší oblasti, přečtěte si následující části dokumentu.

### <a name="use-a-single-regional-service-tag"></a>Použít jednu regionální značku služby

Pokud upřednostňujete možnost označení služby dvě a váš cluster se nachází v jedné z oblastí uvedených v této tabulce, stačí do skupiny zabezpečení sítě přidat jenom jednu oblastní značku služby.

| Země | Oblast | Značka služby |
| ---- | ---- | ---- |
| Austrálie | Austrálie – východ | HDInsight. AustraliaEast |
| &nbsp; | Austrálie – jihovýchod | HDInsight. AustraliaSoutheast |
| &nbsp; | Austrálie – střed | HDInsight. AustraliaCentral |
| Čína | Čína – východ 2 | HDInsight. ChinaEast2 |
| &nbsp; | Čína – sever 2 | HDInsight. ChinaNorth2 |
| Spojené státy | USA – středosever | HDInsight. NorthCentralUS |
| &nbsp; | USA – západ 2 | HDInsight. WestUS2 |
| &nbsp; | USA – středozápad | HDInsight. WestCentralUS |
| Kanada | Východní Kanada | HDInsight. CanadaEast |
| Brazílie | Brazílie – jih | HDInsight. BrazilSouth |
| Jižní Korea | Jižní Korea – střed | HDInsight. KoreaCentral |
| &nbsp; | Jižní Korea – jih | HDInsight. KoreaSouth |
| Indie | Indie – střed | HDInsight. CentralIndia |
| &nbsp; | Indie – jih | HDInsight. SouthIndia |
| Japonsko | Japonsko – západ | HDInsight. JapanWest |
| Francie | Francie – střed| HDInsight. FranceCentral |
| Spojené království | Velká Británie – jih | HDInsight. UKSouth |
| Azure Government | USDoD – střed   | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | UsDoD východ | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Použití více značek regionální služby

Pokud upřednostňujete možnost označení služby dvě a oblast, ve které se váš cluster vytvořil, není uvedená výše, je potřeba, abyste povolili více značek regionální služby. Nutnost použít více než jednu je v důsledku rozdílů v uspořádání poskytovatelů prostředků pro různé oblasti.

Zbývající oblasti jsou rozdělené do skupin podle toho, které značky regionální služby používají.

#### <a name="group-1"></a>Skupina 1

Pokud je váš cluster vytvořen v jedné z oblastí v následující tabulce, kromě uvedené značky regionální služby povolte značky služby `HDInsight.WestUS` a `HDInsight.EastUS`. Oblasti v této části vyžadují tři značky služeb.

Pokud je například cluster vytvořen v oblasti `East US 2`, bude nutné do skupiny zabezpečení sítě přidat následující značky služby:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Země | Oblast | Značka služby |
| ---- | ---- | ---- |
| Spojené státy | USA – východ 2 | HDInsight. EastUS2 |
| &nbsp; | USA – střed | HDInsight. CentralUS |
| &nbsp; | NorthCentral nás | HDInsight. NorthCentralUS |
| &nbsp; | USA – středojih | HDInsight. SouthCentralUS |
| &nbsp; | USA – východ | HDInsight. EastUS |
| &nbsp; | USA – západ | HDInsight. WestUS |
| Japonsko | Japonsko – východ | HDInsight. JapanEast |
| Evropa | Severní Evropa | HDInsight. NorthEurope |
| &nbsp; | Západní Evropa| HDInsight. WestEurope |
| Asie | Východní Asie | HDInsight. EastAsia |
| &nbsp; | Jihovýchodní Asie | HDInsight. SoutheastAsia |
| Austrálie | Austrálie – východ | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Skupina 2

Clustery v oblastech **Čína – sever** a **Čína – východ**musí umožňovat dvě značky služby: `HDInsight.ChinaNorth` a `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Skupina 3

Clustery v oblastech **US gov – Iowa** a **US gov – Virginie**musí umožňovat dvě značky služby: `HDInsight.USGovIowa` a `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Skupina 4

Clustery v oblastech **Německa (střední** a **Německo – severovýchod**) musí umožňovat dvě značky služby: `HDInsight.GermanyCentral` a `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Další kroky

- [Skupiny zabezpečení sítě – značky služeb](../virtual-network/security-overview.md#security-rules)
- [Vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md)
