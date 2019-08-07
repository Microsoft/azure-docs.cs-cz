---
title: Vytvoření clusteru selhalo s InvalidNetworkConfigurationErrorCode ve službě Azure HDInsight
description: Různé důvody pro neúspěšné vytváření clusterů pomocí InvalidNetworkConfigurationErrorCode ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 9d55041e05101c610a050574f2e940c40dac991a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817144"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Vytvoření clusteru selhalo s InvalidNetworkConfigurationErrorCode ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

Pokud se zobrazí kód `InvalidNetworkConfigurationErrorCode` chyby s popisem "konfigurace Virtual Network není kompatibilní s požadavkem HDInsight", obvykle to znamená problém s [konfigurací virtuální sítě](../hdinsight-plan-virtual-network-deployment.md) pro váš cluster. Na základě zbytku popisu chyby použijte následující části k vyřešení vašeho problému.

## <a name="hostname-resolution-failed"></a>"Překlad názvu hostitele se nezdařil"

### <a name="issue"></a>Problém

Popis chyby obsahuje "překlad názvu hostitele se nezdařil".

### <a name="cause"></a>Příčina

Tato chyba odkazuje na problém s vlastní konfigurací DNS. Servery DNS v rámci virtuální sítě můžou předávat dotazy DNS do rekurzivních překladačů Azure za účelem překladu názvů hostitelů v této virtuální síti (podrobnosti najdete [v tématu překlad IP adres ve virtuálních sítích](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ). Přístup k rekurzivním překladačům Azure se poskytuje prostřednictvím virtuální IP adresy 168.63.129.16. Tato IP adresa je dostupná jenom z virtuálních počítačů Azure. Takže nebude fungovat, pokud používáte server DNS OnPrem nebo server DNS je virtuální počítač Azure, který není součástí virtuální sítě clusteru.

### <a name="resolution"></a>Řešení

1. Připojte se přes SSH k virtuálnímu počítači, který je součástí clusteru, a `hostname -f`spusťte příkaz. Tato akce vrátí plně kvalifikovaný název domény hostitele (dále jen `<host_fqdn>` v následujících pokynech).

1. Pak spusťte příkaz `nslookup <host_fqdn>` ( `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`například). Pokud tento příkaz přeloží název na IP adresu, znamená to, že váš server DNS funguje správně. V takovém případě získáte případ podpory v HDInsight a my vám prozkoumáme váš problém. Ve svém případu podpory zahrňte kroky pro řešení potíží, které jste provedli. Tím se nám pomůžete problém vyřešit rychleji.

1. Pokud výše uvedený příkaz nevrátí IP adresu, spusťte `nslookup <host_fqdn> 168.63.129.16` příkaz ( `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`například). Pokud je tento příkaz schopný přeložit IP adresu, znamená to, že buď server DNS nepředává dotaz do DNS Azure, nebo není virtuálním počítačem, který je součástí stejné virtuální sítě jako cluster.

1. Pokud nemáte virtuální počítač Azure, který může fungovat jako vlastní server DNS ve virtuální síti clusteru, musíte ho nejdřív přidat. Vytvořte virtuální počítač ve virtuální síti, který se nakonfiguruje jako služba DNS pro přeposílání.

1. Jakmile ve své virtuální síti nasadíte virtuální počítač, nakonfigurujte pravidla předávání DNS na tomto virtuálním počítači. Předejte všechny požadavky na překlad názvů IDN do 168.63.129.16 a zbytek na váš server DNS. [Tady](../hdinsight-plan-virtual-network-deployment.md) je příklad tohoto instalačního programu pro vlastní server DNS.

1. Přidejte IP adresu tohoto virtuálního počítače jako první položku DNS pro konfiguraci služby Virtual Network DNS.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>Nepovedlo se připojit k Azure Storagemu účtu.

### <a name="issue"></a>Problém

Popis chyby obsahuje "připojení k účtu Azure Storage se nepovedlo nebo se nepovedlo připojit k Azure SQL".

### <a name="cause"></a>Příčina

Azure Storage a SQL nemají pevné IP adresy, proto musíme Povolit odchozí připojení ke všem IP adresám, aby bylo možné získat přístup k těmto službám. Přesný postup řešení závisí na tom, jestli jste nastavili skupinu zabezpečení sítě (NSG) nebo uživatelsky definovaná pravidla (UDR). Podrobnosti o těchto konfiguracích najdete v části [řízení síťového provozu pomocí služby HDInsight se skupinami zabezpečení sítě a uživatelsky definovanými trasami](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) .

### <a name="resolution"></a>Řešení

* Pokud váš cluster používá [skupinu zabezpečení sítě (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Přejít na Azure Portal a Identifikujte NSG, která je přidružená k podsíti, ve které se cluster nasazuje. V části **odchozí pravidla zabezpečení** povolte odchozí přístup k Internetu bez omezení (Všimněte si, že tady je nižší priorita, což znamená vyšší prioritu). V části **podsítě** také potvrďte, zda je tento NSG použit pro podsíť clusteru.

* Pokud váš cluster používá [trasy definované uživatelem (udr)](../../virtual-network/virtual-networks-udr-overview.md).

    Přejít na Azure Portal a Identifikujte směrovací tabulku, která je přidružená k podsíti, ve které se cluster nasazuje. Po nalezení směrovací tabulky pro podsíť zkontrolujte část **trasy** v ní.

    Pokud jsou definované trasy, ujistěte se, že existují trasy pro IP adresy v oblasti, ve které byl cluster nasazený, a **typem** pro každou trasu je **Internet**. Pro každou požadovanou IP adresu popsanou ve výše uvedeném článku by měla být definována trasa.

---

### <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
