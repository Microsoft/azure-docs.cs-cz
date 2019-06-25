---
title: Řešení potíží Azure Data Box Disk disk odemknutí problémy | Dokumentace Microsoftu
description: Popisuje, jak řešit potíže s Azure Data Box Diskem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148280"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Řešení potíží s disk odemknutí v disku Azure Data Box

V tomto článku se vztahuje na Microsoft Azure Data Box Disk a popisuje pracovních postupech, používat k řešení potíží při používání nástroje odemknout. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Chyby odemykacího nástroje Data Box Disku


| Chybová zpráva / chování nástroje      | Doporučení                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Aktuální rozhraní .NET Framework není podporováno. Podporovány jsou verze 4.5 a vyšší.<br><br>Nástroj skončí s chybovou zprávou.  | Rozhraní .NET 4.5 není nainstalováno. Na hostitelský počítač, na kterém běží odemykací nástroj Data Box Disku, nainstalujte rozhraní .NET 4.5 nebo novější.                                                                            |
| Nepodařilo se odemknout nebo ověřit žádné svazky. Obraťte se na podporu Microsoftu.  <br><br>Nástroji se nepodařilo odemknout nebo ověřit žádnou uzamčenou jednotku. | Nástroji se s použitím zadaného klíče nepodařilo odemknout žádnou ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu.                                                |
| Následující svazky jsou odemčené a ověřené. <br>Svazek písmena jednotek: E:<br>Nepodařilo se odemknout žádné svazky s použitím následujících klíčů: werwerqomnf, qwerwerqwdfda <br><br>Nástroj odemkl některé jednotky a zobrazil písmena úspěšných a neúspěšných jednotek.| Částečný úspěch. Nástroji se s použitím zadaného klíče nepodařilo odemknout některé ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu. |
| Nepovedlo se najít zamčené svazky. Zkontrolujte, že je disk od Microsoftu správně připojený a zamčený.          | Nástroji se nepodařilo najít žádné zamčené jednotky. Buď jsou jednotky už odemčené, nebo nebyly nalezeny. Zkontrolujte, že jsou jednotky připojené a zamčené.                                                           |
| Závažná chyba: Neplatný parametr<br>Název parametru: invalid_arg<br>POUŽITÍ:<br>DataBoxDiskUnlock /PassKeys:<seznam_klíčů_oddělený_středníky><br><br>Příklad: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Příklad: DataBoxDiskUnlock /SystemCheck<br>Příklad: / Help DataBoxDiskUnlock<br><br>/ Klíčů:       Tento klíč získáte z objednávka disku Azure DataBox. Klíč vaše disky odemkne.<br>/ Help:           Tato možnost poskytuje nápovědy k rutině využití a příklady.<br>/ SystemCheck:    Tato možnost zkontroluje, pokud váš systém splňuje požadavky na spuštění nástroje.<br><br>Nástroj ukončíte stisknutím libovolné klávesy. | Byl zadán neplatný parametr. Pouze povolené parametry jsou /SystemCheck /PassKey a/Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Odemknout problémy pro disky při používání klienta Windows

Tato část podrobně popisuje některé z hlavních problémů při používání klienta Windows pro kopírování dat ve během nasazení disku Data Box.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problém: Nepovedlo se odemknout jednotku z nástroje BitLocker
 
**Příčina** 

Použili jste heslo v dialogovém okně nástroje BitLocker a pokusu o odemknutí disku pomocí nástroje BitLocker odemknutí jednotky dialogu. To nebude fungovat.

**Řešení**

Odemknout disků Data Box, budete muset použít nástroj Data Box Disk odemknutí a zadejte heslo z portálu Azure portal. Další informace najdete v části [kurzu: Rozbalit, připojení a odemknutí disku Azure Data Box](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problém: Nepodařilo se odemknout nebo ověřit některé svazky. Obraťte se na podporu Microsoftu.
 
**Příčina**

Může se zobrazit následující chyba v protokolu chyb a nejsou odemknout nebo ověřit některé svazky.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
To znamená, že jsou pravděpodobně chybí odpovídající verzi prostředí Windows PowerShell na klientovi Windows.

**Řešení**

Můžete nainstalovat [v prostředí Windows PowerShell 5.0](https://www.microsoft.com/download/details.aspx?id=54616) a zkuste operaci zopakovat.
 
Pokud jste pořád nejste schopni odemknout svazky, zkopírovat protokoly ze složky, který má nástroj Data Box Disk odemknutí a [obraťte se na Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [řešit problémy s ověřením](data-box-disk-troubleshoot.md).
