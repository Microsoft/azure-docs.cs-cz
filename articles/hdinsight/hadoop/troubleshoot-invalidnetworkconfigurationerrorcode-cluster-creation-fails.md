---
title: Chyba InvalidNetworkConfigurationErrorCode – Azure HDInsight
description: Různé důvody pro neúspěšné vytváření clusteru s InvalidNetworkConfigurationErrorCode v Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720380"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Vytvoření clusteru se nezdaří s InvalidNetworkConfigurationErrorCode v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

Pokud se zobrazí `InvalidNetworkConfigurationErrorCode` kód chyby s popisem "Konfigurace virtuální sítě není kompatibilní s požadavkem HDInsight", obvykle označuje problém s [konfigurací virtuální sítě](../hdinsight-plan-virtual-network-deployment.md) pro váš cluster. Na základě zbývající části popisu chyby, postupujte podle následujících částí k vyřešení problému.

## <a name="hostname-resolution-failed"></a>"Překlad názvu hostitele se nezdařil"

### <a name="issue"></a>Problém

Popis chyby obsahuje "Překlad názvu hosta se nezdařil".

### <a name="cause"></a>Příčina

Tato chyba ukazuje na problém s vlastní konfigurací DNS. Servery DNS v rámci virtuální sítě mohou předávat dotazy DNS rekurzivním překládáním Azure, aby vyřešily názvy hostitelů v rámci této virtuální sítě (podrobnosti najdete v tématu [Překlad názvů ve virtuálních sítích).](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) Přístup k rekurzivním překladačům Azure je k dispozici prostřednictvím virtuální IP 168.63.129.16. Tato IP adresa je přístupná jenom z virtuálních počítačů Azure. Takže to nebude fungovat, pokud používáte server ONPrem DNS nebo váš DNS server je virtuální počítač Azure, který není součástí virtuální sítě clusteru.

### <a name="resolution"></a>Řešení

1. Ssh do virtuálního virtuálního ms, který `hostname -f`je součástí clusteru a spusťte příkaz . Tím se vrátí plně kvalifikovaný název domény hostitele `<host_fqdn>` (označovaný jako v následujících pokynech).

1. Potom spusťte `nslookup <host_fqdn>` příkaz `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`(například). Pokud tento příkaz překládá název na adresu IP, znamená to, že server DNS pracuje správně. V takovém případě vyvoláte případ podpory s HDInsight a my váš problém prošetříme. V případě podpory zahrňte kroky řešení potíží, které jste provedli. To nám pomůže problém vyřešit rychleji.

1. Pokud výše uvedený příkaz nevrátí IP adresu, `nslookup <host_fqdn> 168.63.129.16` spusťte `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`(například). Pokud tento příkaz je schopen vyřešit IP, znamená to, že buď váš DNS server není předávání dotazu dns Azure, nebo to není virtuální počítač, který je součástí stejné virtuální sítě jako clusteru.

1. Pokud nemáte virtuální počítač Azure, který může fungovat jako vlastní SERVER DNS ve virtuální síti clusteru, musíte nejprve přidat tento. Vytvořte virtuální počítač ve virtuální síti, který bude nakonfigurovaný jako předávání DNS.

1. Jakmile máte virtuální počítač nasazený ve vaší virtuální síti, nakonfigurujte pravidla předávání DNS na tomto virtuálním počítači. Přepošlete všechny požadavky na překlad názvů iDNS na 168.63.129.16 a zbytek na váš dns server. [Zde](../hdinsight-plan-virtual-network-deployment.md) je příklad tohoto nastavení pro vlastní server DNS.

1. Přidejte IP adresu tohoto virtuálního počítače jako první položku DNS pro konfiguraci DNS virtuální sítě.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Nepodařilo se připojit k účtu úložiště Azure"

### <a name="issue"></a>Problém

Popis chyby obsahuje "Nepodařilo se připojit k účtu úložiště Azure" nebo "Nepodařilo se připojit k Azure SQL".

