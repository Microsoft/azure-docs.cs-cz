---
title: IP adresy pro správu Azure HDInsight
description: Zjistěte, které IP adresy musíte povolit pro příchozí provoz, aby bylo možné správně nakonfigurovat skupiny zabezpečení sítě a uživatelem definované trasy pro virtuální sítě pomocí Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 89d749a04c22967198be217350ff38751336fe67
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944113"
---
# <a name="hdinsight-management-ip-addresses"></a>IP adresy pro správu služby HDInsight

V tomto článku jsou uvedené IP adresy, které používají služby Azure HDInsight Health a Management Services. Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě) nebo uživatelsky definované trasy (udr), možná budete muset přidat některé z těchto IP adres do seznamu povolených adres pro příchozí síťový provoz.

## <a name="introduction"></a>Úvod
 
> [!Important]
> Ve většině případů teď můžete místo ručního přidávání IP adres používat [značky služeb](hdinsight-service-tags.md) pro skupiny zabezpečení sítě. IP adresy nebudou publikované pro nové oblasti Azure a budou mít jenom publikované značky služby. Statické IP adresy pro IP adresy pro správu budou nakonec zastaralé.

Pokud k řízení příchozího provozu do clusteru HDInsight používáte skupiny zabezpečení sítě (skupin zabezpečení sítě) nebo uživatelsky definované trasy (udr), musíte zajistit, aby váš cluster mohl komunikovat s důležitými službami stavu a správy Azure.  Některé z těchto IP adres pro tyto služby jsou specifické pro jednotlivé oblasti a některé z nich se vztahují na všechny oblasti Azure. Možná budete muset povolit provoz také ze služby Azure DNS, pokud nepoužíváte vlastní DNS.

Pokud potřebujete IP adresy pro oblast, která zde není uvedená, můžete k vyhledání IP adres pro vaši oblast použít [rozhraní API zjišťování značek služby](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) . Pokud nemůžete používat rozhraní API, Stáhněte si [soubor JSON pro tag služby](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) a vyhledejte požadovanou oblast.

HDInsight provádí ověřování těchto pravidel s vytvářením a škálováním clusteru, aby nedocházelo k dalším chybám. Pokud ověření neprojde, vytváření a škálování se nezdaří.

Následující části popisují konkrétní IP adresy, které musí být povoleny.

## <a name="azure-dns-service"></a>Služba Azure DNS

Pokud používáte službu DNS poskytovanou službou Azure, povolte přístup k __168.63.129.16__ na portu 53 pro TCP i UDP. Další informace najdete v dokumentu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) . Pokud používáte vlastní server DNS, přeskočte tento krok.

## <a name="health-and-management-services-all-regions"></a>Služby stavu a správy: všechny oblasti

Povolte provoz z následujících IP adres pro služby Azure HDInsight Health a Management, které platí pro všechny oblasti Azure:

| Zdrojová IP adresa | Cíl  | Směr |
| ---- | ----- | ----- |
| 168.61.49.99 | \*: 443 | Příchozí |
| 23.99.5.239 | \*: 443 | Příchozí |
| 168.61.48.131 | \*: 443 | Příchozí |
| 138.91.141.162 | \*: 443 | Příchozí |

## <a name="health-and-management-services-specific-regions"></a>Služby pro stav a správu: konkrétní oblasti

Povolte provoz z IP adres uvedených pro služby stavu a správy Azure HDInsight v konkrétní oblasti Azure, kde se nacházejí vaše prostředky:

> [!IMPORTANT]  
> Pokud oblast Azure, kterou používáte, není v seznamu, použijte funkci [označení služby](hdinsight-service-tags.md) pro skupiny zabezpečení sítě.

