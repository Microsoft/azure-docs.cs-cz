---
title: Řešení potíží s nasazeným zařízením StorSimple | Microsoft Docs
description: Popisuje, jak diagnostikovat a opravovat chyby, ke kterým dochází na zařízení StorSimple, které je aktuálně nasazené a funkční.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6cd1d981737db1e7c852931ecc2449e0afc03530
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956785"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Řešení potíží s operačním StorSimple zařízením
> [!NOTE]
> Portál Classic pro StorSimple je zastaralý. Vaši Správci zařízení StorSimple se automaticky přesunou na nový Azure Portal podle plánu ukončování používání. O tomto přesunu vás budeme informovat prostřednictvím e-mailu a oznámení na portálu. Tento dokument zanedlouho také nebude k dispozici. Pokud máte jakékoli dotazy k tomuto přesunu, přečtěte si [Nejčastější dotazy: Přesun na Azure Portal](./index.yml).

## <a name="overview"></a>Přehled
Tento článek poskytuje užitečné pokyny k odstraňování potíží s konfigurací, se kterými se můžete setkat po nasazení a fungování zařízení StorSimple. Popisuje běžné problémy, možné příčiny a doporučené kroky, které vám pomohou vyřešit problémy, se kterými se můžete setkat při spuštění Microsoft Azure StorSimple. Tyto informace platí pro místní fyzické zařízení StorSimple i pro virtuální zařízení StorSimple.

Na konci tohoto článku najdete seznam kódů chyb, se kterými se můžete setkat během operace Microsoft Azure StorSimple, a také kroky, které můžete provést k vyřešení chyb. 

## <a name="setup-wizard-process-for-operational-devices"></a>Průvodce instalací procesu pro provozní zařízení
Pomocí Průvodce instalací ([Invoke-HcsSetupWizard][1]) zkontrolujete konfiguraci zařízení a v případě potřeby proveďte nápravná opatření.

Když spustíte Průvodce instalací na dříve nakonfigurovaném a provozním zařízení, tok procesu se liší. Můžete změnit jenom tyto položky:

* IP adresa, maska podsítě a brána
* Primární server DNS
* Primární server NTP
* Volitelná konfigurace webového proxy serveru

Průvodce instalací neprovede operace týkající se shromažďování hesel a registrace zařízení.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Chyby, ke kterým dochází během následných spuštění Průvodce instalací
Následující tabulka popisuje chyby, se kterými se můžete setkat při spuštění Průvodce instalací na provozním zařízení, možných příčin chyb a doporučených akcích k jejich vyřešení. 

| No. | Chybová zpráva nebo podmínka | Možné příčiny | Doporučená akce |
|:--- |:--- |:--- |:--- |
| 1 |Chyba 350032: Toto zařízení již bylo deaktivováno. |Tato chyba se zobrazí, pokud spustíte Průvodce instalací na zařízení, které je deaktivováno. |Pro další kroky [kontaktujte podpora Microsoftu](./storsimple-8000-contact-microsoft-support.md) . Deaktivované zařízení nejde umístit do služby. Aby bylo možné zařízení znovu aktivovat, může být vyžadováno obnovení továrního nastavení. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (výjimka z HRESULT: 0x80070001) |Aktualizace serveru DNS se nezdařila. Nastavení DNS jsou globální nastavení a používají se ve všech povolených síťových rozhraních. |Povolte rozhraní a znovu použijte nastavení DNS. To může rušit síť pro jiná povolená rozhraní, protože tato nastavení jsou globální. |
| 3 |Zdá se, že zařízení je online na portálu služby StorSimple Manager Service, ale když se pokusíte dokončit minimální nastavení a uložit konfiguraci, operace se nezdařila. |Během počáteční instalace není webový proxy server nakonfigurovaný, i když se jednalo o skutečné proxy server. |K vyhledání chyby použijte [rutinu test-HcsmConnection][2] . Pokud problém nemůžete vyřešit, [kontaktujte podpora Microsoftu](./storsimple-8000-contact-microsoft-support.md) . |
| 4 |Invoke-HcsSetupWizard: Hodnota nespadá do očekávaného rozsahu. |Tato chyba je vytvářena nesprávnou maskou podsítě. Možné příčiny: <ul><li> Maska podsítě chybí nebo je prázdná.</li><li>Formát předpony IPv6 je nesprávný.</li><li>Rozhraní je povolené pro Cloud, ale brána chybí nebo je nesprávná.</li></ul>Všimněte si, že DATA 0 jsou automaticky povolena v cloudu, pokud je nakonfigurována pomocí Průvodce instalací nástroje. |K určení problému použijte podsíť 0.0.0.0 nebo 256.256.256.256 a potom se podívejte na výstup. V případě potřeby zadejte správné hodnoty masky podsítě, brány a předpony IPv6. |

## <a name="error-codes"></a>Kódy chyb
Chyby jsou uvedeny v číselném pořadí.

| Číslo chyby | Text nebo popis chyby | Akce doporučeného uživatele |
|:--- |:--- |:--- |
| 10502 |Při přístupu k účtu úložiště došlo k chybě. |Počkejte pár minut a pak to zkuste znovu. Pokud chyba přetrvává, kontaktujte prosím podpora Microsoftu pro další kroky. |
| 40017 |Operace zálohování se nezdařila, protože svazek zadaný v zásadách zálohování nebyl v zařízení nalezen. |Opakujte operaci zálohování, pokud chyba přetrvává, obraťte se na podpora Microsoftu. pro další kroky. |
| 40018 |Operace zálohování se nezdařila, protože v zařízení nebyly nalezeny žádné svazky zadané v zásadách zálohování. |Opakujte operaci zálohování, pokud chyba přetrvává, obraťte se na podpora Microsoftu. pro další kroky. |
| 390061 |Systém je zaneprázdněný nebo nedostupný. |Počkejte pár minut a pak to zkuste znovu. Pokud chyba přetrvává, kontaktujte prosím podpora Microsoftu pro další kroky. |
| 390143 |Došlo k chybě s kódem chyby 390143. (Neznámá chyba.) |Pokud chyba přetrvává, kontaktujte prosím podpora Microsoftu pro další kroky. |

## <a name="next-steps"></a>Další kroky
Pokud tento problém nemůžete vyřešit, obraťte se na [Podpora Microsoftu](./storsimple-8000-contact-microsoft-support.md) s žádostí o pomoc. 

[1]: /previous-versions/windows/powershell-scripting/dn688135(v=wps.630)
[2]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)