### <a name="cause"></a>Příčina

Azure Storage a SQL nemají pevné IP adresy, takže musíme povolit odchozí připojení ke všem IP adresám, abychom umožnili přístup k těmto službám. Přesné kroky řešení závisí na tom, zda jste nastavili skupinu zabezpečení sítě (NSG) nebo uživatelem definovaná pravidla (UDR). Podrobnosti o těchto konfiguracích naleznete v části [o řízení síťového provozu pomocí služby HDInsight pomocí skupin zabezpečení sítě a uživatelem definovaných tras.](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)

### <a name="resolution"></a>Řešení

* Pokud váš cluster používá [skupinu zabezpečení sítě (NSG).](../../virtual-network/virtual-network-vnet-plan-design-arm.md)

    Přejděte na portál Azure a identifikujte skupinu zabezpečení sítě, která je přidružená k podsíti, kde se cluster nasazuje. V části **Odchozí pravidla zabezpečení** povolte odchozí přístup k Internetu bez omezení (všimněte si, že menší číslo **priority** zde znamená vyšší prioritu). Také v části **podsítě** zkontrolujte, zda je tento soubor nsg použit v podsíti clusteru.

* Pokud váš cluster používá [uživatelem definované trasy (UDR).](../../virtual-network/virtual-networks-udr-overview.md)

    Přejděte na portál Azure a identifikujte směrovací tabulku, která je přidružená k podsíti, kde se cluster nasazuje. Jakmile najdete tabulku tras pro podsíť, zkontrolujte v ní oddíl **trasy.**

    Pokud jsou definovány trasy, ujistěte se, že existují trasy pro IP adresy pro oblast, kde byl cluster nasazen, a **NextHopType** pro každou trasu je **Internet**. Pro každou požadovanou adresu IP by měla být definována trasa, která by byla popsána ve výše uvedeném článku.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"Konfigurace virtuální sítě není kompatibilní s požadavkem HDInsight"

### <a name="issue"></a>Problém

Popisy chyb obsahují zprávy podobné takto:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Příčina

Pravděpodobně problém s vlastním nastavením DNS.

### <a name="resolution"></a>Řešení

Ověřte, zda je 168.63.129.16 ve vlastním řetězci DNS. Servery DNS v rámci virtuální sítě mohou předávat dotazy DNS rekurzivním překladačům Azure, aby vyřešily názvy hostitelů v rámci této virtuální sítě. Další informace naleznete [v tématu Překlad názvů ve virtuálních sítích](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Přístup k rekurzivním překladačům Azure je k dispozici prostřednictvím virtuální IP 168.63.129.16.

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spusťte následující příkaz:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Mělo by se vám zobrazit přibližně toto:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Na základě výsledku - vyberte jeden z následujících kroků postupujte takto:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 není v tomto seznamu

**Možnost 1**  
Přidejte 168.63.129.16 jako první vlastní DNS pro virtuální síť pomocí kroků popsaných v [části Naplánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Tyto kroky platí pouze v případě, že váš vlastní DNS server běží na Linuxu.

**Možnost č. 2**  
Nasazení virtuálního počítače dns serveru pro virtuální síť To zahrnuje následující kroky:

* Vytvořte virtuální počítač ve virtuální síti, který bude nakonfigurovaný jako předávání DNS (může to být Virtuální počítač s Linuxem nebo Windows).
* Konfigurace pravidel předávání DNS na tomto virtuálním počítači (přepošlete všechny požadavky na překlad názvů iDNS na 168.63.129.16 a zbytek na váš DNS server).
* Přidejte IP adresu tohoto virtuálního počítače jako první položku DNS pro konfiguraci DNS virtuální sítě.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 je v seznamu

V takovém případě vytvořte případ podpory s HDInsight a my váš problém prošetříme. V případě podpory zahrňte výsledek níže uvedených příkazů. To nám pomůže rychleji prošetřit a vyřešit problém.

Z relace ssh na hlavním uzlu upravte a spusťte následující:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
