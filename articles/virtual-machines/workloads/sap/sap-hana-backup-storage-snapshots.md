---
title: Zálohování SAP HANA Azure podle úložiště snímků | Microsoft Docs
description: Existují dvě hlavní zálohování možnosti pro SAP HANA na virtuálních počítačích Azure, tento článek se zabývá SAP HANA zálohování podle úložiště snímků
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
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 819888800b9663f9b920fbaf11b30ad28287a0b5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658719"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Zálohování SAP HANA založené na snímcích úložiště

## <a name="introduction"></a>Úvod

Toto je část třemi částmi řady související články v záloze SAP HANA. [Příručce zálohování pro SAP HANA ve virtuálních počítačích Azure](sap-hana-backup-guide.md) poskytuje přehled a informace o začátcích, a [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) popisuje možnost zálohování na základě souborů.

Při použití funkce zálohování virtuálních počítačů pro jednoduchou vše v jednom ukázku systém, jeden zvažte provedení zálohy virtuálního počítače místo Správa HANA zálohování na úrovni operačního systému. Alternativou je k pořízení snímků objektů blob v Azure k vytvoření kopie jednotlivé virtuální disky, které jsou připojeny k virtuálnímu počítači, a zachovat HANA datových souborů. Ale kritický bod konzistence aplikace při vytváření snímku zálohy nebo disk virtuálního počítače, zatímco systém zapnutý a spouštění. V tématu _konzistenci dat SAP HANA při pořizování snímků úložiště_ v souvisejícím článku [zálohování Průvodce pro SAP HANA ve virtuálních počítačích Azure](sap-hana-backup-guide.md). SAP HANA obsahuje funkce, která podporuje tyto typy úložiště snímků.

## <a name="sap-hana-snapshots"></a>Snímky SAP HANA

