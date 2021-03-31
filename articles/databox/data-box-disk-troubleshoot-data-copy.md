---
title: Azure Data Box Disk řešení potíží s kopírováním dat | Microsoft Docs
description: Popisuje, jak řešit problémy, ke kterým dochází během kopírování dat v Azure Data Box Disk pomocí protokolů.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 5d977fe0b7459af35f678e77681d3b27c31431cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85849185"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Řešení potíží s kopírováním dat v Azure Data Box Disk

Tento článek se týká Microsoft Azure Data Box Disk a popisuje, jak řešit problémy, které vidíte při kopírování dat na disky. Článek také popisuje problémy při použití nástroje pro dělení na kopírování.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problémy při kopírování dat při použití systému Linux

Tato část podrobně popisuje nejdůležitější problémy při použití klienta Linux ke kopírování dat na disky.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problém: jednotka, která je připojená jen pro čtení
 
**Příčina** 

Důvodem může být nečistý systém souborů.

Pokud se jednotka znovu připojí jako čtení i zápis, nefunguje s Data Box disky. Tento scénář se nepodporuje u jednotek dešifrovaných pomocí Weblock. Mohli jste úspěšně znovu připojit zařízení pomocí následujícího příkazu:

```
# mount -o remount, rw /mnt/DataBoxDisk/mountVol1
```

I když opětovné připojení proběhlo úspěšně, data se nezachovají.

**Řešení**

V systému Linux proveďte následující kroky:

1. Nainstalujte `ntfsprogs` balíček pro nástroj ntfsfix.
2. Odpojte přípojné body zadané pro jednotku nástrojem odemčení. Počet přípojných bodů se bude lišit pro jednotky.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Spusťte `ntfsfix` na odpovídající cestě. Zvýrazněné číslo by mělo být stejné jako v kroku 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Spuštěním následujícího příkazu odeberte metadata hibernace, která by mohla způsobit problém s připojením.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Proveďte čisté odpojení.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Proveďte čisté odemčení a připojení.
7. Otestujte přípojný bod zápisem souboru.
8. K ověření trvalosti souboru se připojte a znovu připojte.
9. Pokračujte v kopírování dat.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problém: Chyba při Neuchování dat po zkopírování
 
**Příčina** 

Pokud zjistíte, že vaše jednotka nemá data po odpojení (i když do ní byla zkopírována data), je možné, že jste po připojení jednotky jako jen pro čtení znovu připojili jednotku jako pro čtení i zápis.

**Řešení**
 
Pokud se jedná o tento případ, přečtěte si téma řešení pro jednotky, které se [připravují jako jen pro čtení](#issue-drive-getting-mounted-as-read-only).

Pokud se nejedná o tento případ, zkopírujte protokoly ze složky, ve které je nástroj Data Box Disk Unlock, a obraťte se na [Podpora Microsoftu](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Chyby nástroje rozdělení/kopírování Data Box Disku

Problémy, které se zobrazují při použití nástroje pro rozdělené kopírování k rozdělení dat na více disků, jsou shrnuty v následující tabulce.

|Chybová zpráva / upozornění |Doporučení |
|---------|---------|
|Příjemce Načítá se heslo nástroje BitLocker pro svazek: m. <br>Chyba Při načítání klíče nástroje BitLocker pro svazek m byla zachycena výjimka:<br> Sekvence neobsahuje žádné prvky.|Tato chyba je vyvolána, pokud je cílový Data Box Disk offline. <br> Používejte nástroj `diskmgmt.msc` na disky, které jsou online.|
|[Chyba] Došlo k výjimce: Operace WMI selhala:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=Formát poskytnutého hesla pro obnovení je neplatný. <br>Hesla pro obnovení nástroje BitLocker mají 48 číslic. <br>Zkontrolujte, jestli je heslo pro obnovení ve správném formátu, a pak to zkuste znovu.|Pomocí odemykacího nástroje Data Box Disku nejdřív disk odemkněte a pak zkuste příkaz znovu. Další informace najdete v článcích <li> [Odemknutí Data Box Disku pro klienty Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Odemknutí Data Box Disku pro klienty Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Chyba] Došlo k výjimce: Na cílové jednotce existuje soubor DriveManifest.xml. <br> To signalizuje, že cílový disk mohl být připraven s jiným souborem deníku. <br>Pokud chcete přidat další data na stejnou jednotku, použijte předchozí soubor deníku. Chcete-li odstranit stávající data a znovu použít cílovou jednotku pro novou úlohu importu, odstraňte *DriveManifest.xml* na disku. Spusťte tento příkaz znovu s novým souborem deníku.| Tato chyba se zobrazí, když se pokusíte použít stejnou sadu jednotek pro více relací importu. <br> Používejte jednu sadu jednotek jenom pro jednu relaci rozdělení a kopírování.|
|[Chyba] Došlo k výjimce: CopySessionId importdata-sept-test-1 odkazuje na předchozí relace kopírování a nejde znovu použít pro novou relaci kopírování.|Tato chyba se zobrazí, když se pokusíte použít pro novou úlohu stejný název jako měla předchozí úspěšně dokončená úloha.<br> Přiřaďte nové úloze jedinečný název.|
|[Info] Název cílového souboru nebo adresáře překračuje limit délky platný v systému souborů NTFS. |Tato zpráva je nahlášena, když byl cílový soubor přejmenován z důvodu příliš dlouhé cesty.<br> Toto chování můžete řídit úpravou možnosti disposition v souboru `config.json`.|
|[Chyba] Došlo k výjimce: Chybná řídicí sekvence JSON. |Tato zpráva je nahlášena, když má soubor config.json neplatný formát. <br> Před uložením ověřte platnost souboru `config.json` pomocí nástroje [JSONlint](https://jsonlint.com/).|


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [řešit problémy s nástrojem pro ověřování](data-box-disk-troubleshoot.md).
