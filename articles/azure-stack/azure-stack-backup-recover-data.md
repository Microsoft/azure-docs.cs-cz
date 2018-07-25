---
title: Obnovit ze ztráty dat v pomocí infrastruktury služby zálohování Azure stacku | Dokumentace Microsoftu
description: Při katastrofických selhání způsobuje Azure Stack selhání, můžete obnovit data vaší infrastruktury při obnovení vašeho nasazení Azure stacku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: f1582efa7d357f6f535c562a656ec17024357320
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242865"
---
# <a name="recover-from-catastrophic-data-loss"></a>Obnovit ze ztráty dat

*Platí pro: integrované systémy Azure Stack.*

Azure Stack spouští služby Azure ve vašem datovém centru. Azure Stack můžete spustit v prostředí malá jako čtyři uzly, které jsou nainstalované v jednom racku. Naproti tomu Azure běží ve více než 40 oblastech v několika datových centrech a více zón v jednotlivých oblastech. Prostředky uživatelů se týkají více servery, stojany, datových center a oblastí. Pomocí služby Azure Stack aktuálně pouze máte možnost nasadit celý cloud jeden stojan. Tato třída zveřejňuje cloudu k riziku katastrofickými událostmi v datovém centru nebo chyby způsobené chyb produktů. V případě náhlé havárie, instance služby Azure Stack přejde do režimu offline. Všechna data, je potenciálně Neopravitelná.

V závislosti na hlavní příčinu ztráty dat budete muset službu jediné infrastruktury opravit nebo obnovit celou instanci služby Azure Stack. Dokonce i budete muset obnovit na jiný hardware ve stejném umístění nebo v jiném umístění.

Tento scénář adresy obnovení v případě selhání celé instalace zařízení a opětovné nasazení privátního cloudu.

| Scénář                                                           | Ztráta dat                            | Požadavky                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Obnovit ze ztráty dat z důvodu chyby po havárii nebo produktu | Všechna data infrastruktury a uživatele i aplikace | Uživatelská aplikace a data jsou chráněné odděleně od dat infrastruktury |

## <a name="workflows"></a>Pracovní postupy

Cesty ochrany Start pro Azure začíná zálohování dat infrastruktury a aplikací nebo tenantovi samostatně. Tento dokument popisuje, jak ochránit infrastrukturu. 

![Počáteční nasazení služby Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow1.png)

V nejhorším scénáře kde dojde ke ztrátě všech dat obnovení služby Azure Stack je proces obnovení infrastruktury dat jedinečné, že nasazení Azure Stack a všechna uživatelská data. 

![Opětovné nasazení Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Obnovení

Pokud je ztrátě dat, ale hardware je stále možné použít, vyžaduje se opětovné nasazení Azure stacku. Během opětovného nasazení můžete určit umístění úložiště a přihlašovacích údajů potřebných pro přístup k zálohování. V tomto režimu je potřeba uvést služby, které je potřeba obnovit. Záložní řadič infrastruktury vkládá stavu roviny řízení jako součást pracovního postupu nasazení.

Pokud nedojde k havárii, který vykreslí hardware nepůjdou použít, je pouze opětovné nasazení na nový hardware. Opětovné nasazení může trvat několik týdnů, zatímco je seřazen náhradní hardware a dorazí v datovém centru. Obnovení dat rovina řízení je možné kdykoli. Nicméně obnovení se nepodporuje, pokud zadaná verze instance opakovaně nasazeném více než jedna verze větší než je verze použitá v poslední záloze. 

| Režim nasazení | Počáteční bod | Koncový bod                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Čistá instalace   | Základní sestavení | Výrobce OEM nasadí Azure Stack a aktualizuje na nejnovější verzi.                                                                                                                                          |
| Režim obnovení   | Základní sestavení | Výrobce OEM nasadí Azure Stack v režimu obnovení a zpracovává verzi, která odpovídá požadavky založené na poslední zálohu k dispozici. Výrobce OEM dokončí nasazení aktualizací na nejnovější verzi. |

## <a name="data-in-backups"></a>Data zálohy

Azure Stack podporuje typy nasazení volá režimu obnovení cloudu. Tento režim se používá jenom v případě, že vyberete obnovení po havárii Azure Stack nebo chyb produktu vykreslen řešení neobnovitelná. Tento režim nasazení nedojde k odstranění všech uživatelských dat uložených v řešení. Rozsah tento režim nasazení je omezená na obnovení následujících dat:

 - Vstupy nasazení.
 - Systémy interní identit
 - Federované určit konfiguraci (odpojené nasazení)
 - Kořenové certifikáty použít interní certifikační autoritou
 - Azure Resource Manageru konfigurace a dat uživatele, jako je například odběry, plány, nabídky a kvóty pro úložiště, sítě, výpočetních prostředků
 - Tajné kódy KeyVault a úložišť
 - Přiřazení zásad RBAC a přiřazení rolí 

Žádný z uživatelů infrastruktura jako služba (IaaS) nebo platforma jako služba (PaaS) prostředky se dají obnovit během nasazení. To znamená virtuální počítače IaaS, účty úložiště, objekty BLOB, tabulek, konfigurace sítě a tak dále, se ztratí. Cloudové zotavení slouží k zajištění operátory a uživatelé můžou zpět do portálu po dokončení nasazení. Opětovné přihlášení uživatelé neuvidí všechny svoje prostředky. Uživatelé mají svá předplatná obnovit, a společně s, která původní plány a nabízí zásady definované správcem. Uživatelé přihlašující zpět do systému funguje v rámci stejné omezení vyplývající z původního řešení před po havárii. Po dokončení obnovení cloudu, můžete ručně obnovit operátor přidanou hodnotu a RPs třetích stran a související data.

## <a name="next-steps"></a>Další postup

 - Další informace o osvědčených postupech pro [pomocí infrastruktury služby Backup](azure-stack-backup-best-practices.md).