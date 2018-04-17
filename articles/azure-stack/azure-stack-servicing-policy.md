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
ms.date: 04/09/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 160ba42c5cbdd3e8b999040cba8254d4c87f7c63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-stack-servicing-policy"></a>Údržba zásad Azure zásobníku
Tento článek popisuje údržby zásady pro Azure zásobníku integrované systémy a co je třeba udělat, aby byl váš systém v podporovaném stavu. 

## <a name="update-package-types"></a>Typy balíčku aktualizací

Existují dva typy balíčky aktualizací pro integrované systémy: 

- **Aktualizace softwaru Microsoft**. Microsoft je zodpovědná za začátku do konce životního cyklu údržby pro aktualizace softwaru společnosti Microsoft. Tyto balíčky můžete zahrnout nejnovější aktualizace zabezpečení systému Windows Server, se zabezpečením aktualizace a aktualizace funkcí Azure zásobníku. Tato aktualizace balíčky můžete stáhnout přímo od společnosti Microsoft.

- **Aktualizace pro zadaný dodavatele hardwaru OEM**. Azure zásobníku hardwarovými partnery jsou zodpovědní za začátku do konce údržby životní cyklus (včetně pokyny) pro související s hardwarem firmware a aktualizace balíčků ovladačů. Kromě toho Azure zásobníku hardwarovými partnery vlastní a Udržovat pokyny pro všechny softwarové a hardwarové na hostiteli životního cyklu hardwaru. Na dodavatele hardwaru pro výrobce OEM hostitelem tyto balíčky na vlastní web pro stahování aktualizací.


## <a name="update-package-release-cadence"></a>Aktualizace balíčku verze cadence
Microsoft očekává, že verze balíčky aktualizací softwaru v měsíčním cadence. Je však možné, že více nebo žádné aktualizace verze za měsíc. Výrobce OEM výrobci hardwaru verze jejich aktualizace na podle potřeby. 

Vyhledat dokumentaci pro plánování a správě aktualizací a jak určit vaší aktuální verzí v [spravovat aktualizace přehled](azure-stack-updates.md). Informace o konkrétní aktualizaci, včetně toho, jak stáhnout, naleznete v poznámkách k verzi, aktualizovat: 
- [Azure aktualizace 1803 zásobníku](azure-stack-update-1803.md)
- [Azure aktualizace 1802 zásobníku](azure-stack-update-1802.md)
- [Azure aktualizace 1712 zásobníku](azure-stack-update-1712.md)



## <a name="hotfixes"></a>Opravy hotfix
V některých případech společnost Microsoft poskytuje opravy hotfix pro Azure zásobníku tuto adresu určitého problému, který je často preventivní nebo čase.  Každá oprava hotfix je vydané s odpovídající článek znalostní báze Microsoft Knowledge Base, který podrobně popisuje problém, příčina a řešení. 

Opravy hotfix jsou stáhnout a nainstalovat stejně jako balíčky pravidelné úplné aktualizace pro Azure zásobníku. Na rozdíl od úplné aktualizace, ale opravy hotfix můžete nainstalovat v minutách. Doporučujeme, aby že Azure zásobníku operátory nastavení časového období údržby při instalaci opravy hotfix. Opravy hotfix aktualizovat verzi vašeho cloudu Azure zásobníku tak může snadno zjistit, pokud byl použit opravy hotfix. Samostatná oprava hotfix se poskytuje pro každou verzi zásobník Azure, která je stále v podpory. Každý opravu pro konkrétní iterace kumulativní a zahrnuje předchozí aktualizace pro stejnou verzi. Další informace o použitelnosti konkrétní opravu hotfix v opravy, odpovídající znalostní báze Knowledge Base, článek.  


## <a name="keep-your-system-under-support"></a>Udržování systému v rámci podpory
Chcete-li pokračovat, pokud chcete získat podporu, musí udržovat nasazením Azure zásobníku aktuální. Tato zásada odložení aktualizací je: pro vaše nasazení Azure zásobníku zůstat v podporu, musí se nedávno vydaných verzí aktualizace nebo spusťte některý z dvě předchozí verze aktualizace. Opravy hotfix nejsou považovány za verze hlavní aktualizací. Pokud vaše zásobník Azure cloud je za *více než dvě aktualizace*, se považuje za mimo dodržování předpisů a musíte aktualizovat alespoň minimální podporovaná verze Odborná pomoc. 

Například pokud je 1805 nejnovější dostupné aktualizace verze a předchozí dva balíčky aktualizací byly verze 1804 a 1803, 1803 i 1804 zůstanou na podporu. 1802 však není podporován. Zásady platí, pokud neexistuje žádná verze pro měsíc nebo dvě. Například pokud aktuální verze je 1805 a došlo bez uvolnění 1804, předchozí dva balíčky aktualizací 1803 a 1802 zůstanou na podporu.

Balíčky aktualizací softwaru společnosti Microsoft jsou oddělené a vyžaduje, aby předchozí balíček aktualizace předpokladem je. Pokud se rozhodnete odložení jeden nebo více aktualizací, zvažte celkové modulu runtime, pokud chcete získat nejnovější verzi. 


## <a name="next-steps"></a>Další postup

- [Správa aktualizací v Azure zásobníku](azure-stack-updates.md)


