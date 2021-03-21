---
title: MABS (Azure Backup Server) v3 UR1 Protection Matrix
description: Tento článek obsahuje matrici podpory obsahující všechny úlohy, datové typy a instalace, které Azure Backup Server chrání.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d8f2a38db468ec57cdc0a4f0813fe7ae8e341d52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609766"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Server) v3 UR1 Protection Matrix

V tomto článku jsou uvedené různé servery a úlohy, které můžete chránit pomocí Azure Backup Server. Následující tabulka uvádí, co je možné chránit pomocí Azure Backup Server.

Pro MABS V3 UR1 použijte následující matrici:

* Úlohy – typ zatížení technologie.

* Verze – podporovaná verze MABS pro úlohy.

* Instalace MABS – počítač/umístění, kam chcete nainstalovat MABS.

* Ochrana a obnovení – vypíše podrobné informace o úlohách, jako je například podporovaný kontejner úložiště nebo podporované nasazení.

>[!NOTE]
>Podpora agenta 32 ochrany je zastaralá s MABS V3 UR1. Viz vyřazení [agenta ochrany 32-bit](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Přehled funkcí pro podporu ochrany

Následující části obsahují podrobnosti o matici podpory ochrany pro MABS:

* [Zálohování aplikací](#applications-backup)
* [Zálohování virtuálního počítače](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Zálohování aplikací

| **Úloha**               | **Verze**                                                  | **Instalace Azure Backup Server**                       | **Podporované Azure Backup Server** | **Ochrana a obnovení**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Klientské počítače (64 bitů) | Windows 10                                                  | Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V   <br><br>   Virtuální počítač VMware | V3 UR1                            | Svazek, sdílená složka, složka, soubory, svazky s odstraněnými duplicitami   <br><br>   Chráněné svazky musí obsahovat systém souborů NTFS. Systémy souborů FAT a FAT32 nejsou podporovány.  <br><br>    Svazky musí mít velikost alespoň 1 GB. Azure Backup Server používá k pořízení snímku dat služba Stínová kopie svazku (VSS) a snímek funguje jenom v případě, že je svazek aspoň 1 GB. |
| Servery (64 bitů)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure)  <br><br>    Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V <br><br>     Virtuální počítač VMware  <br><br>    Azure Stack | V3 UR1                            | Svazek, sdílená složka, složka, soubor <br><br>    Svazky s odstraněnými duplicitami (jenom NTFS) <br><br>Při ochraně svazku s odstraněním duplicit WS 2016 NTFS s MABS V3 spuštěným v systému Windows Server 2019 může být zotavení ovlivněno. Máme opravu pro obnovu v neodstraněném způsobu, který bude součástí novějších verzí MABS. Pokud tuto opravu potřebujete na MABS V3 UR1, obraťte se na podporu MABS.<br><br> Při ochraně svazku s odstraněním duplicit WS 2019 NTFS s MABS V3 ve Windows serveru 2016 bude zálohování a obnovení duplicity neodstraněno. To znamená, že zálohy budou spotřebovávat více místa na serveru MABS, než je původní svazek NTFS s odstraněním duplicit.   <br><br>   Stav systému a holý počítač (Nepodporováno, pokud je úloha spuštěná jako virtuální počítač Azure) |
| Servery (64 bitů)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (je třeba nainstalovat [rozhraní Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V  <br><br>      Virtuální počítač VMware  <br><br>   Azure Stack | V3 UR1                            | Svazek, sdílená složka, složka, soubor, stav/úplné obnovení systému        |
| SQL Server                | SQL Server 2019, 2017, 2016 a [podporované SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 [a podporované aktualizace](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) Service Pack | Fyzický server  <br><br>     Virtuální počítač s technologií Hyper-V   <br><br>     Virtuální počítač VMware  <br><br>   Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure)  <br><br>     Azure Stack | V3 UR1                            | Všechny scénáře nasazení: databáze       <br><br>  MABS V3 UR1 podporuje zálohování databází SQL přes svazky ReFS.     <br><br>     MABS nepodporuje SQL Server databáze hostované na Windows Serveru 2012 Scale-Out souborové servery (SOFS). <br><br>   MABS nemůže chránit SQL Server Distributed Availability Group (DAG) nebo Availability Group (AG), kde se název role v clusteru s podporou převzetí služeb při selhání liší od názvu s názvem AG na SQL.       |
| Výměna                   | Exchange 2019, 2016                                         | Fyzický server   <br><br>   Virtuální počítač s technologií Hyper-V  <br><br>      Virtuální počítač VMware  <br><br>   Azure Stack  <br><br>    Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure) | V3 UR1                            | Ochrana (všechny scénáře nasazení): samostatný server Exchange, databáze ve skupině dostupnosti databáze (DAG)  <br><br>    Obnovení (všechny scénáře nasazení): Poštovní schránka, databáze poštovní schránky ve skupině DAG    <br><br>  Záloha Exchange přes ReFS je podporovaná s MABS V3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 s nejnovější službou SPs                       | Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V <br><br>    Virtuální počítač VMware  <br><br>   Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure)   <br><br>   Azure Stack | V3 UR1                            | Ochrana (všechny scénáře nasazení): farma, obsah webového serveru front-endu  <br><br>    Obnovení (všechny scénáře nasazení): farma, databáze, Webová aplikace, soubor nebo položka seznamu, vyhledávání služby SharePoint, webový server front-end  <br><br>    Ochrana farmy služby SharePoint používající funkci SQL Server 2012 AlwaysOn pro databáze obsahu není podporována. |

## <a name="vm-backup"></a>Zálohování virtuálního počítače

| **Úloha**                                                 | **Verze**                                             | **Instalace Azure Backup Server**                      | **Podporované Azure Backup Server** | **Ochrana a obnovení**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V Host – agent MABS Protection na hostitelském serveru Hyper-V, clusteru nebo virtuálním počítači | Windows Server 2019, 2016, 2012 R2, 2012               | Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V <br><br>    Virtuální počítač VMware | V3 UR1                             | Ochrana: počítače Hyper-V, sdílené svazky clusteru (CSV)  <br><br>    Obnovení: virtuální počítač, obnovení souborů a složek na úrovni položek, které je dostupné jenom pro Windows, svazky, virtuální pevné disky |
| Virtuální počítače VMware                                                  | VMware Server 5,5, 6,0 nebo 6,5, 6,7 (licencovaná verze) | Virtuální počítač Hyper-V  <br><br>   Virtuální počítač VMware         | V3 UR1                             | Ochrana: virtuální počítače VMware na svazcích CSV (cluster-Shared Volume), NFS a SAN Storage   <br><br>     Obnovení: virtuální počítač, obnovení souborů a složek na úrovni položek, které je dostupné jenom pro Windows, svazky, virtuální pevné disky <br><br>    VMware vApp se nepodporuje. |

>[!NOTE]
> MABS nepodporuje zálohování virtuálních počítačů s průchozími disky nebo počítači, které používají vzdálený virtuální pevný disk. V těchto scénářích doporučujeme použít zálohování na úrovni hosta pomocí MABS a nainstalovat na virtuální počítač agenta pro zálohování dat.

## <a name="linux"></a>Linux

| **Úloha** | **Verze**                               | **Instalace Azure Backup Server**                      | **Podporované Azure Backup Server** | **Ochrana a obnovení**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux spuštěný jako [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) nebo host [VMware](backup-azure-backup-server-vmware.md) | Fyzický server, místní virtuální počítač Hyper-V, virtuální počítač s Windows ve VMware | V3 UR1                             | Technologie Hyper-V musí být spuštěná v systému Windows Server 2012 R2, Windows Server 2016 nebo Windows Server 2019. Ochrana: celý virtuální počítač   <br><br>   Obnovení: Celý virtuální počítač   <br><br>    Podporovány jsou pouze snímky konzistentních souborů.    <br><br>   Úplný seznam podporovaných distribucí a verzí systému Linux najdete v článku [Linux v distribucích, které jsou schváleny v Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Podpora Azure ExpressRoute

Data můžete zálohovat přes Azure ExpressRoute s veřejným partnerským vztahem (k dispozici pro staré okruhy) a partnerským vztahem Microsoftu. Zálohování přes soukromý partnerský vztah se nepodporuje.

S veřejným partnerským vztahem: Zajistěte přístup k následujícím doménám nebo adresám:

* Adresy URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresy
  * 20.190.128.0/18
  * 40.126.0.0/18

S partnerským vztahem Microsoftu vyberte následující služby nebo oblasti a příslušné hodnoty komunity:

* Azure Active Directory (12076:5060)
* Oblast Microsoft Azure (podle umístění vašeho trezoru Recovery Services)
* Azure Storage (podle umístění vašeho trezoru Recovery Services)

Další informace najdete v tématu [požadavky na směrování ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Veřejné partnerské vztahy se pro nové okruhy zastaraly.

## <a name="cluster-support"></a>Podpora clusteru

Azure Backup Server může chránit data v následujících clusterovaných aplikacích:

* Souborové servery

* SQL Server

* Hyper-V – Pokud chráníte cluster Hyper-V pomocí MABS agenta ochrany s použitím horizontálního rozšíření kapacity, nemůžete pro chráněné úlohy Hyper-V Přidat sekundární ochranu.

* Exchange Server – Azure Backup Server může chránit nesdílené diskové clustery pro podporované verze Exchange serveru (průběžná replikace clusteru) a taky může chránit Exchange Server nakonfigurovaný pro místní průběžnou replikaci.

* SQL Server-Azure Backup Server nepodporuje zálohování SQL Server databází hostovaných na svazcích sdílených clusterů (CSV).

>[!NOTE]
>MABS podporuje pouze ochranu virtuálních počítačů Hyper-V na sdílených svazcích clusteru (CSV). Ochrana jiných úloh hostovaných na sdílených svazcích clusteru není podporována.

Azure Backup Server může chránit úlohy clusteru, které jsou umístěné ve stejné doméně jako server MABS, a v podřízené nebo důvěryhodné doméně. Pokud chcete chránit zdroje dat v nedůvěryhodných doménách nebo pracovních skupinách, použijte ověřování NTLM nebo ověření certifikátu pro jeden server nebo ověřování certifikátu jenom pro cluster.

## <a name="data-protection-issues"></a>Potíže s ochranou dat

* MABS nemůže zálohovat virtuální počítače pomocí sdílených jednotek (které jsou potenciálně připojené k ostatním virtuálním počítačům), protože zapisovač VSS technologie Hyper-V nemůže zálohovat svazky zálohované sdílenými virtuálními pevnými disky.

* Když chráníte sdílenou složku, cesta ke sdílené složce obsahuje logickou cestu ve svazku. Pokud tuto sdílenou složku odstraníte, ochrana selže. Pokud potřebujete chráněnou sdílenou složku přesunout, odeberte ji ze skupiny ochrany a potom ji po přesunutí přidejte do ochrany.  Pokud změníte cestu k chráněnému zdroji dat ve svazku, který používá systém souborů EFS (systém souborů EFS (Encrypting File System)), a nová cesta k souboru je delší než 5120 znaků, ochrana dat se nezdaří.

* Nemůžete změnit doménu chráněného počítače a zachovat ochranu bez přerušení. Navíc nemůžete změnit doménu chráněného počítače a přidružit existující repliky a body obnovení k počítači, když je znovu chráněn. Pokud je třeba změnit doménu chráněného počítače, nejprve z ochrany odeberte zdroje dat z počítače. Pak po vytvoření nové domény chránit zdroj dat v počítači.

* Nemůžete změnit název chráněného počítače a zachovat ochranu bez přerušení. Navíc nemůžete změnit název chráněného počítače a přidružit existující repliky a body obnovení k počítači, když je znovu chráněn. Pokud musíte název chráněného počítače změnit, vyjměte nejdřív zdroje dat v počítači z ochrany. Jakmile má počítač nový název, ochraňte zdroj dat.

* MABS automaticky identifikuje časové pásmo chráněného počítače během instalace agenta ochrany. Pokud se po nakonfigurování ochrany chráněný počítač přesune do jiného časového pásma, nezapomeňte změnit čas v počítači v Ovládacích panelech. Pak aktualizujte časové pásmo v databázi MABS.

* MABS může chránit úlohy ve stejné doméně jako server MABS nebo v podřízených a důvěryhodných doménách. Následující úlohy v pracovních skupinách a nedůvěryhodných doménách můžete taky chránit pomocí protokolu NTLM nebo ověření certifikátu:

  * SQL Server
  * Souborový server
  * Hyper-V

  Tyto úlohy můžou běžet na jednom serveru nebo v konfiguraci clusteru. Pokud chcete chránit úlohu, která není v důvěryhodné doméně, přečtěte si téma [Příprava počítačů v pracovních skupinách a nedůvěryhodných doménách](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm) , ve kterém najdete přesné informace o tom, co je podporováno a jaké je ověřování vyžadováno.

## <a name="unsupported-data-types"></a>Nepodporované datové typy

MABS nepodporuje ochranu následujících datových typů:

* Pevné odkazy

* Body rozboru, včetně odkazů DFS a spojovacích bodů

* Metadata přípojného bodu – skupina ochrany může obsahovat data s přípojnými body. V tomto případě DPM chrání připojený svazek, který je cílem přípojného bodu, ale nechrání metadata přípojného bodu. Při obnovování dat obsahujících přípojné body je potřeba ručně znovu vytvořit hierarchii přípojných bodů.

* Data v připojených svazcích uvnitř připojených svazků

* Koš

* Stránkovací soubory

* Složka System Volume Information. Chcete-li chránit systémové informace počítače, musíte vybrat stav systému počítače jako člena skupiny ochrany.

* Svazky, které nejsou typu NTFS

* Soubory obsahující pevné odkazy nebo symbolické odkazy z Windows Vista.

* Data ve sdílených složkách hostujících hostujících UPD (disky profilů uživatelů)

* Soubory s jakoukoli následující kombinací atributů:

  * Šifrování a změna zpracování

  * Šifrování a SIS (Single Instance Storage)

  * Šifrování a rozlišování velkých a malých písmen

  * Šifrování a zhuštění

  * Rozlišování velkých a malých písmen a SIS

  * Komprese a SIS

## <a name="next-steps"></a>Další kroky

* [Matice podpory pro zálohování pomocí serveru Microsoft Azure Backup nebo aplikace System Center DPM](backup-support-matrix-mabs-dpm.md)
