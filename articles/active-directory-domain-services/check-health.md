---
title: Ověřit stav Azure Active Directory Domain Services | Microsoft Docs
description: Přečtěte si, jak kontrolovat stav Azure Active Directory Domain Services (Azure služba AD DS) spravované domény a porozumět stavovým zprávám pomocí Azure Portal.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620320"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Zkontroluje stav Azure Active Directory Domain Services spravované domény.

Azure Active Directory Domain Services (Azure služba AD DS) spouští některé úlohy na pozadí, aby byla spravovaná doména v pořádku a aktuální. Mezi tyto úlohy patří zálohování, použití aktualizací zabezpečení a synchronizace dat ze služby Azure AD. Pokud dojde k problémům se spravovanou doménou Azure služba AD DS, nemusí se tyto úlohy úspěšně dokončit. Chcete-li zkontrolovat a vyřešit všechny problémy, můžete zkontrolovat stav spravované domény pomocí Azure Portal.

V tomto článku se dozvíte, jak zobrazit stav služby Azure služba AD DS Health a pochopit zobrazené informace nebo výstrahy.

## <a name="view-the-health-status"></a>Zobrazit stav

Stav pro spravovanou doménu je zobrazený pomocí Azure Portal. Informace o posledním čase zálohování a synchronizaci se službou Azure AD najdete spolu s případnými výstrahami, které indikují problém se stavem spravované domény. Chcete-li zobrazit stav spravované domény, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**.
1. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. Na levé straně okna prostředků Azure služba AD DS vyberte **stav**. Následující ukázkový snímek obrazovky ukazuje v pořádku spravovanou doménu a stav poslední zálohy a synchronizace Azure AD:

    ![Přehled stránky stavu v Azure Portal zobrazující stav Azure Active Directory Domain Services](./media/check-health/health-page.png)

*Poslední vyhodnocené* časové razítko stránky stavu ukazuje, kdy byla spravovaná doména naposledy ověřena. Stav spravované domény se vyhodnocuje každou hodinu. Pokud ve spravované doméně provedete nějaké změny, počkejte na další cyklus hodnocení, abyste zobrazili aktualizovaný stav.

Stav v pravém horním rohu udává celkový stav spravované domény. Stav má všechny existující výstrahy ve vaší doméně. Následující tabulka podrobně popisuje dostupné indikátory stavu:

| Status | Ikona | Vysvětlení |
| --- | :----: | --- |
| Spuštěno | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Spravovaná doména je spuštěná správně a nemá žádné kritické výstrahy nebo upozornění. Doména může obsahovat informativní výstrahy. |
| Vyžaduje pozornost (upozornění) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Ve spravované doméně nejsou k dispozici žádné kritické výstrahy, ale je potřeba vyřešit aspoň jedno upozornění. |
| Vyžaduje pozornost (kritická) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Ve spravované doméně je minimálně jeden kritický výstraha, kterou je potřeba adresovat. Mohou být také zobrazovány upozornění nebo informativní výstrahy. |
| Nasazení | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Nasazuje se spravovaná doména. |

## <a name="understand-monitors-and-alerts"></a>Pochopení monitorování a výstrah

Stav spravované domény zobrazuje dva typy informací – *monitorování* a *výstrahy*. Monitory ukazují dobu, po kterou byly dokončeny základní úlohy na pozadí. Výstrahy obsahují informace nebo návrhy, které zlepšují stabilitu spravované domény.

### <a name="monitors"></a>Monitory

Monitory jsou oblasti spravované domény, které jsou pravidelně kontrolovány. Pokud pro spravovanou doménu existují aktivní výstrahy, může dojít k tomu, že některé z monitorování nahlásí problém. Služba Azure služba AD DS aktuálně obsahuje monitory pro následující oblasti:

* Backup
* Synchronizace s Azure AD

#### <a name="backup-monitor"></a>Monitorování zálohování

Monitorování zálohování kontroluje, zda byl úspěšně spuštěn automatický pravidelný záložní zálohování spravované domény. Následující tabulka podrobně popisuje dostupný stav monitorování zálohování:

| Hodnota podrobností | Vysvětlení |
| --- | --- |
| Nikdy nezálohované | Tento stav je pro nové spravované domény normální. První záloha by měla být vytvořena po dobu 24 hodin po nasazení spravované domény. Pokud tento stav přetrvává, [otevřete žádost o podporu Azure][azure-support]. |
| Poslední záloha byla odebrána před 1 až 14 dny. | Tento časový rozsah je očekávaným stavem monitorování zálohování. V této lhůtě by se měla provádět automatizovaná pravidelná zálohování. |
| Poslední záloha proběhla před víc než 14 dny. | Časové rozpětí delší než dva týdny znamená, že došlo k potížím s automatizovanými pravidelnými zálohami. Aktivní kritické výstrahy můžou zabránit zálohování spravované domény. Vyřešte všechny aktivní výstrahy pro spravovanou doménu. Pokud monitorování zálohování neaktualizuje stav, aby nahlásilo poslední zálohu, [otevřete žádost o podporu Azure][azure-support]. |

#### <a name="synchronization-with-azure-ad-monitor"></a>Synchronizace s monitorováním Azure AD

Spravovaná doména se pravidelně synchronizuje s Azure Active Directory. Počet uživatelů a skupinových objektů a počet změn provedených v adresáři služby Azure AD od poslední synchronizace, který ovlivňuje dobu potřebnou k synchronizaci. Pokud byla spravovaná doména naposledy synchronizována před třemi dny, vyhledejte a vyřešte všechny aktivní výstrahy. Pokud monitorování synchronizace po vyřešení všech aktivních výstrah neaktualizuje stav tak, aby zobrazoval poslední synchronizaci, [otevřete žádost o podporu Azure][azure-support].

### <a name="alerts"></a>Výstrahy

Výstrahy se generují pro problémy ve spravované doméně, které je potřeba adresovat, aby služba běžela správně. Každé upozornění vysvětluje problém a poskytuje adresu URL, která popisuje konkrétní kroky k vyřešení problému. Další informace o možných výstrahách a jejich řešení najdete v tématu [řešení potíží s výstrahami](troubleshoot-alerts.md).

Výstrahy týkající se stavu jsou rozdělené do kategorií podle následujících úrovní závažnosti:

 * **Kritické výstrahy** jsou problémy, které mají vážně vliv na spravovanou doménu. Tyto výstrahy by se měly řešit okamžitě. Platforma Azure nemůže monitorovat, spravovat, opravovat a synchronizovat spravovanou doménu, dokud se problémy nevyřeší.
 * **Upozornění** upozorňující na problémy, které mohou mít vliv na operace spravované domény, pokud potíže potrvají. Tyto výstrahy také nabízejí doporučení k zabezpečení spravované domény.
 * **Informativní výstrahy** jsou oznámení, která nemají negativní vliv na spravovanou doménu. Informativní výstrahy poskytují přehled o tom, co se děje ve spravované doméně.

## <a name="next-steps"></a>Další kroky

Další informace o výstrahách, které se zobrazují na stránce stav, najdete v tématu [řešení výstrah ve spravované doméně][troubleshoot-alerts] .

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
