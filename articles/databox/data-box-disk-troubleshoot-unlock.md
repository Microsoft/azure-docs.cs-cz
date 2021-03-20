---
title: Azure Data Box Disk řešení potíží s odemknutím disku | Microsoft Docs
description: Přečtěte si o pracovních postupech pro řešení potíží s nástrojem pro odemknutí pomocí Azure Data Box Disk. Informace o chybách nástrojů Data Box Disk odemkněte.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87925557"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Řešení potíží při odemykání disků v Azure Data Box Disk

Tento článek se týká Microsoft Azure Data Box Disk a popisuje pracovní postupy, které se používají k odstraňování potíží při použití nástroje odemknout. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Chyby odemykacího nástroje Data Box Disku


| Chybová zpráva / chování nástroje      | Doporučení                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Aktuální rozhraní .NET Framework není podporováno. Podporovány jsou verze 4.5 a vyšší.<br><br>Nástroj skončí s chybovou zprávou.  | Rozhraní .NET 4.5 není nainstalováno. Na hostitelský počítač, na kterém běží odemykací nástroj Data Box Disku, nainstalujte rozhraní .NET 4.5 nebo novější.                                                                            |
| Nepodařilo se odemknout nebo ověřit žádné svazky. Obraťte se na podporu Microsoftu.  <br><br>Nástroji se nepodařilo odemknout nebo ověřit žádnou uzamčenou jednotku. | Nástroji se s použitím zadaného klíče nepodařilo odemknout žádnou ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu.                                                |
| Následující svazky jsou odemčené a ověřené. <br>Písmena jednotek: E:<br>Nepodařilo se odemknout žádné svazky s použitím následujících klíčů: werwerqomnf, qwerwerqwdfda <br><br>Nástroj odemkl některé jednotky a zobrazil písmena úspěšných a neúspěšných jednotek.| Částečný úspěch. Nástroji se s použitím zadaného klíče nepodařilo odemknout některé ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu. |
| Nepovedlo se najít zamčené svazky. Zkontrolujte, že je disk od Microsoftu správně připojený a zamčený.          | Nástroji se nepodařilo najít žádné zamčené jednotky. Buď jsou jednotky už odemčené, nebo nebyly nalezeny. Zkontrolujte, že jsou jednotky připojené a zamčené. <br> <br>Tato chyba se může zobrazit také v případě, že jste naformátovali disky. Pokud jste své disky naformátovali, teď jsou nepoužitelné. O dalších krocích se poraďte s podporou Microsoftu.                                                          |
| Závažná chyba: Neplatný parametr<br>Název parametru: invalid_arg<br>POUŽITÍ:<br>DataBoxDiskUnlock /PassKeys:<seznam_klíčů_oddělený_středníky><br><br>Příklad: DataBoxDiskUnlock /PassKeys:klíč1;klíč2;klíč3<br>Příklad: DataBoxDiskUnlock /SystemCheck<br>Příklad: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Tento klíč najdete v objednávce služby Azure Data Box Disk. Klíč vaše disky odemkne.<br>/Help:           Tato možnost poskytuje nápovědu k použití rutiny a příklady.<br>/SystemCheck:    Tato možnost zkontroluje, jestli váš systém splňuje požadavky na spuštění nástroje.<br><br>Nástroj ukončíte stisknutím libovolné klávesy. | Byl zadán neplatný parametr. Jediné povolené parametry jsou/SystemCheck,/PassKey a/help..|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Odemknout problémy s disky při použití klienta Windows

Tato část podrobně popisuje některé hlavní problémy, na které čelí během nasazování Data Box Disk při použití klienta Windows pro kopírování dat.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problém: jednotku nejde odemknout pomocí BitLockeru.
 
**Příčina** 

Heslo jste použili v dialogovém okně BitLocker a pokusíte se odemknout disk přes dialogové okno jednotky odemčení BitLockerem. Tato činnost nebude fungovat.

**Řešení**

Chcete-li odemknout Data Box disky, je nutné použít nástroj Data Box Disk Unlock a zadat heslo z Azure Portal. Další informace najdete v [kurzu: rozbalení, připojení a odemčení Azure Data box disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problém: nepovedlo se odemknout nebo ověřit některé svazky. Obraťte se na podporu Microsoftu.
 
**Příčina**

V protokolu chyb se může zobrazit následující chyba a není možné odemknout nebo ověřit některé svazky.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
To znamená, že na vašem klientovi Windows pravděpodobně chybí odpovídající verze Windows PowerShellu.

**Řešení**

Můžete nainstalovat [prostředí Windows PowerShell v 5,0](https://www.microsoft.com/download/details.aspx?id=54616) a operaci zopakovat.
 
Pokud stále nemůžete odemknout svazky, zkopírujte protokoly ze složky, ve které je nástroj Data Box Disk Unlock, a obraťte se na [Podpora Microsoftu](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Další kroky

- Naučte se [řešit problémy s ověřením](data-box-disk-troubleshoot.md).
