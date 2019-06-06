---
title: Diagnostika problémů s výkonem grafiky ve vzdálené ploše – Azure
description: Tento článek popisuje, jak pomocí technologie RemoteFX grafiky čítačů v relací vzdálené plochy protokolu můžete diagnostikovat problémy s výkonem s grafikou v virtuální plochy Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: 0b4113f1e0024415135aa99d1fb4e881efe448a3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499261"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostika problémů s výkonem grafiky ve vzdálené ploše

Když systém nebude fungovat podle očekávání, je potřeba identifikovat příčiny problému. Tento článek pomáhá identifikovat a opravit kritická místa výkonu související s grafiky během relace protokolu RDP (Remote Desktop).

## <a name="find-your-remote-session-name"></a>Vyhledání názvu vzdálené relace

Budete potřebovat vaše vzdálené relace název pro identifikaci grafiky čítače výkonu. Postupujte podle pokynů v této části a identifikujte název vzdálené relace Windows virtuální plochy, ve verzi Preview.

1. Otevřete příkazový řádek Windows ze vzdálené relace.
2. Spustit **qwinsta** příkazu.
    - Pokud vaše relace je hostovaná v rámci více relace virtuálního počítače (VM): Přípona pro každý název čítače je stejný přípona váš název relace, například "rdp-tcp 37."
    - Pokud vaše relace je hostovaný na virtuálním počítači, který podporuje virtuální jednotky grafiky zpracování (vGPU): Čítače jsou uloženy na serveru místo ve virtuálním počítači. Instance čítače zahrnují název virtuálního počítače, ne číslo název relace, například "Win8 Enterprise virtuálního počítače."

>[!NOTE]
> Zatímco čítače mají v názvu RemoteFX, patří mezi ně vzdálené plochy grafiky v vGPU podporuje i scénáře.

## <a name="access-performance-counters"></a>Přístup k čítačům výkonu

Čítače výkonu v grafické RemoteFX pomoct odhalit kritická místa tím, že pomáhá sledovat, například rámec kódování čas a přeskočí snímků.

Jakmile potvrdíte vaše_jméno vzdálenou relaci, postupujte podle těchto pokynů ke shromažďování čítačů výkonu RemoteFX grafiky pro svou relaci vzdáleného ladění.

1. Vyberte **Start** > **nástroje pro správu** > **sledování výkonu**.
2. V **sledování výkonu** dialogového okna rozbalte **nástroje pro sledování**vyberte **sledování výkonu**a pak vyberte **přidat**.
3. V **přidat čítače** dialogové okno, ze **dostupné čítače** seznamu, rozbalte objekt čítače výkonu pro grafické RemoteFX.
4. Vyberte čítače, která se má monitorovat.
5. V **instancí vybraný objekt** vyberte konkrétní instance se dá monitorovat pro vybrané čítače a potom vyberte **přidat**. Chcete-li vybrat všechny dostupné čítače nezátěžových, vyberte **všechny instance**.
6. Po přidání čítačů, vyberte **OK**.

Vybrané čítače výkonu se zobrazí na obrazovce sledování výkonu.

>[!NOTE]
>Každý aktivní relace na hostiteli má svoji vlastní instanci jednotlivé čítače výkonu.

## <a name="diagnosis"></a>Diagnostika

Problémy s výkonem souvisejících s grafiky se obecně dělí do čtyř kategorií:

- Nízká Snímková frekvence.
- Náhodné přepínání
- Vysoká latence vstupu
- Kvalita nízký snímků

Začněte tím, že adresování nízká Snímková frekvence, náhodných přepínání a vstupní vysokou latenci. Následující části vám sdělí, které čítače výkonu měření jednotlivých kategorií.

### <a name="performance-counters"></a>Čítače výkonu

Tato část umožňuje identifikovat kritické body.

Nejprve zkontrolujte výstupní Frames/Second čítače. Měří počet snímků, které jsou k dispozici ke klientovi. Pokud je tato hodnota menší než čítač Frames/Second vstup, snímků jsou přeskočeno. K identifikaci problémové místo, použijte snímků čítače vynecháno za sekundu.

