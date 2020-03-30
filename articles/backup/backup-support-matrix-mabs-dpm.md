---
title: MABS & Matice podpory System Center DPM
description: Tento článek shrnuje podporu Azure Backup při použití Microsoft Azure Backup Server (MABS) nebo System Center DPM zálohovat místní a prostředky virtuálních zařízení Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582649"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matice podpory pro zálohování pomocí microsoft azure zálohovacího serveru nebo systémového centra DPM

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování místních počítačů a úloh a virtuálních počítačů (Virtuální počítače) Azure. Tento článek shrnuje nastavení podpory a omezení pro zálohování počítačů pomocí Microsoft Azure Backup Server (MABS) nebo System Center Data Protection Manager (DPM) a Azure Backup.

## <a name="about-dpmmabs"></a>O dpm/mabs

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) je podnikové řešení, které konfiguruje, usnadňuje a spravuje zálohování a obnovu podnikových počítačů a dat. Je to součást sady produktů [System Center.](https://www.microsoft.com/cloud-platform/system-center-pricing)

MABS je serverový produkt, který lze použít k zálohování místních fyzických serverů, virtuálních počítačích a aplikací, které na nich běží.

MABS je založen na System Center DPM a poskytuje podobné funkce s několika rozdíly:

- Ke spuštění MABS není vyžadována žádná licence System Center.
- Pro MABS i DPM poskytuje Azure dlouhodobé úložiště zálohování. Aplikace DPM navíc umožňuje zálohovat data pro dlouhodobé ukládání na pásku. MABS neposkytuje tuto funkci.
- Primární server DPM můžete zálohovat pomocí sekundárního serveru DPM. Sekundární server ochrání databázi primárního serveru a repliky zdrojů dat uložených na primárním serveru. Pokud primární server selže, může sekundární server pokračovat v ochraně úloh, které jsou chráněné primárním serverem, dokud nebude primární server opět k dispozici.  MABS neposkytuje tuto funkci.

MABS lze stáhnout ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=57520). Může se spouštět místně nebo na virtuálním počítači Azure.

DPM a MABS podporují zálohování široké škály aplikací a serverových a klientských operačních systémů. Poskytují více scénářů zálohování:

- Můžete zálohovat na úrovni počítače pomocí systémového stavu nebo holého kovu.
- Můžete zálohovat určité svazky, sdílené složky, složky a soubory.
- Konkrétní aplikace můžete zálohovat pomocí optimalizovaných nastavení podporujících aplikace.

## <a name="dpmmabs-backup"></a>Záloha DPM/MABS

Zálohování pomocí DPM/MABS a Azure Backup funguje následujícím způsobem:

1. Na každém počítači, který bude zálohován, je nainstalován osazený prostředek ochrany DPM/MABS.
1. Počítače a aplikace jsou zálohovány do místního úložiště na DPM/MABS.
1. Agent služby Microsoft Azure Recovery Services (MARS) je nainstalovaný na serveru DPM/MABS.
1. Agent MARS zálohuje disky DPM/MABS do trezoru služby recovery services zálohování v Azure pomocí služby Azure Backup.

Další informace najdete tady:

