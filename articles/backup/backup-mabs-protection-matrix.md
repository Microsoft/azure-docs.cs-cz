---
title: MABS (Azure Backup Server) v3 UR1 Protection Matrix
description: Tento článek obsahuje matrici podpory obsahující všechny úlohy, datové typy a instalace, které Azure Backup Server chrání.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: 70d8b8cd26a40b0c7ec8b538bcb702d281f829e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88826833"
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
| Servery (64 bitů)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure)  <br><br>    Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V <br><br>     Virtuální počítač VMware  <br><br>    Azure Stack | V3 UR1                            | Svazek, sdílená složka, složka, soubor <br><br>    Svazky s odstraněnými duplicitami (jenom NTFS)  <br><br>   Stav systému a holý počítač (Nepodporováno, pokud je úloha spuštěná jako virtuální počítač Azure) |
| Servery (64 bitů)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (je třeba nainstalovat [rozhraní Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)) | Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V  <br><br>      Virtuální počítač VMware  <br><br>   Azure Stack | V3 UR1                            | Svazek, sdílená složka, složka, soubor, stav/úplné obnovení systému        |
| SQL Server                | SQL Server 2019, 2017, 2016 a [podporované SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 [a podporované aktualizace](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) Service Pack | Fyzický server  <br><br>     Virtuální počítač s technologií Hyper-V   <br><br>     Virtuální počítač VMware  <br><br>   Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure)  <br><br>     Azure Stack | V3 UR1                            | Všechny scénáře nasazení: databáze       <br><br>  MABS V3 UR1 podporuje zálohování databází SQL přes svazky ReFS.                  |
| Výměna                   | Exchange 2019, 2016                                         | Fyzický server   <br><br>   Virtuální počítač s technologií Hyper-V  <br><br>      Virtuální počítač VMware  <br><br>   Azure Stack  <br><br>    Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure) | V3 UR1                            | Ochrana (všechny scénáře nasazení): samostatný server Exchange, databáze ve skupině dostupnosti databáze (DAG)  <br><br>    Obnovení (všechny scénáře nasazení): Poštovní schránka, databáze poštovní schránky ve skupině DAG    <br><br>  Záloha Exchange přes ReFS je podporovaná s MABS V3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 s nejnovější službou SPs                       | Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V <br><br>    Virtuální počítač VMware  <br><br>   Virtuální počítač Azure (když úloha běží jako virtuální počítač Azure)   <br><br>   Azure Stack | V3 UR1                            | Ochrana (všechny scénáře nasazení): farma, obsah webového serveru front-endu  <br><br>    Obnovení (všechny scénáře nasazení): farma, databáze, Webová aplikace, soubor nebo položka seznamu, vyhledávání služby SharePoint, webový server front-end  <br><br>    Ochrana farmy služby SharePoint používající funkci SQL Server 2012 AlwaysOn pro databáze obsahu není podporována. |

## <a name="vm-backup"></a>Zálohování virtuálního počítače

| **Úloha**                                                 | **Verze**                                             | **Instalace Azure Backup Server**                      | **Podporované Azure Backup Server** | **Ochrana a obnovení**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V Host – agent MABS Protection na hostitelském serveru Hyper-V, clusteru nebo virtuálním počítači | Windows Server 2019, 2016, 2012 R2, 2012               | Fyzický server  <br><br>    Virtuální počítač s technologií Hyper-V <br><br>    Virtuální počítač VMware | V3 UR1                             | Ochrana: počítače Hyper-V, sdílené svazky clusteru (CSV)  <br><br>    Obnovení: virtuální počítač, obnovení souborů a složky na úrovni položky, které jsou k dispozici pouze pro Windows, svazky, virtuální pevné disky |
| Virtuální počítače VMware                                                  | VMware Server 5,5, 6,0 nebo 6,5, 6,7 (licencovaná verze) | Virtuální počítač Hyper-V  <br><br>   Virtuální počítač VMware         | V3 UR1                             | Ochrana: virtuální počítače VMware na svazcích CSV (cluster-Shared Volume), NFS a SAN Storage   <br><br>     Obnovení: virtuální počítač, obnovení souborů a složky na úrovni položky, které jsou k dispozici pouze pro Windows, svazky, virtuální pevné disky <br><br>    VMware vApp se nepodporuje. |

## <a name="linux"></a>Linux

| **Úloha** | **Verze**                               | **Instalace Azure Backup Server**                      | **Podporované Azure Backup Server** | **Ochrana a obnovení**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux spuštěný jako Hyper-V nebo host VMware | Fyzický server, místní virtuální počítač Hyper-V, virtuální počítač s Windows ve VMWare | V3 UR1                             | Technologie Hyper-V musí být spuštěná v systému Windows Server 2012 R2, Windows Server 2016 nebo Windows Server 2019. Ochrana: celý virtuální počítač   <br><br>   Obnovení: Celý virtuální počítač   <br><br>    Podporovány jsou pouze snímky konzistentních souborů.    <br><br>   Úplný seznam podporovaných distribucí a verzí systému Linux najdete v článku [Linux v distribucích, které jsou schváleny v Azure](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Podpora Azure ExpressRoute

Data můžete zálohovat přes Azure ExpressRoute s veřejným partnerským vztahem (k dispozici pro staré okruhy) a partnerským vztahem Microsoftu. Zálohování přes soukromý partnerský vztah se nepodporuje.

S veřejným partnerským vztahem: Zajistěte přístup k následujícím doménám nebo adresám:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

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

Azure Backup Server může chránit úlohy clusteru, které jsou umístěné ve stejné doméně jako server MABS, a v podřízené nebo důvěryhodné doméně. Pokud chcete chránit zdroje dat v nedůvěryhodných doménách nebo pracovních skupinách, použijte ověřování NTLM nebo ověření certifikátu pro jeden server nebo ověřování certifikátu jenom pro cluster.
