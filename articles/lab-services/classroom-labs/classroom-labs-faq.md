---
title: Laboratoře učeben v Azure Lab Services – nejčastější dotazy | Microsoft Docs
description: V Azure Lab Services najdete odpovědi na běžné otázky týkající se učebn Labs.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 411037dd97350d877aff4e2d094c3408f168f9fd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648582"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Cvičení v učebně Azure Lab Services – nejčastější dotazy (FAQ)
Získejte odpovědi na některé nejčastější dotazy týkající se učebn Labs v Azure Lab Services. 

## <a name="quotas"></a>Kvóty

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Je kvóta na uživatele nebo za týden nebo na celou dobu trvání testovacího prostředí? 
Kvóta, kterou nastavíte pro testovací prostředí, je určena pro každého studenta po celou dobu trvání testovacího prostředí. A [plánovaná doba spuštění virtuálních počítačů](how-to-create-schedules.md) se nepočítá s kvótou přidělenou uživateli. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích.  Další informace o kvótách najdete v tématu [nastavení kvót pro uživatele](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Plány

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Spustí se všechny virtuální počítače v testovacím prostředí automaticky při nastavení plánu? 
Ne. Ne všechny virtuální počítače. Pouze virtuální počítače, které jsou přiřazeny uživatelům podle plánu. Virtuální počítače, které nejsou přiřazeny uživateli, se automaticky nespustí. Je to záměrné. 

## <a name="lab-accounts"></a>Účty testovacího prostředí

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Proč není možné vytvořit testovací prostředí z důvodu nedostupnosti rozsahu adres? 
Vývojové laboratoře můžou vytvořit testovací virtuální počítače v rámci rozsahu IP adres, který zadáte při vytváření účtu testovacího prostředí v Azure Portal. Když je poskytnutý rozsah adres, každé testovací prostředí, které se vytvoří po přidělené IP adrese pro virtuální počítače v testovacím prostředí, se 512. Rozsah adres pro účet testovacího prostředí musí být dostatečně velký, aby vyhovoval všem laboratořím, které v rámci účtu testovacího prostředí máte v úmyslu vytvořit. 

Například pokud máte blok z/19-10.0.0.0/19, tento rozsah adres bude vyhovovat 8192 IP adres a 16 Labs (8192/512 = 16 Labs). V tomto případě se vytvoření testovacího prostředí v testovacím prostředí 17 nezdařilo.

## <a name="blog-post"></a>Příspěvek blogu
Přihlaste se k odběru [blogu Azure Lab Services](https://azure.microsoft.com/blog/tag/azure-lab-services/).

## <a name="update-notifications"></a>Aktualizovat oznámení
Přihlaste se k odběru [aktualizací testovacích služeb](https://azure.microsoft.com/updates/?product=lab-services) a udržujte si informace o nových funkcích v testovacích službách.

## <a name="general"></a>Obecné
### <a name="what-if-my-question-isnt-answered-here"></a>Co když tady není odpověď na moji otázku?
Pokud tady uvedený dotaz není, sdělte nám, abychom vám pomohli najít odpověď.

- Na konci těchto nejčastějších dotazů si vystavte otázku. 
- Pokud chcete oslovit širší cílovou skupinu, odešlete dotaz na [fórum Azure Lab Services-Stack Overflow](https://stackoverflow.com/questions/tagged/azure-lab-services). 
- V případě žádostí o funkce odešlete žádosti a nápady do [Azure Lab Services hlas uživateli](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

