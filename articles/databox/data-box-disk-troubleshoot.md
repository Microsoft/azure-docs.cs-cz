---
title: Azure Data Box Disk – řešení potíží | Microsoft Docs
description: Popisuje, jak řešit potíže s Azure Data Box Diskem.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/03/2018
ms.author: alkohli
ms.openlocfilehash: e1a5cb33bb473daf5b9e45e7c64bcb297eca7733
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595541"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk-preview"></a>Řešení potíží s Azure Data Box Diskem (Preview)

Tento článek se týká služby Microsoft Azure Data Box ve verzi Preview. Popisuje některé komplexní pracovní postupy a úlohy správy, které lze provádět v rámci služby Data Box a se zařízením Data Box Disk. 

Data Box Disk je možné spravovat prostřednictvím webu Azure Portal. Tento článek se zaměřuje na úlohy, které můžete provést pomocí webu Azure Portal. Azure Portal můžete použít ke správě objednávek a zařízení a ke sledování stavu objednávek až do jejich dokončení.

Tento článek obsahuje následující návody:

- Stažení protokolů diagnostiky
- Dotazy na protokoly aktivit


> [!IMPORTANT]
> Data Box je ve verzi Preview. Než toto řešení nasadíte, přečtěte si [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="download-diagnostic-logs"></a>Stažení diagnostických protokolů

Pokud při procesu kopírování dat nedojde k žádným chybám, na portálu se objeví cesta ke složce, ve které najdete diagnostické protokoly. 

Diagnostické protokoly mohou zahrnovat:
- Protokoly chyb
- Podrobné protokoly  

Chcete-li se dostat do příslušného umístění a zkopírovat si protokoly, přejděte na účet úložiště spojený s vaší objednávkou Data Box. 

1.  Přejděte na **Obecné > Podrobnosti objednávky** a poznamenejte si účet úložiště přidružený k vaší objednávce.
 

2.  Přejděte na stránku **Všechny prostředky** a vyhledejte účet úložiště získaný v předchozím kroku. Vyberte a klikněte na účet úložiště.

    ![Kopírování protokolů 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Přejděte na **Blob service > Procházet objekty blob** a vyhledejte blob odpovídající účtu úložiště. Přejděte na **diagnosticslogcontainer > waies**. 

    ![Kopírování protokolů 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Měli byste vidět protokoly chyb i podrobné protokoly. Vyberte a klikněte na každý soubor a stáhněte si místní kopii.

## <a name="query-activity-logs"></a>Dotazy na protokoly aktivit

Protokoly aktivit můžete použít k vyhledání chyby při řešení potíží nebo k monitorování toho, jak uživatel ve vaší organizaci upravil prostředek. Na základě protokolů aktivit můžete zjistit:

- Jaké operace byly provedeny s prostředky ve vašem předplatném.
- Kdo operaci zahájil. 
- Kdy k operaci došlo.
- Stav operace.
- Hodnoty dalších vlastností, které vám mohou pomoci při zkoumání operace.

Protokoly aktivit obsahují všechny operace zápisu (jako PUT, POST nebo DELETE) prováděné s vašimi prostředky, ale neobsahuje operace čtení (např. GET). 

Protokoly aktivit se uchovávají 90 dnů. Můžete se dotazovat na libovolný rozsah kalendářních dat, pokud počáteční datum neleží více než 90 dnů v minulosti. Můžete také filtrovat protokol s použitím některého z předdefinovaných dotazů ve službě Insights. Například můžete kliknout na chybu a pak na konkrétní selhání, abyste zjistili hlavní příčinu.

## <a name="data-box-disk-unlock-tool-errors"></a>Chyby odemykacího nástroje Data Box Disku


| Chybová zpráva / chování nástroje      | Doporučení                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Žádný<br><br>Odemykací nástroj Data Box Disku skončí s chybou.                                                                            | BitLocker není nainstalovaný. Ujistěte se, že na hostitelském počítači, na kterém běží odemykací nástroj Data Box Disku, je nainstalovaný nástroj BitLocker.                                                                            |
| Aktuální rozhraní .NET Framework není podporováno. Podporovány jsou verze 4.5 a vyšší.<br><br>Nástroj skončí s chybovou zprávou.  | Rozhraní .NET 4.5 není nainstalováno. Na hostitelský počítač, na kterém běží odemykací nástroj Data Box Disku, nainstalujte rozhraní .NET 4.5 nebo novější.                                                                            |
| Nepodařilo se odemknout nebo ověřit žádné svazky. Obraťte se na podporu Microsoftu.  <br><br>Nástroji se nepodařilo odemknout nebo ověřit žádnou uzamčenou jednotku. | Nástroji se s použitím zadaného klíče nepodařilo odemknout žádnou ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu.                                                |
| Následující svazky jsou odemčené a ověřené. <br>Písmena jednotek: E:<br>Nepodařilo se odemknout žádné svazky s použitím následujících klíčů: werwerqomnf, qwerwerqwdfda <br><br>Nástroj odemkl některé jednotky a zobrazil písmena úspěšných a neúspěšných jednotek.| Částečný úspěch. Nástroji se s použitím zadaného klíče nepodařilo odemknout některé ze zamčených jednotek. O dalších krocích se poraďte s podporou Microsoftu. |
| Nepovedlo se najít zamčené svazky. Zkontrolujte, že je disk od Microsoftu správně připojený a zamčený.          | Nástroji se nepodařilo najít žádné zamčené jednotky. Buď jsou jednotky už odemčené, nebo nebyly nalezeny. Zkontrolujte, že jsou jednotky připojené a zamčené.                                                           |
| Závažná chyba: Neplatný parametr<br>Název parametru: invalid_arg<br>POUŽITÍ:<br>DataBoxDiskUnlock /PassKeys:<seznam_klíčů_oddělený_středníky><br><br>Příklad: DataBoxDiskUnlock /PassKeys:klíč1;klíč2;klíč3<br>Příklad: DataBoxDiskUnlock /SystemCheck<br>Příklad: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Tento klíč najdete v objednávce služby Azure Data Box Disk. Klíč vaše disky odemkne.<br>/Help:           Tato možnost poskytuje nápovědu k použití rutiny a příklady.<br>/SystemCheck:    Tato možnost zkontroluje, jestli váš systém splňuje požadavky na spuštění nástroje.<br><br>Nástroj ukončíte stisknutím libovolné klávesy. | Byl zadán neplatný parametr. Jediné povolené parametry jsou /SystemCheck /PassKey a /Help.                                                                            |
## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat Data Box Disk pomocí webu Azure Portal](data-box-portal-ui-admin.md).
