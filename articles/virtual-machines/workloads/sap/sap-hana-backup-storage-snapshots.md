---
title: SAP HANA zálohování Azure na základě snímků úložiště | Microsoft Docs
description: Existují dvě hlavní možnosti zálohování SAP HANA na virtuálních počítačích Azure. Tento článek se zabývá SAP HANA zálohování na základě snímků úložiště.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 8bcfdefa2ea9de12ca6029839a41c91111a5c61c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078596"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Zálohování SAP HANA založené na snímcích úložiště

## <a name="introduction"></a>Úvod

Tato část je součástí série článků souvisejících v rámci zálohování SAP HANA. [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md) poskytuje přehled a informace o tom, jak začít, a [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) pokrývá možnost zálohování na základě souborů.

Při použití funkce zálohování virtuálního počítače pro ukázkový systém s jednou instancí je potřeba, aby se místo správy záloh HANA na úrovni operačního systému mělo zvážit zálohování virtuálního počítače. Alternativou je pořizování snímků Azure Blob pro vytváření kopií jednotlivých virtuálních disků, které jsou připojené k virtuálnímu počítači, a zachování datových souborů HANA. Kritická je ale konzistence aplikací při vytváření zálohy virtuálního počítače nebo snímku disku v době, kdy je systém v provozu. V tématu _SAP HANA konzistence dat při pořizování snímků úložiště_ v související [příručce zálohování článků pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md). SAP HANA má funkci, která podporuje tyto druhy snímků úložiště.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>SAP HANA snímky jako centrální součást záloh konzistentních vzhledem k aplikacím

