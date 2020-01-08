---
title: Konfigurace omezení odchozích síťových přenosů – Azure HDInsight
description: Přečtěte si, jak nakonfigurovat omezení odchozích síťových přenosů pro clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 6771cdb206920c8e3b746e28573de1742543b4c8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646689"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurace odchozího síťového provozu pro clustery Azure HDInsight pomocí brány firewall

Tento článek popisuje kroky pro zabezpečení odchozího provozu z clusteru HDInsight pomocí Azure Firewall. Následující postup předpokládá, že konfigurujete Azure Firewall pro existující cluster. Pokud nasazujete nový cluster a za bránou firewall, vytvořte nejdřív cluster HDInsight a podsíť a pak postupujte podle pokynů v této příručce.

## <a name="background"></a>Pozadí

Clustery Azure HDInsight se obvykle nasazují ve vaší vlastní virtuální síti. Cluster má závislosti na službách mimo tuto virtuální síť, které vyžadují, aby síťový přístup správně fungoval.

K dispozici je několik závislostí, které vyžadují příchozí provoz. Příchozí provoz správy nejde odeslat přes zařízení brány firewall. Zdrojové adresy tohoto provozu jsou známé a jsou publikovány [zde](hdinsight-management-ip-addresses.md). Pomocí těchto informací můžete také vytvořit pravidla skupiny zabezpečení sítě (NSG), která zajistí zabezpečení příchozího provozu do clusterů.

Závislosti odchozích přenosů HDInsight jsou skoro zcela definované s plně kvalifikovanými názvy domén, které nemají statické IP adresy za nimi. Nedostatek statických adres znamená, že skupiny zabezpečení sítě (skupin zabezpečení sítě) se nedají použít k uzamknutí odchozího provozu z clusteru. Adresy se často mění, protože nemůžou nastavit pravidla na základě aktuálního překladu názvů a použít ho k nastavení pravidel NSG.

