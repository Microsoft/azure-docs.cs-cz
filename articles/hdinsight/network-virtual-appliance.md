---
title: Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight
description: Naučte se konfigurovat řadu dalších funkcí pro virtuální síťové zařízení ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: abedaf6f66bdd7aea36512c90bb5ee799f8e7a99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800940"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight

> [!Important]
> Následující informace jsou požadovány **pouze** v případě, že chcete nakonfigurovat síťové virtuální zařízení (síťové virtuální zařízení) jiné než Azure firewall.

Azure Firewall se automaticky nakonfiguruje tak, aby povolovala provoz pro spoustu běžných důležitých scénářů. Použití jiného síťového virtuálního zařízení bude vyžadovat, abyste nakonfigurovali řadu dalších funkcí. Při konfiguraci síťového virtuálního zařízení mějte na paměti následující faktory:

* Služby podporující koncové body služby je možné konfigurovat pomocí koncových bodů služby, které mají za následek obejít síťové virtuální zařízení, obvykle pro náklady nebo požadavky na výkon.
* Závislosti IP adres jsou pro přenos bez HTTP/S (provoz TCP i UDP).
* V zařízení síťové virtuální zařízení se můžou na seznam povolených názvů koncových bodů HTTP/HTTPS.
* Přiřaďte směrovací tabulku, kterou vytvoříte ve své podsíti HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby

Volitelně můžete povolit jeden nebo více následujících koncových bodů služby, které budou mít za následek obejít síťové virtuální zařízení. Tato možnost může být užitečná pro velké objemy datových přenosů za účelem úspory nákladů a také pro optimalizace výkonu. 

| **Služba** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Závislosti IP adres

| **Služba** | **Podrobnosti** |
|---|---|
| [Zde](hdinsight-management-ip-addresses.md) publikované IP adresy | Tyto IP adresy jsou pro řízení služby HDInsight a měly by být zahrnuté do UDR, aby se předešlo asymetrickému směrování. |
| Privátní IP adresy AAD-DS | Je potřeba jenom pro clustery ESP.|


### <a name="fqdn-httphttps-dependencies"></a>Závislosti HTTP/HTTPS v plně kvalifikovaném názvu domény

> [!Important]
> Následující seznam obsahuje jenom několik nejdůležitějších plně kvalifikovaných názvů domén. Úplný seznam plně kvalifikovaných názvů domén (většinou Azure Storage a Azure Service Bus) můžete získat pro konfiguraci síťové virtuální zařízení [v tomto souboru](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json). Tyto závislosti jsou používány operacemi na úrovni ovládacího prvku HDInsight k úspěšnému vytvoření clusteru.

| **Služba**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Další kroky

* [Použití brány firewall k omezení odchozího provozu](./hdinsight-restrict-outbound-traffic.md)
* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
