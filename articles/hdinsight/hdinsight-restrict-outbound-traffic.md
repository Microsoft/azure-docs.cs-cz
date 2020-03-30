---
title: Konfigurace omezení odchozího síťového provozu – Azure HDInsight
description: Přečtěte si, jak nakonfigurovat omezení odchozího síťového provozu pro clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370181"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Konfigurace odchozího síťového provozu pro clustery Azure HDInsight pomocí brány firewall

Tento článek obsahuje postup zabezpečení odchozího provozu z clusteru HDInsight pomocí Azure Firewall. Následující kroky předpokládají, že konfigurujete bránu Azure Firewall pro existující cluster. Pokud nasazujete nový cluster a za bránou firewall, nejprve vytvořte cluster a podsíť HDInsight a postupujte podle pokynů v této příručce.

## <a name="background"></a>Pozadí

Clustery Azure HDInsight se obvykle nasazují ve vlastní virtuální síti. Cluster má závislosti na službách mimo tuto virtuální síť, které vyžadují přístup k síti, aby správně fungovaly.

Existuje několik závislostí, které vyžadují příchozí provoz. Příchozí provoz správy nelze odeslat prostřednictvím zařízení brány firewall. Zdrojové adresy pro tento provoz jsou známy a jsou publikovány [zde](hdinsight-management-ip-addresses.md). Můžete také vytvořit pravidla skupiny zabezpečení sítě (NSG) s těmito informacemi pro zabezpečení příchozího provozu do clusterů.

Závislosti odchozího provozu HDInsight jsou téměř zcela definovány pomocí plně kvalifikovaných adres, které nemají za sebou statické IP adresy. Nedostatek statických adres znamená, že skupiny zabezpečení sítě (NSG) nelze použít k uzamčení odchozího provozu z clusteru. Adresy se mění dostatečně často, že nelze nastavit pravidla na základě aktuálního překladu názvů a použít je k nastavení pravidel nsg.

