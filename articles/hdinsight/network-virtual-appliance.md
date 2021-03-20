---
title: Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight
description: Naučte se konfigurovat řadu dalších funkcí pro virtuální síťové zařízení ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943966"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurace síťového virtuálního zařízení ve službě Azure HDInsight

> [!Important]
> Následující informace jsou požadovány **pouze** v případě, že chcete nakonfigurovat síťové virtuální zařízení (síťové virtuální zařízení) jiné než [Azure firewall](./hdinsight-restrict-outbound-traffic.md).

Azure Firewall značka plně kvalifikovaného názvu domény se automaticky nakonfiguruje tak, aby umožňovala provoz pro mnoho běžných důležitých plně kvalifikovaných názvů domén. Použití jiného síťového virtuálního zařízení bude vyžadovat, abyste nakonfigurovali řadu dalších funkcí. Při konfiguraci síťového virtuálního zařízení mějte na paměti následující faktory:

* Služby podporující koncové body služby je možné konfigurovat pomocí koncových bodů služby, které mají za následek obejít síťové virtuální zařízení, obvykle pro náklady nebo požadavky na výkon.
* Pokud je ResourceProviderConnection nastavené na *odchozí*, můžete pro úložiště a SQL servery použít privátní koncové body pro metaúložiště a nemusíte je PŘIDÁVAT do síťové virtuální zařízení.
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
| [Zde](hdinsight-management-ip-addresses.md) publikované IP adresy | Tyto IP adresy jsou pro poskytovatele prostředků HDInsight a měly by být součástí UDR, aby se zabránilo asymetrickému směrování. Toto pravidlo je potřeba jenom v případě, že je ResourceProviderConnection nastavené na *příchozí*. Pokud je ResourceProviderConnection nastavené na *odchozí* , pak tyto IP adresy nejsou v udr potřeba.  |
| Privátní IP adresy AAD-DS | Vyžaduje se jenom pro clustery ESP, pokud virtuální sítě nejsou partnerského vztahu.|


### <a name="fqdn-httphttps-dependencies"></a>Závislosti HTTP/HTTPS v plně kvalifikovaném názvu domény

Můžete získat seznam závislostí plně kvalifikovaného názvu domény (většinou Azure Storage a Azure Service Bus) pro konfiguraci síťové virtuální zařízení [v tomto úložišti](https://github.com/Azure-Samples/hdinsight-fqdn-lists/). Místní seznam najdete [tady](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional). Tyto závislosti používá poskytovatel prostředků HDInsight (RP) k úspěšnému vytváření a monitorování a správě clusterů. Patří mezi ně telemetrie/diagnostické protokoly, zřizování metadat, konfigurace související s clustery, skripty atd. Tento seznam závislostí plně kvalifikovaného názvu domény se může změnit s vydáním budoucích aktualizací HDInsight.

Následující seznam obsahuje jenom několik plně kvalifikovaných názvů domén, které může být potřeba pro operační systém a opravy zabezpečení nebo ověřování certifikátů *po* vytvoření clusteru a během životnosti operací clusteru:

| **Plně kvalifikované názvy domén závislosti modulu runtime**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Další kroky

* [Použití brány firewall k omezení odchozího provozu](./hdinsight-restrict-outbound-traffic.md)
* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
