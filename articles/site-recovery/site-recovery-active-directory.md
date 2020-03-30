---
title: Nastavení zotavení po havárii služby Active Directory/DNS pomocí nástroje Azure Site Recovery
description: Tento článek popisuje, jak implementovat řešení zotavení po havárii pro službu Active Directory a DNS pomocí azure site recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 8c1f85217db12b60cdcd8ea0bdb65792b8d02648
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257807"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Nastavení zotavení po havárii pro službu Active Directory a službu DNS

Podnikové aplikace jako SharePoint, Dynamics AX a SAP závisí na službě Active Directory a infrastruktuře DNS, aby fungovaly správně. Při nastavování zotavení po havárii pro aplikace je často nutné obnovit služby Active Directory a DNS před obnovením dalších součástí aplikace, abyste zajistili správnou funkčnost aplikace.

Pomocí [nástroje Site Recovery](site-recovery-overview.md) můžete vytvořit plán zotavení po havárii pro službu Active Directory. Dojde-li k narušení, můžete zahájit převzetí služeb při selhání. Služba Active Directory může být zprovozněna během několika minut. Pokud jste nasadili službu Active Directory pro více aplikací v primární lokalitě, například pro SharePoint a SAP, můžete chtít převzít služby při selhání celé lokality. Službu Active Directory můžete nejprve použít pomocí nástroje Site Recovery. Potom převzetí služeb při selhání jiných aplikací, pomocí plánů obnovení specifické pro aplikaci.

Tento článek vysvětluje, jak vytvořit řešení zotavení po havárii pro službu Active Directory. Obsahuje požadavky a pokyny pro převzetí služeb při selhání. Než začnete, měli byste být obeznámeni se službou Active Directory a obnovením webu.

## <a name="prerequisites"></a>Požadavky

* Pokud se replikujete do Azure, [připravte prostředky Azure](tutorial-prepare-azure.md), včetně předplatného, virtuální sítě Azure, účtu úložiště a trezoru služby Recovery Services.
* Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-azure.md) pro všechny komponenty.

## <a name="replicate-the-domain-controller"></a>Replikace řadiče domény

