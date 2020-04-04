---
title: Kontrola stavu služby Azure Active Directory Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak zkontrolovat stav spravované domény služby Azure Active Directory Domain Services (Azure AD DS) a porozumět stavovým zprávám pomocí webu Azure Portal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 27ab14af25704a4f7fb46aa5e86cdaf881c49442
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655643"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Kontrola stavu spravované domény služby Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) spouští některé úlohy na pozadí, aby spravovanou doménu udržela v pořádku a aktuální. Mezi tyto úkoly patří zálohování, použití aktualizací zabezpečení a synchronizace dat z Azure AD. Pokud se problémy se spravovanou doménou Azure AD DS, tyto úkoly nemusí být úspěšně dokončena. Chcete-li zkontrolovat a vyřešit všechny problémy, můžete zkontrolovat stav spravované domény Azure AD DS pomocí webu Azure Portal.

Tento článek ukazuje, jak zobrazit stav Azure AD DS a pochopit zobrazené informace nebo výstrahy.

## <a name="view-the-health-status"></a>Zobrazit stavový stav

Stav spravované domény Azure AD DS se zobrazí pomocí portálu Azure. Informace o poslední době zálohování a synchronizaci s Azure AD lze vidět, spolu s výstrahami, které označují problém se stavem spravované domény. Chcete-li zobrazit stav spravované domény Služby Azure AD DS, postupujte takto:

1. Na webu Azure Portal vyhledejte a vyberte **služby Domény Azure AD**.
1. Vyberte spravovanou doménu Azure AD DS, například *aaddscontoso.com*.
1. Na levé straně okna prostředku Azure AD DS vyberte **stav**. Následující příklad snímku ukazuje v pořádku spravované domény Azure AD DS a stav poslední zálohy a synchronizace Azure AD:

    ![Přehled stránky o stavu na portálu Azure zobrazující stav Služby Azure Active Directory Domain Services](./media/check-health/health-page.png)

*Poslední vyhodnocené* časové razítko stránky stavu zobrazuje, kdy byla naposledy zkontrolována spravovaná doména Azure AD DS. Stav spravované domény Azure AD DS se vyhodnocuje každou hodinu. Pokud provedete nějaké změny spravované domény Azure AD DS, počkejte na další cyklus hodnocení k zobrazení aktualizovaného stavu.

Stav v pravém horním rohu označuje celkový stav spravované domény Azure AD DS. Stav zvýrazní všechny existující výstrahy ve vaší doméně. V následující tabulce jsou uvedeny dostupné indikátory stavu:

| Status | Ikona | Vysvětlení |
| --- | :----: | --- |
| Spuštěno | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Spravovaná doména Azure AD DS běží správně a nemá žádné kritické nebo varovné výstrahy. Doména může mít informační výstrahy. |
| Vyžaduje pozornost (varování) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Neexistují žádné kritické výstrahy na spravované doméně Azure AD DS, ale existuje jeden nebo více upozornění, které by měly být vyřešeny. |
| Potřebuje pozornost (kritická) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Existuje jeden nebo více důležitých výstrah ve spravované doméně Azure AD DS, které je třeba řešit. Můžete také mít varování a / nebo informační výstrahy. |
| Nasazení | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Doména Azure AD DS se nasazuje. |

## <a name="understand-monitors-and-alerts"></a>Principy monitorování a výstrah

Stav spravované domény Azure AD DS zobrazuje dva typy informací – *monitorování*a *výstrahy*. Monitory ukazují čas dokončení základních úloh na pozadí. Výstrahy poskytují informace nebo návrhy ke zlepšení stability spravované domény.

### <a name="monitors"></a>Monitory

Monitory jsou oblasti spravované domény Azure AD DS, které jsou pravidelně kontrolovány. Pokud existují nějaké aktivní výstrahy pro spravovanou doménu Azure AD DS, může způsobit, že jeden z monitorů ohlásí problém. Služba Azure AD Domain Services má aktuálně monitory pro následující oblasti:

* Backup
* Synchronizace s Azure AD

#### <a name="backup-monitor"></a>Sledování zálohování

Monitorování zálohování kontroluje, že automatické pravidelné zálohování spravované domény Azure AD DS úspěšně spustit. V následující tabulce je uveden stav monitoru zálohování:

| Detailní hodnota | Vysvětlení |
| --- | --- |
| Nikdy zálohováno | Tento stav je normální pro nové domény spravované službou Azure AD DS. První záloha by měla být vytvořena 24 hodin po nasazení spravované domény Azure AD DS. Pokud tento stav přetrvává, [otevřete žádost o podporu Azure][azure-support]. |
| Poslední záloha byla provedena před 1 až 14 dny. | Tento časový rozsah je očekávaný stav pro záložní monitor. V tomto období by mělo dojít k automatickému pravidelnému zálohování. |
| Poslední záloha byla pořízena před více než 14 dny. | Časový interval delší než dva týdny znamená, že došlo k problému s automatickým pravidelným zálohováním. Aktivní kritické výstrahy mohou zabránit zálohování spravované domény Azure AD DS. Vyřešte všechny aktivní výstrahy pro spravovanou doménu Azure AD DS. Pokud se na monitoru zálohování neaktualizuje stav, aby nahlásil poslední zálohu, [otevřete žádost o podporu Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronizace s monitorováním Azure AD

Spravovaná doména Azure AD DS se pravidelně synchronizuje se službou Azure Active Directory. Počet uživatelů a objektů skupiny a počet změn provedených v adresáři Azure AD od poslední synchronizace ovlivňuje, jak dlouho trvá synchronizace. Pokud byla spravovaná doména Azure AD DS naposledy synchronizována před třemi dny, zkontrolujte a vyřešte všechny aktivní výstrahy. Pokud monitor synchronizace neaktualizuje stav tak, aby se po adresách všech aktivních výstrah zobrazovala poslední synchronizace, [otevřete žádost o podporu Azure][azure-support].

### <a name="alerts"></a>Výstrahy

Výstrahy jsou generovány pro problémy ve spravované doméně Azure AD DS, které je třeba řešit, aby služba správně běžela. Každá výstraha vysvětluje problém a poskytuje adresu URL, která popisuje konkrétní kroky k vyřešení problému. Další informace o možných výstrahách a jejich řešeních naleznete v [tématu Poradce při potížích s výstrahami](troubleshoot-alerts.md).

Výstrahy stavu jsou rozděleny do následujících úrovní závažnosti:

 * **Kritické výstrahy** jsou problémy, které vážně ovlivňují spravovanou doménu Azure AD DS. Tyto výstrahy by měly být řešeny okamžitě. Platforma Azure nemůže monitorovat, spravovat, opravovat a synchronizovat spravovanou doménu, dokud se problémy nevyřeší.
 * **Upozornění upozornění** upozornit na problémy, které mohou mít vliv na operace spravované domény Azure AD DS, pokud problém přetrvává. Tyto výstrahy také nabízejí doporučení k zabezpečení spravované domény.
 * **Informační výstrahy** jsou oznámení, která nemají negativní vliv na spravovanou doménu Azure AD DS. Informační výstrahy poskytují určitý přehled o tom, co se děje ve spravované doméně.

## <a name="next-steps"></a>Další kroky

Další informace o výstrahách, které se zobrazují na stránce se stavem, najdete v [tématu Řešení výstrah ve spravované doméně.][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
