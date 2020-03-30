---
title: Disk datové schránky Azure řeší problémy s kopií dat| Dokumenty společnosti Microsoft
description: Popisuje, jak řešit problémy, které se zobrazit při kopírování dat v Disku datové schránky Azure pomocí protokolů.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148345"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Poradce při potížích s kopírováním dat na disku Azure Data Box

Tento článek se vztahuje na disk datové schránky Microsoft Azure a popisuje, jak řešit všechny problémy, které se zobrazí při kopírování dat na disky. Článek také popisuje problémy při použití nástroje rozdělené kopie.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problémy s kopírováním dat při používání systému Linux

Tato část podrobně popisuje některé z hlavních problémů, kterým čelíte při použití klienta Linuxu ke kopírování dat na disky.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problém: Namontovat jednotku jen pro čtení
 
**Příčina** 

To může být způsobeno nečistým souborovým systémem.

Opětovné připojení jednotky jako čtení a zápisu nefunguje s disky datových schráptek. Tento scénář není podporován s jednotkami dešifrovanými nástrojem dislocker. Je možné, že jste úspěšně znovu namontovali zařízení pomocí následujícího příkazu:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

I když byla remontáž úspěšná, data nebudou přetrvávat.

**Rozlišení**

Udělejte si následující kroky na vašem systému Linux:

1. Nainstalujte `ntfsprogs` balíček pro nástroj ntfsfix.
2. Odpojte přípojné body, které jsou pro jednotku k dispozici, nástrojem pro odemknutí. Počet přípojných bodů se u jednotek bude lišit.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Spustit `ntfsfix` na odpovídající cestě. Zvýrazněné číslo by mělo být stejné jako krok 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Spusťte následující příkaz a odeberte metadata režimu spánku, která mohou způsobit problém s připojením.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Proveďte čistou odpojit.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Proveďte čisté odemknutí a montáž.
7. Otestujte přípojný bod zápisem souboru.
8. Odpojte a znovu ověřte trvalost souboru.
9. Pokračujte v kopírování dat.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problém: Chyba s daty, která po kopírování nepřetrvávají
 
**Příčina** 

Pokud zjistíte, že vaše jednotka nemá data po odpojení (i když data byla zkopírována do něj), pak je možné, že jste znovu jednotku jako čtení a zápis po jednotce byla připojena jako jen pro čtení.

**Rozlišení**
 
Pokud tomu tak je, podívejte se na rozlišení pro [jednotky, které jsou připojeny jen pro čtení](#issue-drive-getting-mounted-as-read-only).

Pokud tomu tak nebylo, zkopírujte protokoly ze složky, která obsahuje nástroj pro odemknutí disku datové schránky, a [obraťte se na podporu společnosti Microsoft](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Chyby nástroje rozdělení/kopírování Data Box Disku

Problémy, které se objevují při použití nástroje rozdělit kopii rozdělit data na více disků jsou shrnuty v následující tabulce.

|Chybová zpráva / upozornění |Doporučení |
|---------|---------|
|[Informace] Načítání hesla nástroje BitLocker pro svazek: m <br>[Chyba] Při načítání klíče nástroje BitLocker pro svazek m byla zachycena výjimka:<br> Sekvence neobsahuje žádné prvky.|Tato chyba je vyvolána, pokud je cílový Data Box Disk offline. <br> Používejte nástroj `diskmgmt.msc` na disky, které jsou online.|
|[Chyba] Došlo k výjimce: Operace WMI selhala:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=Formát poskytnutého hesla pro obnovení je neplatný. <br>Hesla pro obnovení nástroje BitLocker mají 48 číslic. <br>Zkontrolujte, jestli je heslo pro obnovení ve správném formátu, a pak to zkuste znovu.|Pomocí odemykacího nástroje Data Box Disku nejdřív disk odemkněte a pak zkuste příkaz znovu. Další informace najdete v článcích <li> [Odemknutí Data Box Disku pro klienty Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Odemknutí Data Box Disku pro klienty Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Chyba] Došlo k výjimce: Na cílové jednotce existuje soubor DriveManifest.xml. <br> To signalizuje, že cílový disk mohl být připraven s jiným souborem deníku. <br>Pokud chcete přidat další data na stejnou jednotku, použijte předchozí soubor deníku. Chcete-li odstranit existující data a znovu použít cílovou jednotku pro novou úlohu importu, odstraňte na jednotce *soubor DriveManifest.xml.* Spusťte tento příkaz znovu s novým souborem deníku.| Tato chyba se zobrazí, když se pokusíte použít stejnou sadu jednotek pro více relací importu. <br> Používejte jednu sadu jednotek jenom pro jednu relaci rozdělení a kopírování.|
|[Chyba] Došlo k výjimce: CopySessionId importdata-sept-test-1 odkazuje na předchozí relace kopírování a nejde znovu použít pro novou relaci kopírování.|Tato chyba se zobrazí, když se pokusíte použít pro novou úlohu stejný název jako měla předchozí úspěšně dokončená úloha.<br> Přiřaďte nové úloze jedinečný název.|
|[Info] Název cílového souboru nebo adresáře překračuje limit délky platný v systému souborů NTFS. |Tato zpráva je nahlášena, když byl cílový soubor přejmenován z důvodu příliš dlouhé cesty.<br> Toto chování můžete řídit úpravou možnosti disposition v souboru `config.json`.|
|[Chyba] Došlo k výjimce: Chybná řídicí sekvence JSON. |Tato zpráva je nahlášena, když má soubor config.json neplatný formát. <br> Před uložením ověřte platnost souboru `config.json` pomocí nástroje [JSONlint](https://jsonlint.com/).|


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [řešit problémy s ověřovacím nástrojem](data-box-disk-troubleshoot.md).
