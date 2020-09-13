---
title: MABS & – matice podpory DPM pro System Center
description: Tento článek shrnuje Azure Backup podporu při použití Microsoft Azure Backup serveru (MABS) nebo System Center DPM k zálohování místních a prostředků virtuálních počítačů Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e3ca83b8a2874304521d84f6901fcb9627ce80db
ms.sourcegitcommit: 655e4b75fa6d7881a0a410679ec25c77de196ea3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89506606"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matice podpory pro zálohování pomocí serveru Microsoft Azure Backup nebo aplikace System Center DPM

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování místních počítačů a úloh a virtuálních počítačů Azure. Tento článek shrnuje podporu nastavení a omezení pro zálohování počítačů pomocí Microsoft Azure Backupho serveru (MABS) nebo nástroje System Center Data Protection Manager (DPM) a Azure Backup.

## <a name="about-dpmmabs"></a>O DPM/MABS

[System Center DPM](/system-center/dpm/dpm-overview) je podnikové řešení, které konfiguruje, usnadňuje a spravuje zálohování a obnovu podnikových počítačů a dat. Je součástí sady produktů [System Center](https://www.microsoft.com/system-center/pricing) .

MABS je serverový produkt, který se dá použít k zálohování místních fyzických serverů, virtuálních počítačů a aplikací, které jsou na nich spuštěné.

MABS je založený na aplikaci System Center DPM a poskytuje podobné funkce s několika rozdíly:

- Ke spuštění MABS není nutná žádná licence System Center.
- Pro MABS i DPM poskytuje Azure dlouhodobé úložiště záloh. Kromě toho aplikace DPM umožňuje zálohovat data pro dlouhodobé ukládání na pásku. MABS tuto funkci neposkytuje.
- [Primární server DPM můžete zálohovat pomocí sekundárního serveru DPM](/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019). Sekundární server ochrání databázi primárního serveru a repliky zdrojů dat uložených na primárním serveru. Pokud primární server selže, může sekundární server pokračovat v ochraně úloh, které jsou chráněné primárním serverem, dokud nebude primární server opět k dispozici.  MABS tuto funkci neposkytuje.

MABS si můžete stáhnout z webu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57520). Dá se spustit místně nebo na virtuálním počítači Azure.

DPM a MABS podporují zálohování široké škály aplikací a serverových a klientských operačních systémů. Poskytují několik scénářů zálohování:

- Na úrovni počítače se můžete zálohovat pomocí zálohování stavu systému nebo úplného zálohování.
- Můžete zálohovat konkrétní svazky, sdílené složky, složky a soubory.
- Konkrétní aplikace můžete zálohovat pomocí optimalizovaného nastavení pro aplikace.

## <a name="dpmmabs-backup"></a>Zálohování DPM/MABS

Zálohování pomocí DPM/MABS a Azure Backup funguje takto:

1. Na každém počítači, který se bude zálohovat, se nainstaluje DPM nebo MABS Protection Agent.
1. Počítače a aplikace se zálohují do místního úložiště v DPM/MABS.
1. Agent Microsoft Azure Recovery Services (MARS) je nainstalovaný na serveru DPM nebo v MABS.
1. Agent MARS zálohuje disky DPM/MABS do trezoru služby Backup Recovery Services v Azure pomocí Azure Backup.

Další informace najdete tady:

