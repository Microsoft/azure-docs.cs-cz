---
title: Nastavení služby Active Directory/zotavení po havárii DNS pomocí Azure Site Recovery
description: Tento článek popisuje, jak implementovat řešení zotavení po havárii pro Active Directory a DNS pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257807"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Nastavení zotavení po havárii pro služby Active Directory a DNS

Podnikové aplikace, jako je SharePoint, Dynamics AX a SAP, závisí na službě Active Directory a na infrastruktuře DNS, aby fungovaly správně. Při nastavování zotavení po havárii pro aplikace je často potřeba obnovit službu Active Directory a DNS předtím, než obnovíte jiné součásti aplikace, abyste zajistili správnou funkčnost aplikace.

Pomocí [Site Recovery](site-recovery-overview.md) můžete vytvořit plán zotavení po havárii pro službu Active Directory. Když dojde k narušení, můžete iniciovat převzetí služeb při selhání. Službu Active Directory můžete mít v průběhu několika minut. Pokud jste nasadili službu Active Directory pro více aplikací v primární lokalitě, například pro SharePoint a SAP, můžete převzít služby při selhání celého webu. Službu Active Directory můžete nejprve převzít pomocí Site Recovery. Pak můžete převzít služby při selhání dalších aplikací pomocí plánů obnovení specifických pro jednotlivé aplikace.

Tento článek vysvětluje, jak vytvořit řešení zotavení po havárii pro službu Active Directory. Zahrnuje požadavky a pokyny pro převzetí služeb při selhání. Než začnete, měli byste být obeznámeni se službou Active Directory a Site Recovery.

## <a name="prerequisites"></a>Požadavky

* Pokud provádíte replikaci do Azure, [připravíte prostředky Azure](tutorial-prepare-azure.md), včetně předplatného, Virtual Network Azure, účtu úložiště a trezoru Recovery Services.
* Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-azure.md) pro všechny komponenty.

## <a name="replicate-the-domain-controller"></a>Replikace řadiče domény

