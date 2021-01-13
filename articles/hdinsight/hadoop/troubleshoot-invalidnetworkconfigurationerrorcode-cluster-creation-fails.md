---
title: Chyba InvalidNetworkConfigurationErrorCode – Azure HDInsight
description: Různé důvody pro neúspěšné vytváření clusterů pomocí InvalidNetworkConfigurationErrorCode ve službě Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.openlocfilehash: 2478148f946ddc88e571b76396544b028455ec75
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132100"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Vytvoření clusteru selhalo s InvalidNetworkConfigurationErrorCode ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

Pokud se zobrazí kód chyby `InvalidNetworkConfigurationErrorCode` s popisem "Virtual Network konfigurace není kompatibilní s požadavkem HDInsight", obvykle to znamená problém s [konfigurací virtuální sítě](../hdinsight-plan-virtual-network-deployment.md) pro váš cluster. Na základě zbytku popisu chyby použijte následující části k vyřešení vašeho problému.

## <a name="hostname-resolution-failed"></a>"Překlad názvu hostitele se nezdařil"

### <a name="issue"></a>Problém

Popis chyby obsahuje "překlad názvu hostitele se nezdařil".

### <a name="cause"></a>Příčina

Tato chyba odkazuje na problém s vlastní konfigurací DNS. Servery DNS v rámci virtuální sítě můžou předávat dotazy DNS do rekurzivních překladačů Azure za účelem překladu názvů hostitelů v této virtuální síti (podrobnosti najdete [v tématu překlad IP adres ve virtuálních sítích](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ). Přístup k rekurzivním překladačům Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16. Tato IP adresa je dostupná jenom z virtuálních počítačů Azure. Takže nebude fungovat, pokud používáte server DNS OnPrem nebo server DNS je virtuální počítač Azure, který není součástí virtuální sítě clusteru.

### <a name="resolution"></a>Řešení

1. Připojte se přes SSH k virtuálnímu počítači, který je součástí clusteru, a spusťte příkaz `hostname -f` . Tato akce vrátí plně kvalifikovaný název domény hostitele (dále jen `<host_fqdn>` v následujících pokynech).

1. Pak spusťte příkaz `nslookup <host_fqdn>` (například `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Pokud tento příkaz přeloží název na IP adresu, znamená to, že váš server DNS funguje správně. V takovém případě získáte případ podpory pomocí služby HDInsight a my prozkoumáme váš problém. Ve svém případu podpory zahrňte kroky pro řešení potíží, které jste provedli. Tím se nám pomůžete problém vyřešit rychleji.

1. Pokud výše uvedený příkaz nevrátí IP adresu, spusťte příkaz `nslookup <host_fqdn> 168.63.129.16` (například `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` ). Pokud je tento příkaz schopný přeložit IP adresu, znamená to, že server DNS nepředává dotaz do DNS Azure, nebo není virtuálním počítačem, který je součástí stejné virtuální sítě jako cluster.

1. Pokud nemáte virtuální počítač Azure, který může fungovat jako vlastní server DNS ve virtuální síti clusteru, musíte ho nejdřív přidat. Vytvořte virtuální počítač ve virtuální síti, který se nakonfiguruje jako služba DNS pro přeposílání.

1. Jakmile ve virtuální síti nasadíte virtuální počítač, nakonfigurujte pravidla předávání DNS na tomto virtuálním počítači. Předejte všechny požadavky na překlad názvů IDN do 168.63.129.16 a zbytek na váš server DNS. [Tady](../hdinsight-plan-virtual-network-deployment.md) je příklad tohoto instalačního programu pro vlastní server DNS.

1. Přidejte IP adresu tohoto virtuálního počítače jako první položku DNS pro konfiguraci služby Virtual Network DNS.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>Nepovedlo se připojit k Azure Storagemu účtu.

### <a name="issue"></a>Problém

Popis chyby obsahuje "připojení k účtu Azure Storage se nepovedlo nebo se nepovedlo připojit k Azure SQL".

### <a name="cause"></a>Příčina

Azure Storage a SQL nemají pevné IP adresy, proto musíme Povolit odchozí připojení ke všem IP adresám, aby bylo možné získat přístup k těmto službám. Přesné kroky řešení závisí na tom, jestli jste nastavili skupinu zabezpečení sítě (NSG) nebo pravidla User-Defined (UDR). Podrobnosti o těchto konfiguracích najdete v části [řízení síťového provozu pomocí služby HDInsight se skupinami zabezpečení sítě a uživatelsky definovanými trasami](../control-network-traffic.md) .

### <a name="resolution"></a>Řešení

* Pokud váš cluster používá [skupinu zabezpečení sítě (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Přejít na Azure Portal a Identifikujte NSG, která je přidružená k podsíti, ve které se cluster nasazuje. V části **odchozí pravidla zabezpečení** povolte odchozí přístup k Internetu bez omezení (Všimněte si, že tady je **nižší priorita** , což znamená vyšší prioritu). V části **podsítě** také potvrďte, zda je tento NSG použit pro podsíť clusteru.

* Pokud váš cluster používá [trasy definované uživatelem (udr)](../../virtual-network/virtual-networks-udr-overview.md).

    Přejít na Azure Portal a Identifikujte směrovací tabulku, která je přidružená k podsíti, ve které se cluster nasazuje. Po nalezení směrovací tabulky pro podsíť zkontrolujte část **trasy** v ní.

    Pokud jsou definované trasy, ujistěte se, že existují trasy pro IP adresy v oblasti, ve které byl cluster nasazený, a **typem** pro každou trasu je **Internet**. Pro každou požadovanou IP adresu popsanou ve výše uvedeném článku by měla být definována trasa.

## <a name="failed-to-establish-an-outbound-connection-from-the-cluster-for-the-communication-with-the-hdinsight-resource-provider-please-ensure-that-outbound-connectivity-is-allowed"></a>"Nepovedlo se navázat odchozí připojení z clusteru pro komunikaci se zprostředkovatelem prostředků HDInsight. Ujistěte se prosím, že je povolené odchozí připojení.

### <a name="issue"></a>Problém

Popis chyby obsahuje "selhalo navázání odchozího připojení z clusteru pro komunikaci se zprostředkovatelem prostředků HDInsight. Ujistěte se prosím, že je povolené odchozí připojení.

### <a name="cause"></a>Příčina

Při použití privátních propojených clusterů HDInsight musí být odchozí přístup z clusteru nakonfigurovaný tak, aby umožňoval připojení k poskytovateli prostředků HDInsight.

### <a name="resolution"></a>Řešení

* Pokud chcete tento problém vyřešit, přečtěte si postup nastavení privátního odkazu HDInsight při [instalaci privátního odkazu](../hdinsight-private-link.md) .
---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Konfigurace virtuální sítě není kompatibilní s požadavkem HDInsight".

### <a name="issue"></a>Problém

Popisy chyb obsahují zprávy podobné následujícímu:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Příčina

Zřejmě došlo k potížím s vlastním nastavením DNS.

### <a name="resolution"></a>Řešení

Ověřte, že je 168.63.129.16 ve vlastním řetězci DNS. Servery DNS v rámci virtuální sítě můžou předávat dotazy DNS do rekurzivních překladačů Azure za účelem překladu názvů hostitelů v této virtuální síti. Další informace najdete v tématu [překlad názvů ve virtuálních sítích](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Přístup k rekurzivním překladačům Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16.

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spusťte následující příkaz:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Měli byste vidět přibližně toto:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Na základě výsledku vyberte jeden z následujících kroků a postupujte takto:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 není v tomto seznamu.

**Možnost 1**  
Přidejte 168.63.129.16 jako první vlastní DNS pro virtuální síť pomocí postupu popsaného v tématu [plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Tyto kroky platí jenom v případě, že vlastní server DNS běží na Linux.

**Možnost 2**  
Nasazení virtuálního počítače serveru DNS pro virtuální síť. Zahrnuje následující kroky:

* Vytvořte virtuální počítač ve virtuální síti, který se nakonfiguruje jako služba DNS pro překládání (může to být Linux nebo Windows VM).
* Nakonfigurujte pravidla předávání DNS na tomto virtuálním počítači (předejte všechny požadavky na překlad názvů IDN do 168.63.129.16 a zbývající na váš server DNS).
* Přidejte IP adresu tohoto virtuálního počítače jako první položku DNS pro Virtual Network konfiguraci DNS.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 je v seznamu

V takovém případě prosím vytvořte případ podpory v HDInsight a my prozkoumáme váš problém. Do případu podpory zahrňte výsledek níže uvedených příkazů. Pomůže nám to prozkoumat a vyřešit problém rychleji.

Z relace SSH na hlavním uzlu upravte a pak spusťte následující:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```
### <a name="cause"></a>Příčina

Další příčinou tohoto `InvalidNetworkConfigurationErrorCode` kódu chyby může být použití zastaralého parametru `EnableVmProtection` v PowerShellu nebo Runbooku Azure.

### <a name="resolution"></a>Řešení

Použijte platné parametry pro `Get-AzVirtualNetwork` , jak je popsáno v [sadě AZ PowerShell SDK](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-5.3.0&viewFallbackFrom=azps-4.2.0)

---

### <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
