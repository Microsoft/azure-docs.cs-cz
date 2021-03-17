---
title: Diagnostika problémů s výkonem grafiky Vzdálená plocha – Azure
description: Tento článek popisuje, jak pomocí čítačů grafiky RemoteFX v relacích protokolu vzdálené plochy diagnostikovat problémy s výkonem grafiky na virtuálním počítači s Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc5c6499fa47a1e32a517032d5cc2a97b3f2677f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88007162"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostika problémů s výkonem grafiky na vzdálené ploše

Chcete-li diagnostikovat problémy s kvalitou ve vzdálených relacích, jsou čítače uvedeny v části grafiky RemoteFX v nástroji Sledování výkonu. Tento článek vám pomůže určit a opravit problémy s výkonem související s grafikou během relací protokol RDP (Remote Desktop Protocol) (RDP), které tyto čítače využívají.

## <a name="find-your-remote-session-name"></a>Vyhledání názvu vzdálené relace

K identifikaci čítačů výkonu grafiky budete potřebovat název vzdálené relace. Podle pokynů v této části Identifikujte instanci každého čítače.

1. Z vzdálené relace otevřete příkazový řádek systému Windows.
2. Spusťte příkaz **qwinsta** a vyhledejte název vaší relace.
    - Pokud je vaše relace hostovaná na virtuálním počítači s více relacemi (VM): vaše instance každého čítače má příponu stejným číslem, jako přípona názvu relace, například RDP-TCP 37.
    - Pokud je vaše relace hostovaná na VIRTUÁLNÍm počítači, který podporuje jednotky vGPU (Virtual Graphics Processing Disks): vaše instance každého čítače je uložená na serveru místo na vašem VIRTUÁLNÍm počítači. Instance čítače obsahují název virtuálního počítače místo čísla v názvu relace, například "virtuální počítač Win8 Enterprise".

>[!NOTE]
> I když čítače mají v názvech RemoteFX, zahrnují také grafiku vzdálené plochy ve scénářích vGPU.

## <a name="access-performance-counters"></a>Přístup k čítačům výkonu

Po určení názvu vzdálené relace postupujte podle těchto pokynů a Shromážděte čítače výkonu grafiky RemoteFX pro vzdálenou relaci.

1. Vyberte možnost **Spustit**  >  monitorování výkonu**nástrojů pro správu**  >  **Performance Monitor**.
2. V dialogovém okně **sledování výkonu** rozbalte položku **Nástroje pro monitorování**, vyberte **sledování výkonu**a pak vyberte **Přidat**.
3. V dialogovém okně **Přidat čítače** rozbalte v seznamu **Dostupné čítače** oddíl grafiky RemoteFX.
4. Vyberte čítače, které chcete monitorovat.
5. V seznamu **instance vybraného objektu** vyberte konkrétní instance, které chcete monitorovat pro vybrané čítače, a pak vyberte **Přidat**. Chcete-li vybrat všechny dostupné instance čítače, vyberte možnost **všechny instance**.
6. Po přidání čítačů vyberte **OK**.

Vybrané čítače výkonu se zobrazí na obrazovce sledování výkonu.

>[!NOTE]
>Každá aktivní relace na hostiteli má svou vlastní instanci každého čítače výkonu.

## <a name="diagnose-issues"></a>Diagnostika problémů

Problémy s výkonem související s grafikou obecně spadají do čtyř kategorií:

- Nízká snímková frekvence
- Náhodné kabiny
- Vysoká latence vstupu
- Špatná kvalita snímků

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Řešení nízké frekvence snímků, náhodných parkovacích míst a vysoké latence vstupu

Nejdřív ověřte výstupní snímky/druhý čítač. Měří počet rámců, které byly klientovi k dispozici. Pokud je tato hodnota menší než vstupní rámce/druhý čítač, snímky se přeskočí. Chcete-li identifikovat kritický bod, použijte čítače vynechané/sekundové rámce.

Existují tři typy vynechaných snímků/sekundu čítače:

- Počet vynechaných snímků za sekundu (nedostatek prostředků serveru)
- Počet vynechaných snímků za sekundu (nedostatek síťových prostředků)
- Počet vynechaných snímků za sekundu (nedostatek zdrojů klienta)

