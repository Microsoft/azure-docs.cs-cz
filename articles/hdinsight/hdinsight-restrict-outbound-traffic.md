---
title: Konfigurace omezení odchozích síťových přenosů – Azure HDInsight
description: Přečtěte si, jak nakonfigurovat omezení odchozích síťových přenosů pro clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: f87c3665f558b3185e95b0ad0aa18a883439a221
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006513"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurace odchozího síťového provozu pro clustery Azure HDInsight pomocí brány firewall

Tento článek popisuje kroky pro zabezpečení odchozího provozu z clusteru HDInsight pomocí Azure Firewall. Následující postup předpokládá, že konfigurujete Azure Firewall pro existující cluster. Pokud nasazujete nový cluster za bránou firewall, vytvořte nejdřív cluster HDInsight a podsíť. Pak postupujte podle kroků uvedených v této příručce.

## <a name="background"></a>Pozadí

Clustery HDInsight jsou obvykle nasazené ve virtuální síti. Cluster má závislosti na službách mimo tuto virtuální síť.

K dispozici je několik závislostí, které vyžadují příchozí provoz. Příchozí provoz správy nejde odeslat přes zařízení brány firewall. Zdrojové adresy tohoto provozu jsou známé a jsou publikovány [zde](hdinsight-management-ip-addresses.md). Pomocí těchto informací můžete také vytvořit pravidla skupiny zabezpečení sítě (NSG), která zajistí zabezpečení příchozího provozu do clusterů.

Závislosti odchozích přenosů HDInsight jsou skoro zcela definované s plně kvalifikovanými názvy domén. Které nejsou za nimi statické IP adresy. Nedostatek statických adres znamená, že skupiny zabezpečení sítě (skupin zabezpečení sítě) nemůžou uzamknout odchozí přenosy z clusteru. U adres, které se často mění, se nedají nastavit pravidla na základě aktuálního překladu názvů a použití.

