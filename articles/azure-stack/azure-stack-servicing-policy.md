---
title: Azure zásobníku obsluhy zásad | Microsoft Docs
description: Další informace o Azure zásobníku údržby zásad a jak zajistit, aby integrovaný systém v podporovaném stavu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: f495ca12e7cdb1bf61f09bd2d4a8a21654745d8a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-servicing-policy"></a>Údržba zásad Azure zásobníku
Tento článek popisuje údržby zásady pro Azure zásobníku integrované systémy a co je třeba udělat, aby byl váš systém v podporovaném stavu. 

## <a name="update-package-types"></a>Typy balíčku aktualizací

Existují dva typy balíčků aktualizací pro integrované systémy; Aktualizací softwaru společnosti Microsoft a aktualizace, které jsou specifické pro dodavatele hardwaru výrobce (OEM), jako jsou ovladače a firmware. Tyto aktualizace se dodávají jako samostatné balíčky aktualizací zásobník Azure a jsou spravovány samostatně.

- **Aktualizace softwaru Microsoft**. Microsoft je zodpovědná za začátku do konce životního cyklu údržby pro aktualizace softwaru společnosti Microsoft. Tyto balíčky můžete zahrnout nejnovější aktualizace zabezpečení systému Windows Server, se zabezpečením aktualizace a aktualizace funkcí Azure zásobníku. Tato aktualizace balíčky můžete stáhnout přímo od společnosti Microsoft.
- **Aktualizace pro zadaný dodavatele hardwaru OEM**. Azure zásobníku hardwarovými partnery jsou zodpovědní za začátku do konce údržby životní cyklus (včetně pokyny) pro související s hardwarem firmware a aktualizace balíčků ovladačů. Kromě toho Azure zásobníku hardwarovými partnery vlastní a Udržovat pokyny pro všechny softwarové a hardwarové na hostiteli životního cyklu hardwaru. Na dodavatele hardwaru pro výrobce OEM hostitelem tyto balíčky na vlastní web pro stahování aktualizací.

## <a name="update-package-release-cadence"></a>Aktualizace balíčku verze cadence

Microsoft očekává, že verze balíčky aktualizací softwaru v měsíčním cadence. Je však možné, že více nebo žádné aktualizace verze za měsíc. Výrobce OEM výrobci hardwaru verze jejich aktualizace na podle potřeby.

Balíček Microsoft aktualizace má následující zásady vytváření názvů, který vám pomůže snadno identifikovat datum vydání:

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

Aktualizace softwaru Microsoft vydala 15. června 2017 by měla mít například verze "1.0.170615.1".

## <a name="keep-your-system-under-support"></a>Udržování systému v rámci podpory

Pokud chcete získat podporu pro systém, je nutné zachovat zásobník Azure aktualizovat v určitém časovém intervalu. Naše zásady pro odložení aktualizací softwaru společnosti Microsoft jsou tři měsíce. Pokud je systém tři měsíce zastaralé, se považují za mimo dodržování předpisů. Je třeba aktualizovat systém na alespoň minimální podporovaná verze Odborná pomoc. 

Balíčky aktualizací softwaru společnosti Microsoft jsou oddělené a vyžaduje, aby předchozí balíček aktualizace předpokladem je. Pokud se rozhodnete odložení jeden nebo více aktualizací, zvažte celkové modulu runtime, pokud chcete získat nejnovější verzi.

Následující tabulka uvádí příklad aktualizace balíčku verze, jejich požadovaných součástí a minimální podporovaná verze systému musí být na pro zachování podpory. Tabulka je založená na počáteční verzi Azure zásobníku integrované systémy (sestavení. 1708), verze první aktualizace balíčku. (1709) v září 2017. 

| Nejnovější balíček aktualizace (*příklad*) | Požadavek | Minimální podporovaná verze |
| -- | -- | -- |
| 1710 | 1709 | neuvedeno |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1801 | 1712 |
| 1803 | 1802 | 1801 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 

## <a name="next-steps"></a>Další postup

- [Správa aktualizací v Azure zásobníku](azure-stack-updates.md)


