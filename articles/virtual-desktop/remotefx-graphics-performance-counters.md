---
title: Diagnostika problémů s grafickým výkonem Vzdálená plocha – Azure
description: Tento článek popisuje, jak používat čítače grafiky RemoteFX v relacích protokolu vzdálené plochy k diagnostice problémů s výkonem grafiky ve virtuální ploše systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127549"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostika problémů s grafickým výkonem ve vzdálené ploše

Chcete-li diagnostikovat problémy s kvalitou prostředí se vzdálenými relacemi, byly k dispozici čítače v části RemoteFX Graphics sledování výkonu. Tento článek vám pomůže určit a opravit kritické body výkonu související s grafikou během relací protokolu RDP (RdP) pomocí těchto čítačů.

## <a name="find-your-remote-session-name"></a>Vyhledání názvu vzdálené relace

K identifikaci čítačů grafického výkonu budete potřebovat název vzdálené relace. Podle pokynů v této části určete svou instanci každého čítače.

1. Otevřete příkazový řádek systému Windows ze vzdálené relace.
2. Spusťte příkaz **qwinsta** a najděte název relace.
    - Pokud je vaše relace hostovaná ve virtuálním počítači s více relacemi: Vaše instance každého čítače je suffixed stejným číslem, které přípony název relace, například "rdp-tcp 37."
    - Pokud je vaše relace hostovaná ve virtuálním počítači, který podporuje virtuální grafické procesory (vGPU): Vaše instance každého čítače je uložena na serveru místo ve vašem virtuálním počítači. Instance čítačů zahrnují název virtuálního počítače namísto čísla v názvu relace, například "Win8 Enterprise VM".

>[!NOTE]
> Zatímco čítače mají RemoteFX v jejich názvech, obsahují grafiku vzdálené plochy ve scénářích vGPU také.

## <a name="access-performance-counters"></a>Čítače výkonu přístupu

Po určení názvu vzdálené relace, postupujte podle těchto pokynů shromažďovat čítače výkonu remoteFX grafiky pro vzdálené relace.

1. Vyberte **možnost Spustit** > **nástroj pro správu** > **sledování výkonu**.
2. V dialogovém okně **Sledování výkonu** rozbalte **položku Nástroje pro sledování**, vyberte Sledování **výkonu**a pak vyberte **Přidat**.
3. V dialogovém okně **Přidat čítače** rozbalte v seznamu **Dostupné čítače** oddíl Pro grafiku RemoteFX.
4. Vyberte čítače, které chcete sledovat.
5. V seznamu **Instance vybraných objektů** vyberte konkrétní instance, které mají být sledovány pro vybrané čítače, a pak vyberte **Přidat**. Chcete-li vybrat všechny dostupné instance čítačů, vyberte **možnost Všechny instance**.
6. Po přidání čítačů vyberte **OK**.

Vybrané čítače výkonu se zobrazí na obrazovce Sledování výkonu.

>[!NOTE]
>Každá aktivní relace na hostiteli má svou vlastní instanci každého čítače výkonu.

## <a name="diagnose-issues"></a>Diagnostika problémů

Problémy s výkonem související s grafikou obvykle spadají do čtyř kategorií:

- Nízká kmitočet snímků
- Náhodné stánky
- Vysoká vstupní latence
- Špatná kvalita rámu

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Řešení nízké kmitočetsnímků snímků, náhodných stánků a vysoké vstupní latence

Nejprve zkontrolujte čítač Výstupní snímky/Druhý. Měří počet rámců zpřístupněných klientovi. Pokud je tato hodnota menší než čítač Vstupní rámce/Sekunda, rámce jsou přeskočeny. Chcete-li identifikovat kritické místo, použijte čítače Přeskoče/Druhý rámce.

Existují tři typy čítačů Přeskoče/Sekunda:

- Přesihnuté rámce za sekundu (nedostatek prostředků serveru)
- Přesihnuté rámce za sekundu (nedostatek síťových prostředků)
- Přesihnuté rámce/sekunda (nedostatek prostředků klienta)