Zabezpečte odchozí adresy pomocí brány firewall, která může řídit odchozí přenosy na základě názvů domén. Azure Firewall omezuje odchozí provoz na základě plně kvalifikovaného názvu domény (FQDN) cílového nebo [plně kvalifikovaného názvu domény](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurace Azure Firewall s využitím HDInsight

Souhrn kroků pro uzamčení odchozích dat ze stávajícího HDInsight s Azure Firewall:

1. Vytvořte podsíť.
1. Vytvořte bránu firewall.
1. Přidat do brány firewall pravidla aplikací
1. Přidejte do brány firewall Síťová pravidla.
1. Vytvořte směrovací tabulku.

### <a name="create-new-subnet"></a>Vytvořit novou podsíť

Ve virtuální síti, ve které se nachází váš cluster, vytvořte podsíť s názvem **AzureFirewallSubnet** .

### <a name="create-a-new-firewall-for-your-cluster"></a>Vytvoření nové brány firewall pro cluster

Pomocí postupu v části **nasazení brány firewall** z kurzu vytvořte bránu firewall s názvem **test-FW01** [: nasaďte a nakonfigurujte Azure firewall pomocí Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurace brány firewall pomocí pravidel aplikací

Vytvořte kolekci pravidel aplikace, která umožňuje clusteru odesílat a přijímat důležitá sdělení.

1. Z Azure Portal vyberte nový **test brány firewall – FW01** .

1. Přejděte do **Nastavení**  >  **pravidla**  >  **aplikace kolekce pravidel**  >  **+ přidat kolekci pravidel aplikace**.

    ![Title: přidat kolekci pravidel aplikace](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Na obrazovce **přidat kolekci pravidel aplikace** zadejte následující informace:

    **Horní oddíl**

    | Vlastnost|  Hodnota|
    |---|---|
    |Název| FwAppRule|
    |Priorita|200|
    |Akce|Povolit|

    **Oddíl značek plně kvalifikovaného názvu domény**

    | Name | Zdrojová adresa | FQDN – značka | Poznámky |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate a HDInsight | Vyžadováno pro služby HDI Services |

    **Oddíl cílové plně kvalifikované názvy domén**

    | Name | Zdrojové adresy | Protokol: port | Cílové plně kvalifikované názvy domén | Poznámky |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https: 443 | login.windows.net | Povoluje aktivitu přihlášení systému Windows. |
    | Rule_3 | * | https: 443 | login.microsoftonline.com | Povoluje aktivitu přihlášení systému Windows. |
    | Rule_4 | * | https: 443, http: 80 | storage_account_name. blob. Core. Windows. NET | Nahraďte `storage_account_name` skutečným názvem účtu úložiště. Pokud je váš cluster zálohovaný pomocí WASB, přidejte pravidlo pro WASB. Pokud chcete použít jenom připojení HTTPS, ujistěte se, že je v účtu úložiště povolený [možnost zabezpečený přenos vyžaduje](../storage/common/storage-require-secure-transfer.md) . |

   ![Title: zadejte podrobnosti kolekce pravidel aplikace.](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Vyberte **Přidat**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurace brány firewall pomocí síťových pravidel

Vytvořte Síťová pravidla pro správnou konfiguraci clusteru HDInsight.

1. Pokračujeme z předchozího kroku, přejdete na **kolekce pravidel sítě**  >  **+ přidat kolekci pravidel sítě**.

1. Na obrazovce **přidat kolekci pravidel sítě** zadejte následující informace:

    **Horní oddíl**

    | Vlastnost|  Hodnota|
    |---|---|
    |Název| FwNetRule|
    |Priorita|200|
    |Akce|Povolit|

    **Část IP adresy**

    | Name | Protokol | Zdrojové adresy | Cílové adresy | Cílové porty | Poznámky |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Časová služba |
    | Rule_2 | Všechny | * | DC_IP_Address_1 DC_IP_Address_2 | * | Pokud používáte Balíček zabezpečení podniku (ESP), pak v části IP adresy přidejte síťové pravidlo, které umožňuje komunikaci s clustery AAD-DS pro ESP. IP adresy řadičů domény najdete v části AAD-DS na portálu. |
    | Rule_3 | TCP | * | IP adresa vašeho účtu Data Lake Storage | * | Pokud používáte Azure Data Lake Storage, můžete v části IP adresy přidat síťové pravidlo, které řeší problém s SNI s ADLS Gen1 a Gen2. Tato možnost bude směrovat provoz do brány firewall. To může mít za následek vyšší náklady na načtení velkých objemů dat, ale přenos bude protokolován a auditován v protokolech brány firewall. Určete IP adresu účtu Data Lake Storage. Můžete použít příkaz prostředí PowerShell, například `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` k překladu plně kvalifikovaného názvu domény na IP adresu.|
    | Rule_4 | TCP | * | * | 12000 | Volitelné Pokud používáte Log Analytics, vytvořte v části IP adresy síťové pravidlo, které umožní komunikaci s pracovním prostorem Log Analytics. |

    **Oddíl Service Tags**

    | Name | Protokol | Zdrojové adresy | Značky služeb | Cílové porty | Poznámky |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Nakonfigurujte pravidlo sítě v části značky služby pro SQL, které vám umožní protokolovat a auditovat provoz SQL. Pokud jste nenakonfigurovali koncové body služby pro SQL Server v podsíti HDInsight, která bude bránu firewall obejít. |
    | Rule_8 | TCP | * | Azure Monitor | * | volitelné Toto pravidlo by mělo přidat zákazníci, kteří chtějí používat funkci automatického škálování. |
    
   ![Title: zadejte kolekci pravidel aplikace.](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Vyberte **Přidat**.

### <a name="create-and-configure-a-route-table"></a>Vytvoření a konfigurace směrovací tabulky

Vytvořte směrovací tabulku s následujícími položkami:

* Všechny IP adresy ze [služeb stavu a správy: všechny oblasti](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) s typem dalšího segmentu směrování **Internet**.

* Dvě IP adresy pro oblast, ve které se cluster vytváří ze [služeb stavu a správy: konkrétní oblasti](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) s typem dalšího segmentu směrování **Internet**.

* Jedna trasa virtuálního zařízení pro IP adresu 0.0.0.0/0 s dalším segmentem směrování je vaše Azure Firewall privátní IP adresa.

Pokud chcete například nakonfigurovat směrovací tabulku pro cluster vytvořený v oblasti USA "Východní USA", použijte následující postup:

1. Vyberte Azure firewall **test-FW01**. Zkopírujte **privátní IP adresu** uvedenou na stránce **Přehled** . V tomto příkladu použijeme **ukázkovou adresu 10.0.2.4**.

1. Pak přejděte na **všechny služby**  >  **sítě**  >  **směrovací tabulky** a **vytvořte tabulku směrování**.

1. Z nové trasy přejděte do **Nastavení**  >  **trasy**  >  **+ Přidat**. Přidejte následující trasy:

| Název trasy | Předpona adresy | Typ dalšího přesměrování | Adresa dalšího segmentu |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Není k dispozici |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Není k dispozici |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Není k dispozici |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Není k dispozici |
| 13.82.225.233 | 13.82.225.233/32 | Internet | Není k dispozici |
| 40.71.175.99 | 40.71.175.99/32 | Internet | Není k dispozici |
| 0.0.0.0 | 0.0.0.0/0 | Virtuální zařízení | 10.0.2.4 |

Dokončete konfiguraci směrovací tabulky:

1. Přiřaďte směrovací tabulku, kterou jste vytvořili v podsíti HDInsight, a v části **Nastavení**vyberte **podsítě** .

1. Vyberte **+ přidružit**.

1. Na obrazovce **přidružit podsíť** vyberte virtuální síť, do které byl cluster vytvořen. A **podsíť** , kterou jste použili pro cluster HDInsight.

1. Vyberte **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Provoz hraničního uzlu nebo vlastní aplikace

Výše uvedené kroky umožní clusteru bez problémů fungovat. Stále musíte nakonfigurovat závislosti, aby vyhovovaly vlastním aplikacím běžícím na hraničních uzlech, pokud jsou k dispozici.

Závislosti aplikace musí být identifikovány a přidány do Azure Firewall nebo směrovací tabulky.

Pro přenos aplikací je nutné vytvořit trasy, aby se předešlo problémům s asymetrickým směrováním.

Pokud vaše aplikace mají jiné závislosti, musí být do Azure Firewall přidány. Vytvořte pravidla aplikací pro povolení přenosů HTTP/HTTPS a síťových pravidel pro všechno ostatní.

## <a name="logging-and-scale"></a>Protokolování a škálování

Azure Firewall může odesílat protokoly do několika různých systémů úložiště. Pokyny ke konfiguraci protokolování pro bránu firewall najdete v následujících krocích v [kurzu: monitorování Azure firewall protokolů a metrik](../firewall/tutorial-diagnostics.md).

Pokud jste dokončili nastavení protokolování, pokud používáte Log Analytics, můžete zobrazit blokovaný provoz s dotazem, jako je například:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrace Azure Firewall s protokoly Azure Monitor je užitečná při prvním zprovoznění aplikace. Obzvláště pokud si nejste vědomi všech závislostí aplikace. Další informace o protokolech Azure Monitor můžete získat z [analýzy dat protokolu v Azure monitor](../azure-monitor/log-query/log-query-overview.md)

Další informace o omezeních Azure Firewall a žádosti o zvýšení kapacity najdete v [tomto](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokumentu, nebo se podívejte na [Nejčastější dotazy](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Přístup ke clusteru

Po úspěšném nastavení brány firewall můžete pomocí interního koncového bodu ( `https://CLUSTERNAME-int.azurehdinsight.net` ) získat přístup k Ambari zevnitř virtuální sítě.

Pokud chcete použít veřejný koncový bod ( `https://CLUSTERNAME.azurehdinsight.net` ) nebo koncový bod SSH ( `CLUSTERNAME-ssh.azurehdinsight.net` ), ujistěte se, že máte správné trasy v tabulce směrování a NSG pravidla, abyste se vyhnuli [here](../firewall/integrate-lb.md)problému s asymetrickým směrováním. Konkrétně v takovém případě musíte povolit IP adresu klienta v příchozích pravidlech NSG a také ji přidat do tabulky směrování definované uživatelem s další segmenty směrování `internet` . Pokud není směrování správně nastavené, zobrazí se chyba časového limitu.

## <a name="next-steps"></a>Další kroky

* [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Konfigurace síťového virtuálního zařízení](./network-virtual-appliance.md)
