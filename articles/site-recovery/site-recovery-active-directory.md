---
title: Nastavení zotavení po havárii pro Active Directory a DNS pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak implementovat řešení zotavení po havárii pro Active Directory a DNS pomocí Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: f4da0a4672bc50688d0a25bbd2db1f3be984ee8b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821384"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Nastavení zotavení po havárii pro Active Directory a DNS

Podnikové aplikace, jako je SharePoint, Dynamics AX a SAP závisí na Active Directory a infrastruktury služby DNS správně fungovat. Při nastavování zotavení po havárii pro aplikace, často potřebujete k obnovení služby Active Directory a DNS před obnovením další součásti aplikace, abyste zajistili funkčnost správné aplikace.

Můžete použít [Site Recovery](site-recovery-overview.md) vytvořit plán obnovení po havárii pro službu Active Directory. Pokud dojde k přerušení, můžete zahájit převzetí služeb při selhání. Můžete mít služby Active Directory si zprovoznit během pár minut. Pokud jste nasadili služby Active Directory pro několik aplikací v primární lokalitě, například pro Sharepointu a SAP, můžete převzít služby při selhání lokality dokončeno. Nejprve můžete převzít služby Active Directory pomocí služby Site Recovery. Potom převzetí služeb při selhání jiných aplikací, pomocí plánů obnovení specifické pro aplikaci.

Tento článek vysvětluje, jak vytvořit řešení zotavení po havárii pro službu Active Directory. Zahrnují požadavky a pokyny k převzetí služeb při selhání. Byste měli vědět, jak služby Active Directory a Site Recovery, než začnete.

## <a name="prerequisites"></a>Požadavky

* Pokud replikujete do Azure, [Příprava prostředků Azure](tutorial-prepare-azure.md), včetně předplatného, virtuální síť Azure, účet úložiště a trezor služby Recovery Services.
* Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-azure.md) pro všechny komponenty.

## <a name="replicate-the-domain-controller"></a>Replikace řadiče domény

