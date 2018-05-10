---
title: Ochrana služby Active Directory a DNS s Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak implementovat řešení zotavení po havárii pro službu Active Directory pomocí Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: manayar
ms.openlocfilehash: 73562fee58b38999fd3bb281b5be32917f6a3394
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>Pomocí Azure Site Recovery k ochraně služby Active Directory a DNS

Podnikové aplikace, například SharePoint, Dynamics AX a SAP závisí na služby Active Directory a infrastruktury služby DNS, aby správně fungoval. Při nastavování zotavení po havárii pro aplikace, můžete často potřebují k obnovení služby Active Directory a DNS před obnovením další součásti aplikace, abyste zajistili funkčnost správnou aplikaci.

Můžete použít [Site Recovery](site-recovery-overview.md) k vytvoření plánu zotavení po havárii pro službu Active Directory. Když dojde k narušení služeb, můžete spustit převzetí služeb při selhání. Můžete mít služby Active Directory nahoru a spuštění za pár minut. Pokud jste nasadili služby Active Directory pro více aplikací ve vaší primární lokalitě, například pro Sharepointu a SAP, můžete chtít převzít dokončení lokality. Nejprve můžete převzít služby Active Directory pomocí u URL webového serveru pro obnovení. Potom převzetí služeb při selhání jiné aplikace, pomocí plánů obnovení specifické pro aplikaci.

Tento článek vysvětluje, jak vytvořit řešení zotavení po havárii pro službu Active Directory. Obsahuje požadavky a pokyny převzetí služeb při selhání. Byste měli být obeznámeni s Active Directory a Site Recovery, než začnete.

## <a name="prerequisites"></a>Požadavky

* Pokud replikujete do Azure, [Příprava prostředků Azure](tutorial-prepare-azure.md), včetně předplatného, virtuální síť Azure, účet úložiště a trezoru služeb zotavení.
* Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-azure.md) pro všechny komponenty.

## <a name="replicate-the-domain-controller"></a>Replikace řadiče domény

