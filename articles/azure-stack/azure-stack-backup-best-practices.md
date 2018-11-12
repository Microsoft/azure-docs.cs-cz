---
title: Infrastruktura služby Backup osvědčené postupy pro Azure Stack | Dokumentace Microsoftu
description: Sada osvědčených postupů můžete použít při nasazení a Správa služby Azure Stack ve vašem datovém centru, aby mohlo včas reagovat ztrátu dat při katastrofických selhání.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: ec17f6923fc1c928f24fcb762daedbaea5b688ac
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035097"
---
# <a name="infrastructure-backup-service-best-practices"></a>Osvědčené postupy infrastruktury služby Backup

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Osvědčené postupy můžete použít při nasazení a Správa služby Azure Stack ve vašem datovém centru, aby mohlo včas reagovat ztrátě dat v případě závažného selhání.

Přečtěte si osvědčené postupy v pravidelných intervalech ověření, že instalace je stále v dodržování předpisů při změně tok operace. Budete mít nějaké problémy při implementaci těchto osvědčených postupů, požádejte o pomoc Microsoft Support.

## <a name="configuration-best-practices"></a>Osvědčenými postupy konfigurace

### <a name="deployment"></a>Nasazení

Povolte zálohování infrastruktury po nasazení každý Cloud Azure Stack. Pomocí Azure Stack Powershellu můžete naplánovat zálohování z jakéhokoli klienta nebo serveru s přístupem na koncový bod rozhraní API pro správu operátor.

### <a name="networking"></a>Sítě

Řetězec konvenci UNC (Universal Naming) pro cestu musí používat použitím plně kvalifikovaného názvu domény (FQDN). IP adresa je možný v případě, že překlad názvů není možné. Řetězec ve formátu UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení.

### <a name="encryption"></a>Šifrování

Šifrovací klíč se používá k šifrování zálohovaných dat, které bude exportováno do externího úložiště. Klíč je generována jako součást [povolení zálohování pro Azure Stack s prostředím PowerShell](azure-stack-backup-enable-backup-powershell.md).

Klíč musí být uložen na bezpečném místě (například veřejné služby Azure Key Vault tajný klíč). Tento klíč musíte použít při opětovné nasazení Azure stacku. 

![Uloží klíč na bezpečném místě.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Aplikovatelné nejlepší postupy

### <a name="backups"></a>Zálohování

 - Úlohy zálohování provést při spuštěném systému, takže neexistuje žádný výpadek prostředí pro správu nebo uživatelské aplikace. Očekávané úlohy zálohování trvat 20 – 40 minut pro řešení, které je v rozumné zatížení.
 - Pomocí pokyny výrobce OEM ručně záložní síťové přepínače a životního cyklu hostitelů hardwaru (HLH) musí být uložené ve stejné zálohování sdílené složky, kde roviny řízení úložišť zálohování Kontroleru infrastruktury zálohovaná data. Zvažte uložení přepínače a konfigurací HLH ve složce oblasti. Pokud máte více instancí služby Azure Stack ve stejné oblasti, zvažte použití identifikátor pro každou konfiguraci, která patří k jednotce škálování.

### <a name="folder-names"></a>Názvy složek

 - Infrastruktury automaticky vytvoří MASBACKUP složku. To je sdílená složka spravovaná Microsoftem. Sdílené složky můžete vytvořit na stejné úrovni jako MASBACKUP. Není doporučeno vytváření složek nebo úložiště dat v rámci MASBACKUP, který nevytváří Azure Stack. 
 -  Uživatel plně kvalifikovaný název domény a oblasti v názvu složky k rozlišení zálohovaná data z různých cloudů. Plně kvalifikovaný název domény (FQDN) koncových bodů a nasazení Azure Stack je kombinací parametrů oblasti a parametr externí název domény. Další informace najdete v tématu [integrace datových center Azure Stack – DNS](azure-stack-integrate-dns.md).

Sdílené složky záloh je například AzSBackups hostitelem fileserver01.contoso.com. V této sdílené složce souboru může být složka jedno nasazení služby Azure Stack pomocí názvu externí domény a do podsložky, která používá název oblasti. 

Plně kvalifikovaný název domény: contoso.com  
Oblast: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Složka MASBackup je, kde Azure Stack ukládá svých dat záloh. Tuto složku byste neměli používat k ukládání svoje vlastní data. Výrobce OEM neměli používat této složky k ukládání dat záloh buď. 

Výrobci OEM doporučujeme zálohovat data pro jejich komponent ve složce oblasti. Každé síťové přepínače, hardware životního cyklu hostitelů (HLH) a tak dále mohou uloženy ve vlastní podsložce. Příklad:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitorování

Tyto výstrahy jsou podporovány v systému:

| Výstrahy                                                   | Popis                                                                                     | Náprava                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zálohování selhalo, protože sdílená složka je mimo kapacitu | Sdílená složka je mimo kapacitu a zálohování řadiče nelze exportovat záložní soubory do umístění. | Přidejte další kapacitu úložiště a opakujte zálohování. Odstraňte existující zálohy (od nejstarší nejprve) pro uvolnění místa.                    |
| Zálohování nebylo úspěšné kvůli problémům s připojením.             | Síť mezi Azure Stack a soubor sdílené složky se setkává s problémy.                          | Řešení problémů se sítí a opakujte zálohování.                                                                                            |
| Zálohování se nezdařilo z důvodu chyby v cestě                | Cesta ke sdílené složce souboru se nepodařilo najít                                                          | Mapování sdílené složky z jiného počítače zajistit, že je sdílená složka přístupná. Budete muset aktualizovat cestu, pokud již není platný.       |
| Zálohování se nezdařilo z důvodu potíží s ověřováním               | Může být problém s přihlašovacími údaji nebo potíže se sítí, která má vliv na ověřování.    | Mapování sdílené složky z jiného počítače zajistit, že je sdílená složka přístupná. Budete muset aktualizovat přihlašovací údaje, pokud již nejsou platné. |
| Zálohování se nezdařilo z důvodu obecné chyby                    | Neúspěšné žádosti může být způsobeno o dočasný problém. Zkuste zálohování znovu.                    | Obraťte se na podporu                                                                                                                               |

## <a name="next-steps"></a>Další postup

Projděte si referenční materiál pro [infrastruktury služby Backup](azure-stack-backup-reference.md).

Povolit [zálohovací služby infrastruktury](azure-stack-backup-enable-backup-console.md).
