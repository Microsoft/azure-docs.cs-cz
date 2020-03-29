---
title: Azure Data Box Disk řešení problémů s odemknutím disku | Dokumenty společnosti Microsoft
description: Popisuje, jak řešit potíže s Azure Data Box Diskem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148280"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Poradce při potížích s odemknutím disku na disku Azure Data Box

Tento článek se vztahuje na disk datové schránky Microsoft Azure a popisuje pracovní postupy používané k řešení problémů při použití nástroje pro odemknutí. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Chyby odemykacího nástroje Data Box Disku


| Chybová zpráva / chování nástroje      | Doporučení                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Aktuální rozhraní .NET Framework není podporováno. Podporovány jsou verze 4.5 a vyšší.<br><br>Nástroj skončí s chybovou zprávou.  | Rozhraní .NET 4.5 není nainstalováno. Na hostitelský počítač, na kterém běží odemykací nástroj Data Box Disku, nainstalujte rozhraní .NET 4.5 nebo novější.                                                                            |
| Nepodařilo se odemknout nebo ověřit žádné svazky. Obraťte se na podporu Microsoftu.  <br><br>Nástroji se nepodařilo odemknout nebo ověřit žádnou uzamčenou jednotku. | Nástroji se s použitím zadaného klíče nepodařilo odemknout žádnou ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu.                                                |
| Následující svazky jsou odemčené a ověřené. <br>Písmena jednotek: E:<br>Nepodařilo se odemknout žádné svazky s použitím následujících klíčů: werwerqomnf, qwerwerqwdfda <br><br>Nástroj odemkl některé jednotky a zobrazil písmena úspěšných a neúspěšných jednotek.| Částečný úspěch. Nástroji se s použitím zadaného klíče nepodařilo odemknout některé ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu. |
| Nepovedlo se najít zamčené svazky. Zkontrolujte, že je disk od Microsoftu správně připojený a zamčený.          | Nástroji se nepodařilo najít žádné zamčené jednotky. Buď jsou jednotky už odemčené, nebo nebyly nalezeny. Zkontrolujte, že jsou jednotky připojené a zamčené.                                                           |
| Závažná chyba: Neplatný parametr<br>Název parametru: invalid_arg<br>POUŽITÍ:<br>DataBoxDiskUnlock /PassKeys:<seznam_klíčů_oddělený_středníky><br><br>Příklad: DataBoxDiskUnlock /PassKeys:klíč1;klíč2;klíč3<br>Příklad: DataBoxDiskUnlock /SystemCheck<br>Příklad: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Tento klíč najdete v objednávce služby Azure Data Box Disk. Klíč vaše disky odemkne.<br>/Help:           Tato možnost poskytuje nápovědu k použití rutiny a příklady.<br>/SystemCheck:    Tato možnost zkontroluje, jestli váš systém splňuje požadavky na spuštění nástroje.<br><br>Nástroj ukončíte stisknutím libovolné klávesy. | Byl zadán neplatný parametr. Pouze povolené parametry jsou /SystemCheck, /PassKey a /Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Problémy s odemknutím disků při použití klienta systému Windows

Tato část podrobně popisuje některé z hlavních problémů, kterým čelí při nasazení disku datové schránky při použití klienta systému Windows pro kopírování dat.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problém: Nelze odemknout jednotku z nástroje BitLocker.
 
**Příčina** 

Použili jste heslo v dialogovém okně Nástrojem bitlocker a pokoušíte se odemknout disk pomocí dialogového okna Odemknutí jednotek nástroje BitLocker. Tohle by nefungovalo.

**Rozlišení**

Chcete-li odemknout disky datové schránky, musíte použít nástroj pro odemknutí disku datové schránky a zadat heslo z webu Azure Portal. Další informace najdete [v kurzu: Rozbalit, připojit a odemknout disk Azure Data Box](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problém: Některé svazky nelze odemknout ani ověřit. Obraťte se na podporu Microsoftu.
 
**Příčina**

V protokolu chyb se může zobrazit následující chyba a některé svazky nelze odemknout ani ověřit.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
To znamená, že pravděpodobně chybí příslušná verze prostředí Windows PowerShell v klientovi systému Windows.

**Rozlišení**

Můžete nainstalovat [prostředí Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) a operaci zopakovat.
 
Pokud stále nemůžete odemknout svazky, zkopírujte protokoly ze složky, která obsahuje nástroj pro odemknutí disku datové schránky, a [obraťte se na podporu společnosti Microsoft](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [řešit problémy s ověřením](data-box-disk-troubleshoot.md).
