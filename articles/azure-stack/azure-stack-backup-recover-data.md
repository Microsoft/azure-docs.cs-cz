---
title: Obnovit z závažné ztrátě dat v zásobníku Azure pomocí služby zálohování infrastruktury | Microsoft Docs
description: Když způsobí, že k závažnému selhání zásobník Azure k selhání, můžete Pokud opětovným vytvořením nasazení Azure zásobník obnovit data vaší infrastruktury.
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
ms.openlocfilehash: 7ca3945dd3768ac71e0a962417f0a621aa83be1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075716"
---
# <a name="recover-from-catastrophic-data-loss"></a>Obnovit z závažné ztráty dat

*Platí pro: Azure zásobníku integrované systémy.*

Azure zásobníku spouští služby Azure ve vašem datovém centru. Azure zásobníku lze spustit v prostředí co čtyři uzly, které jsou nainstalované v jednom racku. Naproti tomu Azure běží ve více než 40 oblasti v několik datových center a několika zón v každé oblasti. Prostředky uživatelů může mít rozsah více serverů, stojany, datovými centry a oblastech. S Azure zásobníku aktuálně pouze máte možnost volby nasazení celý cloudu do jediného racku. To zpřístupňuje cloudu riziku závažné události v datovém centru nebo selhání z důvodu chyby hlavní produktu. Při havárii narazilo, instanci Azure zásobníku přejde do režimu offline. Všechna data, je potenciálně Neopravitelná.

V závislosti na hlavní příčinu ztrátě dat musíte opravit jedna infrastruktura služby nebo obnovit celý zásobník Azure instance. I budete muset obnovit na jiný hardware ve stejném umístění, nebo v jiném umístění.

Tento scénář adresy obnovení celé instalace v případě selhání zařízení a opakované nasazení privátního cloudu.

| Scénář                                                           | Ztrátě dat.                            | Požadavky                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Obnovit z závažné ztráty dat z důvodu chyb po havárii nebo produkt | Všechna data infrastruktury a uživatele a aplikace | Uživatelská aplikace a data jsou chráněny samostatně z infrastruktury dat |

## <a name="workflows"></a>Pracovní postupy

Cesty ochrany Azure spustit začíná zálohování dat infrastruktury a aplikace/klientů samostatně. Tento dokument popisuje, jak chránit infrastruktury. 

![Počáteční nasazení Azure zásobníku](media\azure-stack-backup\azure-stack-backup-workflow1.png)

Ve scénářích nejhorší případů kde dojde ke ztrátě všech dat obnovení zásobník Azure je proces obnovení jedinečné infrastruktury data pro toto nasazení Azure zásobníku a všechna uživatelská data. 

![Znovu nasaďte Azure zásobníku](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Obnovení

Pokud je závažné ztráty dat, ale je stále možné použít hardware, je třeba opakované nasazení Azure zásobníku. Během opětovného nasazení můžete zadat umístění úložiště a přihlašovací údaje potřebné pro přístup k zálohování. V tomto režimu není nutné specifikovat služby, které je nutné obnovit. Infrastruktura zálohování řadiče vloží stavu rovině řízení jako součást pracovního postupu nasazení.

Pokud dojde k havárii, který vykreslí hardware nepoužitelný, je možné na nový hardware pouze opětovného nasazení. Opětovné nasazení může trvat několik týdnů, při nahrazení hardwaru se seřadí a dorazí v datovém centru. Obnovení dat o rovině řízení je možné kdykoli. Obnovení však není podporována, pokud je verze opakovaně nasazeném instance více než jedna verze větší než je verze použitá v poslední zálohy. 

| Nasazení režimu | Počáteční bod | Koncový bod                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Čistá instalace   | Základní sestavení | Výrobce OEM nasadí zásobník Azure a aktualizuje na nejnovější podporovanou verzi.                                                                                                                                          |
| Obnovení režimu   | Základní sestavení | Výrobce OEM nasadí zásobník Azure v režimu obnovení a zpracovává verze odpovídající požadavky založené na nejnovější zálohu k dispozici. OEM dokončení nasazení aktualizací nejnovější podporovanou verzi. |

## <a name="data-in-backups"></a>Data v zálohování

Azure zásobníku podporuje typ nasazení názvem režimu obnovení cloudu. Tento režim se používá pouze v případě, že vyberete obnovení po havárii zásobník Azure nebo produktu chyb vykresluje neopravitelné řešení. V tomto režimu nasazení nedojde k odstranění žádná data uživatele uložena v řešení. Rozsah tomto režimu nasazení je omezený na obnovení následující data:

 - Vstupy nasazení
 - Systémy interní identit
 - Federované identifikovat konfigurace (odpojené nasazení)
 - Kořenové certifikáty používané interní certifikační autority
 - Azure Resource Manager konfigurace a dat uživatele, jako je například odběry, plány, nabídky a kvóty pro úložiště, sítě, výpočetní prostředky
 - KeyVault tajných klíčů a trezory
 - Přiřazení zásad RBAC a přiřazení rolí 

Žádný z uživatelů infrastruktury jako služby (IaaS) nebo platforma jako služba (PaaS) prostředky jsou obnoveny při nasazení. Virtuální počítače IaaS, účty úložiště, objekty BLOB, tabulek, konfigurace sítě a tak dále, který je se ztratí. Účelem cloudu pro obnovení je zajistit vaší operátory a uživatelé můžete přihlásit zpátky do portálu po dokončení nasazení. Zpět se přihlášení uživatelé neuvidí kterýkoli z jejich prostředků. Uživatelé mají svoje předplatné obnovit a společně s, který původní plány a nabízí zásady, které jsou definované správcem. Uživatelé protokolování zpět do systému funguje v rámci stejné omezení vyplývající z původní řešení před po havárii. Po dokončení obnovení cloudu, můžete ručně obnovit operátor přidanou hodnotou a RPs třetích stran a přidružená data.

## <a name="next-steps"></a>Další postup

 - Další informace o osvědčených postupech pro [pomocí služby zálohování infrastruktury](azure-stack-backup-best-practices.md).
