---
title: Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight
description: Naučte se konfigurovat řadu dalších funkcí pro virtuální síťové zařízení ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864705"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight

> [!Important]
> Následující informace jsou požadovány **pouze** v případě, že chcete nakonfigurovat síťové virtuální zařízení (síťové virtuální zařízení) jiné než Azure firewall.

Azure Firewall se automaticky nakonfiguruje tak, aby povolovala provoz pro spoustu běžných důležitých scénářů. Použití jiného síťového virtuálního zařízení bude vyžadovat, abyste nakonfigurovali řadu dalších funkcí. Při konfiguraci síťového virtuálního zařízení mějte na paměti následující faktory:

* Služby podporující koncový bod služby by měly být nakonfigurované s koncovými body služby.
* Závislosti IP adres jsou pro přenos bez HTTP/S (provoz TCP i UDP).
* Koncové názvy koncových bodů HTTP/HTTPS můžete umístit do zařízení síťové virtuální zařízení.
* Koncové body HTTP/HTTPS se zástupnými znaky jsou závislosti, které se můžou lišit podle počtu kvalifikátorů.
* Přiřaďte směrovací tabulku, kterou vytvoříte ve své podsíti HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby

| **Služba** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Závislosti IP adres

| **Služba** | **Zobrazí** |
|---|---|
| \*: 123 | Kontroluje se čas NTP. Provoz se kontroluje na více koncových bodech na portu 123. |
| [Zde](hdinsight-management-ip-addresses.md) publikované IP adresy | Tyto IP adresy jsou službou HDInsight. |
| Privátní IP adresy AAD-DS pro clustery ESP |
| \*: 16800 pro aktivaci Windows služby správy klíčů |
| \*12000 pro Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>Závislosti HTTP/HTTPS v plně kvalifikovaném názvu domény

> [!Important]
> Následující seznam obsahuje jenom několik nejdůležitějších plně kvalifikovaných názvů domén. Další plně kvalifikované názvy domény (většinou Azure Storage a Azure Service Bus) můžete získat pro konfiguraci síťové virtuální zařízení [v tomto souboru](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Služba**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Další kroky

* [Použití brány firewall k omezení odchozího provozu](./hdinsight-restrict-outbound-traffic.md)
* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