Řešením pro zabezpečení odchozích adres je použití zařízení brány firewall, které může řídit odchozí přenosy na základě názvů domén. Azure Firewall může omezit odchozí přenosy HTTP a HTTPS na základě plně kvalifikovaného názvu domény (FQDN) cílového nebo [plně kvalifikovaného názvu domény](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurace Azure Firewall s využitím HDInsight

Souhrn kroků pro uzamčení odchozích dat ze stávajícího HDInsight s Azure Firewall:

1. Vytvořte bránu firewall.
1. Přidat do brány firewall pravidla aplikací
1. Přidejte do brány firewall Síťová pravidla.
1. Vytvoření směrovací tabulky.

### <a name="create-new-subnet"></a>Vytvořit novou podsíť

Ve virtuální síti, ve které se nachází váš cluster, vytvořte podsíť s názvem **AzureFirewallSubnet** .

### <a name="create-a-new-firewall-for-your-cluster"></a>Vytvoření nové brány firewall pro cluster

Pomocí postupu v části **nasazení brány firewall** z kurzu vytvořte bránu firewall s názvem **test-FW01** [: nasaďte a nakonfigurujte Azure firewall pomocí Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurace brány firewall pomocí pravidel aplikací

Vytvořte kolekci pravidel aplikace, která umožňuje clusteru odesílat a přijímat důležitá sdělení.

1. Z Azure Portal vyberte nový **test brány firewall – FW01** .

1. Přejděte na **nastavení** > **pravidla** > **kolekce pravidel aplikace** >  **+ přidat kolekci pravidel aplikace**.

    ![Title: přidat kolekci pravidel aplikace](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Na obrazovce **přidat kolekci pravidel aplikace** zadejte následující informace:

    **Horní oddíl**

    | Vlastnost|  Hodnota|
    |---|---|
    |Name (Název)| FwAppRule|
    |Priorita|200|
    |Akce|Povolit|

    **Oddíl značek plně kvalifikovaného názvu domény**

    | Name (Název) | Zdrojová adresa | FQDN – značka | Poznámky |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate a HDInsight | Vyžadováno pro služby HDI Services |

    **Oddíl cílové plně kvalifikované názvy domén**

    | Name (Název) | Zdrojové adresy | Protokol: port | Cílové plně kvalifikované názvy domén | Poznámky |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Povoluje aktivitu přihlášení systému Windows. |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Povoluje aktivitu přihlášení systému Windows. |
    | Rule_4 | * | https:443,http:80 | storage_account_name. blob. Core. Windows. NET | Nahraďte `storage_account_name` skutečným názvem svého účtu úložiště. Pokud je váš cluster zálohovaný pomocí WASB, přidejte pravidlo pro WASB. Pokud chcete použít jenom připojení HTTPS, ujistěte se, že je v účtu úložiště povolený [možnost zabezpečený přenos vyžaduje](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) . |

   ![Title: zadejte podrobnosti kolekce pravidel aplikace.](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Vyberte **Přidat**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurace brány firewall pomocí síťových pravidel

Vytvořte Síťová pravidla pro správnou konfiguraci clusteru HDInsight.

1. Pokračujeme z předchozího kroku, přejdete na **kolekce pravidel sítě** >  **+ přidat kolekci pravidel sítě**.

1. Na obrazovce **přidat kolekci pravidel sítě** zadejte následující informace:

    **Horní oddíl**

    | Vlastnost|  Hodnota|
    |---|---|
    |Name (Název)| FwNetRule|
    |Priorita|200|
    |Akce|Povolit|

    **Část IP adresy**

    | Name (Název) | Protocol (Protokol) | Zdrojové adresy | Cílové adresy | Cílové porty | Poznámky |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Časová služba |
    | Rule_2 | Všechny | * | DC_IP_Address_1, DC_IP_Address_2 | * | Pokud používáte Balíček zabezpečení podniku (ESP), pak v části IP adresy přidejte síťové pravidlo, které umožňuje komunikaci s clustery AAD-DS pro ESP. IP adresy řadičů domény najdete v části AAD-DS na portálu. |
    | Rule_3 | TCP | * | IP adresa vašeho účtu Data Lake Storage | * | Pokud používáte Azure Data Lake Storage, můžete v části IP adresy přidat síťové pravidlo, které řeší problém s SNI s ADLS Gen1 a Gen2. Tato možnost přesměruje provoz do brány firewall, což může vést k vyšším nákladům na načtení velkých objemů dat, ale přenos bude protokolován a auditován v protokolech brány firewall. Určete IP adresu účtu Data Lake Storage. K překladu plně kvalifikovaného názvu domény na IP adresu můžete použít příkaz prostředí PowerShell, například `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`.|
    | Rule_4 | TCP | * | * | 12000 | Volitelné Pokud používáte Log Analytics, vytvořte v části IP adresy síťové pravidlo, které umožní komunikaci s pracovním prostorem Log Analytics. |

    **Oddíl Service Tags**

    | Name (Název) | Protocol (Protokol) | Zdrojové adresy | Značky služeb | Cílové porty | Poznámky |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Nakonfigurujte pravidlo sítě v části značky služby pro SQL, které umožní protokolovat a auditovat provoz SQL, pokud jste nenakonfigurovali koncové body služby pro SQL Server v podsíti HDInsight, která bude bránu firewall obejít. |

   ![Title: zadejte kolekci pravidel aplikace.](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Vyberte **Přidat**.

### <a name="create-and-configure-a-route-table"></a>Vytvoření a konfigurace směrovací tabulky

Vytvořte směrovací tabulku s následujícími položkami:

* Všechny IP adresy ze [služeb stavu a správy: všechny oblasti](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) s typem dalšího segmentu směrování **Internet**.

* Dvě IP adresy pro oblast, ve které se cluster vytváří ze [služeb stavu a správy: konkrétní oblasti](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) s typem dalšího segmentu směrování **Internet**.

* Jedna trasa virtuálního zařízení pro IP adresu 0.0.0.0/0 s dalším segmentem směrování je vaše Azure Firewall privátní IP adresa.

Pokud chcete například nakonfigurovat směrovací tabulku pro cluster vytvořený v oblasti USA "Východní USA", použijte následující postup:

1. Vyberte Azure firewall **test-FW01**. Zkopírujte **privátní IP adresu** uvedenou na stránce **Přehled** . V tomto příkladu použijeme **ukázkovou adresu 10.0.2.4**.

1. Pak přejděte ke **všem službám** > **sítě** > **směrovat tabulky** a **vytvořit směrovací tabulku**.

1. Z nové trasy přejděte do **nastavení** > **trasy** >  **+ Přidat**. Přidejte následující trasy:

| Název trasy | Předpona adresy | Typ dalšího segmentu | Adresa dalšího segmentu |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | není k dispozici |
| 23.99.5.239 | 23.99.5.239/32 | Internet | není k dispozici |
| 168.61.48.131 | 168.61.48.131/32 | Internet | není k dispozici |
| 138.91.141.162 | 138.91.141.162/32 | Internet | není k dispozici |
| 13.82.225.233 | 13.82.225.233/32 | Internet | není k dispozici |
| 40.71.175.99 | 40.71.175.99/32 | Internet | není k dispozici |
| 0.0.0.0 | 0.0.0.0/0 | Virtuální zařízení | 10.0.2.4 |

Dokončete konfiguraci směrovací tabulky:

1. Přiřaďte směrovací tabulku, kterou jste vytvořili v podsíti HDInsight, a v části **Nastavení**vyberte **podsítě** .

1. Vyberte **+ přidružit**.

1. Na obrazovce **přidružit podsíť** vyberte virtuální síť, do které byl cluster vytvořen, a **podsíť** , kterou jste použili pro cluster HDInsight.

1. Vyberte **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Provoz hraničního uzlu nebo vlastní aplikace

Výše uvedené kroky umožní clusteru bez problémů fungovat. Stále musíte nakonfigurovat závislosti, aby vyhovovaly vlastním aplikacím běžícím na hraničních uzlech, pokud jsou k dispozici.

Závislosti aplikace musí být identifikovány a přidány do Azure Firewall nebo směrovací tabulky.

Pro přenos aplikací je nutné vytvořit trasy, aby se předešlo problémům s asymetrickým směrováním.

Pokud vaše aplikace mají jiné závislosti, musí být do Azure Firewall přidány. Vytvořte pravidla aplikací pro povolení přenosů HTTP/HTTPS a síťových pravidel pro všechno ostatní.

## <a name="logging-and-scale"></a>Protokolování a škálování

Azure Firewall může odesílat protokoly do několika různých systémů úložiště. Pokyny ke konfiguraci protokolování pro bránu firewall najdete v následujících krocích v [kurzu: monitorování Azure firewall protokolů a metrik](../firewall/tutorial-diagnostics.md).

Po dokončení nastavení protokolování můžete při protokolování dat do Log Analytics zobrazit blokované přenosy s dotazem, jako je například následující:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrace vašich Azure Firewall s protokoly Azure Monitor je užitečná při prvním získání aplikace, když si nejste vědomi všech závislostí aplikace. Další informace o protokolech Azure Monitor můžete získat z [analýzy dat protokolu v Azure monitor](../azure-monitor/log-query/log-query-overview.md)

Další informace o omezeních Azure Firewall a žádosti o zvýšení kapacity najdete v [tomto](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokumentu, nebo se podívejte na [Nejčastější dotazy](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Přístup ke clusteru

Po úspěšném nastavení brány firewall můžete pomocí interního koncového bodu (`https://CLUSTERNAME-int.azurehdinsight.net`) přistupovat k Ambari zevnitř virtuální sítě.

Pokud chcete použít veřejný koncový bod (`https://CLUSTERNAME.azurehdinsight.net`) nebo koncový bod SSH (`CLUSTERNAME-ssh.azurehdinsight.net`), ujistěte se, že máte správné trasy v tabulce směrování a NSG pravidla, abyste se vyhnuli [](../firewall/integrate-lb.md)problému s asymetrickým směrováním. Konkrétně v takovém případě je potřeba povolit IP adresu klienta v příchozích pravidlech NSG a také ji přidat do uživatelsky definované tabulky směrování s dalším segmentem směrování nastaveným jako `internet`. Pokud to není správně nastavené, zobrazí se chyba časového limitu.

## <a name="configure-another-network-virtual-appliance"></a>Konfigurace jiného síťového virtuálního zařízení

> [!Important]
> Následující informace jsou požadovány **pouze** v případě, že chcete nakonfigurovat síťové virtuální zařízení (síťové virtuální zařízení) jiné než Azure firewall.

Předchozí pokyny vám pomůžou nakonfigurovat Azure Firewall pro omezení odchozího provozu z clusteru HDInsight. Azure Firewall se automaticky nakonfiguruje tak, aby povolovala provoz pro spoustu běžných důležitých scénářů. Pokud chcete použít jiné síťové virtuální zařízení, budete muset ručně nakonfigurovat řadu dalších funkcí. Při konfiguraci síťového virtuálního zařízení mějte na paměti následující:

* Služby podporující koncový bod služby by měly být nakonfigurované s koncovými body služby.
* Závislosti IP adres jsou pro přenos bez HTTP/S (provoz TCP i UDP).
* Koncové názvy koncových bodů HTTP/HTTPS můžete umístit do zařízení síťové virtuální zařízení.
* Koncové body HTTP/HTTPS se zástupnými znaky jsou závislosti, které se můžou lišit podle počtu kvalifikátorů.
* Přiřaďte směrovací tabulku, kterou vytvoříte ve své podsíti HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby

| **Koncový bod** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Závislosti IP adres

| **Koncový bod** | **Podrobnosti** |
|---|---|
| \*:123 | Kontroluje se čas NTP. Provoz se kontroluje na více koncových bodech na portu 123. |
| [Zde](hdinsight-management-ip-addresses.md) publikované IP adresy | Jedná se o službu HDInsight |
| Privátní IP adresy AAD-DS pro clustery ESP |
| \*: 16800 pro aktivaci Windows služby správy klíčů |
| \*12000 pro Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Závislosti HTTP/HTTPS v plně kvalifikovaném názvu domény

> [!Important]
> Následující seznam obsahuje jenom několik nejdůležitějších plně kvalifikovaných názvů domén. Úplný seznam plně kvalifikovaných názvů domény pro konfiguraci síťové virtuální zařízení můžete získat [v tomto souboru](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

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
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Další kroky

* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
