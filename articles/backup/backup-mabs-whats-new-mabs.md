---
title: Novinky v Microsoft Azure Backup Serveru
description: Microsoft Azure Backup Server poskytuje rozšířené možnosti zálohování pro ochranu virtuálních počítačů, souborů a složek, úloh a dalších.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332759"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Novinky ve Microsoft Azure Backup serveru (MABS)

## <a name="whats-new-in-mabs-v3-ur1"></a>Co je nového v MABS V3 UR1

Microsoft Azure Backup Server (MABS) verze 3 UR1 je nejnovější aktualizace a zahrnuje důležité opravy chyb a další funkce a vylepšení. Pokud chcete zobrazit seznam opravených chyb a pokyny k instalaci pro MABS V3 UR1, přečtěte si článek znalostní báze [4534062](https://support.microsoft.com/help/4534062).

>[!NOTE]
>Podpora agenta pro 32 bitové ochrany je zastaralá s MABS V3 UR1. Viz vyřazení [agenta ochrany před 32](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Rychlejší zálohování s vrstveném úložištěm pomocí SSD

MABS v2 představil [moderní úložiště zálohování](backup-mabs-add-storage.md) (MBS), což zlepšuje využití a výkon úložiště. MB používá jako základní systém souborů ReFS a je navržená tak, aby používala hybridní úložiště, jako třeba vrstvené úložiště.

Aby bylo možné dosáhnout škálování a výkonu pomocí aplikace MBS, doporučujeme, abyste v kombinaci s úložištěm HDD DPM používali malé procento (4% celkového úložiště) úložiště Flash (SSD) s MABS V3 UR1 jako vrstvený svazek. MABS V3 UR1 s vrstveném úložištěm přináší 50-70% rychlejší zálohy. Postup pro konfiguraci vrstveného úložiště najdete v článku o aplikaci DPM nakonfigurovaném [pomocí sady MBS s Tiered Storage](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) .

### <a name="support-for-refs-volumes"></a>Podpora pro svazky ReFS

Pomocí MABS V3 UR1 můžete zálohovat svazky a úlohy ReFS nasazené na svazku ReFS. Můžete zálohovat následující úlohy nasazené na svazcích ReFS:

* Operační systém (64 bitů): Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* SharePoint: SharePoint 2019, 2016 s nejnovější aktualizací SP.

>[!NOTE]
> Zálohování virtuálních počítačů Hyper-V uložených na svazku s více instancemi je podporované v MABS v3.

>VÝZNAMNÁ Zjistili jsme několik problémů se zálohováním svazků ReFS s odstraněnými duplicitními daty. Pracujeme na jejich opravě a aktualizujeme tuto část, jakmile bude k dispozici oprava. Až pak odebereme podporu pro zálohování svazků ReFS s odstraněnými duplicitními daty z MABSv3 UR1.

### <a name="azure-vmware-solution-protection-support"></a>Podpora ochrany řešení Azure VMware

S MABS V3 UR1 teď můžete chránit virtuální počítače nasazené v [Řešení Azure VMware](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>Paralelní zálohování VMware

V MABS V3 UR1 budou všechny zálohy virtuálních počítačů VMware v rámci jedné skupiny ochrany paralelní, což vede k 25% rychlejším zálohování virtuálních počítačů.
V dřívějších verzích MABS byly souběžné zálohy provedeny pouze v rámci skupin ochrany. S MABS V3 UR1 běží souběžné úlohy rozdílové replikace VMware. Ve výchozím nastavení je počet spuštěných úloh paralelně nastaven na hodnotu 8. Přečtěte si další informace o [paralelních zálohování VMware](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Vyloučení disku pro zálohování virtuálních počítačů VMware

Pomocí MABS V3 UR1 můžete vyloučit konkrétní disky ze zálohy virtuálního počítače VMware. Přečtěte si další informace o [vyloučení disků ze zálohy virtuálních počítačů VMware](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Podpora dalších vrstev ověřování pro odstranění online zálohování

U MABS V3 UR1 je pro kritické operace přidána další vrstva ověřování. Po **zastavení ochrany s operacemi odstranit data** se zobrazí výzva k zadání bezpečnostního kódu PIN.

### <a name="offline-backup-improvements"></a>Vylepšení zálohování offline

MABS V3 UR1 vylepšuje možnosti zálohování offline pomocí služby Azure import/export. Další informace najdete [tady](./backup-azure-backup-server-import-export.md): aktualizované kroky.

>[!NOTE]
>Aktualizace také přináší verzi Preview pro offline zálohování pomocí Azure Data Box v MABS. [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)Pro další informace se obraťte na kontakt.

### <a name="new-cmdlet-parameter"></a>Nový parametr rutiny

MABS V3 UR1 obsahuje nový parametr **[-CheckReplicaFragmentation]**. Nový parametr vypočítá procento fragmentace pro repliku a je zahrnutý do rutiny **copy-DPMDatasourceReplica** .

### <a name="32-bit-protection-agent-deprecation"></a>zastaralá verze agenta ochrany 32

S MABS V3 UR1 už není podporovaná podpora pro 32 agenta ochrany. Po upgradu serveru MABS V3 na UR1 nebude možné chránit 32 úloh. Všechna stávající 32 agenti ochrany budou v zakázaném stavu a plánované zálohy selžou s chybou **agenta** . Pokud chcete zachovat data záloh pro tyto agenty, můžete ochranu zastavit pomocí možnosti zachovat data. V opačném případě je možné agenta ochrany odebrat.

>[!NOTE]
>Podívejte se na [aktualizovanou matrici ochrany](./backup-mabs-protection-matrix.md) a Naučte se podporované úlohy ochrany pomocí MABS ur 1.

## <a name="whats-new-in-mabs-v3-rtm"></a>Co je nového v MABS V3 RTM

Microsoft Azure Backup Server verze 3 (MABS V3) obsahuje opravy kritické chyby, podporu Windows serveru 2019, podporu SQL 2017 a další funkce a vylepšení. Pokud chcete zobrazit seznam opravených chyb a pokyny k instalaci pro MABS v3, přečtěte si článek znalostní báze [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3).

MABS V3 obsahuje následující funkce:

### <a name="volume-to-volume-migration"></a>Migrace svazků na svazek

S Moderní úložiště zálohování (MBS) ve službě MABS v2 jsme oznámili úložiště s podporou úloh, ve kterém nakonfigurujete určité úlohy, které se mají zálohovat do konkrétního úložiště, a to na základě vlastností úložiště. Po dokončení konfigurace ale může být potřeba přesunout zálohy určitých zdrojů dat do jiného úložiště, aby se vyoptimalizované využití prostředků vyhledalo. MABS V3 vám dává možnost migrovat zálohy a nakonfigurovat jejich uložení na jiný svazek ve [třech krocích](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Ochrana před neočekávanou ztrátou dat

V podnicích je MABS spravován týmem správců. I když existují pokyny pro úložiště, které by se měly použít pro zálohování, nesprávný svazek MABS jako úložiště záloh může vést ke ztrátě důležitých dat. S MABS V3 můžete takové scénáře zabránit tak, že tyto svazky nakonfigurujete jako ty, které nejsou dostupné pro úložiště pomocí [těchto rutin PowerShellu](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Přidělení vlastní velikosti

Moderní úložiště zálohování (MBS) spotřebovává úložiště dynamicky a v případě potřeby. Pokud to chcete udělat, MABS vypočítá velikost zálohovaných dat, když je nakonfigurovaná ochrana. Pokud je však mnoho souborů a složek zálohováno společně, jako v případě souborového serveru, výpočet velikosti může trvat dlouhou dobu. Pomocí MABS V3 můžete nakonfigurovat MABS tak, aby přijímal velikost svazku jako výchozí místo výpočtu velikosti každého souboru, který šetří čas.

### <a name="optimized-cc-for-rct-vms"></a>Optimalizované virtuální počítače CC pro RCT

MABS používá RCT (nativní sledování změn v technologii Hyper-V), což snižuje nutnost časově náročné kontroly konzistence ve scénářích jako chyby virtuálních počítačů. RCT nabízí lepší odolnost než sledování změn poskytované zálohami založenými na snímcích služby Stínová kopie svazku (VSS). MABS V3 optimalizuje využití sítě a úložiště tím, že během jakýchkoli kontrol konzistence přenáší jenom změněná data.

### <a name="support-to-tls-12"></a>Podpora pro TLS 1,2

Protokol TLS 1,2 představuje zabezpečený způsob komunikace, kterou Microsoft navrhuje, s nejlepším šifrováním tříd. MABS teď podporuje komunikaci TLS 1,2 mezi MABS a chráněnými servery, ověřování založené na certifikátech a zálohování cloudu.

### <a name="vmware-vm-protection-support"></a>Podpora ochrany virtuálních počítačů VMware

Pro produkční nasazení se teď podporuje zálohování virtuálních počítačů VMware. MABS v3 nabízí pro ochranu virtuálních počítačů VMware následující:

* Podpora pro vCenter a ESXi 6,5, společně s podporou pro 5,5 a 6,0.
* Automatická ochrana virtuálních počítačů VMware do cloudu. Pokud jsou do chráněné složky přidány nové virtuální počítače VMware, budou automaticky chráněny na disk a Cloud.
* Vylepšení efektivity obnovení pro obnovení alternativního umístění VMware.

### <a name="sql-2017-support"></a>Podpora SQL 2017

MABS V3 se dá nainstalovat pomocí SQL 2017 jako databáze MABS. SQL Server můžete upgradovat z SQL 2016 na SQL 2017 nebo si ho nainstalovat čerstvě. Zatížení SQL 2017 můžete zálohovat taky v clusterovém i neclusterovém prostředí s MABS v3.

### <a name="windows-server-2019-support"></a>Podpora systému Windows Server 2019

MABS V3 se dá nainstalovat na Windows Server 2019. Pokud chcete používat MABS v3 s WS2019, můžete buď upgradovat operační systém na WS2019 před instalací nebo upgradem na MABS v3, nebo můžete upgradovat operační systém po instalaci nebo upgradu V3 na WS2016.

MABS v3 je plná verze, která se dá nainstalovat přímo na Windows Server 2016, Windows Server 2019 nebo se dá upgradovat z MABS v2. Před upgradem na nebo instalaci záložního serveru V3 si přečtěte informace o požadavcích na instalaci.
Další informace o postupu instalace/upgradu pro MABS najdete [zde](./backup-azure-microsoft-azure-backup.md#software-package).

> [!NOTE]
>
> MABS má stejný základ kódu jako Data Protection Manager nástroje System Center. MABS v3 je ekvivalentem Data Protection Manager 1807. MABS V3 UR1 je ekvivalentem Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak připravit server nebo začít chránit úlohu:

* [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
* [Použití záložního serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
* [Zálohování SQL Server pomocí záložního serveru](backup-azure-sql-mabs.md)
* [Použití Moderní úložiště zálohování se záložním serverem](backup-mabs-add-storage.md)
