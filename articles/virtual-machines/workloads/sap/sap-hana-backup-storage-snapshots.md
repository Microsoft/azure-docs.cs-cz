---
title: Zálohování SAP HANA Azure založené na snímcích úložiště | Dokumentace Microsoftu
description: Existují dvě hlavní zálohování možnosti pro SAP HANA na virtuálních počítačích Azure, tento článek se týká zálohování SAP HANA založené na snímcích úložiště
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 031cb10e476ba068f7e3d7baf3b19f7703caf170
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580031"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Zálohování SAP HANA založené na snímcích úložiště

## <a name="introduction"></a>Úvod

Toto je část z třídílné série článků související na zálohování SAP HANA. [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md) poskytuje přehled a informace pro zahájení práce a [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) zahrnuje možnost zálohování souborů.

Pokud používáte funkci zálohování virtuálních počítačů pro ukázku All-in-one jednou instancí systému, jeden zvažte provedení zálohování virtuálních počítačů namísto správy záloh HANA na úrovni operačního systému. Alternativou je pořizovat snímky objektů blob v Azure k vytvoření kopie jednotlivé virtuální disky, které jsou připojené k virtuálnímu počítači, a zachovat HANA datových souborů. Ale konzistence aplikací při vytváření snímku zálohy nebo disk virtuálního počítače, zatímco systém je v provozu a spouštění je kritický bod. Zobrazit _konzistence dat SAP HANA při pořizování snímků úložiště_ v příslušném článku [Průvodci zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md). SAP HANA má funkci, která podporuje tyto druhy snímků úložiště.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>SAP HANA snímků jako centrální část Aplikačně konzistentní zálohování

