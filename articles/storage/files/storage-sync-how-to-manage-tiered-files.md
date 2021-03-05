---
title: Správa Synchronizace souborů Azure vrstvených souborů | Microsoft Docs
description: Tipy a prostředí PowerShell rutin, které vám pomůžou se správou vrstvených souborů
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204318"
---
# <a name="how-to-manage-tiered-files"></a>Správa vrstvených souborů

Tento článek obsahuje pokyny pro uživatele, kteří mají dotazy související se správou vrstvených souborů. Koncepční otázky týkající se vrstvení cloudu najdete v tématu [Nejčastější dotazy k souborům Azure](storage-files-faq.md).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Jak zjistit, jestli jsou vaše soubory vrstveny

Bez ohledu na to, jestli je potřeba soubory rozvrstvit na nastavené zásady, se vyhodnotí jednou za hodinu. Při vytvoření nového koncového bodu serveru můžete přijít do dvou různých situací:

Při prvním přidání nového koncového bodu serveru se často soubory nacházejí v umístění serveru. Aby bylo možné začít s vrstvou cloudu, je potřeba je nahrát. Zásada volného místa svazku nezahájí svou práci, dokud se nedokončí počáteční nahrávání všech souborů. Volitelné zásady kalendářních dat ale začnou pracovat na jednotlivých souborech, jakmile se soubor nahraje. V tomto případě platí také interval hodin. 

Když přidáte nový koncový bod serveru, je možné, že jste připojili prázdné umístění serveru ke sdílené složce Azure s Vašimi daty. Pokud se rozhodnete ke stažení oboru názvů a k odvolání obsahu během počátečního stahování na server, pak se po skončení tohoto oboru názvů soubory odvolají na základě posledního upravovaného časového razítka do zásad volného místa svazku a na volitelná omezení zásad pro datum.

