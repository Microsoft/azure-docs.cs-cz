---
title: Nastavení služby Active Directory/zotavení po havárii DNS pomocí Azure Site Recovery
description: Tento článek popisuje, jak implementovat řešení zotavení po havárii pro Active Directory a DNS pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132325"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Nastavení zotavení po havárii pro služby Active Directory a DNS

Podnikové aplikace, jako je SharePoint, Dynamics AX a SAP, závisí na službě Active Directory a na infrastruktuře DNS, aby fungovaly správně. Při nastavování zotavení po havárii pro aplikace je často potřeba obnovit službu Active Directory a DNS (Domain Name System), než obnovíte jiné součásti aplikace, abyste zajistili správnou funkčnost aplikace.

Pomocí [Site Recovery](site-recovery-overview.md) můžete vytvořit plán zotavení po havárii pro službu Active Directory. Když dojde k narušení, můžete iniciovat převzetí služeb při selhání. Služba Active Directory může být zprovozněna během několika minut. Pokud jste nasadili službu Active Directory pro více aplikací v primární lokalitě, například pro SharePoint a SAP, můžete převzít služby při selhání celého webu. Službu Active Directory můžete nejprve převzít pomocí Site Recovery. Pak můžete převzít služby při selhání dalších aplikací pomocí plánů obnovení specifických pro jednotlivé aplikace.

Tento článek vysvětluje, jak vytvořit řešení zotavení po havárii pro službu Active Directory. Zahrnuje požadavky a pokyny pro převzetí služeb při selhání. Než začnete, měli byste být obeznámeni se službou Active Directory a Site Recovery.

## <a name="prerequisites"></a>Předpoklady

- Pokud provádíte replikaci do Azure, [připravíte prostředky Azure](tutorial-prepare-azure.md), včetně předplatného, Virtual Network Azure, účtu úložiště a trezoru Recovery Services.
- Zkontrolujte [požadavky na podporu](./vmware-physical-azure-support-matrix.md) pro všechny komponenty.

## <a name="replicate-the-domain-controller"></a>Replikace řadiče domény

