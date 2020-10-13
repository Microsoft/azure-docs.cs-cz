---
title: Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight
description: Naučte se konfigurovat řadu dalších funkcí pro virtuální síťové zařízení ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 407160a5c315844003db4c5e371a03e6e25d2694
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630929"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight

> [!Important]
> Následující informace jsou požadovány **pouze** v případě, že chcete nakonfigurovat síťové virtuální zařízení (síťové virtuální zařízení) jiné než Azure firewall.

Azure Firewall se automaticky nakonfiguruje tak, aby povolovala provoz pro spoustu běžných důležitých scénářů. Použití jiného síťového virtuálního zařízení bude vyžadovat, abyste nakonfigurovali řadu dalších funkcí. Při konfiguraci síťového virtuálního zařízení mějte na paměti následující faktory:

* Služby podporující koncové body služby je možné konfigurovat pomocí koncových bodů služby, které mají za následek obejít síťové virtuální zařízení, obvykle pro náklady nebo požadavky na výkon.
* Závislosti IP adres jsou pro přenos bez HTTP/S (provoz TCP i UDP).
* V zařízení síťové virtuální zařízení se dají schvalovat koncové body HTTP/HTTPS s plně kvalifikovaným názvem domény.
* Přiřaďte směrovací tabulku, kterou vytvoříte ve své podsíti HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby

Volitelně můžete povolit jeden nebo více následujících koncových bodů služby, které budou mít za následek obejít síťové virtuální zařízení. Tato možnost může být užitečná pro velké objemy datových přenosů za účelem úspory nákladů a také pro optimalizace výkonu. 

| **Koncový bod** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Závislosti IP adres

| **Koncový bod** | **Podrobnosti** |
|---|---|
| [Zde](hdinsight-management-ip-addresses.md) publikované IP adresy | Tyto IP adresy jsou pro řízení služby HDInsight a měly by být zahrnuté do UDR, aby se předešlo asymetrickému směrování. |
| Privátní IP adresy AAD-DS | Je potřeba jenom pro clustery ESP.|


### <a name="fqdn-httphttps-dependencies"></a>Závislosti HTTP/HTTPS v plně kvalifikovaném názvu domény

> [!Important]
> Následující seznam obsahuje jenom několik plně kvalifikovaných názvů domén, které může být potřeba pro operační systém a opravy zabezpečení nebo ověřování certifikátů po vytvoření clusteru a během doby života clusterových operací. Můžete získat seznam závislostí plně kvalifikovaného názvu domény (většinou Azure Storage a Azure Service Bus) ke konfiguraci síťové virtuální zařízení [v tomto souboru](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json). Tyto závislosti používá poskytovatel prostředků HDInsight (RP) k úspěšnému vytváření a monitorování a správě clusterů. Patří mezi ně telemetrie/diagnostické protokoly, zřizování metadat, konfigurace související s clustery, skripty, šablony ARM atd. Seznam závislostí plně kvalifikovaného názvu domény se může změnit s vydáním budoucích aktualizací HDIngisht.

| **Koncový bod**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>Další kroky

* [Použití brány firewall k omezení odchozího provozu](./hdinsight-restrict-outbound-traffic.md)
* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