- Musíte nastavit Site Recovery replikaci aspoň na jednom virtuálním počítači, který je hostitelem řadiče domény nebo DNS.
- Pokud máte ve svém prostředí více řadičů domény, musíte také nastavit další řadič domény v cílové lokalitě. Další řadič domény může být v Azure nebo v sekundárním místním datacentru.
- Pokud máte jen několik aplikací a jeden řadič domény, můžete chtít převzít služby při selhání celé lokality dohromady. V takovém případě doporučujeme použít Site Recovery k replikaci řadiče domény do cílové lokality (buď v Azure, nebo v sekundárním místním datacentru). Pro [testovací převzetí služeb při selhání](#test-failover-considerations)můžete použít stejný replikovaný řadič domény nebo virtuální počítač DNS.
- - Pokud máte ve svém prostředí spoustu aplikací a více než jeden řadič domény, nebo pokud plánujete převzít služby při selhání několika aplikacemi najednou Site Recovery, doporučujeme, abyste nastavili Další řadič domény v cílové lokalitě (buď v Azure, nebo v sekundárním místním datacentru). Pro [testovací převzetí služeb při selhání](#test-failover-considerations)můžete použít řadič domény, který je replikovaný pomocí Site Recovery. Pro převzetí služeb při selhání můžete použít další řadič domény v cílové lokalitě.

## <a name="enable-protection-with-site-recovery"></a>Povolení ochrany pomocí Site Recovery

Pomocí Site Recovery můžete chránit virtuální počítač, který je hostitelem řadiče domény nebo DNS.

### <a name="protect-the-vm"></a>Ochrana virtuálního počítače
Řadič domény, který se replikuje pomocí Site Recovery slouží k [testovacímu převzetí služeb při selhání](#test-failover-considerations). Zajistěte, aby splňovaly následující požadavky:

1. Řadič domény je server globálního katalogu.
2. Řadič domény by měl být vlastníkem role FSMO pro role, které jsou potřeba během testovacího převzetí služeb při selhání. V opačném případě bude nutné po převzetí služeb při selhání [převzít](https://aka.ms/ad_seize_fsmo) tyto role.

### <a name="configure-vm-network-settings"></a>Konfigurovat nastavení sítě virtuálních počítačů
U virtuálního počítače, který je hostitelem řadiče domény nebo DNS, v části Site Recovery nakonfigurujte nastavení sítě v nastavení **výpočty a síť** replikovaného virtuálního počítače. Tím se zajistí, že se virtuální počítač připojí ke správné síti po převzetí služeb při selhání.

## <a name="protect-active-directory"></a>Ochrana služby Active Directory

### <a name="site-to-site-protection"></a>Ochrana mezi lokalitami
Vytvořte řadič domény v sekundární lokalitě. Při zvýšení úrovně serveru na roli řadiče domény zadejte název stejné domény, která se používá v primární lokalitě. Modul snap-in **lokality a služby Active Directory** můžete použít ke konfiguraci nastavení na objektu propojení lokalit, ke kterému se lokality přidávají. Konfigurací nastavení na propojení lokalit můžete řídit, kdy probíhá replikace mezi dvěma nebo více lokalitami a jak často k ní dochází. Další informace najdete v tématu [Plánování replikace mezi lokalitami](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Ochrana mezi weby a Azure
Nejdřív vytvořte řadič domény ve službě Azure Virtual Network. Při povýšení serveru na roli řadiče domény zadejte stejný název domény, který se používá v primární lokalitě.

Potom překonfigurujte server DNS pro virtuální síť tak, aby používala server DNS v Azure.

![Síť Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Ochrana z Azure do Azure
Nejdřív vytvořte řadič domény ve službě Azure Virtual Network. Při povýšení serveru na roli řadiče domény zadejte stejný název domény, který se používá v primární lokalitě.

Potom překonfigurujte server DNS pro virtuální síť tak, aby používala server DNS v Azure.

## <a name="test-failover-considerations"></a>Požadavky testovacího převzetí služeb při selhání
Aby se zabránilo dopadu na produkční úlohy, dojde k testovacímu převzetí služeb při selhání v síti, která je izolovaná od produkční sítě.

Většina aplikací vyžaduje přítomnost řadiče domény nebo serveru DNS. Proto je před převzetím služeb při selhání nutné v izolované síti vytvořit řadič domény, který se použije pro testovací převzetí služeb při selhání. Nejjednodušší způsob, jak to provést, je použít Site Recovery k replikaci virtuálního počítače, který je hostitelem řadiče domény nebo DNS. Potom spusťte testovací převzetí služeb při selhání virtuálního počítače řadiče domény před spuštěním testovacího převzetí služeb při selhání v plánu obnovení pro aplikaci. Tady je postup:

1. K [replikaci](vmware-azure-tutorial.md) virtuálního počítače, který je hostitelem řadiče domény nebo DNS, použijte Site Recovery.
2. Vytvořte izolovanou síť. Každá virtuální síť, kterou vytvoříte v Azure, je ve výchozím nastavení izolovaná od ostatních sítí. Doporučujeme použít pro tuto síť stejný rozsah IP adres, který používáte ve své produkční síti. V této síti nepovolujte připojení Site-to-site.
3. Zadejte IP adresu DNS v izolované síti. Použijte IP adresu, kterou očekáváte, že se virtuální počítač DNS dostane. Pokud provádíte replikaci do Azure, zadejte IP adresu pro virtuální počítač, který se používá při převzetí služeb při selhání. Pokud chcete zadat IP adresu, v replikovaném virtuálním počítači v nastavení **výpočty a síť** vyberte nastavení **cílové IP adresy** .

    ![Testovací síť Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery se pokusí vytvořit testovací virtuální počítače v podsíti se stejným názvem a pomocí stejné IP adresy, která je k dispozici v nastavení **výpočty a síť** virtuálního počítače. Pokud ve službě Azure Virtual Network, která je pro testovací převzetí služeb při selhání, není dostupná podsíť se stejným názvem, testovací virtuální počítač se vytvoří v abecedně první podsíti.
    >
    > Pokud je cílová IP adresa součástí vybrané podsítě, Site Recovery se pokusí vytvořit virtuální počítač testovacího převzetí služeb při selhání pomocí cílové IP adresy. Pokud cílová IP adresa není součástí vybrané podsítě, vytvoří se virtuální počítač testovacího převzetí služeb při selhání pomocí další dostupné IP adresy ve vybrané podsíti.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Test převzetí služeb při selhání na sekundární lokalitu

1. Pokud provádíte replikaci do jiné místní lokality a používáte protokol DHCP, [nastavte pro testovací převzetí služeb při selhání službu DNS a DHCP](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Proveďte test převzetí služeb při selhání virtuálního počítače řadiče domény, který běží v izolované síti. K provedení testovacího převzetí služeb při selhání použijte nejnovější bod obnovení *konzistentní vzhledem k aplikacím* virtuálního počítače řadiče domény.
3. Spusťte testovací převzetí služeb při selhání pro plán obnovení, který obsahuje virtuální počítače, na kterých je aplikace spuštěná.
4. Po dokončení testování *vyčistěte testovací převzetí služeb při selhání* na virtuálním počítači řadiče domény. Tento krok odstraní řadič domény, který byl vytvořen pro testovací převzetí služeb při selhání.


### <a name="remove-references-to-other-domain-controllers"></a>Odebrat odkazy na jiné řadiče domény
Když zahájíte testovací převzetí služeb při selhání, nezahrnujte všechny řadiče domény do testovací sítě. Pokud chcete odebrat odkazy na jiné řadiče domény, které existují ve vašem produkčním prostředí, možná budete muset [převzít role FSMO Active Directory](https://aka.ms/ad_seize_fsmo) a [vyčistit metadata](https://technet.microsoft.com/library/cc816907.aspx) pro chybějící řadiče domény.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problémy způsobené ochranou virtualizace

> [!IMPORTANT]
> Některé z konfigurací popsaných v této části nejsou standardní nebo výchozí konfigurace řadiče domény. Pokud nechcete provádět tyto změny v produkčním řadiči domény, můžete vytvořit řadič domény vyhrazený pro Site Recovery, který se má použít pro testovací převzetí služeb při selhání. Tyto změny proveďte pouze pro tento řadič domény.  
>
>

Počínaje systémem Windows Server 2012 [jsou do Active Directory Domain Services (služba AD DS) integrována další bezpečnostní opatření](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Tato ochrana chrání virtualizované řadiče domény proti vrácení hodnot USN, pokud základní platforma hypervisoru podporuje **VM-GenerationID**. Azure podporuje **VM-GenerationID**. Z tohoto důvodu mají řadiče domény, na kterých běží Windows Server 2012 nebo novější, na virtuálních počítačích Azure tyto další bezpečnostní opatření.


Když se resetuje **VM-GenerationID** , obnoví se taky hodnota **InvocationID** databáze služba AD DS. Kromě toho se zahodí fond identifikátorů RID a složka SYSVOL je označena jako neautoritativní. Další informace najdete v tématu [Úvod do Active Directory Domain Services virtualizace](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) a [bezpečné virtualizace DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Převzetí služeb při selhání do Azure může způsobit resetování **virtuálního počítače – GenerationID** . Resetování **virtuálního počítače – GenerationID** aktivuje dodatečnou ochranu, když se virtuální počítač řadiče domény spustí v Azure. To může mít za následek *výrazné zpoždění* při přihlášení k virtuálnímu počítači řadiče domény.

Vzhledem k tomu, že tento řadič domény se používá jenom v testovacím převzetí služeb při selhání, není ochrana virtualizace nutná. Pokud se chcete ujistit, že se hodnota **GenerationID** virtuálního počítače pro virtuální počítač řadiče domény nemění, můžete v místním řadiči domény změnit hodnotu následujícího typu DWORD na **4** :


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Příznaky ochrany virtualizace

Pokud se po testovacím převzetí služeb při selhání aktivují bezpečnostní opatření virtualizace, může se zobrazit jeden nebo několik následujících příznaků:  

* Změní se hodnota **GenerationID** .

    ![Změna ID generování](./media/site-recovery-active-directory/Event2170.png)

* Změní se hodnota **InvocationID** .

    ![Změna ID vyvolání](./media/site-recovery-active-directory/Event1109.png)

* Složka SYSVOL a sdílené složky NETLOGON nejsou k dispozici.

    ![Sdílená složka SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![Složka NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* Databáze DFSR se odstraní.

    ![Databáze DFSR se odstraní.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Řešení potíží s řadičem domény během testovacího převzetí služeb

> [!IMPORTANT]
> Některé z konfigurací popsaných v této části nejsou standardní nebo výchozí konfigurace řadiče domény. Pokud nechcete provádět tyto změny v produkčním řadiči domény, můžete vytvořit řadič domény, který je vyhrazený pro Site Recovery testovací převzetí služeb při selhání. Změny proveďte pouze pro vyhrazený řadič domény.  
>
>

1. Na příkazovém řádku spusťte následující příkaz, který zkontroluje, jestli je sdílená složka SYSVOL a složka NETLOGON:

    `NET SHARE`

2. Na příkazovém řádku spusťte následující příkaz, abyste zajistili, že řadič domény správně funguje:

    `dcdiag /v > dcdiag.txt`

3. Ve výstupním protokolu vyhledejte následující text. Text potvrdí, že řadič domény funguje správně.

    * "úspěšné připojení testu"
    * "úspěšná zkušební inzerce"
    * "Pass test MachineAccount"

Pokud jsou předchozí podmínky splněné, je pravděpodobnější, že řadič domény funguje správně. Pokud není, proveďte následující kroky:

1. Proveďte autoritativní obnovení řadiče domény. Mějte na paměti následující informace:
    * I [když replikace FRS nedoporučujeme](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), pokud používáte replikaci FRS, postupujte podle kroků pro autoritativní obnovení. Tento proces je popsán v tématu [použití klíče registru Burflags k opětovné inicializaci služby replikace souborů](https://support.microsoft.com/kb/290762).

        Další informace o BurFlags najdete v příspěvku na blogu [D2 a D4: co je pro?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Pokud používáte replikaci DFSR, proveďte kroky pro autoritativní obnovení. Tento postup je popsaný v tématu [Vynutit autoritativní a neautoritativní synchronizaci složky SYSVOL replikované službou DFSR (jako D4/D2 pro službu FRS)](https://support.microsoft.com/kb/2218556).

        Můžete také použít funkce PowerShellu. Další informace najdete v tématu [funkce prostředí PowerShell pro autoritativní nebo neautoritativní obnovení systému DFSR-SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Vynechejte požadavek na počáteční synchronizaci nastavením následujícího klíče registru na **hodnotu 0** v místním řadiči domény. Pokud hodnota DWORD neexistuje, můžete ji vytvořit pod uzlem **parametry** .

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Další informace najdete v tématu [řešení potíží s ID události dns 4013: Server DNS nemohl načíst zóny DNS integrované se službou Active Directory](https://support.microsoft.com/kb/2001093).

3. Zakažte požadavek, aby byl server globálního katalogu dostupný pro ověření přihlášení uživatele. Uděláte to tak, že v místním řadiči domény nastavíte následující klíč registru na hodnotu **1**. Pokud hodnota DWORD neexistuje, můžete ji vytvořit pod uzlem **LSA** .

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Další informace najdete v tématu [zakázání požadavku na zpřístupnění serveru globálního katalogu pro ověřování přihlášení uživatelů](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS a řadič domény v různých počítačích

Pokud používáte řadič domény a DNs na stejném virtuálním počítači, můžete tento postup přeskočit.


Pokud DNS není na stejném virtuálním počítači jako řadič domény, musíte pro testovací převzetí služeb při selhání vytvořit virtuální počítač DNS. Můžete použít nový server DNS a vytvořit všechny požadované zóny. Pokud je například doména služby Active Directory contoso.com, můžete vytvořit zónu DNS s názvem contoso.com. Položky, které odpovídají službě Active Directory, je třeba aktualizovat v DNS následujícím způsobem:

1. Zajistěte, aby byla tato nastavení zavedena před tím, než se spustí kterýkoli jiný virtuální počítač v plánu obnovení:
   * Zóna musí být pojmenována za kořenovým názvem doménové struktury.
   * Zóna musí být zálohovaná souborem.
   * V zóně musí být povolená zabezpečená a nezabezpečená aktualizace.
   * Překladač virtuálního počítače, který je hostitelem řadiče domény, by měl odkazovat na IP adresu virtuálního počítače DNS.

2. Na virtuálním počítači, který je hostitelem řadiče domény, spusťte následující příkaz:

    `nltest /dsregdns`

3. Spuštěním následujících příkazů přidejte zónu na server DNS, povolte nezabezpečené aktualizace a přidejte položku pro zónu do DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [ochraně podnikových úloh pomocí Azure Site Recovery](site-recovery-workload.md).