SAP HANA, který podporuje pořízení snímku úložiště, je funkce. Existují omezení pro systémy s jedním kontejnerem. Scénáře jednotlivě SAP HANA MCS s více než jedním klientem nepodporují tento druh snímku databáze SAP HANA (viz [vytvoření snímku úložiště (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Funguje takto:

- Příprava snímku úložiště zahájením SAP HANA snímku
- Spusťte snímek úložiště (například snímek Azure BLOB).
- Potvrzení SAP HANA snímku

![Tento snímek obrazovky ukazuje, že SAP HANA snímek dat lze vytvořit pomocí příkazu SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Tento snímek obrazovky ukazuje, že SAP HANA snímek dat lze vytvořit pomocí příkazu jazyka SQL.

![Snímek se pak zobrazí také v katalogu záloh v SAP HANA studiu.](media/sap-hana-backup-storage-snapshots/image012.png)

Snímek se pak zobrazí také v katalogu záloh v SAP HANA studiu.

![Na disku se snímek zobrazí v adresáři SAP HANA dat.](media/sap-hana-backup-storage-snapshots/image013.png)

Na disku se snímek zobrazí v adresáři SAP HANA dat.

Jedna z nich musí zajistit, aby před spuštěním snímku úložiště byla zaručena konzistence systému souborů, zatímco SAP HANA je v režimu přípravy snímků. V tématu _SAP HANA konzistence dat při pořizování snímků úložiště_ v související [příručce zálohování článků pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md).

Po dokončení snímku úložiště je důležité potvrdit SAP HANA snímek. Existuje odpovídající příkaz SQL ke spuštění: Zavřít snímek ZÁLOHOVANých dat (viz [příkaz Zavřít snímek zálohovaných dat (zálohování a obnovení)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Potvrďte snímek HANA. Z důvodu&quot; kopírování na zápis SAP HANA může vyžadovat další místo na disku v režimu přípravy snímku a není možné spustit nové zálohy, dokud se nepotvrdí SAP HANA snímku. &quot;

## <a name="hana-vm-backup-via-azure-backup-service"></a>Zálohování virtuálního počítače HANA prostřednictvím služby Azure Backup

Agent zálohování služby Azure Backup není k dispozici pro virtuální počítače se systémem Linux. Kromě toho Linux nemá podobné funkce jako systém Windows s VSS.  Aby bylo možné používat službu Azure Backup na úrovni souboru nebo adresáře, může jeden zkopírovat SAP HANA záložní soubory na virtuální počítač s Windows a potom použít agenta zálohování. 

V opačném případě je možné prostřednictvím služby Azure Backup použít pouze úplné zálohování virtuálních počítačů se systémem Linux. Další informace najdete v tématu [Přehled funkcí v Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) .

Služba Azure Backup nabízí možnost zálohování a obnovení virtuálního počítače. Další informace o této službě a o tom, jak to funguje, najdete v článku [plánování infrastruktury zálohování virtuálních počítačů v Azure](../../../backup/backup-azure-vms-introduction.md).

Existují dva důležité informace, které jsou v souladu s tímto článkem:

_&quot;Pro virtuální počítače se systémem Linux jsou možné pouze zálohy konzistentní se soubory, protože systém Linux nemá odpovídající platformu pro službu Stínová kopie svazku (VSS).&quot;_

_&quot;Aplikace potřebují pro obnovená data &quot;implementovat vlastní&quot; mechanizmus pro opravy.&quot;_

Proto jedna z nich musí mít jistotu, že SAP HANA je v konzistentním stavu na disku při spuštění zálohování. Viz _snímky SAP HANA_ popsané dříve v dokumentu. Existuje však potenciální problém, když SAP HANA zůstane v tomto režimu přípravy snímku. Další informace najdete v tématu [vytvoření snímku úložiště (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) .

Tento článek uvádí:

_&quot;Důrazně doporučujeme, abyste snímek úložiště po jeho vytvoření potvrdili nebo zrušili. Během přípravy nebo vytváření snímku úložiště jsou data relevantní pro snímek zmrazena. I když zůstanou data relevantní pro snímek zablokovaná, můžou se v databázi provádět změny. Tyto změny nezpůsobí změnu zmrazených dat týkajících se snímků. Místo toho se změny zapisují do pozic v oblasti dat, které jsou oddělené od snímku úložiště. Do protokolu se zapisují také změny. Data relevantní pro snímek se ale uchovávají zmrazená, čím víc objem dat může růst.&quot;_

Azure Backup zabere v potaz konzistenci systému souborů prostřednictvím rozšíření virtuálních počítačů Azure. Tato rozšíření nejsou samostatně dostupná a fungují pouze v kombinaci s Azure Backup službou. Nicméně je stále potřeba poskytnout skripty pro vytvoření a odstranění snímku SAP HANA pro zajištění konzistence aplikace.

Azure Backup má čtyři hlavní fáze:

- Spustit přípravu skriptu-skript musí vytvořit snímek SAP HANA
- Pořídit snímek
- Spustit skript po snímku – skript musí odstranit SAP HANA vytvořené skriptem Prepare.
- Přenos dat do trezoru

Podrobnosti o tom, kam zkopírovat tyto skripty a podrobnosti o tom, jak Azure Backup funguje přesně, najdete v následujících článcích:

- [Plánování infrastruktury zálohování virtuálních počítačů v Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Zálohování virtuálních počítačů Azure Linux konzistentní vzhledem k aplikacím](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



V tomto okamžiku společnost Microsoft nezveřejnila přípravu skriptů a skripty po snímku pro SAP HANA. Pokud byste chtěli, aby se tyto skripty vytvořily jako zákazník nebo systémový integrátor, je nutné nakonfigurovat postup na základě dokumentace, na kterou se odkazuje výše.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Obnovení z zálohování konzistentního vzhledem k aplikacím na virtuálním počítači
Proces obnovení zálohování konzistentního vzhledem k aplikacím pořízeným službou Azure Backup je popsán v článku [obnovení souborů ze zálohy virtuálního počítače Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> V článku [obnovení souborů ze zálohy virtuálního počítače Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) je seznam výjimek a kroků, které jsou uvedené při použití sad Stripe disks. Prokládané disky jsou nejspíš standardní konfigurací virtuálních počítačů pro SAP HANA. Proto je nezbytné si přečíst článek a otestovat proces obnovení pro tyto případy, jak je uvedeno v článku. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Licenční klíč HANA a obnovení virtuálního počítače prostřednictvím služby Azure Backup

Služba Azure Backup je navržená tak, aby při obnovení vytvořila nový virtuální počítač. Nyní není k dispozici žádný plán pro &quot;místní&quot; obnovení stávajícího virtuálního počítače Azure.

![Tento obrázek ukazuje možnost obnovení služby Azure v Azure Portal](media/sap-hana-backup-storage-snapshots/image019.png)

Tento obrázek ukazuje možnost obnovení služby Azure v Azure Portal. Jednu z nich si můžete vybrat mezi vytvořením virtuálního počítače během obnovování nebo obnovování disků. Po obnovení disků je stále potřeba vytvořit nový virtuální počítač nad ním. Pokaždé, když se v Azure vytvoří nový virtuální počítač, změní se jedinečné ID virtuálního počítače (viz [přístup k virtuálnímu počítači Azure a jeho používání jedinečné ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Tento obrázek ukazuje jedinečné ID virtuálního počítače Azure před a po obnovení prostřednictvím služby Azure Backup.](media/sap-hana-backup-storage-snapshots/image020.png)

Na tomto obrázku vidíte jedinečné ID virtuálního počítače Azure před a po obnovení prostřednictvím služby Azure Backup. Hardwarový klíč SAP, který se používá pro licencování SAP, používá toto jedinečné ID virtuálního počítače. V důsledku toho je potřeba po obnovení virtuálního počítače nainstalovat novou licenci SAP.

V režimu náhledu byla během vytváření tohoto průvodce zálohováním nabídnuta nová funkce Azure Backup. Umožňuje obnovení na úrovni souborů na základě snímku virtuálního počítače, který byl proveden pro zálohování virtuálního počítače. Tím se vyhnete nutnosti nasazovat nový virtuální počítač, takže jedinečné ID virtuálního počítače zůstane stejné a nemusíte mít žádný nový SAP HANA licenční klíč. Další dokumentace k této funkci bude poskytována po úplném otestování.

Azure Backup nakonec povolí zálohování jednotlivých virtuálních disků Azure, včetně souborů a adresářů z virtuálního počítače. Důležitou výhodou Azure Backup je Správa všech záloh, které se od zákazníka ukládají. Pokud bude obnovení nezbytné, Azure Backup vybere správnou zálohu, která se má použít.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA zálohování virtuálních počítačů pomocí ručního snímku disku

Místo používání služby Azure Backup by se jedna mohla nakonfigurovat jednotlivé řešení zálohování tím, že se snímky objektů BLOB v Azure ručně vytvoří pomocí PowerShellu. Popis kroků najdete v tématu [použití snímků objektů BLOB v prostředí PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) .

Nabízí větší flexibilitu, ale neřeší problémy popsané dříve v tomto dokumentu:

- Stále je nutné zajistit, aby se SAP HANA v konzistentním stavu vytvořením snímku SAP HANA
- Disk s operačním systémem nejde přepsat, i když se virtuální počítač oddělí kvůli chybě, která uvádí, že zapůjčení existuje. Funguje jenom po odstranění virtuálního počítače, což by vedlo k novému jedinečnému ID virtuálního počítače a potřeba nainstalovat novou licenci SAP.

![Je možné obnovit jenom datové disky virtuálního počítače Azure.](media/sap-hana-backup-storage-snapshots/image021.png)

Je možné obnovit jenom datové disky virtuálního počítače Azure, aby nedošlo k potížím s získáním nového jedinečného ID virtuálního počítače, a proto jste zrušili platnost licence SAP:

- Pro tento test se k virtuálnímu počítači připojily dva datové disky Azure a na nich byl definován softwarový RAID. 
- Bylo potvrzeno, že SAP HANA bylo v konzistentním stavu pomocí funkce snímku SAP HANA
- Zablokování systému souborů (viz _SAP HANA konzistence dat při pořizování snímků úložiště_ v související [příručce zálohování článků pro SAP HANA na Azure Virtual Machines](sap-hana-backup-guide.md))
- Snímky objektů BLOB byly získány z obou datových disků.
- Odblokování systému souborů
- Potvrzení snímku SAP HANA
- Chcete-li obnovit datové disky, byl virtuální počítač vypnutý a oba disky odpojeny.
- Po odpojení disků byly přepsány předchozími snímky objektů BLOB
- Obnovené virtuální disky se pak znovu připojily k virtuálnímu počítači.
- Po spuštění virtuálního počítače se všechno na základě přípravku softwaru RAID vypracovalo správně a nastavilo se zpátky na čas snímku objektu BLOB.
- HANA se nastavil zpátky na snímek HANA.

Pokud bylo možné ukončit SAP HANA před snímky objektu blob, procedura bude méně složitá. V takovém případě může jeden snímek HANA přeskočit a pokud v systému nebude nic dalšího, přeskočte také zablokování systému souborů. Pokud je potřeba udělat snímky, když všechno je online, přijdete o složitost na obrázek. V tématu _SAP HANA konzistence dat při pořizování snímků úložiště_ v související [příručce zálohování článků pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Další kroky
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md) poskytuje přehled a informace o tom, jak začít.
* [SAP HANA zálohování na základě úrovně souboru](sap-hana-backup-file-level.md) pokrývá možnost zálohování na základě souborů.
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA v Azure (velké instance), najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
