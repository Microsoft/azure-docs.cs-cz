---
title: Poradce při potížích s nasazeným zařízením StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak diagnostikovat a opravit chyby, ke kterým dochází na zařízení StorSimple, které je aktuálně nasazeno a funkční.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: ca79e4240c1a82e46bea44a9d018a3c681920480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933294"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Poradce při potížích s provozním zařízením StorSimple
> [!NOTE]
> Portál Classic pro StorSimple je zastaralý. Vaši Správci zařízení StorSimple se automaticky přesunou na nový Azure Portal podle plánu ukončování používání. O tomto přesunu vás budeme informovat prostřednictvím e-mailu a oznámení na portálu. Tento dokument zanedlouho také nebude k dispozici. Pokud máte jakékoli dotazy k tomuto přesunu, přečtěte si [Nejčastější dotazy: Přesun na Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Přehled
Tento článek obsahuje užitečné pokyny pro řešení problémů s konfigurací, se kterými se můžete setkat po nasazení a provozu zařízení StorSimple. Popisuje běžné problémy, možné příčiny a doporučené kroky, které vám pomohou vyřešit problémy, které mohou nastat při spuštění Microsoft Azure StorSimple. Tyto informace platí pro místní fyzické zařízení StorSimple i virtuální zařízení StorSimple.

Na konci tohoto článku najdete seznam kódů chyb, se kterými se můžete setkat během operace Microsoft Azure StorSimple, a také kroky, které můžete provést k vyřešení chyb. 

## <a name="setup-wizard-process-for-operational-devices"></a>Proces průvodce instalací pro provozní zařízení
Pomocí průvodce instalací ([Invoke-HcsSetupWizard][1]) zkontrolujte konfiguraci zařízení a v případě potřeby provedete nápravná opatření.

Při spuštění průvodce instalací na dříve nakonfigurovaném a provozním zařízení se tok procesu liší. Můžete změnit pouze následující položky:

* IP adresa, maska podsítě a brána
* Primární server DNS
* Primární server NTP
* Volitelná konfigurace webového proxy serveru

Průvodce instalací neprovádí operace související s shromažďováním hesel a registrací zařízení.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Chyby, ke kterým dochází při následných spuštěních průvodce instalací
Následující tabulka popisuje chyby, se kterými se může dojít při spuštění průvodce instalací na provozním zařízení, možné příčiny chyb a doporučené akce k jejich vyřešení. 

| Ne. | Chybová zpráva nebo stav | Možné příčiny | Doporučená akce |
|:--- |:--- |:--- |:--- |
| 1 |Chyba 350032: Toto zařízení již bylo deaktivováno. |Tato chyba se zobrazí, pokud spustíte průvodce nastavením na zařízení, které je deaktivováno. |Další kroky [vám poskytne podpora společnosti Microsoft.](storsimple-contact-microsoft-support.md) Deaktivované zařízení nelze uvést do provozu. Před znovuuaktivací zařízení může být vyžadováno obnovení továrního nastavení. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Výjimka z HRESULT: 0x80070001) |Aktualizace serveru DNS selhává. Nastavení DNS jsou globální nastavení a jsou použita ve všech povolených síťových rozhraních. |Povolte rozhraní a znovu použijte nastavení DNS. To může narušit síť pro jiná povolená rozhraní, protože tato nastavení jsou globální. |
| 3 |Zařízení se zdá být online na portálu služeb StorSimple Manager, ale při pokusu o dokončení minimální nastavení a uložení konfigurace se operace nezdaří. |Během počátečního nastavení nebyl webový proxy server nakonfigurován, přestože byl na místě skutečný proxy server. |Pomocí [rutiny Test-HcsmConnection][2] vyhledejte chybu. Pokud se vám nedaří problém vyřešit, obraťte se na podporu společnosti [Microsoft.](storsimple-contact-microsoft-support.md) |
| 4 |Invoke-HcsSetupWizard: Hodnota nespadá do očekávaného rozsahu. |Nesprávná maska podsítě způsobí tuto chybu. Možné příčiny jsou: <ul><li> Maska podsítě chybí nebo je prázdná.</li><li>Formát předpony Ipv6 je nesprávný.</li><li>Rozhraní je povoleno v cloudu, ale brána chybí nebo je nesprávná.</li></ul>Všimněte si, že data 0 je automaticky povolena v cloudu, pokud je nakonfigurována pomocí průvodce instalací. |Chcete-li zjistit problém, použijte podsíť 0.0.0.0 nebo 256.256.256.256 a pak se podívejte na výstup. Podle potřeby zadejte správné hodnoty pro masku podsítě, bránu a předponu Ipv6. |

## <a name="error-codes"></a>Kódy chyb
Chyby jsou uvedeny v číselném pořadí.

| Číslo chyby | Text nebo popis chyby | Doporučená akce uživatele |
|:--- |:--- |:--- |
| 10502 |Při přístupu k účtu úložiště došlo k chybě. |Počkejte několik minut a akci opakujte. Pokud chyba přetrvává, obraťte se na podporu společnosti Microsoft pro další kroky. |
| 40017 |Operace zálohování se nezdařila, protože svazek zadaný v zásadách zálohování nebyl v zařízení nalezen. |Opakujte operaci zálohování, pokud chyba přetrvává, obraťte se na podporu společnosti Microsoft. pro další kroky. |
| 40018 |Operace zálohování se nezdařila, protože v zařízení nebyl nalezen žádný ze svazků určených v zásadách zálohování. |Opakujte operaci zálohování, pokud chyba přetrvává, obraťte se na podporu společnosti Microsoft. pro další kroky. |
| 390061 |Systém je zaneprázdněn nebo není k dispozici. |Počkejte několik minut a akci opakujte. Pokud chyba přetrvává, obraťte se na podporu společnosti Microsoft pro další kroky. |
| 390143 |Došlo k chybě s kódem chyby 390143. (Neznámá chyba.) |Pokud chyba přetrvává, obraťte se na podporu společnosti Microsoft pro další kroky. |

## <a name="next-steps"></a>Další kroky
Pokud se vám nedaří problém vyřešit, požádejte o pomoc [podporu společnosti Microsoft.](storsimple-contact-microsoft-support.md) 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
