---
title: Infrastruktura služby zálohování osvědčené postupy pro Azure zásobníku | Microsoft Docs
description: Sadou osvědčených postupů můžete provést při nasazení a správě zásobník Azure ve vašem datovém centru pro zmírnění ztrátě dat, pokud dojde k závažné chybě.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: ec30832e6863ad92eff8f5c2e613adc503c73af5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="infrastructure-backup-service-best-practices"></a>Osvědčené postupy služby zálohování infrastruktury

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Osvědčené postupy můžete provést při nasazení a správě zásobník Azure ve vašem datovém centru pro zmírnění ztrátě dat v případě závažné selhání.

Měli byste zkontrolovat osvědčené postupy v pravidelných intervalech ověřte, zda vaše instalace je stále v dodržování předpisů při změně toku operaci. Microsoft Support by měl narazíte na problémy při implementaci těchto osvědčené postupy, požádejte o pomoc.

## <a name="configuration-best-practices"></a>Osvědčenými postupy konfigurace

### <a name="deployment"></a>Nasazení

Povolení zálohování infrastruktury po nasazení každý Cloud Azure zásobníku. Pomocí AzureStack-nástrojů můžete naplánovat zálohování z jakéhokoli klienta nebo serveru s přístupem ke koncovému bodu operátor správy rozhraní API.

### <a name="networking"></a>Sítě

Řetězec Universal Naming Convention (UNC) pro cestu musí používat platný plně kvalifikovaný název domény (FQDN). IP adresa je možné v případě, že překlad není možné. Řetězec UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení.

### <a name="encryption"></a>Šifrování

Šifrovací klíč se používá k šifrování zálohovaných dat, který získá exportovali do externího úložiště. Klíč lze vygenerovat pomocí nástrojů AzureStack. 

![AzureStack nástroje](media\azure-stack-backup\azure-stack-backup-encryption1.png)

Klíč musí být uložen v zabezpečeném umístění (například veřejný Azure Key Vault tajný klíč). Tento klíč musíte použít během opětovného nasazení Azure zásobníku. 

![Uložené klíče v zabezpečeném umístění.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Aplikovatelné nejlepší postupy

### <a name="backups"></a>Zálohování

 - Infrastruktura zálohování řadiče je potřeba spustit na vyžádání. Doporučuje se zálohování alespoň dvakrát za den.
 - Úlohy zálohování provést, pokud je spuštěn systém, neexistuje žádný výpadek správy nebo uživatelské aplikace. Očekávají, že úlohy zálohování trvat 20 40 minut pro řešení, které je možné logicky zatížení.
 - Pomocí OEM zadané instrukce, přepínače ručně záložní sítě a hostitele životního cyklu hardwaru (HLH) musí být uložené ve stejné zálohování sdílené složky, kde ovládacího prvku infrastruktura zálohování řadiče úložiště roviny zálohovaná data. Zvažte uložení přepínač a konfigurace HLH ve složce oblast. Pokud máte víc instancí Azure zásobníku ve stejné oblasti, zvažte použití identifikátor pro každou konfiguraci, která patří k jednotce škálování.

### <a name="folder-names"></a>Názvy složek

 - Infrastruktura vytvoří složku MASBACKUP automaticky. Toto je sdílenou složku spravovaný společností Microsoft. Můžete vytvořit sdílené složky na stejné úrovni jako MASBACKUP. Není doporučeno vytváření složek nebo úložiště dat v rámci MASBACKUP, která nevytváří zásobník Azure. 
 -  Uživatel plně kvalifikovaný název domény a oblast v názvu složky k odlišení zálohování dat z různých cloudů. Plně kvalifikovaný název domény (FQDN) nasazení Azure zásobníku a koncových bodů je kombinace parametr oblasti a parametr externí název domény. Další informace najdete v tématu [zásobník Azure datacenter integrace - DNS](azure-stack-integrate-dns.md).

Zálohování sdílené složce je například AzSBackups hostované na fileserver01.contoso.com. V této sdílené složce může být složka na Azure zásobníku nasazení pomocí názvu externí domény a do podsložky, která používá název oblasti. 

Plně kvalifikovaný název domény: contoso.com  
Oblast: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Složka MASBackup je, kde Azure zásobníku ukládá data zálohování. Tato složka byste neměli používat k ukládání svoje vlastní data. Výrobce OEM neměli používat tuto složku k uložení zálohy dat buď. 

Výrobci OEM doporučujeme ukládat zálohovaná data pro jejich součásti ve složce oblast. Každé síťové přepínače, hardwaru životního cyklu hostitele (HLH) a tak dále mohou být uloženy ve vlastní podsložce. Příklad:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitorování

Tyto výstrahy jsou podporovány v systému:

| Výstrahy                                                   | Popis                                                                                     | Náprava                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zálohování se nezdařilo, protože je sdílená složka mimo kapacity | Zálohování řadiče nelze exportovat záložní soubory do umístění sdílené složky je mimo kapacitu. | Přidat další kapacitu úložiště a zkuste zálohovat znovu. Odstraňte existující zálohy (od nejstarší nejprve) pro uvolnění místa.                    |
| Zálohování se nezdařilo kvůli problémům s připojením.             | Síť mezi zásobník Azure a soubor sdílené složky dochází k problémům.                          | Vyřešit problém sítě a operaci zálohování opakujte.                                                                                            |
| Zálohování se nezdařilo z důvodu chyby v cestě                | Cesta sdílení souborů nelze přeložit                                                          | Mapování sdílené položky z jiného počítače a zda že je přístupný sdílenou složku. Musíte aktualizovat cestu, pokud již není platný.       |
| Zálohování se nezdařilo z důvodu problému ověřování               | Může být problém s přihlašovacími údaji nebo potíže se sítí, který má dopad na ověřování.    | Mapování sdílené položky z jiného počítače a zda že je přístupný sdílenou složku. Pravděpodobně muset aktualizovat přihlašovací údaje, pokud již nejsou platné. |
| Zálohování se nezdařilo z důvodu obecné chyby                    | Z důvodu občasný problém může být chybných požadavků. Opakujte zálohování.                    | Obraťte se na podporu                                                                                                                               |

## <a name="next-steps"></a>Další postup

 - Zkontrolujte referenčního materiálu pro [infrastruktura zálohování služba](azure-stack-backup-reference.md).  
 - Povolit [infrastruktury služby zálohování](azure-stack-backup-enable-backup-console.md).