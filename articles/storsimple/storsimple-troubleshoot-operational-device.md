---
title: Řešení potíží s nasazených zařízení StorSimple | Dokumentace Microsoftu
description: Popisuje, jak diagnostikovat a opravit chyby, ke kterým dochází na zařízení StorSimple, která je aktuálně nasazené a funkční.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8ad3f09bf46caf426b2008b583ebd2ff78522462
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713059"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Řešení potíží s operational zařízení StorSimple
> [!NOTE]
> Portál Classic pro StorSimple je zastaralý. Vaši Správci zařízení StorSimple se automaticky přesunou na nový Azure Portal podle plánu ukončování používání. O tomto přesunu vás budeme informovat prostřednictvím e-mailu a oznámení na portálu. Tento dokument zanedlouho také nebude k dispozici. Dotazy týkající se přesunu, naleznete v tématu [– nejčastější dotazy: Přesunout na webu Azure portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Přehled
Tento článek obsahuje užitečné pokyny k odstraňování problémů pro řešení problémů s konfigurací, může dojít po zařízení StorSimple je nasazené a funkční. Popisuje běžné problémy, možné příčiny a doporučené kroky, které vám pomohou vyřešit problémy, které se můžete setkat při spuštění Microsoft Azure StorSimple. Tyto informace se vztahují k fyzickým zařízením StorSimple místní a virtuální zařízení StorSimple.

Na konci tohoto článku, které můžete najít seznam kódů chyb, které se můžete setkat během operace Microsoft Azure StorSimple a také kroky můžete využít k vyřešení chyby. 

## <a name="setup-wizard-process-for-operational-devices"></a>Průvodce instalací pro provozní zařízení
Pomocí Průvodce instalací ([Invoke-HcsSetupWizard][1]) zkontrolujte konfiguraci zařízení a v případě potřeby provést opravné akce.

Když spustíte Průvodce instalací na zařízení dříve nakonfigurovaná a funkční, průběh procesu se liší. Můžete změnit pouze následující položky:

* IP adresa, maska podsítě a bránu
* Primary DNS server
* Primární server NTP.
* Konfigurace volitelných webového proxy serveru

Průvodce instalací neprovádí operace související s kolekcí a zařízení registrace hesla.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Chyby, ke kterým dochází při dalším spuštění Průvodce instalací
Následující tabulka popisuje chyby mohou setkat, když spustíte Průvodce instalací na zařízení s provozní, možné příčiny chyby a doporučené akce k jejich řešení. 

| Ne. | Chybová zpráva nebo podmínku | Možné příčiny | Doporučená akce |
|:--- |:--- |:--- |:--- |
| 1 |Chyba 350032: Toto zařízení je už deaktivované. |Tato chyba se zobrazí, pokud spustíte Průvodce instalací na zařízení, na kterém je deaktivováno. |O dalším postupu se [poraďte s podporou Microsoftu](storsimple-contact-microsoft-support.md). Deaktivované zařízení nelze umístit do služby. Předtím, než zařízení můžete znovu aktivovat, může být vyžadováno obnovení do výrobního nastavení. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (výjimka z HRESULT: 0x80070001) |Aktualizace serveru DNS se nezdařilo. Nastavení DNS jsou globální nastavení a nastavení použilo pro všechny povolené síťové rozhraní. |Povolení rozhraní a znovu použít nastavení DNS. Protože tato nastavení jsou globální to může přerušit sítě pro ostatní povoleno rozhraní. |
| 3 |Zařízení se zobrazí jako online na portálu služby StorSimple Manager, ale při pokusu o dokončení minimální instalace a uložte konfiguraci, se operace nezdaří. |Při počátečním nastavení nebylo nakonfigurováno webový proxy server, i když se skutečný proxy serveru na místě. |Použití [rutiny Test-HcsmConnection] [ 2] Najít chybu. [Obraťte se na Microsoft Support](storsimple-contact-microsoft-support.md) Pokud nemůžete problém vyřešit. |
| 4 |Invoke-HcsSetupWizard: Hodnota nespadá do očekávaného rozsahu. |Masku podsítě nesprávné vytvoří tuto chybu. Možné příčiny: <ul><li> Maska podsítě je chybí nebo je prázdný.</li><li>Formátu předpony protokolu Ipv6 je nesprávný.</li><li>Rozhraní je povolenou podporu cloudu, ale brána je chybí nebo není správný.</li></ul>Všimněte si, že rozhraní DATA 0 má automaticky povolenou podporu cloudu Pokud je nakonfigurovaný pomocí Průvodce instalací. |Pokud chcete zjistit problém, použijte podsíť 0.0.0.0 nebo 256.256.256.256 a podívejte se na výstup. Zadejte správné hodnoty pro masku podsítě, brány a předponu Ipv6, podle potřeby. |

## <a name="error-codes"></a>Kódy chyb
Chyby jsou uvedeny v pořadí.

| Číslo chyby | Text chyby nebo popisu | Akce doporučené uživatele |
|:--- |:--- |:--- |
| 10502 |Při přístupu k účtu úložiště došlo k chybě. |Počkejte pár minut a pak to zkuste znovu. Pokud potíže potrvají, požádejte prosím podporu Microsoftu o dalších krocích. |
| 40017 |Zálohování operace se nezdařila, protože svazek, zadaný v zásadě zálohování nebyl nalezen v zařízení. |Zkuste zálohování zopakovat operaci, pokud potíže potrvají, obraťte se na Microsoft Support. pro další kroky. |
| 40018 |Operace zálohování se nezdařilo, jak na zařízení se nenašly žádné svazky určené v zásadě zálohování. |Zkuste zálohování zopakovat operaci, pokud potíže potrvají, obraťte se na Microsoft Support. pro další kroky. |
| 390061 |Systém je zaneprázdněn nebo není k dispozici. |Počkejte pár minut a pak to zkuste znovu. Pokud potíže potrvají, požádejte prosím podporu Microsoftu o dalších krocích. |
| 390143 |Došlo k chybě s kódem chyby 390143. (Neznámá chyba.) |Pokud potíže potrvají, kontaktujte prosím Microsoft Support pro další kroky. |

## <a name="next-steps"></a>Další postup
Pokud nemůžete vyřešit problém, [obraťte se na Microsoft Support](storsimple-contact-microsoft-support.md) žádostí o pomoc. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