- [Další informace](backup-architecture.md#architecture-back-up-to-dpmmabs) o architektuře MABS.
- [Zkontrolujte, co je podporováno](backup-support-matrix-mars-agent.md) pro agenta MARS.

## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Agent** | **Umístění**
--- | --- | ---
**Zálohování místních počítačů/úloh** | Agent ochrany DPM/MABS běží na počítačích, které chcete zálohovat.<br/><br/> Agent MARS na serveru DPM/MABS.<br/> Minimální verze agenta služby Microsoft Azure Recovery Services nebo agenta Azure Backup, která je k povolení této funkce vyžadována, je 2.0.8719.0.  | DPM/MABS musí být spuštěna místně.

## <a name="supported-deployments"></a>Podporovaná nasazení

DPM/MABS lze nasadit jako shrnuté v následující tabulce.

**Nasazení** | **Podpora** | **Podrobnosti**
--- | --- | ---
**Nasazeno místně** | Fyzický server<br/><br/>Virtuální počítač s technologií Hyper-V<br/><br/> Virtuální měna v vmware | Pokud je dpm/mabs nainstalován jako virtuální počítač VMware, pouze zálohuje virtuální počítačvvaaaaaa a úlohy, které jsou spuštěny na těchto virtuálních počítačích.
**Nasazený jako virtuální počítač zásobníku Azure** | Pouze MABS | DPM nelze použít k zálohování virtuálních počítačích zásobníku Azure.
**Nasazený jako virtuální počítač Azure** | Chrání virtuální počítače Azure a úlohy, které běží na těchto virtuálních počítačích | DPM/MABS spuštěné v Azure nemůže zálohovat místní počítače.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Podporované operační systémy MABS a DPM

Azure Backup můžete zálohovat DPM/MABS instance, které jsou spuštěny některý z následujících operačních systémů. Operační systémy by měly být spuštěny nejnovější aktualizace Service Pack a aktualizace.

**Scénář** | **DPM/MABS**
--- | ---
**MABS na virtuálním počítači Azure** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Doporučujeme začít s obrázkem z trhu.<br/><br/> Minimálně Standard_A4_v2 se čtyřmi jádry a 8 GB RAM.
**DPM na virtuálním počítači Azure** | System Center 2012 R2 s aktualizací 3 nebo novější.<br/><br/> Operační systém Windows [podle požadavků system center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Doporučujeme začít s obrázkem z trhu.<br/><br/> Minimálně Standard_A4_v2 se čtyřmi jádry a 8 GB RAM.
**MABS v místním prostředí** |  MABS v3 a novější: Windows Server 2016 nebo Windows Server 2019
**DPM v místním prostředí** | Fyzický server/Virtuální v. technologie Hyper-V: System Center 2012 SP1 nebo novější.<br/><br/> VMware VM: System Center 2012 R2 s aktualizací 5 nebo novější.

>[!NOTE]
>Instalace serveru Azure Backup Server není podporována na windows serverovém jádru nebo serveru Microsoft Hyper-V Server.

## <a name="management-support"></a>Podpora pro management

**Problém** | **Podrobnosti**
--- | ---
**Instalace** | Nainstalujte DPM/MABS do jednoúčelového počítače.<br/><br/> Neinstalujte dpm/mabs do řadiče domény, do počítače s instalací role Aplikační server, do počítače se systémem Microsoft Exchange Server nebo System Center Operations Manager nebo do uzlu clusteru.<br/><br/> [Zkontrolujte všechny požadavky na systém DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domény** | DPM/MABS by měly být připojeny k doméně. Nejprve nainstalujte a potom připojte dpm/mabs k doméně. Přesunutí dpm/mabs do nové domény po nasazení není podporováno.
**Úložiště** | Moderní úložiště záloh (MBS) je podporováno od DPM 2016/MABS v2 a novějších. Není k dispozici pro MABS v1.
**Upgrade MABS** | Můžete přímo nainstalovat MABS v3, nebo upgrade na MABS v3 z MABS v2. [Další informace](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Stěhování MABS** | Přesunutí MABS na nový server při zachování úložiště je podporováno, pokud používáte MBS.<br/><br/> Server musí mít stejný název jako originál. Název nelze změnit, pokud chcete zachovat stejný fond úložiště a použít stejnou databázi MABS k ukládání bodů obnovení dat.<br/><br/> Budete potřebovat zálohu databáze MABS, protože ji budete muset obnovit.

## <a name="mabs-support-on-azure-stack"></a>Podpora MABS v Azure Stacku

MABS můžete nasadit na virtuální počítač Azure Stack, takže můžete spravovat zálohování virtuálních počítačích a úloh azure zásobníku z jednoho umístění.

**Komponenta** | **Podrobnosti**
--- | ---
**MABS na virtuálním počítači zásobníku Azure** | Alespoň velikost A2. Doporučujeme začít s image Windows Server 2012 R2 nebo Windows Server 2016 z Azure Marketplace.<br/><br/> Neinstalujte nic jiného na MABS VM.
**Skladování MABS** | Použijte samostatný účet úložiště pro virtuální mm MABS. Agent MARS spuštěný na MABS potřebuje dočasné úložiště pro umístění mezipaměti a pro ukládání dat obnovených z cloudu.
**Fond úložiště MABS** | Velikost fondu úložiště MABS je určena počtem a velikostí disků, které jsou připojeny k virtuálnímu počítače MABS. Každá velikost virtuálního počítače zásobníku Azure má maximální počet disků. Například A2 je čtyři disky.
**Retence MABS** | Neuchovávejte zálohovaná data na místních discích MABS déle než pět dní.
**MABS vertikálně navýšit kapacitu** | Chcete-li vertikálně navýšit kapacitu nasazení, můžete zvětšit velikost virtuálního počítače MABS. Můžete například změnit z řady A na D.<br/><br/> Můžete také zajistit, že pravidelně překládáte data se zálohováním do Azure. V případě potřeby můžete nasadit další servery MABS.
**Rozhraní .NET Framework on MABS** | Virtuální počítač MABS potřebuje rozhraní .NET Framework 3.3 SP1 nebo novější nainstalovaný.
**Doména MABS** | Virtuální modul MABS musí být připojen k doméně. Uživatel domény s oprávněními správce musí nainstalovat MABS na virtuální počítač.
**Zálohování dat virtuálního počítače azure zásobníku** | Můžete zálohovat soubory, složky a aplikace.
**Podporovaná záloha** | Tyto operační systémy jsou podporované pro virtuální počítače, které chcete zálohovat:<br/><br/> Pololetní kanál systému Windows Server (datové centrum, podnik, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Podpora SQL Serveru pro virtuální počítače Azure Stack** | Zálohovat SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Zálohovat a obnovit databázi.
**Podpora SharePointu pro virtuální počítače Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Záloha a obnovení farmy, databáze, front-endu a webového serveru.
**Požadavky na síť pro zálohované virtuální hody** | Všechny virtuální počítače v azure zásobníku úlohy musí patřit do stejné virtuální sítě a patří do stejného předplatného.

## <a name="dpmmabs-networking-support"></a>Podpora sítě DPM/MABS

### <a name="url-access"></a>Přístup k adrese URL

Server/MABS aplikace DPM potřebuje přístup k těmto adresám URL:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Podpora Azure ExpressRoute

Svá data můžete zálohovat přes Azure ExpressRoute pomocí veřejného partnerského vztahu (dostupného pro staré okruhy) a partnerského vztahu Microsoftu. Zálohování přes soukromý partnerský vztah není podporováno.

S veřejným partnerským vztahem: Zajistěte přístup k následujícím doménám/adresám:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

V partnerské společnosti Microsoft vyberte následující služby nebo oblasti a příslušné hodnoty komunity:

* Služba Azure Active Directory (12076:5060)
* Oblast Microsoft Azure (podle umístění trezoru služby Recovery Services)
* Azure Storage (podle umístění trezoru služby Recovery Services)

Další podrobnosti naleznete v [požadavcích na směrování ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Veřejný partnerský vztah je zastaralé pro nové okruhy.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Připojení DPM/MABS k azure backupu

Připojení ke službě Azure Backup je vyžadováno pro zálohování správně fungovat a předplatné Azure by mělo být aktivní. V následující tabulce je uvedeno chování, pokud tyto dvě věci nenastanou.

**MABS do Azure** | **Předplatné** | **Zálohování a obnovení**
--- | --- | ---
Připojeno | Aktivní | Zálohování na disk DPM/MABS.<br/><br/> Zpátky do Azure.<br/><br/> Obnovit z disku.<br/><br/> Obnovení z Azure.
Připojeno | Vypršela platnost/zrušení zřízení | Žádné zálohování na disk nebo Azure.<br/><br/> Pokud vypršela platnost předplatného, můžete obnovit z disku nebo Azure.<br/><br/> Pokud je předplatné vyřazeno z provozu, nelze obnovit z disku nebo Azure. Body obnovení Azure se odstraní.
Žádné připojení po dobu delší než 15 dní | Aktivní | Žádné zálohování na disk nebo Azure.<br/><br/> Můžete obnovit z disku nebo Azure.
Žádné připojení po dobu delší než 15 dní | Vypršela platnost/zrušení zřízení | Žádné zálohování na disk nebo Azure.<br/><br/> Pokud vypršela platnost předplatného, můžete obnovit z disku nebo Azure.<br/><br/> Pokud je předplatné vyřazeno z provozu, nelze obnovit z disku nebo Azure. Body obnovení Azure se odstraní.

## <a name="dpmmabs-storage-support"></a>Podpora úložiště DPM/MABS

Data zálohovaná do aplikace DPM/MABS jsou uložena v místním diskovém úložišti.

**Úložiště** | **Podrobnosti**
--- | ---
**Mbs** | Moderní úložiště záloh (MBS) je podporováno od DPM 2016/MABS v2 a novějších. Není k dispozici pro MABS v1.
**Úložiště MABS na virtuálním počítači Azure** | Data jsou uložena na discích Azure, které jsou připojené k virtuálnímu počítači DPM/MABS a které jsou spravovány v dpm/mabs. Počet disků, které lze použít pro fond úložiště DPM/MABS, je omezen velikostí virtuálního počítače.<br/><br/> A2 VM: 4 disky; A3 VM: 8 disků; A4 VM: 16 disků s maximální velikostí 1 TB pro každý disk. To určuje celkový fond úložiště záloh, který je k dispozici.<br/><br/> Množství dat, která můžete zálohovat, závisí na počtu a velikosti připojených disků.
**Uchovávání dat MABS na virtuálním počítači Azure** | Doporučujeme uchovávat data pro jeden den na disku DPM/MABS Azure a zálohovat z DPM/MABS do trezoru pro delší uchovávání. Můžete tedy chránit větší množství dat převedením na Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Moderní úložiště záloh (MBS)

Z DPM 2016/MABS v2 (spuštěno na Windows Server 2016) a novější, můžete využít využití moderního úložiště záloh (MBS).

- Zálohy MBS jsou uloženy na disku ReFS (Resilient File System).
- MBS používá klonování bloků ReFS pro rychlejší zálohování a efektivnější využití úložného prostoru.
- Přidáte-li svazky do místního fondu úložiště DPM/MABS, nakonfigurujete je písmeny jednotek. Potom můžete nakonfigurovat úložiště úloh na různých svazcích.
- Když vytvoříte skupiny ochrany pro zálohování dat do dpm/MABS, vyberete jednotku, kterou chcete použít. Můžete například ukládat zálohy pro úlohy SQL nebo jiné úlohy s vysokým iOPS na vysoce výkonné jednotce a ukládat úlohy, které jsou zálohovány méně často na jednotce s nižším výkonem.

## <a name="supported-backups-to-mabs"></a>Podporované zálohy do MABS

Informace o různých serverech a úlohách, které můžete chránit pomocí serveru Azure Backup Server, najdete v části [Azure Backup Server Protection Matrix](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Podporované zálohy do aplikace DPM

Informace o různých serverech a úlohách, které můžete chránit pomocí Správce ochrany dat, naleznete v článku [Co může aplikace DPM zálohovat?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- Clusterované úlohy zálohované dpm/mabs by měly být ve stejné doméně jako DPM/MABS nebo v podřízené/důvěryhodné doméně.
- Ověřování NTLM/certifikátů můžete použít k zálohování dat v nedůvěryhodných doménách nebo pracovních skupinách.

## <a name="next-steps"></a>Další kroky

- [Další informace](backup-architecture.md#architecture-back-up-to-dpmmabs) o architektuře MABS.
- [Zkontrolujte,](backup-support-matrix-mars-agent.md) co je podporováno pro agenta MARS.
- [Nastavte](backup-azure-microsoft-azure-backup.md) server MABS.
- [Nastavte dpm](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
