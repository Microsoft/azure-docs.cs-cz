---
title: Odstraňování dat v Azure Data Box Disk zkopírujte problémy | Dokumentace Microsoftu
description: Popisuje, jak řešit problémy se můžou vyskytnout během kopírování dat v disku Azure Data Box pomocí protokolů.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148345"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Řešení potíží kopie dat v disku Azure Data Box

V tomto článku se vztahuje na Microsoft Azure Data Box Disk a popisuje postupy řešení problémů, na které se zobrazí při kopírování dat na disky. Článek obsahuje také problémy při použití rozdělení nástroj pro kopírování.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Kopírování dat problémy při použití systému Linux

Tato část podrobně popisuje některé z hlavních problémů, kterým čelí při používání klienta Linux ke kopírování dat na disky.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problém: Jednotka získání připojit jako jen pro čtení
 
**Příčina** 

To může být způsobeno systém souborů musí provést.

Jednotky pro čtení i zápis opakovanému připojení nebude fungovat s disků Data Box. Tento scénář není podporován s jednotkami dislocker dešifrovat. Vám může mít úspěšně znovu připojí zařízení, pomocí následujícího příkazu:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

I když opakovanému připojení bylo úspěšné, data nezachovají.

**Řešení**

Proveďte následující kroky v systému Linux:

1. Nainstalujte `ntfsprogs` balíček pro nástroj ntfsfix.
2. Odpojte přípojné body podle nástroj odemknout jednotku. Počet přípojné body se bude lišit u jednotek.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Spustit `ntfsfix` na odpovídající cestě. Zvýrazněná čísla musí být stejná jako kroku 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Spuštěním následujícího příkazu odeberte režimu hibernace metadata, která může způsobit problém připojení.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Proveďte čistou odpojení.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Proveďte čistou odemknutí a její připojení.
7. Otestujte přípojný bod zápisu souboru.
8. Odpojte Image a znovu připojit k ověření trvalost souborů.
9. Pokračujte v kopírování dat.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problém: Chyba s daty není uchování po kopírování
 
**Příčina** 

Pokud zjistíte, že jednotka nemá data poté, co byla odpojit (v případě, že data byla zkopírována do ní), je možné znovu připojit jednotku pro čtení i zápis, po jednotky byl připojený jen pro čtení.

**Řešení**
 
Pokud je to tento případ, podívat na řešení pro [jednotky získávání připojit jako jen pro čtení](#issue-drive-getting-mounted-as-read-only).

Pokud, který nebyl tento případ, zkopírovat protokoly ze složky, která se má nástroj Data Box Disk odemknutí a [obraťte se na Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Chyby nástroje rozdělení/kopírování Data Box Disku

Problémy s viděli při použití nástroje rozdělení kopírovat data rozdělit přes více disků jsou shrnuty v následující tabulce.

|Chybová zpráva / upozornění |Doporučení |
|---------|---------|
|[Informace o] Načtení hesla nástroje BitLocker pro svazek: m <br>[Chyba] Byla zachycena výjimka při načítání klíč Bitlockeru pro svazek m:<br> Sekvence neobsahuje žádné prvky.|Tato chyba je vyvolána, pokud je cílový Data Box Disk offline. <br> Používejte nástroj `diskmgmt.msc` na disky, které jsou online.|
|[Chyba] Došlo k výjimce: Operace WMI se nezdařilo:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=Formát poskytnutého hesla pro obnovení je neplatný. <br>Hesla pro obnovení nástroje BitLocker mají 48 číslic. <br>Zkontrolujte, jestli je heslo pro obnovení ve správném formátu, a pak to zkuste znovu.|Pomocí odemykacího nástroje Data Box Disku nejdřív disk odemkněte a pak zkuste příkaz znovu. Další informace najdete v článcích <li> [Odemknutí Data Box Disku pro klienty Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Odemknutí Data Box Disku pro klienty Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Chyba] Došlo k výjimce: DriveManifest.xml soubor existuje na cílové jednotce. <br> To signalizuje, že cílový disk mohl být připraven s jiným souborem deníku. <br>Pokud chcete přidat další data na stejnou jednotku, použijte předchozí soubor deníku. Pokud chcete odstranit existující data a znovu použít cílovou jednotku pro nové úlohy importu, odstraňte *DriveManifest.xml* na jednotce. Spusťte tento příkaz znovu s novým souborem deníku.| Tato chyba se zobrazí, když se pokusíte použít stejnou sadu jednotek pro více relací importu. <br> Používejte jednu sadu jednotek jenom pro jednu relaci rozdělení a kopírování.|
|[Chyba] Došlo k výjimce: Data importu CopySessionId – září test-1 odkazuje na předchozí relace kopírování a nesmí znovu použít pro novou relaci kopírování.|Tato chyba se zobrazí, když se pokusíte použít pro novou úlohu stejný název jako měla předchozí úspěšně dokončená úloha.<br> Přiřaďte nové úloze jedinečný název.|
|[Info] Název cílového souboru nebo adresáře překračuje limit délky platný v systému souborů NTFS. |Tato zpráva je nahlášena, když byl cílový soubor přejmenován z důvodu příliš dlouhé cesty.<br> Toto chování můžete řídit úpravou možnosti disposition v souboru `config.json`.|
|[Chyba] Došlo k výjimce: Chybný JSON řídicí sekvence. |Tato zpráva je nahlášena, když má soubor config.json neplatný formát. <br> Před uložením ověřte platnost souboru `config.json` pomocí nástroje [JSONlint](https://jsonlint.com/).|


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [řešit problémy s ověřením nástroj](data-box-disk-troubleshoot.md).