V SAP HANA, který podporuje pořizování snímku úložiště je funkce. Od prosince 2016, je však omezení pro systémy jeden kontejner. Víceklientské kontejneru konfigurace nepodporují tento druh snímek databáze (viz [vytvořit úložiště snímků (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Funguje takto:

- Příprava pro vytvoření snímku úložiště pomocí inicializace snímku SAP HANA
- Spustit úložiště snímků (objektů blob v Azure vytvořit snímek, například)
- Potvrďte snímku SAP HANA

![Tento snímek obrazovky ukazuje, že SAP HANA data snímku lze vytvořit pomocí příkazu jazyka SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Tento snímek obrazovky ukazuje, že SAP HANA data snímku lze vytvořit pomocí příkazu jazyka SQL.

![Snímek pak se zobrazí také v katalogu zálohování v SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Snímek pak se zobrazí také v katalogu zálohování v SAP HANA Studio.

![Na disku snímku se zobrazí v adresáři data SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

Na disku snímku se zobrazí v adresáři data SAP HANA.

Jeden má zajistit, aby konzistence systému souborů je také garantované před spuštěním úložiště snímku, zatímco SAP HANA je v režimu přípravy snímku. V tématu _konzistenci dat SAP HANA při pořizování snímků úložiště_ v souvisejícím článku [zálohování Průvodce pro SAP HANA ve virtuálních počítačích Azure](sap-hana-backup-guide.md).

Po dokončení úložiště snímků je důležité pro potvrzení snímku SAP HANA. Neexistuje odpovídající ke spuštění příkazu jazyka SQL: zavřít SNÍMKU zálohování dat (v tématu [dat zavřít SNÍMKU příkaz BACKUP (zálohování a obnovení)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Potvrďte HANA snímku. Z důvodu &quot;kopie při zápisu,&quot; SAP HANA může vyžadovat další místo na disku v Příprava snímku režimu, a není možné spustit nové zálohování, dokud je potvrzen snímku SAP HANA.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Zálohování virtuálních počítačů HANA prostřednictvím služby zálohování Azure

Od prosince 2016 zálohování agent služby Azure Backup není k dispozici pro virtuální počítače s Linuxem. Chcete-li používat Azure zálohování na úrovni soubor nebo adresář, jeden by zkopírujte SAP HANA záložní soubory pro virtuální počítač s Windows a potom pomocí agenta zálohování. Pouze úplná záloha virtuálního počítače s Linuxem, jinak je možné pomocí služby zálohování Azure. V tématu [přehled funkcí zálohování Azure](../../../backup/backup-introduction-to-azure-backup.md) Další.

Služba Azure Backup nabízí možnost pro zálohování a obnovení virtuálních počítačů. Další informace o této služby a jak to funguje najdete v článku [plánování vaší infrastruktury zálohování virtuálních počítačů v Azure](../../../backup/backup-azure-vms-introduction.md).

Existují dvě důležité aspekty podle tohoto článku:

_&quot;Pro virtuální počítače s Linuxem je možné, pouze konzistentními soubory zálohy, protože Linux není má ekvivalentní platformu pro službu VSS.&quot;_

_&quot;Aplikace je třeba implementovat vlastní &quot;opravu&quot; mechanismus na obnovená data.&quot;_

Proto jeden má zajistěte, aby byl SAP HANA v konzistentním stavu na disku při spuštění zálohování. V tématu _SAP HANA snímky_ popsané dříve v dokumentu. Ale existuje potenciální problém při SAP HANA zůstane v tomto režimu přípravy snímku. V tématu [vytvořit úložiště snímků (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) Další informace.

Tento článek stavy:

_&quot;Důrazně doporučujeme k potvrzení, nebo zrušte snímku úložiště co nejdříve po jeho vytvoření. Když se snímek úložiště připravené nebo vytvořili, snímek příslušných dat nereaguje. Při současném zachování ukotvené snímek příslušných dat, může stále provádět změny v databázi. Tyto změny nezpůsobí ukotvené snímek příslušných dat se musí změnit. Místo toho změny se zapisují do pozice v datové oblasti, které jsou oddělené od snímku úložiště. Změny se zapisují taky do protokolu. Ale tím déle snímek příslušných dat je udržováno ukotvené, čím můžou růst datový svazek.&quot;_

Zálohování Azure má na starosti konzistence systému souborů prostřednictvím rozšíření virtuálního počítače Azure. Tato rozšíření nejsou k dispozici samostatná a fungovat pouze v kombinaci s služby zálohování Azure. Je však stále požadavek na správu SAP HANA snímku zaručit konzistenci aplikace.

Zálohování Azure má dva hlavní fáze:

- Pořízení snímku
- Přenos dat do trezoru

Po dokončení fázi služby Azure Backup pořízení snímku apod. může potvrďte snímku SAP HANA. Může trvat několik minut, pokud chcete zobrazit na portálu Azure.

![Následující obrázek ukazuje součást úlohy zálohování seznamu služby Azure Backup](media/sap-hana-backup-storage-snapshots/image014.png)

Následující obrázek ukazuje části seznamu úlohu zálohování Azure Backup služby, který byl použit k zálohování HANA testovací virtuální počítač.

![Chcete-li zobrazit podrobnosti úlohy, klikněte na úlohu zálohování na portálu Azure](media/sap-hana-backup-storage-snapshots/image015.png)

Chcete-li zobrazit podrobnosti úlohy, klikněte na úlohu zálohování na portálu Azure. Zde jeden můžete vidět dvě fáze. Může trvat několik minut, dokud se zobrazí fázi snímků jako dokončenou. Ve většině případů je stráví ve fázi data.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>Zálohování automatizace HANA virtuálních počítačů pomocí služby zálohování Azure

SAP HANA snímku jeden může potvrdit, ručně po dokončení fázi Azure Backup snímku, jak je popsáno výše, ale je vhodné zvážit automatizace, protože správce nemusí monitorování seznamu úlohu zálohování na portálu Azure.

Následuje vysvětlení, jak je možné dosáhnout pomocí rutin prostředí Azure PowerShell.

![Služby Azure Backup byl vytvořen s názvem hana--úložiště záloh](media/sap-hana-backup-storage-snapshots/image016.png)

Služby Azure Backup byl vytvořen s názvem &quot;hana úložiště záloh.&quot; Příkaz PS **Get AzureRmRecoveryServicesVault-název úložiště záloh hana** načte odpovídající objekt. Tento objekt se pak používá nastavit kontext zálohování, jak je vidět na následující obrázek.

![Jeden můžete zkontrolovat pro aktuálně probíhá úloha zálohování](media/sap-hana-backup-storage-snapshots/image017.png)

Po nastavení správný kontext, jeden kontrolovat aktuálně probíhá úloha zálohování a potom vyhledejte jeho podrobnosti úlohy. V seznamu dílčí úlohy uvedena, pokud je již dokončeno fázi snímku Azure úlohy zálohování:

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![Dotazování hodnota ve smyčce, dokud se změní na dokončeno](media/sap-hana-backup-storage-snapshots/image018.png)

Jakmile podrobnosti úlohy jsou uložené v proměnné, je jednoduše PS syntaxe mít přístup k první vstupní pole a načíst hodnotu stavu. K dokončení skriptu pro automatizaci, dotazování hodnota ve smyčce, dokud se změní &quot;dokončeno.&quot;

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Licenční klíč HANA a virtuální počítač obnovit pomocí služby zálohování Azure

Služba Azure Backup je určené k vytvoření nového virtuálního počítače během obnovení. Neexistuje žádný plán teď chcete &quot;na místě&quot; obnovení existující virtuální počítač Azure.

![Následující obrázek ukazuje možnost obnovení služby Azure na portálu Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Následující obrázek ukazuje možnost obnovení služby Azure na portálu Azure. Jeden můžete vybrat mezi vytvoření virtuálního počítače během obnovení nebo při obnovování disky. Po obnovení disků, je stále nutné vytvořit nový virtuální počítač nad ho. Při každém vytvoření nového virtuálního počítače se vytvoří v Azure jedinečné ID virtuálního počítače změní (viz [zpřístupnění a pomocí jedinečné ID virtuálního počítače Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Následující obrázek ukazuje jedinečné ID virtuálního počítače Azure před a po obnovení pomocí služby zálohování Azure](media/sap-hana-backup-storage-snapshots/image020.png)

Následující obrázek ukazuje jedinečné ID virtuálního počítače Azure, před a po obnovení pomocí služby zálohování Azure. Klíč hardwaru SAP, který se používá pro SAP licencování, používá tato jedinečné ID virtuálních počítačů. V důsledku toho novou licenci SAP musí být nainstalovaná po obnovení virtuálního počítače.

Nová funkce Azure Backup se zobrazí v režimu náhledu při vytváření této příručce zálohování. To umožňuje obnovení souboru úrovně založené na snímku virtuálního počítače, která byla provedena pro virtuální počítač zálohování. Díky tomu není nutné k nasazení nového virtuálního počítače a proto jedinečné ID virtuálního počítače zůstane stejný a není třeba žádný nový licenční klíč SAP HANA. Další dokumentaci k této funkce bude poskytnuta po je plně testována.

Zálohování Azure vám umožní nakonec zálohování Azure jednotlivé virtuální disky, a soubory a adresáře z ve virtuálním počítači. Hlavní výhodou Azure Backup je jeho správu všechny zálohy, ukládání zákazník muset provést. Pokud obnovení je nutné, bude zálohování Azure vyberte správné zálohu pro případ.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Zálohování virtuálních počítačů SAP HANA prostřednictvím snímku ruční disku

Místo použití služba Azure Backup, jeden může konfigurovat jednotlivých řešení zálohování vytváření snímků objektu blob Azure virtuální pevné disky ručně pomocí prostředí PowerShell. V tématu [snímky použití objektů blob v prostředí PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) popis kroků.

Poskytuje větší flexibilitu, ale nevyřeší problémy popsané výše v tomto dokumentu:

- Jeden stále musí Ujistěte se, že SAP HANA je v konzistentním stavu
- Disk operačního systému nelze přepsat, i když je virtuální počítač navrácený z důvodu chyby s oznámením, že existuje zapůjčení. Funguje pouze po odstranění virtuálního počítače, což by vedlo k nové jedinečné ID virtuálního počítače a není nutné nainstalovat novou licenci SAP.

![Je možné obnovit pouze datových disků virtuálního počítače Azure](media/sap-hana-backup-storage-snapshots/image021.png)

Je možné obnovit pouze datových disků virtuálního počítače Azure, zabraňující problém získávání nové jedinečné ID virtuálního počítače a, proto zrušena SAP licencí:

- Pro test dva disky dat Azure byla připojena k virtuálnímu počítači a použít jako byl definován softwaru diskového pole RAID 
- Funkce snímku SAP HANA bylo potvrzeno, že SAP HANA bylo v konzistentním stavu
- Freeze – systém souborů (v tématu _konzistenci dat SAP HANA při pořizování snímků úložiště_ v souvisejícím článku [zálohování Průvodce pro SAP HANA ve virtuálních počítačích Azure](sap-hana-backup-guide.md))
- Objekt BLOB snímky byly odebrány z obou datových disků
- Uvolní systému souborů
- Potvrzení snímku SAP HANA
- Pokud chcete obnovit datové disky, byl virtuální počítač vypnout a odpojit oba disky
- Po odpojení disky, měla přepsat se snímky bývalé objektů blob
- Pak se obnovený virtuálních disků připojených znovu k virtuálnímu počítači
- Po spuštění virtuálního počítače, vše na softwaru diskového pole RAID fungovala bez problémů a byla nastavena zpět na čas snímku objektů blob
- HANA se nastaví zpět na snímku HANA

Pokud bylo možné vypnout SAP HANA před snímky objektů blob, bude postup méně složitých. V takovém případě jeden může přeskočit HANA snímku a, pokud nic jiného se děje v systému, také přeskočit zablokování systému souborů. Náročnost dodává do obrázku, pokud je potřeba udělat snímky, zatímco všechno, co je online. V tématu _konzistenci dat SAP HANA při pořizování snímků úložiště_ v souvisejícím článku [zálohování Průvodce pro SAP HANA ve virtuálních počítačích Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Další postup
* [Příručce zálohování pro SAP HANA ve virtuálních počítačích Azure](sap-hana-backup-guide.md) nabízí přehled a informace o zahájení práce.
* [Na základě SAP HANA zálohování na úrovni souborů](sap-hana-backup-file-level.md) popisuje možnost zálohování na základě souborů.
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance) naleznete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
