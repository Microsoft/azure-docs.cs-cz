---
title: Azure AD Domain Services – ověřte stav spravované domény | Microsoft Docs
description: Ověřte stav spravované domény pomocí stránky stavu v Azure Portal.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 6b808126fe4366d3ca3cc19c674b489ec3055665
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234162"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Zkontroluje stav Azure AD Domain Services spravované domény.

## <a name="overview-of-the-health-page"></a>Přehled stránky stavu
Na stránce Stav na svém Azure Portal můžete udržovat aktuální informace o tom, co se děje ve vaší spravované doméně. Tento článek vás provede prvky stránky stavu.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Jak zobrazit stav spravované domény
1. V Azure Portal přejděte na [stránku Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) .
2. Klikněte na doménu, pro kterou chcete zobrazit stav.
3. V levém navigačním podokně klikněte na **stav**.

Následující obrázek znázorňuje ukázkovou stránku stavu: ![Příklad stránky stavu](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> Stav vaší spravované domény se vyhodnocuje každou hodinu. Po provedení změn ve spravované doméně Počkejte do dalšího cyklu vyhodnocení, abyste zobrazili aktualizovaný stav spravované domény. Časové razítko "poslední vyhodnocené" v pravém horním rohu ukazuje, kdy byl naposledy vyhodnocen stav vaší spravované domény.
>

### <a name="status-of-your-managed-domain"></a>Stav vaší spravované domény
Stav v pravém horním rohu stránky stavu indikuje celkový stav vaší spravované domény. Stavové faktory všech existujících výstrah ve vaší doméně. Stav vaší domény můžete zobrazit také na stránce Přehled Azure AD Domain Services.

| Stav | Ikona | Vysvětlení |
| --- | :----: | --- |
| Spuštěno | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Vaše spravovaná doména je spuštěná hladce a nemá žádné kritické výstrahy ani upozornění. Tato doména může obsahovat informativní výstrahy. |
| Vyžaduje pozornost (upozornění) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Ve spravované doméně neexistují žádné kritické výstrahy, ale je potřeba vyřešit aspoň jedno upozornění. |
| Vyžaduje pozornost (kritická) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Ve spravované doméně je nejmíň jedna kritická výstraha. Mohou být také zobrazovány upozornění nebo informativní výstrahy. |
| Nasazení | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Vaše doména se právě nasazuje. |

## <a name="monitors"></a>Monitory
Monitory jsou aspekty vaší spravované domény, které Azure AD Domain Services pravidelně monitorují. Nejlepším způsobem, jak udržet monitory v dobrém stavu, je vyřešit všechny aktivní výstrahy pro spravovanou doménu.

Azure AD Domain Services aktuálně sleduje následující:
 - Backup
 - Synchronizace s Azure AD

### <a name="the-backup-monitor"></a>Monitorování zálohování
Tím se monitoruje, jestli se provádí pravidelné zálohování vaší spravované domény. Následující tabulka vysvětluje, co je potřeba očekávat ve sloupci podrobností monitorování zálohování:

| Hodnota podrobností | Vysvětlení |
| --- | --- |
|"Nikdy zálohováno" | Tento stav je pro nově vytvořenou spravovanou doménu normální. Obecně platí, že první záloha se vytvoří 24 hodin po zřízení spravované domény. Pokud se vaše spravovaná doména nově nevytvořila nebo pokud se tento stav zobrazuje po neobvyklém čase, obraťte se na [podporu](contact-us.md). |
| Poslední záloha byla odebrána před 1 až 14 dny. | Obecně platí, že tato hodnota je pro monitorování zálohy očekávaná. |
| Poslední záloha proběhla před víc než 14 dny. | Veškerá doba delší než dva týdny je neobvyklá čas od poslední zálohy. Aktivní kritické výstrahy můžou zabránit tomu, aby se vaše spravovaná doména pravidelně zálohovali. Nejprve vyřešte všechny aktivní výstrahy pro spravovanou doménu a pak Pokud problém přetrvává, obraťte se na [podporu](contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitorování synchronizace s Azure AD
Microsoft monitoruje, jak často je vaše spravovaná doména synchronizovaná s Azure Active Directory. Počet objektů (uživatelů & skupin) a počet změn provedených v adresáři služby Azure AD od poslední synchronizace může mít vliv na to, jak dlouho může trvat synchronní období synchronizace. Pokud byla vaše spravovaná doména naposledy synchronizována před třemi dny, [obraťte](contact-us.md)se na podporu.

## <a name="alerts"></a>Upozornění
Výstrahy se generují pro problémy ve spravované doméně, které je potřeba řešit, aby bylo možné Azure AD Domain Services spustit. Každé upozornění vysvětluje problém a poskytuje adresu URL pro řešení, která popisuje konkrétní kroky pro vyřešení problému. Pokud chcete zobrazit všechna upozornění a jejich řešení, navštivte článek [řešení potíží s výstrahami](troubleshoot-alerts.md) .

### <a name="alert-severity"></a>Závažnost výstrahy
Výstrahy jsou rozdělené do kategorií na tři různé úrovně závažnosti: kritická, varovná a informativní.

 * **Kritické výstrahy** jsou problémy, které mají vážně vliv na spravovanou doménu. Tyto výstrahy by se měly řešit okamžitě, protože Microsoft nemůže monitorovat, spravovat, opravovat a synchronizovat vaši spravovanou doménu. 
 * **Upozornění** upozorňující na problémy, které mohou v budoucnu ovlivnit vaši spravovanou doménu. Tyto výstrahy nabízejí doporučení k zabezpečení vaší spravované domény.
 * **Informativní výstrahy** jsou oznámení, která nemají negativní dopad na vaši doménu. Informační výstrahy jsou navržené tak, aby vám pomohly zjistit, co se děje ve vaší doméně a Azure AD Domain Services.

## <a name="next-steps"></a>Další kroky
- [Řešení výstrah ve spravované doméně](troubleshoot-alerts.md)
- [Přečtěte si další informace o Azure AD Domain Services](overview.md)
- [Kontaktovat produktový tým](contact-us.md)
