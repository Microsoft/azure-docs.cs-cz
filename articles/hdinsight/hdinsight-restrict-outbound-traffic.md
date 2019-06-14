---
title: Konfigurace omezení odchozího síťového provozu pro clustery Azure HDInsight
description: Zjistěte, jak nakonfigurovat omezení odchozího síťového provozu pro clustery Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/30/2019
ms.openlocfilehash: 542813e0f82a1a52142a2b82bea3fdb101fdec28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077162"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Konfigurace odchozího síťového provozu pro clustery Azure HDInsight pomocí brány Firewall (Preview)

Tento článek popisuje kroky pro zabezpečit odchozí provoz z vašeho clusteru HDInsight pomocí Brána Firewall služby Azure. Následující postup předpokládá, že se konfigurace brány Firewall Azure pro stávajícího clusteru. Pokud nasazujete nový cluster a za bránou firewall, nejprve vytvořte HDInsight cluster a podsíť a pak postupujte podle kroků v této příručce.

## <a name="background"></a>Pozadí

Clustery Azure HDInsight se obvykle nasazují ve virtuální síti. Cluster má závislosti na služby mimo tuto virtuální síť, které vyžadují přístup k síti, aby správně fungoval.

Existuje několik závislostí, které vyžadují příchozí provoz. Řízení příchozích přenosů nelze odeslat prostřednictvím zařízení brány firewall. Zdrojové adresy pro tento provoz je známo a publikují [tady](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Pravidla skupiny zabezpečení sítě (NSG) můžete vytvořit také pomocí těchto informací k zabezpečení příchozího provozu do clusterů.

Odchozí provoz závislostí HDInsight jsou téměř úplně definovány pomocí plně kvalifikovaných názvů domén, které nejsou statické IP adresy za nimi stojí. Chybějící statické adresy znamená, že skupiny zabezpečení sítě (Nsg) nelze použít pro uzamčení odchozího provozu z clusteru. Adresy změnit dostatečně často, že jeden nelze nastavit pravidla založená na aktuální překlad a, který slouží k nastavení pravidla skupiny zabezpečení sítě.

Řešení zabezpečení odchozí adresy je použití zařízení brány firewall, která můžete řídit odchozí provoz na základě názvů domén. Brány Firewall na Azure můžete omezit odchozí přenosy HTTP i HTTPS založené na plně kvalifikovaný název domény cílového nebo [plně kvalifikovaný název domény značky](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurace brány Firewall na Azure pomocí HDInsight

Přehled kroků pro uzamčení odchozího přenosu dat z vaší existující HDInsight s bránou Firewall služby Azure jsou:
1. Vytvoření brány firewall.
1. Přidat aplikace pravidla brány firewall
1. Přidání pravidel sítě do brány firewall.
1. Vytvoření směrovací tabulky.

### <a name="create-a-new-firewall-for-your-cluster"></a>Vytvoření nové brány firewall pro váš cluster

1. Vytvořte podsíť s názvem **AzureFirewallSubnet** ve virtuální síti, které se nachází váš cluster. 
1. Vytvoření nové brány firewall **testovací FW01** pomocí postupu v [kurzu: Nasazení a konfiguraci brány Firewall Azure pomocí webu Azure portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurace brány firewall pomocí pravidel aplikace

Vytvořte kolekci pravidel aplikace, která umožňuje odesílat a přijímat komunikační sdělení důležitých clusteru.

Vyberte novou bránu firewall **testovací FW01** z portálu Azure portal. Klikněte na tlačítko **pravidla** pod **nastavení** > **kolekce pravidel aplikace** > **přidat kolekci pravidel aplikace**.

![Název: Přidat kolekci pravidel aplikace](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Na **přidat kolekci pravidel aplikace** obrazovky, proveďte následující kroky:

1. Zadejte **název**, **Priority**a klikněte na tlačítko **povolit** z **akce** rozevírací nabídce a zadejte následující pravidla **Plně kvalifikovaný název domény značky části** :

   | **Název** | **Zdrojová adresa** | **Plně kvalifikovaný název domény značky** | **Poznámky** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight a Windows Update | Vyžaduje se pro služby Hdinsight |

1. Přidat následující pravidla, která **cílový oddíl plně kvalifikované názvy domény** :

   | **Název** | **Zdrojová adresa** | **Protocol:Port** | **Cílový plně kvalifikovaných názvů DOMÉN** | **Poznámky** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | Umožňuje Windows přihlašovací aktivita |
   | Rule_3 | * | https:443,http:80 | <storage_account_name.blob.core.windows.net> | Pokud váš cluster zálohovaný WASB, přidejte pravidlo pro WASB. Pro použití protokolu https pouze připojení Ujistěte se, že ["vyžadovat zabezpečený přenos"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) je povolená v účtu úložiště. |

1. Klikněte na tlačítko **Add** (Přidat).

   ![Název: Zadejte podrobnosti o aplikaci pravidla kolekce](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurace brány firewall pomocí pravidel sítě

Vytvoření pravidel sítě správně nakonfigurovat váš cluster HDInsight.

1. Vyberte novou bránu firewall **testovací FW01** z portálu Azure portal.
1. Klikněte na tlačítko **pravidla** pod **nastavení** > **sítě kolekce pravidel** > **přidat kolekci pravidel sítě**.
1. Na **přidat kolekci pravidel sítě** obrazovky, zadejte **název**, **Priority**a klikněte na tlačítko **povolit** z **akce** rozevírací nabídky.
1. Vytvořte následující pravidla v aplikaci **IP adresy** části:

   | **Název** | **Protokol** | **Zdrojová adresa** | **Cílová adresa** | **Cílový Port** | **Poznámky** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Služba Systémový čas |
   | Rule_2 | Jakýkoli | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Pokud používáte Enterprise Security Package (ESP), přidejte pravidlo pro sítě v sekci IP adres, který umožňuje komunikaci se službou AAD DS ESP clusterů. IP adresy řadičů domény v části AAD DS můžete najít na portálu | 
   | Rule_3 | TCP | * | IP adresa vašeho účtu Data Lake Storage | `*` | Pokud používáte Azure Data Lake Storage, můžete přidat pravidlo pro sítě v části IP adresy k vyřešení problému SNI s ADLS Gen1 a Gen2. Tato možnost bude směrovat provoz do brány firewall, která může mít za následek vyšší náklady na načtení velkých objemů dat, ale provoz bude zaznamenané a auditovatelných v protokolech brány firewall. Určete IP adresu pro váš účet Data Lake Storage. Můžete použít příkaz prostředí powershell například `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` vyřešit plně kvalifikovaný název domény na IP adresu.|
   | Rule_4 | TCP | * | * | `12000` | (Volitelné) Pokud používáte Log Analytics, vytvořte pravidlo pro sítě v části IP adresy umožňují komunikaci s pracovního prostoru Log Analytics. |

1. Vytvořte následující pravidla v aplikaci **značky služeb** části:

   | **Název** | **Protokol** | **Zdrojová adresa** | **Značky služeb** | **Cílový Port** | **Poznámky** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | * | `1433,11000-11999,14000-14999` | Konfigurace pravidla sítě v části značky služeb pro SQL, které vám umožní protokolování a auditování SQL provoz, pokud není nakonfigurované koncové body služby pro SQL Server v HDInsight podsítě, která bude obejít bránu firewall. |

1. Klikněte na tlačítko **přidat** nezbytných k dokončení vytvoření vaší kolekce pravidel sítě.

   ![Název: Zadejte podrobnosti o aplikaci pravidla kolekce](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Vytvořte a nakonfigurujte tabulku směrování

Vytvoření směrovací tabulky s následující položky:

1. Šest řeší [tento seznam požadovaných HDInsight správy IP adres](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) s dalším segmentem směrování **Internet**:
    1. Čtyři IP adres pro všechny clustery ve všech oblastech
    1. Dvě IP adresy, které jsou specifické pro oblast, ve kterém je cluster vytvořen
1. Virtuální zařízení tras pro IP adresy 0.0.0.0/0 s dalším směrováním, přičemž vaše Brána Firewall služby Azure privátní IP adresu.

Třeba ke konfiguraci směrovací tabulky pro cluster se vytvoří v oblasti USA "USA", použijte následující kroky:

1. Přihlaste se k portálu Azure.
1. Vyberte Azure bránu firewall **testovací FW01**. Kopírovat **privátní IP adresa** na **přehled** stránky. V tomto příkladu budeme používat **ukázkový adresu 10.1.1.4**
1. Vytvoření nové směrovací tabulky.
1. Klikněte na tlačítko **trasy** pod **nastavení**.
1. Klikněte na tlačítko **přidat** vytvářet trasy pro IP adresy v následující tabulce.

| Název trasy | Předpona adresy | Typ dalšího segmentu | Adresa dalšího segmentu |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Není k dispozici |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Není k dispozici |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Není k dispozici |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Není k dispozici |
| 13.67.223.215 | 13.67.223.215/32 | Internet | Není k dispozici |
| 40.86.83.253 | 40.86.83.253/32 | Internet | Není k dispozici |
| 0.0.0.0 | 0.0.0.0/0 | Virtuální zařízení | 10.1.1.4 |

Dokončete konfiguraci směrovací tabulky:

1. Přiřazení směrovací tabulky, které jste vytvořili pro vaši podsíť HDInsight kliknutím **podsítě** pod **nastavení** a potom **přidružit**.
1. Na **přidružit podsíť** obrazovky, vyberte virtuální síť vytvořenou do vašeho clusteru a **podsítě HDInsight** jste použili pro váš cluster HDInsight.
1. Klikněte na **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Hraniční uzel nebo provozu vlastní aplikace

Proveďte následující kroky vám umožní cluster fungovat bez problémů. Stále musíte nakonfigurovat závislosti tak, aby vyhovovaly vaší vlastní aplikace běžící na hraničních uzlech, pokud je k dispozici.

Závislosti aplikace musí být identifikovány a přidat do brány Firewall Azure ani do směrovací tabulky.

Trasy musí být vytvořeny pro provoz aplikace, abyste předešli problémům s asymetrického směrování.

Pokud vaše aplikace Další závislosti, musí být přidán do vaše Brána Firewall služby Azure. Vytvoření aplikace pravidla, která umožňují přenosy HTTP/HTTPS a pravidla síťových pro všechno ostatní.

## <a name="logging"></a>Protokolování

Brány Azure můžete odeslat protokoly do několika různých úložných systémů. Pro pokyny ke konfiguraci protokolování pro bránu firewall, postupujte podle kroků v [kurzu: Monitorujte metriky a protokoly brány Firewall Azure](../firewall/tutorial-diagnostics.md).

Po dokončení nastavení protokolování, můžete využít při protokolování dat do služby Log Analytics, můžete zobrazit blokovaného provozu s dotazem, jako je následující:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrace s protokoly Azure monitoru vaše Brána Firewall služby Azure je užitečné, když nejdřív Začínáme aplikací pracovat, když si jich nejste vědomi všech závislostí aplikací. Další informace o Azure Monitor protokoly z [analyzovat data protokolů ve službě Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Přístup ke clusteru
Po nastavení brány firewall s úspěšně, můžete pomocí interního koncového bodu (`https://<clustername>-int.azurehdinsight.net`) pro přístup k Ambari z virtuální sítě. Použít veřejný koncový bod (`https://<clustername>.azurehdinsight.net`) nebo ssh koncový bod (`<clustername>-ssh.azurehdinsight.net`), ujistěte se, že máte správný tras ve směrovací tabulce a nastavit pravidla skupiny zabezpečení sítě se pokud chcete vyhnout směrování problému assymetric vysvětlení [tady](https://docs.microsoft.com/azure/firewall/integrate-lb).

## <a name="configure-another-network-virtual-appliance"></a>Nakonfigurovat jiné síťové virtuální zařízení

>[!Important]
> Tyto informace o **pouze** povinné, pokud chcete nakonfigurovat virtuální síťové zařízení (NVA) než Brána Firewall služby Azure.

Předchozí pokyny vám pomohou nakonfigurovat Firewall služby Azure pro omezení odchozího provozu z vašeho clusteru HDInsight. Azure bránu Firewall se automaticky nakonfiguruje umožňující provoz pro spoustu běžných scénářů důležité. Pokud chcete použít jiné síťové virtuální zařízení, musíte ručně nakonfigurovat celou řadu dalších funkcí. Mějte na paměti jako následující vaše konfigurace síťové virtuální zařízení:

* Koncový bod služby podporující služby by měly být nakonfigurované koncové body služby.
* IP adresa závislosti se pro přenosy mimo HTTP/S (provoz TCP a UDP).
* Koncové body HTTP/HTTPS plně kvalifikovaný název domény je možné použít ve vašem zařízení síťového virtuálního zařízení.
* Koncové body HTTP/HTTPS zástupných znaků jsou závislosti, které se mohou lišit podle počtu kvalifikátory.
* Přiřazení směrovací tabulku, kterou vytvoříte pro vaše podsíť HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Koncový bod služby podporující závislosti

| **Koncový bod** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP adresa závislosti

| **Koncový bod** | **Podrobnosti** |
|---|---|
| \*:123 | Kontrola hodin NTP. Kontroluje provoz při více koncových bodů na portu 123 |
| IP adresy, které jsou publikované [zde](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Toto jsou služby HDInsight |
| AAD – DS privátní IP adresy pro ESP clusterů |
| \*: 16800 aktivace služby správy KLÍČŮ Windows |
| \*12000 ke službě Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Plně kvalifikovaný název domény HTTP/HTTPS závislosti

>[!Important]
> Níže uvedeného seznamu poskytuje jenom některé z vašich nejdůležitějších plně kvalifikovaných názvů domény. Získáte úplný seznam plně kvalifikovaných názvů domény pro konfiguraci vaší síťové virtuální zařízení [v tomto souboru](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Koncový bod**                                                          |
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
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Další postup

* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