Existuje několik způsobů, jak ověřit, zda byl soubor vrstven do sdílené složky Azure:
    
   *  **Ověřte atributy souboru v souboru.**
     Klikněte pravým tlačítkem na soubor, přejděte na **Podrobnosti** a přejděte dolů k vlastnosti **atributy** . Vrstvený soubor má následující sady atributů:     
        
        | Písmeno atributu | Atribut | Definice |
        |:----------------:|-----------|------------|
        | A | Archiv | Indikuje, že by měl být soubor zálohovaný zálohovacím softwarem. Tento atribut je vždy nastaven bez ohledu na to, zda je soubor na disku povrstvený nebo uložený jako plný. |
        | P | Zhuštěný soubor | Označuje, že se jedná o zhuštěný soubor. Zhuštěný soubor je specializovaný typ souboru, který systém souborů NTFS nabízí pro efektivní použití v případě, že je soubor na diskovém streamu většinou prázdný. Synchronizace souborů Azure používá zhuštěné soubory, protože soubor je buď úplně vrstven, nebo částečně odvolán. V plně vrstveném souboru je datový proud souboru uložený v cloudu. V částečně vráceném souboru je tato část souboru již na disku. K tomu může dojít, když jsou soubory částečně čteny aplikacemi, jako jsou multimediální přehrávače nebo nástroje zip. Pokud je soubor zcela znovu volán na disk, Synchronizace souborů Azure jej převede ze zhuštěného souboru do normálního souboru. Tento atribut je nastaven pouze v systémech Windows Server 2016 a starších.|
        | M | Odvolat při přístupu k datům | Indikuje, že data souboru nejsou plně přítomná v místním úložišti. Při čtení souboru dojde k tomu, že se alespoň část obsahu souboru načte ze sdílené složky Azure, ke které je připojený koncový bod serveru. Tento atribut je nastaven pouze v systému Windows Server 2019. |
        | L | Spojovací bod | Označuje, že soubor obsahuje bod rozboru. Bod rozboru je speciální ukazatel pro použití filtrem systému souborů. Synchronizace souborů Azure používá spojovací body k definování do Synchronizace souborů Azureho filtru systému souborů (StorageSync.sys) umístění v cloudu, kde je soubor uložený. To podporuje bezproblémový přístup. Uživatelé nebudou muset znát, že se používá Synchronizace souborů Azure nebo jak získat přístup k souboru ve sdílené složce Azure. Když je soubor zcela znovu vyvolán, Synchronizace souborů Azure odebere bod rozboru ze souboru. |
        | O | Offline | Indikuje, že některé nebo všechny obsahy souboru nejsou uložené na disku. Když je soubor zcela znovu vyvolán, Synchronizace souborů Azure tento atribut odstraní. |

        ![Dialogové okno Vlastnosti souboru s vybranou kartou podrobnosti](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Atributy pro všechny soubory ve složce můžete zobrazit tak, že přidáte pole **atributy** do zobrazení tabulky v Průzkumníkovi souborů. Provedete to tak, že kliknete pravým tlačítkem na existující sloupec (například **Velikost**), vyberete **Další** a v rozevíracím seznamu vyberete **atributy** .
        
        > [!NOTE]
        > Všechny tyto atributy budou viditelné i u částečně vrácených souborů.
        
   * **Slouží `fsutil` ke kontrole bodů rozboru v souboru.**
       Jak je popsáno v předchozí možnosti, vrstvený soubor má vždy nastaven bod rozboru. Bod rozboru umožňuje Synchronizace souborů Azure ovladač filtru systému souborů (StorageSync.sys), který načte obsah ze sdílených složek Azure, který není uložen místně na serveru. 

       Pokud chcete zjistit, jestli má soubor bod rozboru, v příkazovém řádku se zvýšenými oprávněními nebo v okně PowerShellu spusťte `fsutil` Nástroj:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Pokud má soubor bod rozboru, můžete očekávat, že se zobrazí **hodnota značky rozboru: 0x8000001E**. Tato hexadecimální hodnota je hodnota bodu rozboru, která je vlastněna Synchronizace souborů Azure. Výstup také obsahuje data opětovného zpracování, která představují cestu k souboru ve sdílené složce Azure.
        
        > [!WARNING]  
        > `fsutil reparsepoint`Příkaz nástroje má také možnost odstranit spojovací bod. Nespouštějte tento příkaz, pokud vám Synchronizace souborů Azure technický tým nežádá. Spuštění tohoto příkazu může způsobit ztrátu dat. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Jak vyloučit aplikace z vrstvení cloudu na čas posledního přístupu

V případě agenta Synchronizace souborů Azure verze 11,1 můžete nyní vyloučit aplikace ze sledování posledního přístupu. Když aplikace přistupuje k souboru, v databázi vrstev cloudu se aktualizuje čas posledního přístupu k souboru. Aplikace, které prohledávají systém souborů, jako je anti-virus, způsobí, že všechny soubory mají stejný čas posledního přístupu, což má vliv na vrstvení souborů.

Pokud chcete vyloučit aplikace ze sledování času posledního přístupu, přidejte název procesu do nastavení registru HeatTrackingProcessNameExclusionList, které je umístěné v části HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Příklad: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> Ve výchozím nastavení jsou vyloučené procesy odstranění duplicitních dat a správce prostředků souborového serveru Správce prostředků. Změny seznamu vyloučení procesů jsou v systému přijaty každých 5 minut.

## <a name="how-to-access-the-heat-store"></a>Jak získat přístup k úložišti tepla

Vrstvení cloudu využívá čas posledního přístupu a četnost přístupu k souboru k určení, které soubory mají být vrstveny. Ovladač filtru vrstvy cloudu (storagesync.sys) sleduje čas posledního přístupu a zapisuje informace do úložiště s navýšenou úrovní cloudu. Můžete načíst tepelný obchod a uložit ho do souboru CSV pomocí rutiny prostředí PowerShell pro místní server.

Pro všechny soubory na stejném svazku existuje jedno tepelné úložiště. Úložiště tepla může mít velkou velikost. Pokud potřebujete pouze načíst "chladný" počet položek, omezení použití a číslo a také zvážit filtrování podle dílčí cesty vs. kořen svazku.

- Importujte modul prostředí PowerShell:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- VOLNÉ místo na svazku: pro získání pořadí, ve kterém budou soubory vrstveny pomocí zásad pro volné místo svazku:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- ZÁSADY data: pro získání pořadí, ve kterém budou soubory vrstveny pomocí zásad data:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Vyhledejte informace o tepelném úložišti pro určitý soubor:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Zobrazit všechny soubory v sestupném pořadí podle času posledního přístupu:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Zobrazit pořadí, ve kterém se vrstvené soubory odvolají při odvolání na pozadí nebo odvolání na vyžádání prostřednictvím PowerShellu:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Jak vynutit vrstvení souboru nebo adresáře

> [!NOTE]
> Když vyberete adresář, který má být vrstven, budou vrstveny pouze soubory, které jsou nyní v adresáři. Všechny soubory vytvořené po této době nejsou automaticky vrstveny.

Když je povolená funkce vrstvení cloudu, vrstva cloudu automaticky rozchází soubory na základě posledního přístupu a doby úprav, aby se dosáhlo procenta volného místa na svazku, které je zadané na koncovém bodu cloudu. Někdy ale můžete chtít ručně vynutit soubor do vrstvy. To může být užitečné, pokud uložíte velký soubor, který nechcete znovu použít po dlouhou dobu, a chcete, aby volné místo na svazku bylo teď k dispozici pro jiné soubory a složky. Vrstvení můžete vynutit pomocí následujících příkazů PowerShellu:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Jak odvolat vrstvený soubor na disk

Nejjednodušší způsob, jak na disk navrátit soubor, je otevřít soubor. Synchronizace souborů Azure filtr systému souborů (StorageSync.sys) bezproblémově stáhne soubor ze sdílené složky Azure, aniž by byla na vaší straně potřeba žádná práce. V případě typů souborů, které mohou být částečně čteny nebo zasílané do datového proudu, jako jsou například multimédia nebo soubory zip, stačí otevření souboru zajistit stažení celého souboru.

Aby bylo zajištěno, že soubor bude plně stažen na místní disk, je nutné použít PowerShell k vynucení úplného volání souboru. Tato možnost může být užitečná také v případě, že chcete navrátit více souborů najednou, například všechny soubory ve složce. Otevřete relaci PowerShellu pro uzel serveru, na kterém je nainstalovaná Synchronizace souborů Azure, a pak spusťte následující příkazy PowerShellu:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Volitelné parametry:
* `-Order CloudTieringPolicy` Nejdřív odvolá naposledy změněné nebo dostupné soubory a povolí aktuální zásady vrstvení. 
    * Pokud je nakonfigurovaná zásada volného místa svazku, budou se soubory volat, dokud nedosáhnete nastavení zásad pro volné místo svazku. Pokud je například nastavení zásad Free o svazku 20%, bude odvolání zastaveno, jakmile volné místo na svazku dosáhne 20%.  
    * Pokud je nakonfigurované volné místo na svazku a data zásad, budou se soubory volat, dokud nedosáhnete nastavení pro volné místo na svazku nebo pro datum. Pokud má například nastavení zásad Free hodnotu 20% a zásada data je 7 dní, pak se odvolání zastaví, jakmile velikost volného místa na svazku dosáhne 20% nebo jsou všechny soubory, ke kterým se přistupovalo nebo upraveno, do 7 dnů, místní.
* `-ThreadCount` Určuje, kolik souborů lze paralelně volat.
* `-PerFileRetryCount`Určuje, jak často se bude opakovat pokus o odvolání souboru, který je aktuálně blokován.
* `-PerFileRetryDelaySeconds`Určuje dobu v sekundách mezi opakovanými pokusy o odvolání a měla by být vždy použita v kombinaci s předchozím parametrem.

Příklad:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Pokud místní svazek, který hostuje server, nemá dost volného místa pro odvolání všech vrstvených dat, `Invoke-StorageSyncFileRecall` rutina se nezdařila.  

> [!Note]
> Aby bylo možné odvolat soubory, které byly vrstveny, Šířka pásma sítě by měla být minimálně 1 MB/s. Pokud je šířka pásma sítě menší než 1 MB/s, nemusí se soubory po vypršení časového limitu navrátit.

## <a name="next-steps"></a>Další kroky
* [Nejčastější dotazy ke službě Azure Files](storage-files-faq.md)