| Country (Země) | Region (Oblast) | Povolené zdrojové IP adresy | Povolený cíl | Směr |
| ---- | ---- | ---- | ---- | ----- |
| Asie | Východní Asie | 23.102.235.122</br>52.175.38.134 | \*: 443 | Příchozí |
| &nbsp; | Southeast Asia | 13.76.245.160</br>13.76.136.249 | \*: 443 | Příchozí |
| Austrálie | Austrálie – východ | 104.210.84.115</br>13.75.152.195 | \*: 443 | Příchozí |
| &nbsp; | Austrálie – jihovýchod | 13.77.2.56</br>13.77.2.94 | \*: 443 | Příchozí |
| Brazílie | Brazílie – jih | 191.235.84.104</br>191.235.87.113 | \*: 443 | Příchozí |
| Kanada | Kanada – východ | 52.229.127.96</br>52.229.123.172 | \*: 443 | Příchozí |
| &nbsp; | Střední Kanada | 52.228.37.66</br>52.228.45.222 |\*: 443 | Příchozí |
| Čína | Čína – sever | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*: 443 | Příchozí |
| &nbsp; | Čína – východ | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*: 443 | Příchozí |
| &nbsp; | Čína – sever 2 | 40.73.37.141</br>40.73.38.172 | \*: 443 | Příchozí |
| &nbsp; | Čína – východ 2 | 139.217.227.106</br>139.217.228.187 | \*: 443 | Příchozí |
| Evropa | Severní Evropa | 52.164.210.96</br>13.74.153.132 | \*: 443 | Příchozí |
| &nbsp; | West Europe| 52.166.243.90</br>52.174.36.244 | \*: 443 | Příchozí |
| Francie | Francie – střed| 20.188.39.64</br>40.89.157.135 | \*: 443 | Příchozí |
| Německo | Německo – střed | 51.4.146.68</br>51.4.146.80 | \*: 443 | Příchozí |
| &nbsp; | Německo – severovýchod | 51.5.150.132</br>51.5.144.101 | \*: 443 | Příchozí |
| Indie | Indie – střed | 52.172.153.209</br>52.172.152.49 | \*: 443 | Příchozí |
| &nbsp; | Indie – jih | 104.211.223.67<br/>104.211.216.210 | \*: 443 | Příchozí |
| Japonsko | Japonsko – východ | 13.78.125.90</br>13.78.89.60 | \*: 443 | Příchozí |
| &nbsp; | Japonsko – západ | 40.74.125.69</br>138.91.29.150 | \*: 443 | Příchozí |
| Jižní Korea | Jižní Korea – střed | 52.231.39.142</br>52.231.36.209 | \*: 443 | Příchozí |
| &nbsp; | Jižní Korea – jih | 52.231.203.16</br>52.231.205.214 | \*: 443 | Příchozí
| Spojené království | Spojené království – západ | 51.141.13.110</br>51.141.7.20 | \*: 443 | Příchozí |
| &nbsp; | Spojené království – jih | 51.140.47.39</br>51.140.52.16 | \*: 443 | Příchozí |
| USA | USA – střed | 13.89.171.122</br>13.89.171.124 | \*: 443 | Příchozí |
| &nbsp; | East US | 13.82.225.233</br>40.71.175.99 | \*: 443 | Příchozí |
| &nbsp; | USA – středosever | 157.56.8.38</br>157.55.213.99 | \*: 443 | Příchozí |
| &nbsp; | USA – středozápad | 52.161.23.15</br>52.161.10.167 | \*: 443 | Příchozí |
| &nbsp; | USA – západ | 13.64.254.98</br>23.101.196.19 | \*: 443 | Příchozí |
| &nbsp; | Západní USA 2 | 52.175.211.210</br>52.175.222.222 | \*: 443 | Příchozí |
| &nbsp; | Spojené arabské emiráty sever | 65.52.252.96</br>65.52.252.97 | \*: 443 | Příchozí |
| &nbsp; | Spojené arabské emiráty – střed | 20.37.76.96</br>20.37.76.99 | \*: 443 | Příchozí |

Informace o IP adresách, které se mají použít pro Azure Government, najdete v dokumentu [Azure Government Intelligence + Analytics](../azure-government/compare-azure-government-global-azure.md) .

Další informace najdete v tématu [řízení síťového provozu](./control-network-traffic.md).

Pokud používáte uživatelsky definované trasy (udr), měli byste zadat trasu a povolení odchozího provozu z virtuální sítě k výše uvedeným IP adresám s dalším segmentem směrování nastaveným na Internet.

## <a name="next-steps"></a>Další kroky

* [Vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md)
* [Značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight](hdinsight-service-tags.md)