Existují tři typy rámců vynecháno za sekundu čítače:

- Snímky přeskočeno za sekundu (Nedostatek síťových prostředků)
- Snímky přeskočeno za sekundu (klient nedostatek prostředků)
- Snímky přeskočeno za sekundu (Server nemá dostatek prostředků)

Vysoká hodnota pro některý z rámce vynecháno za sekundu čítače znamená, jestli problém souvisí s prostředek čítač sleduje. Například pokud klient nebude dekódovat a k dispozici snímky za stejnou sazbu serveru poskytuje snímky, čítač rámců vynecháno za sekundu (nedostatek prostředků klienta) bude vysoký.

Pokud čítač výstup Frames/Second odpovídá čítač Frames/Second vstup, ještě stále máte neobvyklé lag nebo zablokování, problém může být průměrný čas kódování. Kódování je synchronního procesu, který se nachází na serveru ve scénáři jediné relace (vGPU) a na virtuálním počítači ve scénáři s více relací. Průměrná doba kódování by měl být pod 33 ms. Pokud Průměrná doba kódování je pod 33 ms, ale pořád máte problémy s výkonem, může být problém s aplikací nebo operačního systému, který používáte.

Další informace o diagnostikování problémů souvisejících se aplikace najdete v tématu [čítače výkonu zpoždění vstup uživatele](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

Protože protokol RDP podporuje průměrný čas s kódováním 33 MS, podporuje vstupní kmitočet až 30 snímků za sekundu. Všimněte si, že 33 ms je maximální podporovaný Snímková frekvence. V mnoha případech bude zkušeným uživatelem Snímková frekvence nižší v závislosti na tom, jak často se snímek poskytuje pro protokol RDP ve zdroji. Například úlohy, například sledovat videa vyžadují úplné vstupní Snímková frekvence 30 snímků za sekundu, ale méně prostředků náročné úkoly, jako je zřídka úpravy dokumentu aplikace word nevyžadují vysoká míra vstupních snímků za sekundu pro bezproblémový.

Čítač kvalita snímků můžete diagnostikovat problémy s kvalitou rámce. Tento čítač vyjadřuje kvality rámec výstupu jako procento kvality zdrojový snímek. Ztráta kvality se jednat o důsledek RemoteFX nebo může být přináší zdroji grafiky. Je-li RemoteFX ztráty kvality, problém může být nedostatek prostředků síť nebo server pro odeslání obsahu vyšší přesnosti.

## <a name="mitigation"></a>Omezení rizik

Pokud prostředky serveru jsou pomalí, zkuste použijte jeden z následujících akcí pro zlepšení výkonu:

- Snižte počet relací na hostiteli.
- Zvyšte paměť a výpočetní prostředky na serveru.
- Vyřaďte rozlišení připojení.

Pokud síťové prostředky jsou pomalí, zkuste použijte jeden z následujících akcí ke zlepšení dostupnosti sítě na relaci:

- Snižte počet relací na hostiteli.
- Vyřaďte rozlišení připojení.
- Použijte větší šířku pásma sítě.

Pokud prostředky klienta jsou pomalí, proveďte jednu nebo obě z následujících akcí pro zlepšení výkonu:

- Nainstalujte nejnovější klienta vzdálené plochy.
- Navyšte paměť a výpočetní prostředky na klientském počítači.

> [!NOTE]
> Aktuálně nepodporujeme čítač Frames/Second zdroje. Prozatím se čítač Frames/Second zdroje vždy nastavena na 0.

## <a name="next-steps"></a>Další postup

- Vytvoření virtuálního počítače GPU optimalizované Azure najdete v tématu [konfigurace grafických akcelerace procesor (GPU) pro prostředí Windows Virtual Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Přehled Eskalace a řešení potíží s sleduje, naleznete v tématu [řešení potíží s přehled, zpětná vazba a podpora](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Další informace o služba ve verzi Preview, najdete v článku [prostředí Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