V SAP HANA, který podporuje pořizování snímku úložiště není funkce. Existuje omezení na jeden kontejner systémy. Scénáře sing MCS SAP HANA s více než jednoho tenanta nepodporuje tento typ snímku databáze SAP HANA (viz [vytvořit úložiště snímků (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Funguje takto:

- Příprava úložiště snímků pomocí inicializace snímek SAP HANA
- Spustit úložiště snímku (snapshot, třeba Azure blob)
- Potvrďte snímek SAP HANA

![Tento snímek obrazovky ukazuje, že snímku dat SAP HANA lze vytvořit pomocí příkazu SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Tento snímek obrazovky ukazuje, že snímku dat SAP HANA lze vytvořit pomocí příkazu SQL.

![Snímek se pak také objeví v katalogu záloh v SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Snímek pak se zobrazí také v katalogu záloh v SAP HANA Studio.

![Na disku snímku se zobrazí v adresáři dat SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Na disku zobrazí snímku se v adresáři dat SAP HANA.

Jeden má zajistit, že konzistence systému souborů je také zaručeno, že před spuštěním snímků úložiště SAP HANA je v režimu přípravy snímku. Zobrazit _konzistence dat SAP HANA při pořizování snímků úložiště_ v příslušném článku [Průvodci zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md).

Po dokončení snímků úložiště je velmi důležité pro potvrzení snímek SAP HANA. Neexistuje odpovídající příkaz jazyka SQL spustit: zavřít SNÍMKU zálohování dat (naleznete v tématu [dat zavřít SNÍMKU příkaz BACKUP (zálohování a obnovení)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Potvrďte HANA snímku. Z důvodu &quot;kopie při zápisu,&quot; SAP HANA potřebovat další místo na disku v přípravě snímku režimu a není možné spustit nové zálohování, dokud není potvrzené snímek SAP HANA.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Zálohování virtuálních počítačů HANA prostřednictvím služby Azure Backup

Zálohování agenta služby Azure Backup není k dispozici pro virtuální počítače s Linuxem. Kromě Linuxu nemá podobné funkce, jako Windows je s službu VSS.  Chcete-li používat služby Azure backup na úrovni souborů či složek, jednu by zkopírujte záložní soubory SAP HANA do virtuálního počítače s Windows a pak pomocí agenta zálohování. 

Pouze úplné zálohování virtuálního počítače s Linuxem v opačném případě je možné prostřednictvím služby Azure Backup. Zobrazit [přehled funkcí ve službě Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) Další.

Služba Azure Backup nabízí možnost pro zálohování a obnovení virtuálního počítače. Další informace o této službě a její funkci najdete v článku [plánování infrastruktury zálohování virtuálních počítačů v Azure](../../../backup/backup-azure-vms-introduction.md).

Existují dva důležité aspekty podle tohoto článku:

_&quot;Pro virtuální počítače s Linuxem jsou je to možné, pouze konzistentní zálohování, od Linuxu nemá ekvivalentní platformy pro službu VSS.&quot;_

_&quot;Aplikace je nutné implementovat vlastní &quot;opravit&quot; mechanismus pro obnovená data.&quot;_

Proto jeden má Ujistěte se, že SAP HANA v konzistentním stavu na disku při spuštění zálohování. Zobrazit _SAP HANA snímky_ popsané dříve v dokumentu. Ale když SAP HANA zůstane v tomto režimu přípravy snímku je potenciální problém. Zobrazit [vytvořit úložiště snímků (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) Další informace.

Tento článek uvádí:

_&quot;Se důrazně doporučuje potvrďte nebo zrušte úložiště snímku co nejdříve po jeho vytvoření. Zatímco je snímek úložiště připravené nebo vytvořili, snímek příslušných dat je zmrazen. Když zůstane zmrazené snímek příslušných dat, můžete stále provádět změny v databázi. Tyto změny nezpůsobí zmrazené snímku relevantní data, která mají změnit. Místo toho změny jsou zapsány do pozice v datové oblasti, které jsou oddělené od snímků úložiště. Změny se zapisují taky do protokolu. Ale tím déle snímek příslušných dat se ukládají zmrazené, čím můžou růst objemu dat.&quot;_

Azure Backup se postará o konzistence systému souborů prostřednictvím rozšíření virtuálního počítače Azure. Tato rozšíření nejsou k dispozici samostatná a fungují pouze v kombinaci se službou Azure Backup. Je však stále potřeba zadat skripty k vytvoření a odstranění snímku SAP HANA pro zajištění konzistence aplikace.

Azure Backup má čtyři hlavní fáze:

- Spustit Příprava skriptu – skript je potřeba vytvořit snímek SAP HANA
- Pořídit snímek
- Spuštění skriptu po pořízení snímku – skript je potřeba odstranit SAP HANA vytvořen skriptem přípravy
- Přenést data do úložiště

Podrobnosti, ve kterém můžete zkopírovat tyto skripty a podrobnosti o tom, jak funguje Azure Backup přesně najdete v následujících článcích:

- [Plánování infrastruktury zálohování virtuálních počítačů v Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Konzistentní zálohování virtuálních počítačů Azure s Linuxem](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



V tomto okamžiku v okamžiku, nepublikoval Microsoft připraví a po pořízení snímku skriptů pro SAP HANA. Jako zákazník nebo systém integrátor musíte vytvořit tyto skripty a nakonfigurovat postupem podle dokumentace výše uvedenými.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Obnovení ze zálohy konzistentní vzhledem k aplikacím aplikace na virtuálním počítači
Proces obnovení konzistentní vzhledem k aplikacím zálohu aplikace pořízeným službou Azure backup je popsána v článku [obnovení souborů ze záloh virtuálních počítačů Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> V následujícím článku [obnovení souborů ze záloh virtuálních počítačů Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) je seznam výjimek a kroky uvedené při použití disku prokládané sady. Prokládané disky jsou tak pravděpodobnou příčinou regulární konfigurace virtuálního počítače pro SAP HANA. Proto je důležité najdete v článku a otestovat proces obnovení pro případy, jak je uvedeno v následujícím článku. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Licenční klíč HANA a virtuální počítač obnovit prostřednictvím služby Azure Backup

Služba Azure Backup je určené k vytvoření nového virtuálního počítače během obnovení. Neexistuje žádný plán teď provedete &quot;místní&quot; obnovení existujícího virtuálního počítače Azure.

![Tento obrázek ukazuje možnost obnovení služby Azure na webu Azure Portal](media/sap-hana-backup-storage-snapshots/image019.png)

Tento obrázek ukazuje možnost obnovení služby Azure na webu Azure Portal. Jeden můžete zvolit vytvoření virtuálního počítače během obnovení nebo obnovení disků. Po obnovení disků, je stále potřeba vytvořit nový virtuální počítač, dojde k jeho zvýraznění. Pokaždé, když se nový virtuální počítač se vytvoří v Azure jedinečné ID virtuálního počítače změní (viz [přístup k a pomocí jedinečné ID virtuálního počítače Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Tento obrázek ukazuje jedinečné ID virtuálního počítače Azure, před a po obnovení prostřednictvím služby Azure Backup](media/sap-hana-backup-storage-snapshots/image020.png)

Tento obrázek ukazuje jedinečné ID virtuálního počítače Azure, před a po obnovení prostřednictvím služby Azure Backup. SAP hardwarový klíč, který se používá pro SAP licencování, používá tato jedinečné ID virtuálního počítače. Nová licence SAP v důsledku toho musí být nainstalovaná po obnovení virtuálního počítače.

Nová funkce Azure Backup se zobrazí v režimu náhledu při vytváření této příručce zálohování. To umožňuje úrovně obnovení souborů na základě na, která byla provedena pro virtuální počítač záložní snímek virtuálního počítače. Tím se vyhnete nutnosti nasazení nového virtuálního počítače a proto Jedinečný ID virtuálního počítače zůstala stejná a žádný nový licenční klíč SAP HANA je povinný. Další dokumentaci k této funkci bude poskytována po byl plně testovaný.

Azure Backup vám umožní nakonec zálohování Azure jednotlivé virtuální disky, a navíc souborů a adresářů z uvnitř virtuálního počítače. Hlavní výhodou služby Azure Backup je všechny zálohy, ukládání zákazníka z by bylo nutné provést pro jeho správu. Pokud obnovení chování je nezbytné, bude Azure Backup vyberte správné zálohování používat.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Zálohování virtuálních počítačů SAP HANA pomocí snímku disku ruční

Namísto použití služby Azure Backup, jeden jednotlivých řešení zálohování nakonfigurovat tak, že vytvoříte snímky objektů blob virtuálních pevných disků Azure ručně pomocí Powershellu. Zobrazit [snímky objektů blob pomocí prostředí PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) popis kroků.

Poskytuje větší flexibilitu, ale nevyřeší problémy, které bylo popsáno dříve v tomto dokumentu:

- Stále musíte zkontrolovat, že SAP HANA je v konzistentním stavu tak, že vytvoříte snímek SAP HANA
- Disk s operačním systémem nelze přepsat, i v případě zrušení přidělení virtuálního počítače z důvodu chyby s oznámením, že existuje zapůjčení. Funguje to jenom po odstranění virtuálního počítače, což by mohlo dojít k nové jedinečné ID virtuálního počítače a není nutné k instalaci nové licence SAP.

![Je možné obnovit datové disky virtuálního počítače Azure](media/sap-hana-backup-storage-snapshots/image021.png)

To je možné obnovit datové disky virtuálního počítače Azure, jak se vyhnout potížím získání nové jedinečné ID virtuálního počítače a nezneplatnil SAP licence:

- Pro testování byly dva datové disky Azure připojené k virtuálnímu počítači a nad nich byl definován softwarového pole RAID 
- Bylo potvrzeno, že se SAP HANA v konzistentním stavu snímku funkcí SAP HANA
- Ukotvit systému souborů (viz _konzistence dat SAP HANA při pořizování snímků úložiště_ v příslušném článku [Průvodci zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md))
- Snímky objektů BLOB byly odebrány z obou datových disků
- Uvolnit systému souborů
- Potvrzení snímek SAP HANA
- Pokud chcete obnovit datové disky, byl virtuální počítač vypnutý a oba disky odpojit
- Po odpojení disků, se přepsal se snímky objektů blob v původní
- Pak se obnovené virtuální disky připojené znovu k virtuálnímu počítači
- Po spuštění virtuálního počítače, vše na softwaru diskového pole RAID fungovaly správně a byla nastavena zpět na čas snímku objektu blob
- HANA byla nastavena zpět na snímku HANA

Pokud bylo možné vypnout SAP HANA než snímky objektů blob, bude postup méně složitý. V takovém případě jeden může přeskočit snímku HANA a, pokud nic dalšího se děje v systému, také přeskočit zablokování systému souborů. Přidaná složitost spolu vaše úloha, když je potřeba dělat všechno, co je online snímky. Zobrazit _konzistence dat SAP HANA při pořizování snímků úložiště_ v příslušném článku [Průvodci zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Další postup
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md) poskytuje přehled a informace pro zahájení práce.
* [Zálohování SAP HANA založené na úrovni souboru](sap-hana-backup-file-level.md) zahrnuje možnost zálohování souborů.
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