- Musíte nastavit Site Recovery replikaci aspoň na jednom virtuálním počítači, který je hostitelem řadiče domény nebo DNS.
- Pokud máte ve svém prostředí více řadičů domény, musíte také nastavit další řadič domény v cílové lokalitě. Další řadič domény může být v Azure nebo v sekundárním místním datacentru.
- Pokud máte jen několik aplikací a jeden řadič domény, můžete chtít převzít služby při selhání celé lokality dohromady. V takovém případě doporučujeme použít Site Recovery k replikaci řadiče domény do cílové lokality, a to buď v Azure, nebo v sekundárním místním datacentru. Pro [testovací převzetí služeb při selhání](#test-failover-considerations)můžete použít stejný replikovaný řadič domény nebo virtuální počítač DNS.
- Pokud máte ve svém prostředí spoustu aplikací a více než jeden řadič domény, nebo pokud plánujete převzít služby při selhání několika aplikacemi najednou Site Recovery, doporučujeme, abyste v cílové lokalitě nastavili další řadič domény (v Azure nebo v sekundárním místním datacentru). Pro [testovací převzetí služeb při selhání](#test-failover-considerations)můžete použít řadič domény, který je replikován nástrojem Site Recovery. Pro převzetí služeb při selhání můžete použít další řadič domény v cílové lokalitě.

## <a name="enable-protection-with-site-recovery"></a>Povolení ochrany pomocí Site Recovery

Pomocí Site Recovery můžete chránit virtuální počítač, který je hostitelem řadiče domény nebo DNS.

### <a name="protect-the-vm"></a>Ochrana virtuálního počítače

Řadič domény, který se replikuje pomocí Site Recovery slouží k [testovacímu převzetí služeb při selhání](#test-failover-considerations). Zajistěte, aby splňovaly následující požadavky:

1. Řadič domény je server globálního katalogu.
1. Řadič domény by měl být vlastníkem role FSMO (Flexible Single Master Operations) pro role, které jsou potřeba během testovacího převzetí služeb při selhání. V opačném případě bude nutné po převzetí služeb při selhání [převzít](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) tyto role.

### <a name="configure-vm-network-settings"></a>Konfigurovat nastavení sítě virtuálních počítačů

U virtuálního počítače, který je hostitelem řadiče domény nebo DNS, v části Site Recovery nakonfigurujte nastavení sítě v nastavení **výpočty a síť** replikovaného virtuálního počítače. Tím se zajistí, že se virtuální počítač připojí ke správné síti po převzetí služeb při selhání.

## <a name="protect-active-directory"></a>Ochrana služby Active Directory

### <a name="site-to-site-protection"></a>Ochrana mezi lokalitami

Vytvořte řadič domény v sekundární lokalitě. Při povýšení serveru na roli řadiče domény zadejte název stejné domény, která se používá v primární lokalitě. Modul snap-in **lokality a služby Active Directory** můžete použít ke konfiguraci nastavení na objektu propojení lokalit, ke kterému se lokality přidávají. Konfigurací nastavení na propojení lokalit můžete řídit, kdy probíhá replikace mezi dvěma nebo více lokalitami a jak často k ní dochází. Další informace najdete v tématu [Plánování replikace mezi lokalitami](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Ochrana mezi weby a Azure

Nejdřív vytvořte řadič domény ve službě Azure Virtual Network. Při povýšení serveru na roli řadiče domény zadejte stejný název domény, který se používá v primární lokalitě.

Potom překonfigurujte server DNS pro virtuální síť tak, aby používala server DNS v Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Síť Azure":::

### <a name="azure-to-azure-protection"></a>Ochrana z Azure do Azure

Nejdřív vytvořte řadič domény ve službě Azure Virtual Network. Při povýšení serveru na roli řadiče domény zadejte stejný název domény, který se používá v primární lokalitě.

Potom překonfigurujte server DNS pro virtuální síť tak, aby používala server DNS v Azure.

## <a name="test-failover-considerations"></a>Požadavky testovacího převzetí služeb při selhání

Aby se zabránilo dopadu na produkční úlohy, dojde k testovacímu převzetí služeb při selhání v síti, která je izolovaná od produkční sítě.

Většina aplikací vyžaduje přítomnost řadiče domény nebo serveru DNS. Proto je před převzetím služeb při selhání nutné v izolované síti vytvořit řadič domény, který se použije pro testovací převzetí služeb při selhání. Nejjednodušší způsob, jak to provést, je použít Site Recovery k replikaci virtuálního počítače, který je hostitelem řadiče domény nebo DNS. Potom spusťte testovací převzetí služeb při selhání virtuálního počítače řadiče domény před spuštěním testovacího převzetí služeb při selhání v plánu obnovení pro aplikaci.

1. K [replikaci](vmware-azure-tutorial.md) virtuálního počítače, který je hostitelem řadiče domény nebo DNS, použijte Site Recovery.
1. Vytvořte izolovanou síť. Každá virtuální síť, kterou vytvoříte v Azure, je ve výchozím nastavení izolovaná od ostatních sítí. Doporučujeme použít pro tuto síť stejný rozsah IP adres, který používáte ve své produkční síti. V této síti nepovolujte připojení Site-to-site.
1. Zadejte IP adresu DNS v izolované síti. Použijte IP adresu, kterou očekáváte, že se virtuální počítač DNS dostane. Pokud provádíte replikaci do Azure, zadejte IP adresu pro virtuální počítač, který se používá při převzetí služeb při selhání. Pokud chcete zadat IP adresu, v replikovaném virtuálním počítači v nastavení **výpočty a síť** vyberte nastavení **cílové IP adresy** .

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Testovací síť Azure":::

   > [!TIP]
   > Site Recovery se pokusí vytvořit testovací virtuální počítače v podsíti se stejným názvem a pomocí stejné IP adresy, která je k dispozici v nastavení **výpočty a síť** virtuálního počítače. Pokud ve službě Azure Virtual Network, která je pro testovací převzetí služeb při selhání, není dostupná podsíť se stejným názvem, testovací virtuální počítač se vytvoří v abecedně první podsíti.
   >
   > Pokud je cílová IP adresa součástí vybrané podsítě, Site Recovery se pokusí vytvořit virtuální počítač testovacího převzetí služeb při selhání pomocí cílové IP adresy. Pokud cílová IP adresa není součástí vybrané podsítě, vytvoří se virtuální počítač testovacího převzetí služeb při selhání pomocí další dostupné IP adresy ve vybrané podsíti.

### <a name="test-failover-to-a-secondary-site"></a>Test převzetí služeb při selhání na sekundární lokalitu

1. Pokud provádíte replikaci do jiné místní lokality a používáte protokol DHCP, [nastavte pro testovací převzetí služeb při selhání službu DNS a DHCP](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Proveďte test převzetí služeb při selhání virtuálního počítače řadiče domény, který běží v izolované síti. K provedení testovacího převzetí služeb při selhání použijte nejnovější bod obnovení _konzistentní vzhledem k aplikacím_ virtuálního počítače řadiče domény.
1. Spusťte testovací převzetí služeb při selhání pro plán obnovení, který obsahuje virtuální počítače, na kterých je aplikace spuštěná.
1. Po dokončení testování _vyčistěte testovací převzetí služeb při selhání_ na virtuálním počítači řadiče domény. Tento krok odstraní řadič domény, který byl vytvořen pro testovací převzetí služeb při selhání.

### <a name="remove-references-to-other-domain-controllers"></a>Odebrat odkazy na jiné řadiče domény

Když zahájíte testovací převzetí služeb při selhání, nezahrnujte všechny řadiče domény do testovací sítě. Pokud chcete odebrat odkazy na jiné řadiče domény, které existují ve vašem produkčním prostředí, možná budete muset [převzít role FSMO Active Directory](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) a [vyčistit metadata](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) pro chybějící řadiče domény.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problémy způsobené ochranou virtualizace

> [!IMPORTANT]
> Některé z konfigurací popsaných v této části nejsou standardní nebo výchozí konfigurace řadiče domény. Pokud nechcete provádět tyto změny v produkčním řadiči domény, můžete vytvořit řadič domény vyhrazený pro Site Recovery, který se má použít pro testovací převzetí služeb při selhání. Tyto změny proveďte pouze pro tento řadič domény.

Počínaje systémem Windows Server 2012 [jsou do Active Directory Domain Services (služba AD DS) integrována další bezpečnostní opatření](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Tato ochrana chrání virtualizované řadiče domény proti vrácení hodnoty USN (Update Sequence Number), pokud základní platforma hypervisoru podporuje **VM-GenerationID**. Azure podporuje **VM-GenerationID**. Z tohoto důvodu mají řadiče domény, na kterých běží Windows Server 2012 nebo novější, na virtuálních počítačích Azure tyto další bezpečnostní opatření.

Když se resetuje **VM-GenerationID** , obnoví se taky hodnota **InvocationID** databáze služba AD DS. Kromě toho je fond relativního ID (RID) zahozen a `SYSVOL` Složka je označena jako neautoritativní. Další informace najdete v tématu [Úvod do Active Directory Domain Services virtualizace](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) a [bezpečné virtualizace systém souborů DFS (DISTRIBUTED File System) replikace (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

Převzetí služeb při selhání do Azure může způsobit resetování **virtuálního počítače – GenerationID** . Resetování **virtuálního počítače – GenerationID** aktivuje dodatečnou ochranu, když se virtuální počítač řadiče domény spustí v Azure. To může mít za následek výrazné zpoždění při přihlášení k virtuálnímu počítači řadiče domény.

Vzhledem k tomu, že tento řadič domény se používá jenom v testovacím převzetí služeb při selhání, není ochrana virtualizace nutná. Abyste se ujistili, že se hodnota **GenerationID** virtuálního počítače pro virtuální počítač řadiče domény nemění, můžete v místním řadiči domény změnit hodnotu v následujících hodnotách `DWORD` na **4** :

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Příznaky ochrany virtualizace

Pokud se po testovacím převzetí služeb při selhání aktivují bezpečnostní opatření virtualizace, může se zobrazit jeden nebo několik následujících příznaků:

- Hodnota **GenerationID** se změní:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Změna ID generování":::

- Hodnota **InvocationID** se změní:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Změna ID vyvolání":::

- `SYSVOL` Složka a `NETLOGON` sdílené složky nejsou k dispozici.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Sdílená složka SYSVOL":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Složka NtFrs SYSVOL":::

- Databáze DFSR se odstraní.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="Databáze DFSR se odstraní.":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Řešení potíží s řadičem domény během testovacího převzetí služeb

> [!IMPORTANT]
> Některé z konfigurací popsaných v této části nejsou standardní nebo výchozí konfigurace řadiče domény. Pokud nechcete provádět tyto změny v produkčním řadiči domény, můžete vytvořit řadič domény, který je vyhrazený pro Site Recovery testovací převzetí služeb při selhání. Změny proveďte pouze pro vyhrazený řadič domény.

1. Na příkazovém řádku spusťte následující příkaz, který zkontroluje, jestli `SYSVOL` je složka a `NETLOGON` Složka sdílená:

    `NET SHARE`

1. Na příkazovém řádku spusťte následující příkaz, abyste zajistili, že řadič domény správně funguje:

    `dcdiag /v > dcdiag.txt`

1. Ve výstupním protokolu vyhledejte následující text. Text potvrdí, že řadič domény funguje správně.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Pokud jsou předchozí podmínky splněné, je pravděpodobnější, že řadič domény funguje správně. Pokud není, proveďte následující kroky:

1. Proveďte autoritativní obnovení řadiče domény. Mějte na paměti následující informace:

    - I když nedoporučujeme replikaci pomocí [služby replikace souborů (FRS)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379), pokud používáte replikaci FRS, postupujte podle kroků pro autoritativní obnovení. Tento proces je popsán v tématu [použití klíče registru Burflags k opětovné inicializaci služby replikace souborů](https://support.microsoft.com/kb/290762).

      Další informace o BurFlags najdete v příspěvku na blogu [D2 a D4: co je pro?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for).

    - Pokud používáte replikaci DFSR, proveďte kroky pro autoritativní obnovení. Tento postup je popsaný v tématu [Vynutit autoritativní a neautoritativní synchronizaci složky SYSVOL replikované službou DFSR (jako D4/D2 pro službu FRS)](https://support.microsoft.com/kb/2218556).

      Můžete také použít funkce PowerShellu. Další informace najdete v tématu [funkce prostředí PowerShell pro autoritativní nebo neautoritativní obnovení systému DFSR-SYSVOL](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions).

1. Vynechejte požadavek na počáteční synchronizaci nastavením následujícího klíče registru na **hodnotu 0** v místním řadiči domény. Pokud `DWORD` neexistuje, můžete ho vytvořit pod uzlem **parametry** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Další informace najdete v tématu [řešení potíží s ID události dns 4013: Server DNS nemohl načíst zóny DNS integrované se službou Active Directory](https://support.microsoft.com/kb/2001093).

1. Zakažte požadavek, aby byl server globálního katalogu dostupný pro ověření přihlášení uživatele. Uděláte to tak, že v místním řadiči domény nastavíte následující klíč registru na hodnotu **1**. Pokud `DWORD` neexistuje, můžete ho vytvořit pod uzlem **LSA** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Další informace najdete v tématu [Jak funguje globální katalog](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10)).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS a řadič domény v různých počítačích

Pokud používáte řadič domény a DNs na stejném virtuálním počítači, můžete tento postup přeskočit.

Pokud DNS není na stejném virtuálním počítači jako řadič domény, musíte pro testovací převzetí služeb při selhání vytvořit virtuální počítač DNS. Můžete použít nový server DNS a vytvořit všechny požadované zóny. Pokud je například doména služby Active Directory `contoso.com` , můžete vytvořit ZÓNU DNS s názvem `contoso.com` . Položky, které odpovídají službě Active Directory, je třeba aktualizovat v DNS následujícím způsobem:

1. Zajistěte, aby byla tato nastavení zavedena před tím, než se spustí kterýkoli jiný virtuální počítač v plánu obnovení:

   - Zóna musí být pojmenována za kořenovým názvem doménové struktury.
   - Zóna musí být zálohovaná souborem.
   - V zóně musí být povolená zabezpečená a nezabezpečená aktualizace.
   - Překladač virtuálního počítače, který je hostitelem řadiče domény, by měl odkazovat na IP adresu virtuálního počítače DNS.

1. Na virtuálním počítači, který je hostitelem řadiče domény, spusťte následující příkaz:

   `nltest /dsregdns`

1. Spuštěním následujících příkazů přidejte zónu na server DNS, povolte nezabezpečené aktualizace a přidejte položku pro zónu do DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](site-recovery-workload.md) o ochraně podnikových úloh pomocí Azure Site Recovery.
