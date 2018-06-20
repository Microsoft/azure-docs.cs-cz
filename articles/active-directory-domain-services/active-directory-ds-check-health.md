---
title: Služba Azure AD Domain Services – zkontrolujte stav vaší spravované domény | Microsoft Docs
description: Zkontrolujte stav vaší spravované domény pomocí stránky stavu na portálu Azure.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: ffb00ce85a06812dfc42dfbbba74f712c1877615
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212371"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Zkontrolujte stav spravované doméně služby Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Přehled stavu stránky
Pomocí stránky stavu na portálu Azure, budete moci zachovat aktuální na co se děje na vaší spravované domény. Tento článek vás provede prvků na stránce stavu.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Postup zobrazení stavu služby vaší spravované domény
1. Přejděte na [stránky Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na portálu Azure.
2. Klikněte na domény, které chcete zobrazit stav.
3. V levém navigačním podokně klikněte na tlačítko **stavu**.

Následující obrázek znázorňuje stránku stavu ukázka: ![příklad stavu stránky](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> Stav vaší spravované domény vyhodnotí každou hodinu. Po provedení změn vaší spravované domény, počkejte na další cyklus vyhodnocení zobrazit aktualizovaný stav vaší spravované domény. Časové razítko "Naposled vyhodnoceno" v pravém horním rohu zobrazuje, kdy bylo naposled vyhodnoceno stav vaší spravované domény.
>

### <a name="status-of-your-managed-domain"></a>Stav vaší spravované domény
Tento stav v horní části stránky napravo od vaší stavu označuje, celkový stav vaší spravované domény. Stav faktory všechny existující výstrahy ve vaší doméně. Stav vaší domény můžete také zobrazit na stránce Přehled služby Azure AD Domain Services.

| Status | Ikona | Vysvětlení |
| --- | :----: | --- |
| Spuštěno | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | Spravované domény běží bez problémů a nemá žádné výstrahy kritický nebo upozornění. Tato doména je pravděpodobně informační výstrahy. |
| Vyžaduje pozornost (upozornění) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | Neexistují žádné kritické výstrahy na vaší spravované domény, ale existují jedné nebo několika výstrah upozornění, které je třeba vzít v úvahu. |
| Vyžaduje pozornost (kritická) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Na vaší spravované domény je jeden nebo více kritické výstrahy. Můžete mít rovněž varování nebo informační výstrahy. |
| Nasazování | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | Vaše doména je právě nasazované. |

## <a name="monitors"></a>Monitory
Monitorování jsou aspektů vaší spravované domény, který monitoruje služba Azure AD Domain Services v pravidelných intervalech. Vyřešte všechny aktivní výstrahy pro spravované domény je nejlepší způsob, jak udržovat vaše monitorování v dobrém stavu.

Azure AD Domain Services aktuálně monitoruje následující:
 - Backup
 - Synchronizace se službou Azure AD

### <a name="the-backup-monitor"></a>Monitorování, zálohování.
To monitoruje, zda jsou během provádění pravidelných záloh vaší spravované domény. Následující tabulka vysvětluje, co mají očekávat ve sloupci podrobnosti zálohování monitorování:

| Hodnota podrobností | Vysvětlení |
| --- | --- |
|"Dosud nezálohovali" | Tento stav je normální pro nově vytvořený spravované domény. Obecně platí první zálohování se vytvoří 24 hodin po zřízení vaší spravované domény. Pokud není nově vytvořená vaší spravované domény nebo v tomto stavu pro neobvyklé množství času, [obraťte se na podporu](active-directory-ds-contact-us.md). |
| Poslední zálohy před 1 až 14 dny. | Tato hodnota se obecně očekává zálohování monitorování. |
| Poslední zálohy před více než 14 dny. | Kdykoli delší než dva týdny je neobvykle dlouhou dobu od poslední zálohy. Aktivní kritické výstrahy mohou zabránit zálohovaných v pravidelných intervalech vaší spravované domény. První, vyřešte všechny aktivní výstrahy pro spravované domény a pak pokud stále problém přetrvává, [obraťte se na podporu](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitorování, synchronizace se službou Azure AD.
Microsoft sleduje, jak často je vaší spravované domény synchronizovat se službou Azure Active Directory. Počet objektů (uživatelé a skupiny) a počet změn provedených v adresáři služby Azure AD od poslední synchronizace může obě ovlivnit jak dlouho může trvat interval synchronizace. Pokud vaší spravované domény poslední synchronizace proběhla před, více než tři dny [obraťte se na podporu](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Výstrahy
Výstrahy jsou generovány pro problémy s u vaší spravované domény, který třeba se zabývat v pořadí pro Azure AD Domain Services ke spuštění. Každá výstraha popisuje problém a obsahuje adresu URL řešení, která popisuje konkrétní kroky k vyřešení problému. Chcete-li zobrazit všechny výstrahy a jejich řešení, navštivte [řešení výstrahy](active-directory-ds-troubleshoot-alerts.md) článku.

### <a name="alert-severity"></a>Závažnost upozornění
Výstrahy jsou rozdělené do tří různé úrovně závažnosti: kritické, upozornění a informativní.

 * **Kritické výstrahy** jsou problémy, které vážně ovlivnit vaší spravované domény. Tyto výstrahy by se řešit okamžitě, jak Microsoft nelze monitorovat, spravovat, opravy a synchronizovat vaší spravované domény. 
 * **Upozorňující výstrahy** upozorňují na problémy, které může mít vliv na vaší spravované domény v budoucnu. Tyto výstrahy nabízet doporučení k zabezpečení vaší spravované domény.
 * **Informační výstrahy** jsou oznámení, které nejsou, které mají vliv negativně vaší domény. Informační výstrahy jsou navržené tak, aby je obeznámen s co se děje v doméně a Azure AD Domain Services.

## <a name="next-steps"></a>Další postup
- [Vyřešení výstrahy na vaší spravované domény](active-directory-ds-troubleshoot-alerts.md)
- [Další informace o Azure AD Domain Services](active-directory-ds-overview.md)
- [Obraťte se na tým produktu](active-directory-ds-contact-us.md)
