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
ms.date: 04/03/2018
ms.author: mabrigg
ms.openlocfilehash: e37b63580d8cea4b5772bc54f7b2f79980afc0bc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
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
Chcete-li pokračovat, pokud chcete získat podporu, musí udržovat nasazením Azure zásobníku aktuální. Zásady pro odložení aktualizací je, že pro Azure zásobník zůstat v podporu, musí se nedávno vydaných verzí aktualizace nebo spusťte některý z dvě předchozí verze hlavní aktualizace.  Opravy hotfix nejsou považovány za verze hlavní aktualizací.  Pokud vaše zásobník Azure cloud je za *více než dvě aktualizace*, se považuje za mimo dodržování předpisů a musíte aktualizovat alespoň minimální podporovaná verze Odborná pomoc. 

Například pokud je 1805 nejnovější dostupné aktualizace verze a předchozí dva balíčky aktualizací byly verze 1804 a 1803, 1803 i 1804 zůstanou na podporu. 1802 však není podporován. Zásady platí, pokud neexistuje žádná verze pro měsíc nebo dvě. Například pokud aktuální verze je 1805 a došlo bez uvolnění 1804, předchozí dva balíčky aktualizací 1803 a 1802 by zůstanou na podporu.

Balíčky aktualizací softwaru společnosti Microsoft jsou oddělené a vyžaduje, aby předchozí balíček aktualizace předpokladem je. Pokud se rozhodnete odložení jeden nebo více aktualizací, zvažte celkové modulu runtime, pokud chcete získat nejnovější verzi. 


## <a name="next-steps"></a>Další postup

- [Správa aktualizací v Azure zásobníku](azure-stack-updates.md)