Vysoká hodnota pro některý z čítačů Přeskočených snímků/sekundznamená, že problém souvisí s prostředky, které čítač sleduje. Pokud například klient nedekóduje a neprezentuje rámce stejnou rychlostí, jakou poskytuje server, čítač Přeskočeno/druhý rámce (nedostatečné prostředky klienta) bude vysoký.

Pokud čítač Výstupní snímky/Druhý odpovídá čítači Vstupní snímky/Druhý, přesto si stále všimnete neobvyklého zpoždění nebo zastavení, může být viníkem průměrná doba kódování. Kódování je synchronní proces, ke kterému dochází na serveru ve scénáři jedné relace (vGPU) a na virtuálním počítači ve scénáři s více relacemi. Průměrná doba kódování by měla být pod 33 ms. Pokud průměrná doba kódování je méně než 33 ms, ale stále máte problémy s výkonem, může být problém s aplikací nebo operačním systémem, který používáte.

Další informace o diagnostice problémů souvisejících s aplikací naleznete v [tématu Čítač výkonu Zpoždění vstupu uživatele](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Protože protokol RDP podporuje průměrnou dobu kódování 33 ms, podporuje vstupní kmitočet snímků až 30 snímků za sekundu. Maximální podporovaný kmitočet snímků je 33 ms. V mnoha případech bude kmitočet snímků, který uživatel udává, nižší v závislosti na tom, jak často je snímek poskytnut rdp zdrojem. Například úkoly, jako je sledování videa vyžadují úplnou vstupní kmitočet snímků 30 snímků za sekundu, ale méně výpočetní náročné úkoly, jako je zřídka úprava dokumentu, mají za následek mnohem nižší hodnotu vstupních snímků za sekundu bez snížení kvality uživatelského prostředí.

### <a name="addressing-poor-frame-quality"></a>Řešení špatné kvality snímků

K diagnostice problémů s kvalitou snímků použijte čítač Kvalita rámce. Tento čítač vyjadřuje kvalitu výstupního rámce jako procento kvality zdrojového rámce. Ztráta kvality může být způsobeno RemoteFX nebo může být vlastní zdroj grafiky. Pokud RemoteFX způsobil ztrátu kvality, problém může být nedostatek síťových nebo serverových prostředků pro odesílání obsahu vyšší věrnosti.

## <a name="mitigation"></a>Omezení rizik

Pokud jsou problémové místo příčinou prostředků serveru, vyzkoušejte jeden z následujících přístupů ke zlepšení výkonu:

- Snižte počet relací na hostitele.
- Zvyšte paměť a výpočetní prostředky na serveru.
- Přetažení rozlišení připojení.

Pokud problémové místo způsobuje síťové prostředky, vyzkoušejte jeden z následujících přístupů ke zlepšení dostupnosti sítě na relaci:

- Snižte počet relací na hostitele.
- Použijte síť s vyšší šířkou pásma.
- Přetažení rozlišení připojení.

Pokud jsou problémové místo klienta příčinou kritického místa, vyzkoušejte jeden z následujících přístupů ke zlepšení výkonu:

- Nainstalujte nejnovějšího klienta vzdálené plochy.
- Zvyšte paměť a výpočetní prostředky v klientském počítači.

> [!NOTE]
> V současné době nepodporujeme čítač Zdrojové rámce/Druhý. Pro tuto chvíli se vždy zobrazí čítač Zdrojové snímky/Sekunda 0.

## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit virtuální počítač Azure optimalizovaný pro GPU, přečtěte si téma [Konfigurace akcelerace grafických procesorů (GPU) pro prostředí Virtuální desktopy Windows](configure-vm-gpu.md).
- Přehled tras pro řešení potíží a eskalace najdete [v tématu Přehled řešení potíží, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Další informace o službě naleznete v tématu [Windows Desktop environment](environment-setup.md).