Je nutné nastavit [Site Recovery replikaci](#enable-protection-using-site-recovery), na alespoň jeden virtuální počítač, který je hostitelem řadiče domény nebo DNS. Pokud máte [několika řadičů domény](#environment-with-multiple-domain-controllers) ve vašem prostředí, je třeba také nastavit [další řadič domény](#protect-active-directory-with-active-directory-replication) v cílové lokalitě. Další řadiče domény může být v Azure, nebo do sekundárního místního datacentra.

### <a name="single-domain-controller"></a>Řadič domény jedním
Pokud máte pouze několik aplikací a jeden řadič domény, můžete chtít převzetí služeb při selhání celá lokalita společně. V takovém případě vám doporučujeme používat Site Recovery k replikaci řadič domény do cílové lokality (buď v Azure nebo do sekundárního místního datacentra). Můžete použít stejné řadič domény replikovaný nebo DNS virtuálního počítače pro [testovací převzetí služeb při selhání](#test-failover-considerations).

### <a name="multiple-domain-controllers"></a>Víc řadičů domény
Pokud máte mnoho aplikací a více než jeden řadič domény ve vašem prostředí, nebo pokud máte v úmyslu převzetí služeb při selhání několik aplikací najednou, kromě replikace virtuálního počítače řadiče domény pomocí Site Recovery, doporučujeme, abyste nastavili [další řadič domény](#protect-active-directory-with-active-directory-replication) v cílové lokalitě (buď v Azure nebo do sekundárního místního datacentra). Pro [testovací převzetí služeb při selhání](#test-failover-considerations), můžete použít řadič domény, který se replikují pomocí Site Recovery. Pro převzetí služeb při selhání můžete další řadiče domény v cílové lokalitě.

## <a name="enable-protection-with-site-recovery"></a>Povolení ochrany službou Site Recovery

Site Recovery můžete použít k ochraně virtuálního počítače, který je hostitelem řadiče domény nebo DNS.

### <a name="protect-the-vm"></a>Ochrana virtuálních počítačů
Řadič domény, který se replikují pomocí Site Recovery se používá pro [testovací převzetí služeb při selhání](#test-failover-considerations). Ujistěte se, zda splňuje následující požadavky:

1. Je řadič domény serverem globálního katalogu.
2. Řadič domény musí být vlastníkem této role FSMO pro role, které jsou potřeba při testovací převzetí služeb. Jinak by tyto role bude muset být [převzaty](http://aka.ms/ad_seize_fsmo) po převzetí služeb při selhání.

### <a name="configure-vm-network-settings"></a>Konfigurace nastavení sítě virtuálních počítačů
Pro virtuální počítač, který je hostitelem řadiče domény nebo DNS v Site Recovery, konfigurace nastavení sítě v části **výpočty a síť** nastavení replikované virtuální počítače. Tím se zajistí, že virtuální počítač je připojen ke správné síti po převzetí služeb při selhání.

## <a name="protect-active-directory"></a>Ochrana služby Active Directory

### <a name="site-to-site-protection"></a>Site-to-site ochrany
Vytvoření řadiče domény v sekundární lokalitě. Při povýšení serveru role řadiče domény, zadejte název stejné domény, který se používá v primární lokalitě. Můžete použít **Active Directory Sites and Services** modul snap-in ke konfiguraci nastavení v objektu propojení lokalit, do které jsou přidány webů. Konfigurací nastavení na propojení lokalit, můžete určit, kdy dojde k replikaci mezi dvěma lokalitami a jak často dochází. Další informace najdete v tématu [plánování replikace mezi lokalitami](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Ochrana serveru Azure
Nejprve vytvořte řadič domény v virtuální sítě Azure. Při povýšení serveru role řadiče domény, zadejte se stejným názvem domény, který se používá v primární lokalitě.

Překonfigurujte server DNS pro virtuální síť použít DNS server v Azure.

![Síť Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Ochrana Azure do Azure
Nejprve vytvořte řadič domény v virtuální sítě Azure. Při povýšení serveru role řadiče domény, zadejte se stejným názvem domény, který se používá v primární lokalitě.

Překonfigurujte server DNS pro virtuální síť použít DNS server v Azure.

## <a name="test-failover-considerations"></a>Aspekty testovací převzetí služeb při selhání
Abyste se vyhnuli vlivu na úlohy v produkčním prostředí, dojde k převzetí služeb při selhání v síti, která bude izolovaná od produkční sítě.

Většina aplikací vyžaduje přítomnost řadiči domény nebo serveru DNS. Proto před převezme aplikace, musíte vytvořit řadič domény v izolované sítě, který se má použít pro testovací převzetí služeb při selhání. Nejjednodušší způsob je pomocí Site Recovery můžete replikovat virtuální počítač, který je hostitelem řadiče domény nebo DNS. Potom spusťte testovací převzetí služeb virtuálního počítače pro řadič domény před spuštěním testovací převzetí služeb při selhání plánu obnovení pro aplikaci. Zde je, jak můžete udělat:

1. Pomocí Site Recovery můžete [replikovat](vmware-azure-tutorial.md) virtuální počítač, který je hostitelem řadiče domény nebo DNS.
2. Vytvořte izolovanou síť. Všechny virtuální sítě, který vytvoříte v Azure je izolovaná od jiné sítě ve výchozím nastavení. Doporučujeme vám, že používáte stejného rozsahu IP adres pro tuto síť, které budete používat v produkční sítě. Nepovolíte připojení site-to-site v této síti.
3. Zadejte adresu IP serveru DNS v izolované sítě. Použijte IP adresu, která očekáváte, že virtuální počítač DNS získat. Pokud replikujete do Azure, zadejte IP adresu pro virtuální počítač, který se používá na převzetí služeb při selhání. Zadat IP adresu, v replikované virtuální počítač, v **výpočty a síť** nastavení, vyberte **cílová IP adresa** nastavení.

    ![Azure testovací síti](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery se pokusí vytvořit testovací virtuální počítače v podsíti se stejným názvem a pomocí stejné IP adresy, která je součástí **výpočty a síť** nastavení virtuálního počítače. Pokud se stejným názvem podsíť není k dispozici v Azure virtuální síť, která je k dispozici pro testovací převzetí služeb při selhání, vytvoří se v abecedním pořadí první podsíť testovacího virtuálního počítače.
    >
    > Pokud cílová IP adresa je součástí vybrané podsíti, Site Recovery se pokusí vytvořit testovacího převzetí služeb při selhání virtuálního počítače pomocí cílová IP adresa. Pokud cílová IP adresa není součástí vybrané podsíti, testovacího převzetí služeb při selhání virtuálního počítače se vytvoří pomocí další dostupnou IP adresu do vybrané podsítě.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testovací převzetí služeb při selhání do sekundární lokality

1. Pokud replikujete do jiné lokality v místě a použití DHCP [nastavení DNS a DHCP pro testovací převzetí služeb při selhání](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Proveďte testovací převzetí služeb virtuálního počítače řadiče domény, který běží v izolované sítě. Použijte nejnovější dostupné *aplikace konzistentní* bod obnovení virtuálního počítače pro řadič domény provedete testovací převzetí služeb.
3. Spusťte testovací převzetí služeb při selhání pro plán obnovení, který obsahuje virtuální počítače, které je aplikace spuštěná na.
4. Po dokončení testování *vyčistit testovací převzetí služeb* na virtuálním počítači řadiče domény. Tento krok odstraní řadič domény, který byl vytvořen pro testovací převzetí služeb při selhání.


### <a name="remove-references-to-other-domain-controllers"></a>Odebrat odkazy na ostatní řadiče domény
Když iniciujete testovací převzetí služeb, neobsahují všechny řadiče domény v testovací síti. Odebrat odkazy na ostatní řadiče domény, které existují v provozním prostředí, budete pravděpodobně potřebovat k [převzetí rolí FSMO služby Active Directory](http://aka.ms/ad_seize_fsmo) a [čištění metadat](https://technet.microsoft.com/library/cc816907.aspx) chybějící řadiče domény .


### <a name="issues-caused-by-virtualization-safeguards"></a>Problémů způsobených ochranu virtualizace

> [!IMPORTANT]
> Některé konfigurace popsané v této části nejsou standard nebo výchozí konfigurace řadiče domény. Pokud nechcete, aby k provedení těchto změn na řadič domény výroby, můžete vytvořit řadič domény, který je vyhrazen pro Site Recovery pro testovací převzetí služeb při selhání. Tyto změny proveďte pouze na tento řadič domény.  
>
>

Od verze Windows Server 2012, [další bezpečnostní opatření jsou součástí služby Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Tato bezpečnostní opatření pomoc při ochraně virtualizovaných řadičů domény na vrácení hodnoty USN zpět, pokud podporuje základní platformu hypervisoru **VM-GenerationID**. Azure podporuje **VM-GenerationID**. Z toho důvodu řadiče domény se systémem Windows Server 2012 nebo novější na Azure virtuální počítače mají tyto další bezpečnostní opatření.


Když **VM-GenerationID** se resetuje **InvocationID** hodnotu databázi služby AD DS je také obnovit. Kromě toho se zahodí fond RID a adresáři SYSVOL je označena jako neautoritativní. Další informace najdete v tématu [Úvod do virtualizace služby Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) a [bezpečně virtualizace DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Může dojít k převzetí služeb při selhání do Azure **VM-GenerationID** resetovat. Resetování **VM-GenerationID** další bezpečnostní opatření se aktivuje při spuštění virtuálního počítače pro řadič domény v Azure. Může to způsobit *významné zpoždění* v se moct přihlásit k virtuálnímu počítači řadiče domény.

Protože tento řadič domény se používá jenom v testovací převzetí služeb, nejsou nezbytná bezpečnostní opatření virtualizace. Zajistit, aby **VM-GenerationID** nezmění hodnotu pro virtuální počítač řadiče domény, můžete změnit hodnotu DWORD následující k **4** v místní řadič domény:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Příznaky ochrana virtualizace

Pokud bezpečnostní opatření virtualizace se spustí po testovací převzetí služeb, může se zobrazit jedna nebo více z následujících příznaků:  

* **GenerationID** hodnotu změny.

    ![Změna ID generování](./media/site-recovery-active-directory/Event2170.png)

* **InvocationID** hodnotu změny.

    ![Změna ID vyvolání](./media/site-recovery-active-directory/Event1109.png)

* Sdílené složky SYSVOL a NETLOGON nejsou k dispozici.

    ![Sdílená složka SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* Budou odstraněny databáze DFSR.

    ![Databáze DFSR jsou odstraněny.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Řešení problémů řadič domény během testovacího převzetí služeb při selhání

> [!IMPORTANT]
> Některé konfigurace popsané v této části nejsou standard nebo výchozí konfigurace řadiče domény. Pokud nechcete, aby k provedení těchto změn na řadič domény výroby, můžete vytvořit řadič domény, který je vyhrazen pro Site Recovery testovací převzetí služeb při selhání. Proveďte požadované změny jenom k tomuto řadiči domény vyhrazené.  
>
>

1. Na příkazovém řádku spusťte následující příkaz a zkontrolujte, zda jsou sdílené složky SYSVOL a NETLOGON:

    `NET SHARE`

2. Na příkazovém řádku spusťte následující příkaz a ověřte, že řadič domény správně funguje:

    `dcdiag /v > dcdiag.txt`

3. V protokolu výstupu vyhledejte následující text. Text potvrdí, že je řadič domény správně funguje.

    * "předaný test připojení"
    * "předaný testovací inzerování"
    * "předaný test MachineAccount"

Jestliže výše uvedených podmínek jsou splněny, je pravděpodobné, že řadič domény správně funguje. Pokud není, proveďte následující kroky:

1. Proveďte autoritativního obnovení řadiče domény. Mějte následující informace:
    * I když není doporučeno [FRS replikace](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), pokud používáte FRS replikace, postupujte podle kroků pro autoritativního obnovení. Proces je popsán v [pomocí klíče registru BurFlags k inicializaci služby replikace souborů](https://support.microsoft.com/kb/290762).

        Další informace o BurFlags, naleznete v příspěvku blogu [D2 a D4: co je to pro?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Pokud používáte DFSR replikace, proveďte kroky pro autoritativního obnovení. Proces je popsán v [vynutili autoritativní a neautoritativní synchronizaci adresáře SYSVOL replikovaného DFSR (jako "D4/D2 u služby FRS)](https://support.microsoft.com/kb/2218556).

        Můžete také použít funkce prostředí PowerShell. Další informace najdete v tématu [funkce prostředí PowerShell autoritativní/neautoritativní obnovení DFSR SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Obejít požadavek na počáteční synchronizaci nastavením následujícího klíče registru na **0** v místní řadič domény. Pokud DWORD neexistuje, můžete ho vytvořit **parametry** uzlu.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Další informace najdete v tématu [řešení potíží s DNS událost ID 4013: DNS server se nepodařilo načíst AD integrované zóny DNS](https://support.microsoft.com/kb/2001093).

3. Zakažte požadavek na ověření přihlášení uživatele možné server globálního katalogu. Chcete-li to provést v řadiči domény v místě, nastavte následující klíč registru na **1**. Pokud DWORD neexistuje, můžete ho vytvořit **Lsa** uzlu.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Další informace najdete v tématu [zakažte požadavek na ověření přihlášení uživatelů možné server globálního katalogu](http://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS a řadič domény na různé počítače
Pokud DNS není ve stejné virtuální počítač jako řadič domény, musíte vytvořit virtuální počítač DNS pro testovací převzetí služeb. Pokud DNS a řadiči domény nejsou ve stejném virtuálním počítači, můžete tuto část přeskočit.

Můžete použít nový server DNS a vytvořit všechny požadované zóny. Například pokud vaše doména služby Active Directory je contoso.com, můžete vytvořit zónu DNS s názvem contoso.com. Položky, které odpovídají do služby Active Directory musí aktualizovat ve službě DNS, následujícím způsobem:

1. Ujistěte se, že tato nastavení jsou na místě před zahájením žádným jiným virtuálním počítačem v plánu obnovení:
   * Zóna musí mít název po název kořenové doménové struktury.
   * Zóna musí být záložních souborů.
   * Zóna musí být povolen pro zabezpečené a nezabezpečené aktualizace.
   * Překladač virtuálního počítače, který je hostitelem řadiče domény by měla odkazovat na adresu IP virtuálního počítače DNS.

2. Na virtuálním počítači, který je hostitelem řadiče domény, spusťte následující příkaz:

    `nltest /dsregdns`

3. Spusťte následující příkazy a přidání zóny na serveru DNS, Povolit nezabezpečené aktualizace a přidejte záznam pro zónu DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Další postup
Další informace o [chrání podnikové úlohy s Azure Site Recovery](site-recovery-workload.md).
