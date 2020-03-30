---
title: Novinky v Microsoft Azure Backup Serveru
description: Microsoft Azure Backup Server vám poskytuje rozšířené možnosti zálohování pro ochranu virtuálních počítačů, souborů a složek, úloh a dalších. Přečtěte si, jak nainstalovat nebo upgradovat na Azure Backup Server V3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 61430ce06d3e441fcfe0443eaaf5de3755b04624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582802"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Novinky v Microsoft Azure Backup Serveru

Microsoft Azure Backup Server verze 3 (MABS V3) je nejnovější upgrade a zahrnuje důležité opravy chyb, podporu Windows Serveru 2019, podporu SQL 2017 a další funkce a vylepšení. Seznam opravených chyb a pokyny k instalaci pro MABS V3 naleznete v článku [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)kb.

V MABS V3 jsou zahrnuty následující funkce:

## <a name="volume-to-volume-migration"></a>Migrace svazku na svazek

S moderním úložištěm zálohování (MBS) v MABS V2 jsme oznámili úložiště s ohledem na pracovní zátěž, kde nakonfigurujete určité úlohy, které mají být zálohovány do konkrétního úložiště na základě vlastností úložiště. Po konfiguraci však může být potřeba přesunout zálohy určitých zdrojů dat do jiného úložiště pro optimalizované využití prostředků. MABS V3 umožňuje migrovat zálohy a nakonfigurovat je tak, aby byly uloženy na jiný svazek ve [třech krocích](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Zabránit neočekávané ztrátě dat

V podnicích je MABS řízen týmem správců. I když existují pokyny pro úložiště, které by měly být použity pro zálohování, nesprávný svazek daný MABS jako úložiště záloh může vést ke ztrátě důležitých dat. S MABS V3 můžete zabránit takovým scénářům konfigurací těchto svazků, jako jsou ty, které nejsou k dispozici pro ukládání pomocí [těchto rutin prostředí PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Vlastní přidělení velikosti

Moderní úložiště záloh (MBS) spotřebovává úložiště tence, podle potřeby. Chcete-li tak učinit, MABS vypočítá velikost data zálohována při konfiguraci pro ochranu. Pokud je však mnoho souborů a složek zálohováno společně, jako v případě souborového serveru, může výpočet velikosti trvat dlouho. S MABS V3 můžete nakonfigurovat MABS tak, aby přijaly velikost svazku jako výchozí, namísto výpočtu velikosti každého souboru, a tím šetří čas.

## <a name="optimized-cc-for-rct-vms"></a>Optimalizované CC pro virtuální počítače RCT

MABS používá RCT (nativní sledování změn v Hyper-V), což snižuje potřebu časově náročné kontroly konzistence ve scénářích jako selhání virtuálního zařízení. RCT nabízí lepší odolnost než sledování změn poskytované zálohami založenými na snímcích služby Stínová kopie svazku (VSS). MABS V3 dále optimalizuje spotřebu sítě a úložiště přenosem pouze změněných dat během všech kontrol konzistence.

## <a name="support-to-tls-12"></a>Podpora tls 1.2

TLS 1.2 je bezpečný způsob komunikace navržený společností Microsoft s nejlepším šifrováním ve své třídě. MABS nyní podporuje komunikaci TLS 1.2 mezi MABS a chráněnými servery, pro ověřování na základě certifikátů a pro zálohování v cloudu.

## <a name="vmware-vm-protection-support"></a>Podpora ochrany virtuálních vod v systému VMware

Záloha virtuálních věcí VMware je teď podporovaná pro produkční nasazení. MABS V3 nabízí následující pro ochranu virtuálních vod VMware:

- Podpora pro vCenter a ESXi 6.5, spolu s podporou pro 5.5 a 6.0.
- Automatická ochrana virtuálních měn VMware do cloudu. Pokud jsou nové virtuální počítače VMware přidány do chráněné složky, jsou automaticky chráněny na disk a cloud.
- Vylepšení efektivity obnovení pro obnovení alternativního umístění v systému VMware.

## <a name="sql-2017-support"></a>Podpora SQL 2017

MABS V3 lze nainstalovat s SQL 2017 jako databáze MABS. Sql server můžete upgradovat z SQL 2016 na SQL 2017 nebo ho nainstalovat čerstvě. Můžete také zálohovat úlohy SQL 2017 v clusterovaném i neclusterovaném prostředí pomocí MABS V3.

## <a name="windows-server-2019-support"></a>Podpora pro Windows Server 2019

MABS V3 lze nainstalovat do systému Windows Server 2019. Chcete-li použít MABS V3 s WS2019, můžete buď upgradovat operační systém na WS2019 před instalací nebo upgradem na MABS V3, nebo můžete upgradovat systém OS post instalace / upgrade V3 na WS2016.

MABS V3 je úplná verze a lze ji nainstalovat přímo do systému Windows Server 2016, Windows Server 2019 nebo lze upgradovat z MABS V2. Před upgradem na nebo instalací zálohovacího serveru V3 si přečtěte informace o požadavcích na instalaci.
Další informace o krocích instalace/upgradu pro MABS [naleznete zde](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> MABS má stejný kód jako Správce ochrany dat system center. MABS v3 je ekvivalentní k Data Protection Manager 1807.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak připravit server nebo začít chránit pracovní vytížení:

- [Známé problémy v MABS V3](backup-mabs-release-notes-v3.md)
- [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
- [Zálohování serveru VMware pomocí zálohovacího serveru](backup-azure-backup-server-vmware.md)
- [Zálohování serveru SQL Server pomocí zálohovacího serveru](backup-azure-sql-mabs.md)
- [Použití moderního úložiště záloh se záložním serverem](backup-mabs-add-storage.md)
