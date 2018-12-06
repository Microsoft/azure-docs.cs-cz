---
title: Zkontrolovat stav vaší spravované doméně Azure AD Domain Services - | Dokumentace Microsoftu
description: Zkontrolujte stav vaší spravované domény pomocí webu Azure portal na stránce stavu.
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
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: aee8492c73ace608abaeca9d833494e041d2b9dd
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969000"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Kontrola stavu spravované domény služby Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Přehled stavu stránky
Na stránce stavu na webu Azure portal budete moct aktualizovat na co se děje ve vaší spravované doméně. Tento článek vás provede elementy na stránce stavu.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Jak zobrazit stav vaší spravované domény
1. Přejděte [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) na webu Azure Portal.
2. Klikněte na doménu, kterou chcete zobrazit stav.
3. V levém navigačním podokně klikněte na tlačítko **stavu**.

Následující obrázek znázorňuje ukázkovou stránku Stav: ![příkladu stavu stránky](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Stav vaší spravované domény se vyhodnocuje každou hodinu. Po provedení změn do spravované domény, počkejte do dalšího cyklu hodnocení a zobrazit aktualizovaný stav vaší spravované domény. Časové razítko "Naposledy vyhodnoceno" v pravém horním rohu se zobrazí, kdy bylo naposled vyhodnoceno stav vaší spravované domény.
>

### <a name="status-of-your-managed-domain"></a>Stav vaší spravované domény
Stav v horní části právo na stavu vašeho stránka indikuje celkový stav vaší spravované domény. Stav faktory pro všechny existující výstrahy ve vaší doméně. Na stránce Přehled služby Azure AD Domain Services můžete také zobrazit stav vaší domény.

| Status | Ikona | Vysvětlení |
| --- | :----: | --- |
| Spuštěno | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15"> | Spravované domény běží hladce a nemá žádné výstrahy o kritickém nebo varovném. Tato doména může mít informativní výstrahy. |
| Vyžaduje pozornost (upozornění) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15"> | Neexistují žádné kritické výstrahy ve vaší spravované doméně, ale jeden nebo více upozornění výstrahy, které je potřeba řešit. |
| Vyžaduje pozornost (kritická) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15"> | Existuje jeden nebo více kritické výstrahy ve vaší spravované doméně. Můžete mít rovněž upozornění nebo informační výstrahy. |
| Nasazování | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15"> | Vaše doména je právě probíhá nasazení. |

## <a name="monitors"></a>Monitory
Monitorování jsou aspektů vaší spravované domény, který monitoruje služba Azure AD Domain Services v pravidelných intervalech. Nejlepší způsob, jak zachovat v dobrém stavu monitorů je vyřešit všechny aktivní výstrahy pro spravované domény.

Azure AD Domain Services aktuálně monitoruje následující:
 - Backup
 - Synchronizace se službou Azure AD

### <a name="the-backup-monitor"></a>"Zálohování" monitorování
Tato funkce sleduje, zda provádění pravidelného zálohování vaší spravované domény. Následující tabulka vysvětluje, co můžete očekávat ve sloupci podrobnosti zálohování monitorování:

| Hodnota podrobností | Vysvětlení |
| --- | --- |
|"Dosud nezálohovali" | Tento stav je normální pro nově vytvořenou spravovanou doménu. Obecně platí první zálohování se vytvoří 24 hodin po zřízení spravované domény. Pokud vaše spravovaná doména se nově vytvořit nebo v tomto stavu pro nadměrné množství času, [obraťte se na podporu](active-directory-ds-contact-us.md). |
| Poslední zálohy 1 až 14 dny | Obecně platí tato hodnota se očekává zálohování monitorování. |
| Poslední záloha vytvořila před více než 14 dní. | Žádné delší než dva týdny trvá neobvykle dlouhou dobu od posledního zálohování. Aktivní kritické výstrahy může zabránit vaší spravované domény ze zálohovaného v pravidelných intervalech. První, vyřešte všechny aktivní výstrahy pro spravované domény a pak Pokud problém stále přetrvává, [obraťte se na podporu](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitorování "synchronizaci se službou Azure AD.
Microsoft sleduje, jak často je vaší spravované domény synchronizovány se službou Azure Active Directory. Počet objekty (uživatelé a skupiny) a počet změn provedených v adresáři služby Azure AD od poslední synchronizace může i ovlivnit jak dlouho může trvat interval synchronizace. Pokud vaše spravovaná doména poslední synchronizace před více než tří dnů [obraťte se na podporu](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Výstrahy
Výstrahy jsou generovány pro problémy ve vaší spravované doméně, které musí vzít v pořadí pro službu Azure AD Domain Services ke spuštění. Každé upozornění popisuje problém a adresu URL řešení, která bude uvádět konkrétní kroky k vyřešení daného problému. Chcete-li zobrazit všechny výstrahy a jejich řešení, přejděte [řešení výstrah](active-directory-ds-troubleshoot-alerts.md) článku.

### <a name="alert-severity"></a>Závažnost upozornění
Výstrahy jsou rozdělené do tří různých úrovní závažnosti: kritické, upozornění a informativní.

 * **Kritické výstrahy** jsou problémy, které může mít vliv na vaši spravovanou doménu. Tato upozornění by se řešit okamžitě, jak Microsoft nelze monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu. 
 * **Upozorňující výstrahy** upozorňování na problémy, které může mít vliv na vaši spravovanou doménu v budoucnu. Tyto výstrahy nabízejí doporučení k zabezpečení vaší spravované domény.
 * **Informativní výstrahy** jsou oznámení, která nejsou vaší doméně mít negativní vliv. Informativní výstrahy jsou navržené tak, abyste si víte, co se děje ve vaší doméně a službě Azure AD Domain Services.

## <a name="next-steps"></a>Další postup
- [Vyřešit upozornění ve vaší spravované doméně](active-directory-ds-troubleshoot-alerts.md)
- [Přečtěte si víc o službě Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontaktováním produktového týmu](active-directory-ds-contact-us.md)