- Musíte nastavit replikace Site Recovery na alespoň jeden virtuální počítač, který je hostitelem řadiče domény nebo DNS.
- Pokud máte víc řadičů domény ve vašem prostředí, také musíte vytvořit další řadič domény v cílové lokalitě. Další řadič domény může být v Azure, nebo sekundárního místního datového centra.
- Pokud máte pouze několik aplikací a jeden řadič domény, můžete převzetí služeb při selhání celé lokality společně. V takovém případě vám doporučujeme používat Site Recovery pro replikaci řadiče domény do cílového webu (buď v Azure nebo sekundárního místního datového centra). Můžete použít stejné řadič domény replikovaný nebo DNS virtuálního počítače pro [testovací převzetí služeb při selhání](#test-failover-considerations).
- - Pokud máte mnoho aplikací a více než jeden řadič domény ve vašem prostředí, nebo pokud máte v plánu převzetí služeb při selhání několik aplikací najednou, navíc k replikaci virtuálního počítače řadiče domény s využitím Site Recovery, doporučujeme, abyste nastavili Další řadič domény v cílové lokalitě (buď v Azure nebo sekundárního místního datového centra). Pro [testovací převzetí služeb při selhání](#test-failover-considerations), můžete použít řadič domény, který se replikuje pomocí Site Recovery. Pro převzetí služeb při selhání můžete použít další řadič domény v cílové lokalitě.

## <a name="enable-protection-with-site-recovery"></a>Povolit ochranu pomocí služby Site Recovery

Site Recovery můžete použít k ochraně virtuálního počítače, který je hostitelem řadiče domény nebo DNS.

### <a name="protect-the-vm"></a>Ochranu virtuálního počítače
Řadič domény, který se replikuje s využitím Site Recovery se používá pro [testovací převzetí služeb při selhání](#test-failover-considerations). Ujistěte se, že splňuje následující požadavky:

1. Je řadič domény serverem globálního katalogu.
2. Řadič domény musí být vlastníkem této role FSMO pro role, které jsou potřeba při selhání testu. V opačném případě tyto role bude potřeba [převzaty](https://aka.ms/ad_seize_fsmo) po převzetí služeb při selhání.

### <a name="configure-vm-network-settings"></a>Konfigurace nastavení sítě virtuálního počítače
Pro virtuální počítač, který je hostitelem řadiče domény nebo DNS ve službě Site Recovery, konfigurace nastavení sítě v části **výpočty a síť** nastavení replikovaného virtuálního počítače. Tím se zajistí, že virtuální počítač je připojený ke správné síti po převzetí služeb při selhání.

## <a name="protect-active-directory"></a>Ochrana služby Active Directory

### <a name="site-to-site-protection"></a>Ochrana Site-to-site
Vytvoření řadiče domény v sekundární lokalitě. Když zvýšíte úroveň serveru role řadiče domény, zadejte název stejné domény, který používáte v primární lokalitě. Můžete použít **serverů služby Active Directory a služby** modul snap-in a zadejte nastavení v objektu lokality odkaz, ke kterému jsou přidány lokality. Tím, že nakonfigurujete nastavení na propojení lokalit, můžete určit, kdy dojde k replikaci mezi dva nebo víc lokalit, a jak často k dochází. Další informace najdete v tématu [plánování replikace mezi lokalitami](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Ochrana serveru do Azure
Nejprve vytvořte řadič domény ve službě Azure virtual network. Když zvýšíte úroveň serveru role řadiče domény, zadejte se stejným názvem domény, který se používá v primární lokalitě.

Překonfigurujte server DNS pro virtuální síť použít DNS server v Azure.

![Síť Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Ochrana Azure do Azure
Nejprve vytvořte řadič domény ve službě Azure virtual network. Když zvýšíte úroveň serveru role řadiče domény, zadejte se stejným názvem domény, který se používá v primární lokalitě.

Překonfigurujte server DNS pro virtuální síť použít DNS server v Azure.

## <a name="test-failover-considerations"></a>Důležité informace o převzetí služeb při selhání testu
Aby se zabránilo dopadu na produkční úlohy, testovací převzetí služeb při selhání v síti, která bude izolovaná od produkční sítě.

Většina aplikací vyžaduje přítomnost řadiče domény nebo serveru DNS. Proto se před aplikace převezme služby při selhání, musíte vytvořit řadič domény v izolované síti má být použit pro testovací převzetí služeb při selhání. Nejjednodušší způsob je pomocí Site Recovery můžete replikovat virtuální počítač, který je hostitelem řadiče domény nebo DNS. Pak spusťte testovací převzetí služeb virtuálního počítače řadiče domény před spuštěním testovací převzetí služeb při selhání plánu obnovení pro aplikaci. Tady je postup:

1. Pomocí Site Recovery můžete [replikovat](vmware-azure-tutorial.md) virtuální počítač, který je hostitelem řadiče domény nebo DNS.
2. Vytvoření izolované sítě. Virtuální sítě, který vytvoříte v Azure je izolovaná od jiných sítí ve výchozím nastavení. Doporučujeme použít stejný rozsah IP adres pro tuto síť, který používáte v produkční sítě. Nepovolí připojení site-to-site v této síti.
3. Zadejte adresu IP serveru DNS v izolované síti. Použijte IP adresu, která očekáváte, že virtuální počítač DNS se. Pokud replikujete do Azure, zadejte IP adresu pro virtuální počítač, který se používá na převzetí služeb při selhání. Zadejte IP adresu v replikované virtuální počítače, v **výpočty a síť** nastavení, vyberte **cílová IP adresa** nastavení.

    ![Síť testování v Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery se pokusí vytvořit testovací virtuální počítače v podsíti se stejným názvem a pomocí stejné IP adresy, která je součástí **výpočty a síť** nastavení virtuálního počítače. Pokud podsíť se stejným názvem není k dispozici v Azure virtual network, která je k dispozici pro testovací převzetí služeb při selhání, vytvoří se v abecedním pořadí první podsíť testovacího virtuálního počítače.
    >
    > Pokud cílová IP adresa je součástí vybrané podsíti, Site Recovery se pokusí vytvořit testovacího převzetí služeb při selhání virtuálního počítače pomocí cílová IP adresa. Pokud cílová IP adresa není součástí vybrané podsíti, testovacího převzetí služeb při selhání virtuálního počítače se vytvoří s použitím další dostupné IP adresy ve vybrané podsíti.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Testovací převzetí služeb při selhání do sekundární lokality

1. Pokud replikujete do jiné místní lokality a používáte DHCP, [nastavení DNS a DHCP pro testovací převzetí služeb při selhání](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Proveďte testovací převzetí služeb virtuálního počítače řadiče domény, na kterém běží v izolované síti. Použijte nejnovější dostupné *konzistentní s aplikací* bod obnovení virtuálního počítače řadiče domény provést testovací převzetí služeb.
3. Spuštění testovací převzetí služeb při selhání pro plán obnovení, který obsahuje virtuální počítače, které aplikace bude spuštěna na.
4. Po dokončení testování *vyčistit testovací převzetí služeb* na virtuálního počítače řadiče domény. Tento krok odstraní řadiče domény, který byl vytvořen pro testovací převzetí služeb při selhání.


### <a name="remove-references-to-other-domain-controllers"></a>Odebrat odkazy na ostatní řadiče domény
Když spustíte testovací převzetí služeb, jsou všechny řadiče domény v testovací síti. Chcete-li odebrat odkazy na ostatní řadiče domény, které existují v produkčním prostředí, možná budete muset [převzetí rolí FSMO Active Directory](https://aka.ms/ad_seize_fsmo) a proveďte [Vyčištění metadat](https://technet.microsoft.com/library/cc816907.aspx) chybějících řadiče domény .


### <a name="issues-caused-by-virtualization-safeguards"></a>Potíže způsobené službou ochrana virtualizace

> [!IMPORTANT]
> Některé konfigurace popsané v této části nejsou standard nebo výchozí konfigurace řadiče domény. Pokud nechcete tyto změny provést na řadič domény v produkčním prostředí, můžete vytvořit řadič domény, který je vyhrazen pro Site Recovery pro testovací převzetí služeb při selhání. Tyto změny proveďte pouze na tento řadič domény.  
>
>

Od verze Windows serveru 2012 [další bezpečnostní opatření jsou integrované do Active Directory Domain Services (AD DS)](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Tato bezpečnostní opatření pomáhá chránit virtualizované řadiče domény před vrácení hodnoty USN zpět, pokud podporuje základní platformu hypervisoru **VM-GenerationID**. Azure podporuje **VM-GenerationID**. Z tohoto důvodu mají řadiče domény, na kterých běží Windows Server 2012 nebo novější na Azure virtual machines tyto další bezpečnostní opatření.


Při **VM-GenerationID** se resetuje **InvocationID** hodnota z databáze služby AD DS je také resetovat. Kromě toho se zahodí fond RID a adresáři SYSVOL je označen jako neautoritativní. Další informace najdete v tématu [Úvod do virtualizace služby Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) a [bezpečně virtualizace DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Přebírání služeb při selhání do Azure může způsobit, že **VM-GenerationID** resetovat. Resetuje se **VM-GenerationID** další bezpečnostní opatření se aktivuje při spuštění virtuálního počítače řadiče domény v Azure. To může vést *poměrně dlouhodobému výpadku* v nebudou moct přihlásit k virtuálnímu počítači řadiče domény.

Protože tento řadič domény se používá jenom v testovací převzetí služeb při selhání, ochrana virtualizace nejsou potřebné. Zajistit, aby **VM-GenerationID** nedojde ke změně hodnoty pro virtuální počítač řadiče domény, můžete změnit hodnotu DWORD následující **4** v místní řadič domény:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Mezi příznaky bezpečnostní opatření virtualizace

Pokud se ochrana virtualizace se aktivuje po testovací převzetí služeb, může se zobrazit jedna nebo více z následujících příznaků:  

* **GenerationID** hodnota se mění.

    ![Změna ID generování](./media/site-recovery-active-directory/Event2170.png)

* **InvocationID** hodnota se mění.

    ![Změna ID vyvolání](./media/site-recovery-active-directory/Event1109.png)

* Sdílené složky SYSVOL a NETLOGON nejsou k dispozici.

    ![Sdílená složka SYSVOL](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* Databáze DFSR se odstraní.

    ![Databáze DFSR se odstraní.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Řešení potíží řadič domény během testovacího převzetí služeb při selhání

> [!IMPORTANT]
> Některé konfigurace popsané v této části ještě nejsou standard nebo výchozí konfigurace řadiče domény. Pokud nechcete tyto změny provést na řadič domény v produkčním prostředí, můžete vytvořit řadič domény, který je vyhrazen pro Site Recovery testovací převzetí služeb při selhání. Proveďte změny pouze na tento řadič domény vyhrazené.  
>
>

1. Na příkazovém řádku spusťte následující příkaz a zkontrolujte, zda jsou sdílené složky SYSVOL a NETLOGON:

    `NET SHARE`

2. Na příkazovém řádku spusťte následující příkaz a ověřte, že řadič domény správně funguje:

    `dcdiag /v > dcdiag.txt`

3. Ve výstupu protokolu vyhledejte následující text. Text potvrdí, že řadič domény správně funguje.

    * "úspěšných testů připojení"
    * "inzerování úspěšných testů"
    * "úspěšných testů MachineAccount"

Pokud předchozí podmínky splněny, je pravděpodobné, že řadič domény správně funguje. Pokud není, proveďte následující kroky:

1. Proveďte autoritativním obnovením řadiče domény. Mějte tyto informace:
    * Ale nedoporučujeme [FRS replikace](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), pokud používáte služby FRS replikaci, postupujte podle kroků pro autoritativním obnovením. Proces je popsán v [pomocí klíče registru BurFlags k inicializaci služby replikace souborů](https://support.microsoft.com/kb/290762).

        Další informace o BurFlags, naleznete v příspěvku blogu [D2 a D4: Co je to pro? ](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Pokud používáte replikace DFSR, proveďte kroky pro autoritativním obnovením. Proces je popsán v [vynutit autoritativní a neautoritativní synchronizaci adresáře SYSVOL replikovaného DFSR (jako "D4/D2 u služby FRS)](https://support.microsoft.com/kb/2218556).

        Můžete také použít funkce Powershellu. Další informace najdete v tématu [funkce Powershellu autoritativní a neautoritativní obnovení DFSR SYSVOL](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Obejít požadavek na počáteční synchronizace nastavením následujícího klíče registru na **0** v místní řadič domény. Pokud DWORD neexistuje, můžete ho v části vytvořit **parametry** uzlu.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Další informace najdete v tématu [Poradce při potížích s 4013 ID události DNS: DNS server se nepodařilo načíst AD integrované zóny DNS](https://support.microsoft.com/kb/2001093).

3. Zakažte požadavek na server globálního katalogu se ověření přihlášení uživatele k dispozici. K tomu, v místní řadič domény, nastavte následující klíč registru na **1**. Pokud DWORD neexistuje, můžete ho v části vytvořit **Lsa** uzlu.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Další informace najdete v tématu [zakažte požadavek na server globálního katalogu se ověření přihlášení uživatele k dispozici](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS a řadič domény na různých počítačích

Pokud používáte řadiče domény a DNs na stejný virtuální počítač, můžete tento postup přeskočit.


Pokud DNS není na stejný virtuální počítač jako řadič domény, musíte vytvořit virtuální počítač DNS pro testovací převzetí služeb. Můžete použít nový server DNS a vytvořit všechny požadované zóny. Například pokud je vaší doménou služby Active Directory contoso.com, můžete vytvořit zónu DNS s názvem contoso.com. Položky, které odpovídají služby Active Directory musí aktualizovat ve službě DNS následujícím způsobem:

1. Ujistěte se, že tato nastavení jsou na místě před zahájením ostatních virtuálních počítačů v plánu obnovení:
   * Za názvem kořenového doménové struktury musí mít název zóny.
   * Zóna musí být záložních souborů.
   * Zóna musí být povolena pro aktualizace zabezpečení a nezabezpečené.
   * Překladač virtuální počítač, který je hostitelem řadiče domény by měl odkazovat na IP adresu virtuálního počítače DNS.

2. Spuštěním následujícího příkazu na virtuálním počítači, který je hostitelem řadiče domény:

    `nltest /dsregdns`

3. Spuštěním následujících příkazů přidejte na serveru DNS pro zónu, Povolit nezabezpečené aktualizace a přidat záznam pro zónu DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Další postup
Další informace o [Ochrana firemních procesů pomocí Azure Site Recovery](site-recovery-workload.md).
