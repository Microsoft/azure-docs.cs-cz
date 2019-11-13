---
title: Co je nového v Microsoft Azure Backup serveru
description: Microsoft Azure Backup Server poskytuje rozšířené možnosti zálohování pro ochranu virtuálních počítačů, souborů a složek, úloh a dalších. Přečtěte si, jak nainstalovat nebo upgradovat na Azure Backup Server v3.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 403585a198b2569b700ef304e83b45b0a1138db5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012804"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Co je nového v Microsoft Azure Backup serveru

Nejnovější upgrade je Microsoft Azure Backup Server verze 3 (MABS V3) a zahrnuje kritické opravy chyb, podporu Windows serveru 2019, podporu SQL 2017 a další funkce a vylepšení. Pokud chcete zobrazit seznam opravených chyb a pokyny k instalaci pro MABS v3, přečtěte si článek znalostní báze [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

MABS V3 obsahuje následující funkce:

## <a name="volume-to-volume-migration"></a>Migrace svazků na svazek

S Moderní úložiště zálohování (MBS) ve službě MABS v2 jsme oznámili úložiště s podporou úloh, ve kterém nakonfigurujete určité úlohy, které se mají zálohovat do konkrétního úložiště, a to na základě vlastností úložiště. Po dokončení konfigurace ale může být potřeba přesunout zálohy určitých zdrojů dat do jiného úložiště, aby se vyoptimalizované využití prostředků vyhledalo. MABS V3 vám dává možnost migrovat zálohy a nakonfigurovat jejich uložení na jiný svazek ve [třech krocích](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/).

## <a name="prevent-unexpected-data-loss"></a>Ochrana před neočekávanou ztrátou dat

V podnicích je MABS spravován týmem správců. I když existují pokyny pro úložiště, které by se měly použít pro zálohování, nesprávný svazek MABS jako úložiště záloh může vést ke ztrátě důležitých dat. S MABS V3 můžete takové scénáře zabránit tak, že tyto svazky nakonfigurujete jako ty, které nejsou dostupné pro úložiště pomocí [těchto rutin PowerShellu](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Přidělení vlastní velikosti

Moderní úložiště zálohování (MBS) spotřebovává úložiště dynamicky a v případě potřeby. Pokud to chcete udělat, MABS vypočítá velikost zálohovaných dat, když je nakonfigurovaná na ochranu. Pokud je však mnoho souborů a složek zálohováno společně, jako v případě souborového serveru, výpočet velikosti může trvat dlouhou dobu. Pomocí MABS V3 můžete nakonfigurovat MABS tak, aby přijímala velikost svazku jako výchozí místo výpočtu velikosti každého souboru, takže ušetří čas.

## <a name="optimized-cc-for-rct-vms"></a>Optimalizované virtuální počítače CC pro RCT

MABS používá RCT (nativní sledování změn v technologii Hyper-V), což snižuje nutnost časově náročné kontroly konzistence ve scénářích jako chyby virtuálních počítačů. RCT poskytuje lepší odolnost proti sledování změn, které poskytuje zálohování na základě snímků služby Stínová kopie svazku (VSS). MABS V3 optimalizuje využití sítě a úložiště tím, že během jakýchkoli kontrol konzistence přenáší jenom změněná data.

## <a name="support-to-tls-12"></a>Podpora pro TLS 1,2

Protokol TLS 1,2 představuje zabezpečený způsob komunikace, kterou Microsoft navrhuje, s nejlepším šifrováním tříd. MABS teď podporuje komunikaci TLS 1,2 mezi MABS a chráněnými servery, ověřování založené na certifikátech a zálohování cloudu.

## <a name="vmware-vm-protection-support"></a>Podpora ochrany virtuálních počítačů VMware

Pro produkční nasazení se teď podporuje zálohování virtuálních počítačů VMware. MABS v3 nabízí pro ochranu virtuálních počítačů VMware následující:

- Podpora pro vCenter a ESXi 6,5, společně s podporou pro 5,5 a 6,0.
- Automatická ochrana virtuálních počítačů VMware do cloudu. Pokud jsou do chráněné složky přidány nové virtuální počítače VMware, budou automaticky chráněny na disk a Cloud.
- Vylepšení efektivity obnovení pro obnovení alternativního umístění VMware.

## <a name="sql-2017-support"></a>Podpora SQL 2017

MABS V3 se dá nainstalovat pomocí SQL 2017 jako databáze MABS. SQL Server můžete upgradovat z SQL 2016 na SQL 2017 nebo si ho nainstalovat čerstvě. Zatížení SQL 2017 můžete zálohovat taky v clusterovém i neclusterovém prostředí s MABS v3.

## <a name="windows-server-2019-support"></a>Podpora systému Windows Server 2019

MABS V3 se dá nainstalovat na Windows Server 2019. Pokud chcete používat MABS v3 s WS2019, můžete buď upgradovat operační systém na WS2019 před instalací nebo upgradem na MABS v3, nebo můžete upgradovat svůj operační systém po instalaci/upgrade V3 na WS2016.

MABS v3 je plná verze, která se dá nainstalovat přímo na Windows Server 2016, Windows Server 2019 nebo se dá upgradovat z MABS v2. Před upgradem na nebo instalaci záložního serveru V3 si přečtěte informace o požadavcích na instalaci.
Další informace o postupu instalace/upgradu pro MABS najdete [zde](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS má stejný základ kódu jako Data Protection Manager nástroje System Center. MABS v3 je ekvivalentem Data Protection Manager 1807.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak připravit server nebo začít chránit úlohu:

- [Známé problémy v MABS V3](backup-mabs-release-notes-v3.md)
- [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
- [Použití záložního serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Zálohování SQL Server pomocí záložního serveru](backup-azure-sql-mabs.md)
- [Použití Moderní úložiště zálohování se záložním serverem](backup-mabs-add-storage.md)