- [Další informace](backup-architecture.md#architecture-back-up-to-dpmmabs) o architektuře MABS
- [Přečtěte si, co je podporováno](backup-support-matrix-mars-agent.md) pro agenta Mars.

## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Agent** | **Umístění**
--- | --- | ---
**Zálohování místních počítačů a úloh** | DPM/MABS Protection Agent běží na počítačích, které chcete zálohovat.<br/><br/> Agent MARS na serveru DPM nebo MABS.<br/> Minimální verze agenta Microsoft Azure Recovery Services nebo agenta Azure Backup, který je vyžadován pro povolení této funkce, je 2.0.8719.0.  | DPM/MABS musí běžet místně.

## <a name="supported-deployments"></a>Podporovaná nasazení

DPM/MABS lze nasadit jako shrnuté v následující tabulce.

**Nasazení** | **Podpora** | **Podrobnosti**
--- | --- | ---
**Nasazené místně** | Fyzický server<br/><br/>Virtuální počítač s technologií Hyper-V<br/><br/> Virtuální počítač VMware | Pokud je aplikace DPM/MABS nainstalovaná jako virtuální počítač VMware, zálohuje jenom virtuální počítače VMware a úlohy, které jsou na těchto virtuálních počítačích spuštěné.
**Nasazeno jako Azure Stack virtuální počítač** | Jenom MABS | Aplikaci DPM nelze použít k zálohování Azure Stackch virtuálních počítačů.
**Nasazeno jako virtuální počítač Azure** | Chrání virtuální počítače a úlohy Azure, které běží na těchto virtuálních počítačích. | DPM/MABS běžící v Azure nemůže zálohovat místní počítače.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Podporované MABS a operační systémy DPM

Azure Backup můžou zálohovat instance DPM/MABS, na kterých běží některý z následujících operačních systémů. Operační systémy by měly používat nejnovější aktualizace Service Pack a aktualizace.

**Scénář** | **DPM/MABS**
--- | ---
**MABS na virtuálním počítači Azure** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Doporučujeme začít s obrázkem z webu Marketplace.<br/><br/> Minimální Standard_A4_v2 se čtyřmi jádry a 8 GB paměti RAM.
**DPM na virtuálním počítači Azure** | System Center 2012 R2 s aktualizací Update 3 nebo novější.<br/><br/> Operační systém Windows podle [požadavků v nástroji System Center](/system-center/dpm/prepare-environment-for-dpm#dpm-server).<br/><br/> Doporučujeme začít s obrázkem z webu Marketplace.<br/><br/> Minimální Standard_A4_v2 se čtyřmi jádry a 8 GB paměti RAM.
**Místní MABS** |  MABS v3 a novější: Windows Server 2016 nebo Windows Server 2019
**Místní aplikace DPM** | Fyzický server/virtuální počítač Hyper-V: System Center 2012 SP1 nebo novější.<br/><br/> Virtuální počítač VMware: System Center 2012 R2 s aktualizací Update 5 nebo novější.

>[!NOTE]
>Instalace Azure Backup Server není podporovaná na jádru Windows serveru nebo na serveru Microsoft Hyper-V.

## <a name="management-support"></a>Podpora správy

**Problém** | **Podrobnosti**
--- | ---
**Instalace** | Nainstalujte DPM/MABS na jeden z účelových počítačů.<br/><br/> Neinstalujte DPM/MABS na řadiči domény na počítači s instalací role aplikačního serveru na počítači, na kterém běží Microsoft Exchange Server nebo System Center Operations Manager, nebo na uzlu clusteru.<br/><br/> [Zkontrolujte všechny požadavky na systém aplikace DPM](/system-center/dpm/prepare-environment-for-dpm#dpm-server).
**Doména** | DPM/MABS by měl být připojený k doméně. Nejdřív nainstalujte a pak připojte DPM/MABS k doméně. Přesunutí DPM/MABS do nové domény po nasazení se nepodporuje.
**Storage** | Moderní úložiště záloh (MBS) se podporuje v DPM 2016/MABS v2 a novějších verzích. Není k dispozici pro MABS v1.
**Upgrade MABS** | Můžete přímo nainstalovat MABS V3 nebo upgradovat na MABS V3 z MABS v2. [Přečtěte si další informace](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Přesunutí MABS** | Přesun MABS na nový server a zachování úložiště je podporované, pokud používáte MBS.<br/><br/> Server musí mít stejný název jako původní. Název nemůžete změnit, pokud chcete zachovat stejný fond úložiště a použít stejnou databázi MABS k ukládání bodů obnovení dat.<br/><br/> Budete potřebovat zálohu databáze MABS, protože ji budete muset obnovit.

## <a name="mabs-support-on-azure-stack"></a>Podpora MABS na Azure Stack

MABS můžete nasadit na virtuálním počítači Azure Stack, abyste mohli spravovat zálohování Azure Stack virtuálních počítačů a úloh z jednoho místa.

**Komponenta** | **Podrobnosti**
--- | ---
**MABS na virtuálním počítači Azure Stack** | Aspoň velikost a2. Doporučujeme, abyste začali s imagí Windows Serveru 2012 R2 nebo Windows Server 2016 z Azure Marketplace.<br/><br/> Neinstalujte nic jiného na virtuálním počítači s MABS.
**Úložiště MABS** | Pro virtuální počítač MABS použijte samostatný účet úložiště. Agent MARS běžící na MABS potřebuje dočasné úložiště pro umístění mezipaměti a pro uložení dat obnovených z cloudu.
**MABS fond úložiště** | Velikost fondu úložiště MABS se určuje podle počtu a velikosti disků připojených k virtuálnímu počítači s MABS. Každá velikost virtuálního počítače Azure Stack má maximální počet disků. Například a2 je čtyři disky.
**MABS uchování** | Neuchovávat zálohovaná data na místních discích MABS po dobu delší než pět dní.
**Horizontální navýšení kapacity MABS** | Pro horizontální navýšení kapacity nasazení můžete zvětšit velikost virtuálního počítače MABS. Můžete například změnit z A na řady D.<br/><br/> Také se můžete ujistit, že budete pravidelně překládat data pomocí zálohování do Azure. V případě potřeby můžete nasadit další servery MABS.
**.NET Framework na MABS** | Virtuální počítač MABS musí mít nainstalovanou .NET Framework 3,3 SP1 nebo novější.
**Doména MABS** | Virtuální počítač MABS musí být připojený k doméně. Uživatel domény s oprávněními správce musí na virtuálním počítači nainstalovat MABS.
**Zálohování dat Azure Stack VM** | Můžete zálohovat soubory, složky a aplikace.
**Podporovaná záloha** | Tyto operační systémy jsou podporované pro virtuální počítače, které chcete zálohovat:<br/><br/> Půlroční kanál Windows serveru (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Podpora SQL Server pro virtuální počítače s Azure Stack** | Zálohujte SQL Server 2016 SQL Server 2014 SQL Server 2012 SP1.<br/><br/> Zálohování a obnovení databáze.
**Podpora služby SharePoint pro virtuální počítače s Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Zálohujte a obnovte farmu, databázi, front-end a webový server.
**Požadavky na síť pro zálohované virtuální počítače** | Všechny virtuální počítače v rámci úlohy Azure Stack musí patřit do stejné virtuální sítě a patřit do stejného předplatného.

## <a name="dpmmabs-networking-support"></a>Podpora sítě DPM/MABS

### <a name="url-access"></a>Přístup URL

Server DPM nebo MABS potřebuje přístup k těmto adresám URL:

- `http://www.msftncsi.com/ncsi.txt`
- `*.Microsoft.com`
- `*.WindowsAzure.com`
- `*.microsoftonline.com`
- `*.windows.net`

### <a name="azure-expressroute-support"></a>Podpora Azure ExpressRoute

Data můžete zálohovat přes Azure ExpressRoute s veřejným partnerským vztahem (k dispozici pro staré okruhy) a partnerským vztahem Microsoftu. Zálohování přes soukromý partnerský vztah se nepodporuje.

S veřejným partnerským vztahem: Zajistěte přístup k následujícím doménám nebo adresám:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

S partnerským vztahem Microsoftu vyberte následující služby nebo oblasti a příslušné hodnoty komunity:

- Azure Active Directory (12076:5060)
- Oblast Microsoft Azure (podle umístění vašeho trezoru Recovery Services)
- Azure Storage (podle umístění vašeho trezoru Recovery Services)

Další informace najdete v tématu [požadavky na směrování ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Veřejné partnerské vztahy se pro nové okruhy zastaraly.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Připojení DPM/MABS k Azure Backup

Aby zálohování fungovalo správně, je potřeba připojení ke službě Azure Backup a předplatné Azure by mělo být aktivní. Následující tabulka ukazuje chování, pokud k těmto dvěma akcím nedochází.

**MABS do Azure** | **Předplatné** | **Zálohování a obnovení**
--- | --- | ---
Připojeno | Aktivní | Zálohujte na disk DPM/MABS.<br/><br/> Zálohování do Azure.<br/><br/> Obnovte z disku.<br/><br/> Obnovte z Azure.
Připojeno | Vypršela/bylo zrušeno zřízení | Žádná záloha na disk nebo Azure.<br/><br/> Pokud vypršela platnost předplatného, můžete ho obnovit z disku nebo Azure.<br/><br/> Pokud je odběr vyřazený z provozu, nemůžete ho obnovit z disku nebo Azure. Body obnovení Azure se odstraní.
Žádné připojení více než 15 dní | Aktivní | Žádná záloha na disk nebo Azure.<br/><br/> Můžete obnovit z disku nebo Azure.
Žádné připojení více než 15 dní | Vypršela/bylo zrušeno zřízení | Žádná záloha na disk nebo Azure.<br/><br/> Pokud vypršela platnost předplatného, můžete ho obnovit z disku nebo Azure.<br/><br/> Pokud je odběr vyřazený z provozu, nemůžete ho obnovit z disku nebo Azure. Body obnovení Azure se odstraní.

## <a name="domain-and-domain-trusts-support"></a>Podpora vztahů důvěryhodnosti domén a domén

|Požadavek |Podrobnosti |
|---------|---------|
|Doména    | Server DPM/MABS by měl být v systému Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 Domain.        |
|Důvěryhodnost domény   |  DPM/MABS podporuje ochranu dat napříč doménovými strukturami, pokud mezi různými doménovými strukturami vytvoříte obousměrný vztah důvěryhodnosti na úrovni doménové struktury.   <BR><BR>   DPM/MABS může chránit servery a pracovní stanice napříč doménami v rámci doménové struktury, která má oboustranný vztah důvěryhodnosti s doménou serveru DPM/MABS. Informace o ochraně počítačů v pracovních skupinách nebo nedůvěryhodných doménách najdete v tématu [zálohování a obnovení úloh v pracovních skupinách a nedůvěryhodných doménách.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019)  |

## <a name="dpmmabs-storage-support"></a>Podpora úložiště DPM/MABS

Data zálohovaná do DPM/MABS se ukládají na místní diskové úložiště.

**Storage** | **Podrobnosti**
--- | ---
**MBS** | Moderní úložiště záloh (MBS) se podporuje v DPM 2016/MABS v2 a novějších verzích. Není k dispozici pro MABS v1.
**MABS Storage na virtuálním počítači Azure** | Data jsou uložená na discích Azure, které jsou připojené k virtuálnímu počítači DPM/MABS a jsou spravované v DPM/MABS. Počet disků, které lze použít pro fond úložiště DPM nebo MABS, je omezen velikostí virtuálního počítače.<br/><br/> Virtuální počítač a2:4 disky; Virtuální počítač a3:8 disků; Virtuální počítač A4:16 disků s maximální velikostí 1 TB pro každý disk. Tím se určuje celkový fond úložiště záloh, který je k dispozici.<br/><br/> Množství dat, které můžete zálohovat, závisí na počtu a velikosti připojených disků.
**MABS uchovávání dat na virtuálním počítači Azure** | Doporučujeme uchovávat data na jeden den na disku Azure DPM nebo MABS a zálohovat z aplikace DPM/MABS do trezoru pro delší dobu uchování. Tímto způsobem můžete chránit větší množství dat přesměrováním do Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Moderní úložiště záloh (MB)

Z aplikace DPM 2016/MABS v2 (běžící na Windows serveru 2016) a novějším můžete využít výhod moderního úložiště záloh (MB).

- Zálohy aplikace MBS se ukládají na disk odolného systému souborů (ReFS).
- MB používá klonování bloků ReFS pro rychlejší zálohování a efektivnější využití úložného prostoru.
- Když přidáváte svazky do místního fondu úložiště DPM nebo MABS, nakonfigurujete je pomocí písmen jednotek. Pak můžete nakonfigurovat úložiště úloh na různých svazcích.
- Když vytváříte skupiny ochrany pro zálohování dat do DPM/MABS, vyberete jednotku, kterou chcete použít. Můžete například ukládat zálohy pro SQL nebo jiné úlohy s vysokým počtem vstupně-výstupních operací na vysoce výkonné jednotce a ukládat úlohy, které se zálohují méně často na nižší jednotce výkonu.

## <a name="supported-backups-to-mabs"></a>Podporované zálohy na MABS

Informace o různých serverech a úlohách, které můžete chránit pomocí Azure Backup Server, najdete v tématu [Azure Backup Server Protection Matrix](./backup-mabs-protection-matrix.md#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Podporované zálohy do DPM

Informace o různých serverech a úlohách, které můžete chránit pomocí Data Protection Manager, najdete v článku [co může aplikace DPM zálohovat?](/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- Clusterované úlohy zálohované aplikací DPM/MABS by měly být ve stejné doméně jako DPM/MABS nebo v podřízené/důvěryhodné doméně.
- Ověřování pomocí protokolu NTLM/certifikátu můžete použít k zálohování dat v nedůvěryhodných doménách nebo pracovních skupinách.

## <a name="next-steps"></a>Další kroky

- [Další informace](backup-architecture.md#architecture-back-up-to-dpmmabs) o architektuře MABS
- [Přečtěte si](backup-support-matrix-mars-agent.md) , co je podporováno pro agenta Mars.
- [Nastavte](backup-azure-microsoft-azure-backup.md) Server MABS.
- [Nastavte DPM](/system-center/dpm/install-dpm).