- Replikaci obnovení sítě musíte nastavit alespoň na jednom virtuálním počítači, který je hostitelem řadiče domény nebo DNS.
- Pokud máte ve svém prostředí více řadičů domény, musíte také nastavit další řadič domény v cílové lokalitě. Další řadič domény může být v Azure nebo v sekundárním místním datovém centru.
- Pokud máte pouze několik aplikací a jeden řadič domény, můžete chtít použít služby při selhání celé lokality společně. V takovém případě doporučujeme použít site recovery k replikaci řadiče domény do cílové lokality (buď v Azure, nebo v sekundárním místním datovém centru). Pro [převzetí služeb](#test-failover-considerations)při selhání můžete použít stejný replikovaný řadič domény nebo virtuální počítač DNS .
- - Pokud máte ve svém prostředí mnoho aplikací a více než jeden řadič domény nebo pokud plánujete převzetí služeb při selhání několika aplikací najednou, doporučujeme kromě replikace virtuálního počítače řadiče domény pomocí služby Site Recovery nastavit další řadič domény v cílové lokalitě (buď v Azure, nebo v sekundárním místním datovém centru). Pro [zkušební převzetí služeb při selhání](#test-failover-considerations)můžete použít řadič domény, který je replikován službou Site Recovery. Pro převzetí služeb při selhání můžete použít další řadič domény v cílové lokalitě.

## <a name="enable-protection-with-site-recovery"></a>Povolit ochranu pomocí site recovery

Pomocí nástroje Site Recovery můžete chránit virtuální počítač, který je hostitelem řadiče domény nebo DNS.

### <a name="protect-the-vm"></a>Ochrana virtuálního virtuálního mísy
Řadič domény, který je replikován pomocí služby Site Recovery, se používá pro [převzetí služeb při selhání testu](#test-failover-considerations). Ujistěte se, že splňuje následující požadavky:

1. Řadič domény je server globálního katalogu.
2. Řadič domény by měl být vlastníkem role FSMO pro role, které jsou potřebné během převzetí služeb při selhání testu. V opačném případě bude nutné tyto role po převzetí služeb při selhání [získat.](https://aka.ms/ad_seize_fsmo)

### <a name="configure-vm-network-settings"></a>Konfigurace nastavení sítě virtuálních montovanek
Pro virtuální počítač, který je hostitelem řadiče domény nebo DNS, nakonfigurujte v site recovery nastavení sítě v nastavení **Výpočetní a síťové** replikovaného virtuálního počítače. Tím zajistíte, že virtuální počítač je připojen ke správné síti po převzetí služeb při selhání.

## <a name="protect-active-directory"></a>Ochrana služby Active Directory

### <a name="site-to-site-protection"></a>Ochrana mezi lokalitami
Vytvořte řadič domény v sekundární síti. Při povyšení serveru na roli řadiče domény zadejte název stejné domény, která se používá v primární lokalitě. Pomocí modulu snap-in **Sítě a služby Active Directory** můžete konfigurovat nastavení objektu spojení sítí, ke kterému jsou sítě přidány. Konfigurací nastavení spojení sítí můžete určit, kdy dojde k replikaci mezi dvěma nebo více sítěmi a jak často k ní dojde. Další informace naleznete v [tématu Plánování replikace mezi sítěmi](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Ochrana site-to-Azure
Nejprve vytvořte řadič domény ve virtuální síti Azure. Když převýšíte server na roli řadiče domény, zadejte stejný název domény, který se používá v primární lokalitě.

Potom překonfigurujte server DNS pro virtuální síť tak, aby používal server DNS v Azure.

![Síť Azure](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Ochrana Azure-Azure
Nejprve vytvořte řadič domény ve virtuální síti Azure. Když převýšíte server na roli řadiče domény, zadejte stejný název domény, který se používá v primární lokalitě.

Potom překonfigurujte server DNS pro virtuální síť tak, aby používal server DNS v Azure.

## <a name="test-failover-considerations"></a>Test převzetí služeb při selhání aspekty
Chcete-li se vyhnout dopadu na produkční úlohy, testování převzetí služeb při selhání dochází v síti, která je izolovaná od produkční sítě.

Většina aplikací vyžaduje přítomnost řadiče domény nebo serveru DNS. Proto před převzetím služeb při selhání aplikace je nutné vytvořit řadič domény v izolované síti, který bude použit pro převzetí služeb při selhání testu. Nejjednodušší způsob, jak to udělat, je použít site recovery k replikaci virtuálního počítače, který je hostitelem řadiče domény nebo DNS. Potom spusťte zkušební převzetí služeb při selhání virtuálního počítače řadiče domény před spuštěním testu převzetí služeb při selhání plánu obnovení pro aplikaci. Zde je návod, jak to udělat:

1. Pomocí nástroje Site Recovery [můžete replikovat](vmware-azure-tutorial.md) virtuální počítač, který je hostitelem řadiče domény nebo DNS.
2. Vytvořte izolovanou síť. Všechny virtuální sítě, které vytvoříte v Azure je izolované od jiných sítí ve výchozím nastavení. Doporučujeme použít stejný rozsah IP adres pro tuto síť, který používáte v produkční síti. Nepovolujte připojení mezi sítěmi v této síti.
3. Zadejte adresu IP DNS v izolované síti. Použijte IP adresu, kterou očekáváte, že virtuální počítač DNS získat. Pokud se replikujete do Azure, zadejte IP adresu pro virtuální počítač, který se používá při převzetí služeb při selhání. Chcete-li zadat adresu IP, vyberte v replikovaném virtuálním počítači v nastavení **Výpočetní výkon a síť** nastavení cílové **IP** adresy.

    ![Testovací síť Azure](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery se pokusí vytvořit testovací virtuální počítače v podsíti se stejným názvem a pomocí stejné IP adresy, která je k dispozici v nastavení **výpočetních prostředků a sítě** virtuálního počítače. Pokud podsíť se stejným názvem není k dispozici ve virtuální síti Azure, která je k dispozici pro převzetí služeb při selhání testu, testovací virtuální počítač se vytvoří v abecedně první podsíti.
    >
    > Pokud je cílová adresa IP součástí vybrané podsítě, site recovery se pokusí vytvořit testovací virtuální počítač s podporou převzetí služeb při selhání pomocí cílové IP adresy. Pokud cílová IP adresa není součástí vybrané podsítě, testovací virtuální počítač s podporou převzetí služeb při selhání se vytvoří pomocí další dostupné IP adresy ve vybrané podsíti.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>Test převzetí služeb při selhání do sekundární lokality

1. Pokud se replikujete do jiného místního webu a používáte službu DHCP, [nastavte služby DNS a DHCP pro převzetí služeb při selhání testu](hyper-v-vmm-test-failover.md#prepare-dhcp).
2. Proveďte zkušební převzetí služeb při selhání virtuálního počítače řadiče domény, který běží v izolované síti. K provedení testu převzetí služeb při selhání použijte nejnovější dostupný bod obnovení virtuálního počítače řadiče domény, který je k dispozici konzistentní *aplikace.*
3. Spusťte testovací převzetí služeb při selhání pro plán obnovení, který obsahuje virtuální počítače, na kterých aplikace běží.
4. Po dokončení testování *vyčistěte převzetí služeb při selhání testu* ve virtuálním počítači řadiče domény. Tento krok odstraní řadič domény, který byl vytvořen pro převzetí služeb při selhání testu.


### <a name="remove-references-to-other-domain-controllers"></a>Odebrání odkazů na jiné řadiče domény
Při zahájení testu převzetí služeb při selhání nezahrnujte všechny řadiče domény do testovací sítě. Chcete-li odebrat odkazy na jiné řadiče domény, které existují ve vašem produkčním prostředí, bude pravděpodobně nutné [převzít role služby FSMO služby Active Directory](https://aka.ms/ad_seize_fsmo) a [provést vyčištění metadat](https://technet.microsoft.com/library/cc816907.aspx) pro chybějící řadiče domény.


### <a name="issues-caused-by-virtualization-safeguards"></a>Problémy způsobené virtualizačními zárukami

> [!IMPORTANT]
> Některé konfigurace popsané v této části nejsou standardní nebo výchozí konfigurace řadiče domény. Pokud nechcete provádět tyto změny v produkčním řadiči domény, můžete vytvořit řadič domény, který je vyhrazen pro site recovery pro použití pro zkušební převzetí služeb při selhání. Tyto změny proveďte pouze v tomto řadiči domény.  
>
>

Počínaje systémem Windows Server 2012 [jsou do služby Active Directory Domain Services (AD DS) integrována další ochranná opatření.](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) Tato ochranná opatření pomáhají chránit virtualizované řadiče domény proti vrácení zpět nouzí, pokud základní platforma hypervisoru podporuje **ID generace virtuálních montova.** Azure podporuje **ID generace virtuálních montovin**. Z tohoto důvodu mají tyto další záruky řadiče domény, které na virtuálních počítačích Azure spouštějí Windows Server 2012 nebo novější.


Při **resetování VM-GenerationID** je také obnovit hodnotu **InvocationID** databáze služby AD DS je také obnovit. Kromě toho je fond RID zahozen a složka sysvol je označena jako neautoritativní. Další informace naleznete [v tématu Úvod do virtualizace služby Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) a bezpečné [virtualizace nástroje DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/).

Převzetí při selhání azure může způsobit obnovení **ID vygenerování virtuálního** počítače. Resetování **ID v generaci virtuálního** počítače aktivuje další ochranná opatření při spuštění virtuálního počítače řadiče domény v Azure. To může mít za následek *významné zpoždění* v možnosti přihlášení k virtuálnímu počítači řadiče domény.

Vzhledem k tomu, že tento řadič domény se používá pouze v testu převzetí služeb při selhání, nejsou nutná ochranná opatření virtualizace. Chcete-li zajistit, aby se hodnota **VM-GenerationID** pro virtuální počítač řadiče domény nezměnila, můžete změnit hodnotu následujícího DWORD na **4** v místním řadiči domény:


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>Příznaky virtualizačních záruk

Pokud se po převzetí služeb při selhání testu aktivují ochranná opatření virtualizace, může se zobrazit jeden nebo více následujících příznaků:  

* Změní **hodnotu GenerationID.**

    ![Změna ID generace](./media/site-recovery-active-directory/Event2170.png)

* Změní se hodnota **InvocationID.**

    ![Změna ID vyvolání](./media/site-recovery-active-directory/Event1109.png)

* Sysvol složka a NETLOGON sdílené složky nejsou k dispozici.

    ![Sysvol sdílení složek](./media/site-recovery-active-directory/sysvolshare.png)

    ![Složka NtFrs sysvol](./media/site-recovery-active-directory/Event13565.png)

* Databáze DFSR jsou odstraněny.

    ![Databáze DFSR jsou odstraněny.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Poradce při potížích s řadičem domény během převzetí služeb při selhání testu

> [!IMPORTANT]
> Některé konfigurace popsané v této části nejsou standardní nebo výchozí konfigurace řadiče domény. Pokud nechcete provádět tyto změny v produkčním řadiči domény, můžete vytvořit řadič domény, který je vyhrazen pro převzetí služeb při selhání testu site recovery. Proveďte změny pouze v tomto vyhrazeném řadiči domény.  
>
>

1. Na příkazovém řádku spusťte následující příkaz a zkontrolujte, zda jsou sdíleny složky sysvol a složka NETLOGON:

    `NET SHARE`

2. Na příkazovém řádku spusťte následující příkaz, abyste zajistili, že řadič domény funguje správně:

    `dcdiag /v > dcdiag.txt`

3. Ve výstupním protokolu vyhledejte následující text. Text potvrzuje, že řadič domény funguje správně.

    * "prošel test připojení"
    * "prošel testem Reklama"
    * "prošel test MachineAccount"

Pokud jsou splněny předchozí podmínky, je pravděpodobné, že řadič domény funguje správně. Pokud tomu tak není, proveďte následující kroky:

1. Proveďte autoritativní obnovení řadiče domény. Mějte na paměti následující informace:
    * I když nedoporučujeme [replikaci frs](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), pokud používáte replikaci FRS, postupujte podle pokynů pro autoritativní obnovení. Tento proces je popsán v [části Použití klíče registru BurFlags k opětovné inicializaci služby replikace souborů](https://support.microsoft.com/kb/290762).

        Další informace o BurFlags najdete v příspěvku na blogu [D2 a D4: K čemu je?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Pokud používáte replikaci dfsr, proveďte kroky pro autoritativní obnovení. Proces je popsán v force [autoritativní a neautoritativní synchronizaci pro složku sysvol replikovanou dfsr (například "D4/D2" pro FRS)](https://support.microsoft.com/kb/2218556).

        Můžete také použít funkce prostředí PowerShell. Další informace naleznete v tématu [DFSR-SYSVOL autoritativní/neautoritativní obnovení funkcí prostředí PowerShell](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/).

2. Obejít počáteční požadavek na synchronizaci nastavením následujícího klíče registru na **0** v místním řadiči domény. Pokud DWORD neexistuje, můžete jej vytvořit pod **parametry** uzlu.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    Další informace naleznete [v tématu Poradce při potížích s ID události DNS 4013: Server DNS nemohl načíst zóny DNS integrované službou AD](https://support.microsoft.com/kb/2001093).

3. Zakažte požadavek, aby byl k dispozici server globálního katalogu pro ověření přihlášení uživatele. Chcete-li to provést, nastavte v místním řadiči domény následující klíč registru na **1**. Pokud DWORD neexistuje, můžete jej vytvořit pod uzlem **Lsa.**

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    Další informace naleznete [v tématu Disable the requirement that a global catalog server be available to validate user logons](https://support.microsoft.com/kb/241789).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS a řadič domény na různých počítačích

Pokud používáte řadič domény a dn na stejném virtuálním počítači, můžete tento postup přeskočit.


Pokud DNS není na stejném virtuálním počítači jako řadič domény, musíte vytvořit virtuální počítač DNS pro převzetí služeb při selhání testu. Můžete použít nový dns server a vytvořit všechny požadované zóny. Pokud je například vaše doména služby Active Directory contoso.com, můžete vytvořit zónu DNS s názvem contoso.com. Položky, které odpovídají službě Active Directory, musí být ve službě DNS aktualizovány následujícím způsobem:

1. Ujistěte se, že tato nastavení jsou na místě před spuštěním jiného virtuálního počítače v plánu obnovení:
   * Zóna musí být pojmenována po názvu kořenové doménové struktury.
   * Zóna musí být zálohována souborem.
   * Zóna musí být povolena pro zabezpečené a nezabezpečené aktualizace.
   * Překládání virtuálního počítače, který je hostitelem řadiče domény, by měl ukazovat na IP adresu virtuálního počítače DNS.

2. Na virtuálním počítači, který je hostitelem řadiče domény, spusťte následující příkaz:

    `nltest /dsregdns`

3. Spusťte následující příkazy pro přidání zóny na server DNS, povolení nezabezpečených aktualizací a přidání položky pro zónu do služby DNS:

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [ochraně podnikových úloh pomocí Azure Site Recovery](site-recovery-workload.md).
