---
title: Co je nového v Microsoft Azure Backup serveru
description: Microsoft Azure Backup Server poskytuje rozšířené možnosti zálohování pro ochranu virtuálních počítačů, souborů a složek, úloh a další. Zjistěte, jak nainstalovat nebo upgradovat na Azure Backup Server V3.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan; kasinh
ms.openlocfilehash: 8e80d935bac94f3d4b2380799a1aed256828af75
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625874"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Co je nového v Microsoft Azure Backup serveru

Microsoft Azure Backup serveru verze 3 (MABS V3) je nejnovější inovace a obsahuje důležité opravy chyb, podporu 2019 systému Windows Server, SQL 2017 – podpora a další funkce a vylepšení. Chcete-li zobrazit seznam opravy chyb a pokyny pro MABS V3, najdete v článku KB [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3).

Následující funkce jsou zahrnuté v MABS V3:

## <a name="volume-to-volume-migration"></a>Migrace svazek na svazku
S moderním zálohování úložiště (MB) ve verzi V2 MABS, jsme oznámili, že úložištěm, kde konfigurovat některé úlohy zálohovat na konkrétní úložiště na základě úložiště vlastností. Po konfiguraci, však může být potřeba přesunout zálohy z některých zdrojů dat do jiného úložiště pro využití optimalizované prostředků. MABS V3 vám dává možnost migrovat vaše zálohy a konfigurovat je uloží do jiného svazku v [3 kroky](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Zabránit neočekávané ztráty dat
V podnicích spravuje MABS tým správců. I když existují v úložišti, který se má použít pro zálohování svazku nesprávné věnovat MABS jako úložiště pro zálohování může vést ke ztrátě důležitých dat pokyny. Pro MABS V3, je možné zabránit takové scénáře tím, že nakonfigurujete tyto svazky jako ty, které nejsou k dispozici pro úložiště pomocí [tyto rutiny prostředí PowerShell](https://docs.microsoft.com/system-center/dpm/add-storage#volume-exclusion).

## <a name="custom-size-allocation"></a>Vlastní velikost přidělení
Moderní úložiště zálohování (MB) využívá úložiště dynamicky podle potřeby. Uděláte to tak, MABS vypočítá množství dat zálohovaných, když je nakonfigurován pro ochranu. Nicméně pokud mnoho souborů a složek jsou zálohované společně, stejně jako v případě souborového serveru, výpočet velikosti může trvat dlouhou dobu. Pomocí MABS V3 lze nakonfigurovat MABS tak, aby přijímal velikost svazku jako výchozí, místo výpočet velikosti jednotlivých souborů, proto šetří čas.

## <a name="optimized-cc-for-rct-vms"></a>Optimalizované kopie RCT virtuálních počítačů
MABS používá RCT (nativní sledování změn v Hyper-V), která snižuje potřebu časově náročných konzistence zkontroluje ve scénářích jako virtuální počítač, dojde k chybě. RCT nabízí lepší odolnost než sledování změn poskytované zálohy založené na snímek služby VSS. MABS V3 optimalizuje další využití sítě a úložiště a současný přechod jenom změněná data během žádné kontroly konzistence.

## <a name="support-to-tls-12"></a>Podpora protokolu TLS 1.2
Protokol TLS 1.2 je bezpečný způsob komunikace navrhované společností Microsoft s prvotřídní šifrování. MABS teď podporuje komunikaci TLS 1.2 MABS a chráněné servery pro ověřování pomocí certifikátu a pro cloudové zálohy.

## <a name="vmware-vm-protection-support"></a>Podpora ochrany virtuálních počítačů VMware
Zálohování virtuálních počítačů VMware se teď podporuje pro nasazení v produkčním prostředí. MABS V3 nabízejí pro ochranu virtuálního počítače VMware následující:

-   Podpora pro vCenter a ESXi 6.5, společně s podporou pro 5.5 a 6.0.
- Automatická ochrana virtuálních počítačů VMware do cloudu. Pokud do chráněné složky přidají nové virtuální počítače VMware, budou automaticky chráněny na disk a do cloudu.
- Zlepšení efektivity obnovení pro obnovení do alternativního umístění VMware.

## <a name="sql-2017-support"></a>SQL 2017 – podpora
MABS V3 lze nainstalovat pomocí SQL 2017 jako databáze MABS. Můžete při upgradování SQL serveru 2017 SQL z SQL 2016, nebo nainstalovat čerstvě. Můžete také zálohovat SQL 2017 úlohy pro obě v clusterovaných a neclusterovaných prostředí pro MABS V3.

## <a name="windows-server-2019-support"></a>Podpora systému Windows Server 2019
MABS V3 lze nainstalovat na Windows Server 2019. Chcete-li pomocí WS2019 MABS V3, můžete Upgradujte svůj operační systém na WS2019 předtím, než instalace nebo upgrade na MABS V3, nebo můžete upgradovat operační systém po instalaci nebo upgrade V3 na WS2016.

MABS V3 je plné verzi a lze nainstalovat přímo na Windows serveru 2016, Windows Server 2019 nebo je možné upgradovat z verze 2 MABS. Před upgradováním na nebo instalace zálohování serveru V3, přečtěte si požadavky pro instalaci.
Najít další informace o instalaci nebo upgradu kroků pro MABS [tady](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).


> [!NOTE]

> MABS má stejný kód základní jako System Center Data Protection Manager. Je ekvivalentní k Data Protection Manager 1807 MABS v3.

## <a name="next-steps"></a>Další postup

Další informace o přípravě serveru nebo začít chránit úlohy:
- [Příprava úloh zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Pomocí zálohování serveru k zálohování SQL serveru](backup-azure-sql-mabs.md)
- [Moderní úložiště záloh pomocí zálohování serveru](backup-mabs-add-storage.md)