Řešením zabezpečení odchozích adres je použití zařízení brány firewall, které může řídit odchozí provoz na základě názvů domén. Azure Firewall může omezit odchozí přenosy HTTP a HTTPS na základě hlavního názvového přístupu k cíli cílových značek nebo [značek FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Konfigurace azure firewallu pomocí HDInsightu

Souhrn kroků k uzamčení odchozího přenosu z existujícího HDInsightu pomocí Azure Firewall jsou:

1. Vytvořte podsíť.
1. Vytvořte bránu firewall.
1. Přidání pravidel aplikace do brány firewall
1. Přidejte do brány firewall pravidla sítě.
1. Vytvořte směrovací tabulku.

### <a name="create-new-subnet"></a>Vytvořit novou podsíť

Vytvořte podsíť s názvem **AzureFirewallSubnet** ve virtuální síti, kde existuje váš cluster.

### <a name="create-a-new-firewall-for-your-cluster"></a>Vytvoření nové brány firewall pro cluster

Vytvořte bránu firewall s názvem **Test-FW01** pomocí kroků v **části Nasazení brány firewall** z [kurzu: Nasazení a konfigurace brány Azure firewall pomocí portálu Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Konfigurace brány firewall pomocí pravidel aplikací

Vytvořte kolekci pravidel aplikace, která umožňuje clusteru odesílat a přijímat důležité komunikace.

1. Na webu Azure Portal vyberte nový firewall **Test-FW01.**

1. Přejděte do kolekce**Rules** > **pravidel** > aplikace **nastavení** > **+ Přidat kolekci pravidel aplikace**.

    ![Název: Přidání kolekce pravidel aplikace](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Na obrazovce **Přidat kolekce pravidel aplikace** zadejte následující informace:

    **Horní část**

    | Vlastnost|  Hodnota|
    |---|---|
    |Name (Název)| FwAppPravidlo|
    |Priorita|200|
    |Akce|Povolit|

    **Oddíl značek FQDN**

    | Name (Název) | Zdrojová adresa | Značka FQDN | Poznámky |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate a HDInsight | Vyžadováno pro služby HDI |

    **Oddíl cílové skupiny kvin**

    | Name (Název) | Zdrojové adresy | Protokol:Port | Cílová fqdns | Poznámky |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Umožňuje aktivitu přihlášení systému Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Umožňuje aktivitu přihlášení systému Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Nahraďte `storage_account_name` skutečný název účtu úložiště. Pokud je váš cluster zálohován WASB, přidejte pravidlo pro WASB. Chcete-li používat pouze připojení https, ujistěte se, že ["požadovaný zabezpečený přenos"](../storage/common/storage-require-secure-transfer.md) je povolena na účtu úložiště. |

   ![Název: Zadejte podrobnosti kolekce pravidel aplikace.](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Vyberte **Přidat**.

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurace brány firewall pomocí síťových pravidel

Vytvořte síťová pravidla pro správnou konfiguraci clusteru HDInsight.

1. Pokračujte od předchozího kroku, přejděte do **kolekce síťových** > pravidel **+ Přidat kolekci síťových pravidel**.

1. Na obrazovce **Přidat kolekci síťových pravidel** zadejte následující informace:

    **Horní část**

    | Vlastnost|  Hodnota|
    |---|---|
    |Name (Název)| Pravidlo FwNet|
    |Priorita|200|
    |Akce|Povolit|

    **Oddíl Adresy IP**

    | Name (Název) | Protocol (Protokol) | Zdrojové adresy | Cílové adresy | Cílové porty | Poznámky |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Časová služba |
    | Rule_2 | Všechny | * | DC_IP_Address_1, DC_IP_Address_2 | * | Pokud používáte balíček zabezpečení rozlehlé sítě (ESP), přidejte do části Adresy IP síťové pravidlo, které umožňuje komunikaci se službou AAD-DS pro clustery ESP. IP adresy řadičů domény naleznete v části AAD-DS na portálu |
    | Rule_3 | TCP | * | IP adresa vašeho účtu Data Lake Storage | * | Pokud používáte Azure Data Lake Storage, pak můžete přidat síťové pravidlo v části IP adresy k řešení problému SNI s ADLS Gen1 a Gen2. Tato možnost směruje přenos y do brány firewall, což může mít za následek vyšší náklady na velké zatížení dat, ale přenos bude zaznamenán a auditovatelný v protokolech brány firewall. Určete IP adresu vašeho účtu Úložiště datového jezera. Příkaz powershellu můžete použít `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` například k vyřešení hlavního názvového názvového práva na adresu IP.|
    | Rule_4 | TCP | * | * | 12000 | (Nepovinné) Pokud používáte Log Analytics, vytvořte síťové pravidlo v části IP adresy, abyste povolili komunikaci s pracovním prostorem Log Analytics. |

    **Oddíl Značky servisu**

    | Name (Název) | Protocol (Protokol) | Zdrojové adresy | Značky služeb | Cílové porty | Poznámky |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Nakonfigurujte síťové pravidlo v části Značky služeb pro SQL, které vám umožní protokolovat a auditovat přenosy SQL, pokud jste nenakonfigurovali koncové body služby pro SQL Server v podsíti HDInsight, která obcvede bránu firewall. |

   ![Název: Zadejte kolekci pravidel aplikace.](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Vyberte **Přidat**.

### <a name="create-and-configure-a-route-table"></a>Vytvoření a konfigurace tabulky postupu

Vytvořte tabulku postupu s následujícími položkami:

* Všechny IP adresy ze [služeb Stav a správa: Všechny oblasti](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) s dalším typem směrování **internetu**.

* Dvě ip adresy pro oblast, kde je cluster vytvořen ze [služeb stavu a správy: Určité oblasti](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) s dalším typem směrování **internetu**.

* Jedna trasa virtuálního zařízení pro IP adresu 0.0.0.0/0, přičemž další směrování je privátní IP adresa Azure Firewall.

Chcete-li například nakonfigurovat směrovací tabulku pro cluster vytvořený v oblasti USA "Východní USA", použijte následující kroky:

1. Vyberte azure firewall **Test-FW01**. Zkopírujte **privátní IP adresu** uvedenou na stránce **Přehled.** V tomto příkladu použijeme **ukázkovou adresu 10.0.2.4**.

1. Potom přejděte na všechny **služby,** > v**jako jsou síťové** > **tabulky trasy** a vytvořit **tabulku tras**.

1. Z nové trasy přejděte do **nastavení** > **Trasy** > **+ Přidat**. Přidejte následující trasy:

| Název trasy | Předpona adresy | Typ dalšího segmentu | Adresa dalšího segmentu |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | Není k dispozici |
| 23.99.5.239 | 23.99.5.239/32 | Internet | Není k dispozici |
| 168.61.48.131 | 168.61.48.131/32 | Internet | Není k dispozici |
| 138.91.141.162 | 138.91.141.162/32 | Internet | Není k dispozici |
| 13.82.225.233 | 13.82.225.233/32 | Internet | Není k dispozici |
| 40.71.175.99 | 40.71.175.99/32 | Internet | Není k dispozici |
| 0.0.0.0 | 0.0.0.0/0 | Virtuální zařízení | 10.0.2.4 |

Dokončete konfiguraci tabulky tras:

1. Přiřaďte směrovací tabulku, kterou jste vytvořili, k podsíti HDInsight výběrem **podsítí** v části **Nastavení**.

1. Vyberte **+ Přidružit**.

1. Na obrazovce **Přidružit podsíť** vyberte virtuální síť, do které byl cluster vytvořen, a **podsíť,** kterou jste použili pro cluster HDInsight.

1. Vyberte **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Provoz hraničních uzlů nebo vlastních aplikací

Výše uvedené kroky umožní clusteru pracovat bez problémů. Stále je třeba nakonfigurovat závislosti tak, aby vyhovovaly vašim vlastním aplikacím spuštěným na hraničních uzlech, pokud je to možné.

Závislosti aplikací musí být identifikovány a přidány do brány Azure Firewall nebo směrovací tabulky.

Trasy musí být vytvořeny pro provoz aplikace, aby se zabránilo asymetrické problémy se směrováním.

Pokud vaše aplikace mají jiné závislosti, je třeba je přidat do brány Azure Firewall. Vytvořte pravidla aplikace pro povolení přenosu HTTP/HTTPS a síťových pravidel pro všechno ostatní.

## <a name="logging-and-scale"></a>Protokolování a škálování

Azure Firewall můžete odesílat protokoly do několika různých systémů úložiště. Pokyny ke konfiguraci protokolování pro bránu firewall naleznete v [pokynech k monitorování protokolů a metrik brány Azure Firewall](../firewall/tutorial-diagnostics.md).

Pokud po dokončení nastavení protokolování zaznamenáváte data do log analytics, můžete zobrazit blokovaný provoz pomocí následujícího dotazu:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrace azure firewall u protokolů Azure Monitor udává, když poprvé získáte aplikaci, když si neuvědomujete všechny závislosti aplikací. Další informace o protokolech Azure Monitoru [najdete v aplikaci Analyzovat data protokolu v azure monitoru.](../azure-monitor/log-query/log-query-overview.md)

Informace o omezení škálování brány Azure firewall a zvýšení požadavků najdete v [tomto](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) dokumentu nebo v [nejčastějších dotazech](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Přístup ke clusteru

Po úspěšném nastavení brány firewall můžete použít interní`https://CLUSTERNAME-int.azurehdinsight.net`koncový bod ( ) pro přístup k Ambari zevnitř virtuální sítě.

Chcete-li použít veřejný`https://CLUSTERNAME.azurehdinsight.net`koncový bod (`CLUSTERNAME-ssh.azurehdinsight.net`) nebo koncový bod ssh ( ), ujistěte se, že máte správné trasy v tabulce trasy a pravidla skupiny nsg, abyste se vyhnuli asymetrickému problému směrování, [který je vysvětlen zde](../firewall/integrate-lb.md). Konkrétně v tomto případě je nutné povolit IP adresu klienta v pravidlech příchozí skupiny nsg a `internet`také ji přidat do uživatelem definované směrovací tabulky s další sadou směrování jako . Pokud to není správně nastaveno, zobrazí se chyba časového opojení.

## <a name="configure-another-network-virtual-appliance"></a>Konfigurace jiného síťového virtuálního zařízení

> [!Important]
> Následující informace jsou **vyžadovány pouze** v případě, že chcete nakonfigurovat jiné síťové virtuální zařízení (NVA) než Azure Firewall.

Předchozí pokyny vám pomohou nakonfigurovat Azure Firewall pro omezení odchozího provozu z clusteru HDInsight. Brána Azure Firewall se automaticky nakonfiguruje tak, aby umožňovala provoz pro mnoho běžných důležitých scénářů. Pokud chcete používat jiné síťové virtuální zařízení, budete muset ručně nakonfigurovat řadu dalších funkcí. Při konfiguraci síťového virtuálního zařízení mějte na paměti následující:

* Služby podporující službu koncového bodu by měly být konfigurovány s koncovými body služby.
* Závislosti adres IP jsou určeny pro přenosy bez protokolu HTTP/S (přenosy TCP i UDP).
* Koncové body FQDN HTTP/HTTPS lze umístit do zařízení NVA.
* Koncové body HTTP/HTTPS se zástupnými body jsou závislosti, které se mohou lišit v závislosti na počtu kvalifikátorů.
* Přiřaďte směrovací tabulku, kterou vytvoříte, do podsítě HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Závislosti podporující koncový bod služby

| **Koncový bod** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Závislosti ip adres

| **Koncový bod** | **Podrobnosti** |
|---|---|
| \*:123 | Kontrola hodin NTP. Provoz je kontrolován na více koncových bodech na portu 123 |
| IP adresy zveřejněné [zde](hdinsight-management-ip-addresses.md) | Jedná se o službu HDInsight |
| Soukromé IP adresy AAD-DS pro clustery ESP |
| \*:16800 pro aktivaci systému WINDOWS |
| \*12000 pro analýzu protokolů |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS závislosti

> [!Important]
> Níže uvedený seznam obsahuje pouze několik nejdůležitějších fqdn. V [tomto souboru](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)můžete získat úplný seznam plně kvalifikovaných adres pro konfiguraci služby NVA .

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