Vysoká hodnota pro kterýkoli z vynechaných snímků za sekundu znamená, že problém souvisí s prostředkem, který čítač sleduje. Pokud například klient nekóduje a neprezentuje snímky se stejnou sazbou, než server nabídne snímky, bude čítač vynechaných snímků za sekundu (nedostatečný počet prostředků klienta) vysoký.

Pokud čítače výstupních snímků/sekund odpovídají vstupním rámcům/druhému počítadla, přesto si stále všimnete neobvyklého zpoždění nebo zastavení, Průměrná doba kódování může být příčinou. Kódování je synchronní proces, který probíhá na serveru ve scénáři s jednou relací (vGPU) a na virtuálním počítači ve scénáři s více relacemi. Průměrná doba kódování by měla být pod 33 MS. Pokud je průměrná doba kódování pod 33 MS, ale stále dochází k problémům s výkonem, může se jednat o problém s aplikací nebo operačním systémem, který používáte.

Další informace o diagnostikování problémů souvisejících s aplikacemi najdete v tématu [čítače výkonu zpoždění vstupu uživatele](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Vzhledem k tomu, že protokol RDP podporuje průměrnou dobu kódování 33 MS, podporuje vstupní snímkový kmitočet až 30 snímků za sekundu. Počítejte s tím, že maximální podporovaná frekvence snímků je 33 MS. V mnoha případech bude frekvence snímků, kterou uživatel zjistil, nižší, a to v závislosti na tom, jak často je rámec k protokolu RDP od zdroje k dispozici. Například úlohy, jako je sledování videa, vyžadují snímkovou rychlost 30 snímků za sekundu, ale méně výpočetní úlohy, jako je například zřídka se upravování dokumentu, mají mnohem nižší hodnotu pro vstupní snímky/s bez snížení kvality uživatelského prostředí.

### <a name="addressing-poor-frame-quality"></a>Řešení špatné kvality snímků

Pomocí čítače kvality snímků Diagnostikujte problémy s kvalitou snímků. Tento čítač vyjadřuje kvalitu výstupního rámce jako procento kvality zdrojového rámce. Ztráta kvality může být způsobena funkcí RemoteFX nebo může být podstatná zdroji grafiky. Pokud RemoteFX způsobil ztrátu kvality, může se jednat o nedostatek síťových prostředků nebo prostředků serveru pro odeslání obsahu s vyšší věrností.

## <a name="mitigation"></a>Omezení rizik

Pokud jsou příčinou kritických prostředků prostředky serveru, zkuste zvýšit výkon jedním z následujících přístupů:

- Snižte počet relací na hostitele.
- Zvětšete paměť a výpočetní prostředky na serveru.
- Vyřaďte rozlišení připojení.

Pokud síťové prostředky způsobují kritické body, zkuste k vylepšení síťové dostupnosti na relaci použít jeden z následujících přístupů:

- Snižte počet relací na hostitele.
- Použijte síť s vyšší šířkou pásma.
- Vyřaďte rozlišení připojení.

Pokud prostředky klienta způsobují kritické body, vyzkoušejte jeden z následujících přístupů ke zvýšení výkonu:

- Nainstalujte nejnovějšího klienta vzdálené plochy.
- Zvyšte velikost paměťových a výpočetních prostředků na klientském počítači.

> [!NOTE]
> Momentálně nepodporujeme čítač zdrojové snímky/druhý. V současné době se v čítačích zdrojové rámce/sekunda vždy zobrazí 0.

## <a name="next-steps"></a>Další kroky

- Informace o vytvoření virtuálního počítače Azure optimalizovaného grafickým PROCESORem najdete v tématu [Konfigurace akcelerace GPU (Graphics Processing Unit) pro prostředí virtuálních počítačů s Windows](configure-vm-gpu.md).
- Přehled postupů řešení potíží a eskalace najdete v tématu [řešení potíží – přehled, názory a podpora](troubleshoot-set-up-overview.md).
- Další informace o této službě najdete v tématu [prostředí pro stolní počítače s Windows](environment-setup.md).
