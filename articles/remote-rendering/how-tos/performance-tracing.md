---
title: Vytvoření trasování výkonu na straně klienta
description: Doporučené postupy pro profilování výkonu na straně klienta pomocí WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681308"
---
# <a name="create-client-side-performance-traces"></a>Vytvoření trasování výkonu na straně klienta

Existuje mnoho důvodů, proč výkon vzdáleného vykreslování Azure nemusí být tak dobré, jak je požadováno. Kromě čistého výkonu vykreslování na cloudovém serveru má na zážitek významný vliv zejména kvalita síťového připojení. Chcete-li profilovat výkon serveru, podívejte se na [kapitolu Dotazy na výkon na straně serveru](../overview/features/performance-queries.md).

Tato kapitola se zaměřuje na to, jak identifikovat potenciální kritické body na straně klienta prostřednictvím *sledování výkonu*.

## <a name="getting-started"></a>Začínáme

Pokud s funkcí sledování výkonu systému Windows tečujete, v této části se zmiňují nejzákladnější termíny a aplikace, které vám pomohou začít.

### <a name="installation"></a>Instalace

Aplikace používané k trasování pomocí ARR jsou univerzální nástroje, které lze použít pro veškerý vývoj systému Windows. Jsou poskytovány prostřednictvím [sady Windows Performance Toolkit](https://docs.microsoft.com/windows-hardware/test/wpt/). Chcete-li získat tuto sadu nástrojů, stáhněte si [sadu Windows Assessment and Deployment Kit](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologie

Při hledání informací o sledování výkonu, budete nevyhnutelně narazíte na řadu termínů. Nejdůležitější z nich jsou:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** je zkratka pro [ **E**vent **T**racing pro **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). Je to prostě zastřešující název pro efektivní zařízení pro sledování na úrovni jádra, které je integrováno do systému Windows. Nazývá se trasování *událostí,* protože aplikace, které podporují ETW bude vyzařovat události protokolu akce, které mohou pomoci sledovat problémy s výkonem. Ve výchozím nastavení operační systém již vydává události pro věci, jako jsou přístupy k disku, přepínače úloh a podobně. Aplikace jako ARR navíc vyzařují vlastní události, například o vynechaných rámcích, zpoždění sítě atd.

**ETL** je zkratka pro **E**vent **T**závod **L**ogging. Jednoduše to znamená, že trasování bylo shromážděno (zaznamenáno) a proto se obvykle používá jako přípona souboru pro soubory, které ukládají data trasování. Proto při trasování, obvykle budete mít \*.etl soubor poté.

**WPR** je zkratka pro [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) a je název aplikace, která spouští a zastavuje záznam trasování událostí. WPR trvá soubor\*profilu ( .wprp), který konfiguruje, které přesné události se přihlásit. Takový `wprp` soubor je dodáván s Sadou SDK ARR. Při sledování na stolním počítači můžete spustit WPR přímo. Při trasování na HoloLens, obvykle projít webové rozhraní místo.

**WPA** je zkratka pro [ **W**indows **P**erformance **A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) a \*je název gui aplikace, která se používá k otevření .etl soubory a prosít data k identifikaci problémů s výkonem. WPA umožňuje řadit data podle různých kritérií, zobrazit data několika způsoby, prohledává se do podrobností a koreluje informace.

Zatímco etl stopy mohou být vytvořeny na libovolném zařízení se systémem Windows (místní počítač, HoloLens, cloud server, atd.), jsou obvykle uloženy na disk a analyzovány s WPA na stolním počítači. ETL soubory mohou být zaslány jiným vývojářům pro ně podívat. Uvědomte si, že citlivé informace, jako jsou cesty souborů a IP adresy, mohou být zachyceny v trasování ETL, ačkoli. ETW můžete použít dvěma způsoby: k zaznamenání trasování nebo k analýze trasování. Záznam trasování je přímočarý a vyžaduje minimální nastavení. Analýza stopy na druhé straně vyžaduje slušné pochopení jak nástroj WPA a problém, který vyšetřujete. Obecný materiál pro učení WPA budou uvedeny níže, stejně jako pokyny pro interpretaci ARR-specifické stopy.

## <a name="recording-a-trace-on-a-local-pc"></a>Záznam trasování na místním počítači

Chcete-li identifikovat problémy s výkonem ARR, měli byste raději provést trasování přímo na HoloLens, protože to je jediný způsob, jak získat snímek skutečné charakteristiky výkonu. Nicméně, pokud chcete konkrétně provést trasování bez omezení výkonu HoloLens nebo se chcete jen dozvědět, jak používat WPA a nepotřebujete realistické trasování, zde je, jak to udělat.

### <a name="wpr-configuration"></a>Konfigurace WPR

1. Spusťte [záznam výkonu systému Windows](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) z nabídky *Start*.
1. Rozbalit **další možnosti**
1. Klikněte na **Přidat profily...**
1. Vyberte soubor *AzureRemoteRenderingNetworkProfiling.wprp*. Tento soubor najdete v souboru ARR SDK v části *Nástroje/ETLProfiles*.
   Profil bude nyní uveden v wpr v části *Vlastní měření*. Ujistěte se, že je to jediný povolený profil.
1. Rozbalit *třídění první úrovně*:
    * Pokud vše, co chcete udělat, je zachytit rychlé trasování událostí sítě ARR, **zakázat** tuto možnost.
    * Pokud potřebujete korelovat události sítě ARR s jinými charakteristikami systému, jako je například využití procesoru nebo paměti, **povolte** tuto možnost.
    * Pokud tuto možnost povolíte, trasování bude s největší pravděpodobností více gigabajtů ve velikosti a trvat dlouhou dobu uložit a otevřít v WPA.

Poté by měla konfigurace WPR vypadat takto:

![Konfigurace WPR](./media/wpr.png)

### <a name="recording"></a>Nahrávání

Klepnutím na **tlačítko Start** zahájíte záznam trasování. Nahrávání můžete kdykoli spustit a zastavit. před tím není nutné aplikaci ukončit. Jak můžete vidět, nemusíte určit, kterou aplikaci sledovat, protože ETW bude vždy zaznamenávat trasování pro celý systém. Soubor `wprp` určuje, které typy událostí se mají zaznamenávat.

Klepnutím na **tlačítko Uložit** zastavíte nahrávání a určete, kam se má soubor ETL uložit.

Nyní máte soubor ETL, který můžete buď otevřít přímo v WPA nebo odeslat někomu jinému.

## <a name="recording-a-trace-on-a-hololens"></a>Záznam trasování na HoloLens

Chcete-li zaznamenat trasování na HoloLens, spusťte zařízení a zadejte jeho IP adresu do prohlížeče a otevřete *portál zařízení*.

![Portál zařízení](./media/wpr-hl.png)

1. Na levé straně přejděte na *trasování výkonu > výkonu*.
1. Vybrat **vlastní profily**
1. Klikněte na **Procházet...**
1. Vyberte soubor *AzureRemoteRenderingNetworkProfiling.wprp*. Tento soubor najdete v souboru ARR SDK v části *Nástroje/ETLProfiles*.
1. Klikněte na **Spustit trasování.**
1. HoloLens nyní nahrává stopu. Ujistěte se, že aktivujete problémy s výkonem, které chcete prozkoumat. Potom klepněte na tlačítko **Zastavit trasování**.
1. Trasování bude uvedeno v dolní části webové stránky. Kliknutím na ikonu disku na pravé straně stáhněte soubor ETL.

Nyní máte soubor ETL, který můžete buď otevřít přímo v WPA nebo odeslat někomu jinému.

## <a name="analyzing-traces-with-wpa"></a>Analýza trasování pomocí wpa

### <a name="wpa-basics"></a>Základy WPA

Windows Performance Analyzer je standardní nástroj pro otevření souborů ETL a kontrolu trasování. Vysvětlení, jak funguje WPA je mimo rozsah pro tento článek. Chcete-li začít, podívejte se na tyto zdroje:

* Podívejte se na [úvodní videa](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) pro první přehled.
* Samotná služba WPA má kartu *Začínáme,* která vysvětluje běžné kroky. Podívejte se na dostupná témata. Zejména v části "Zobrazit data" získáte rychlý úvod, jak vytvořit grafy pro konkrétní data.
* Tam je vynikající informace [na této webové stránce](https://randomascii.wordpress.com/2015/09/24/etw-central/), nicméně, ne všechny to je relevantní pro začátečníky.

### <a name="graphing-data"></a>Grafy dat

Chcete-li začít s trasování ARR, následující kusy jsou dobré vědět.

![Graf výkonu](./media/wpa-graph.png)

Obrázek nahoře znázorňuje tabulku trasovacích dat a graf reprezentaci stejných dat.

V tabulce v dolní části si všimněte žlutého (zlatého) pruhu a modrého pruhu. Tyto pruhy můžete přetáhnout a umístit je na libovolné místo.

Všechny **sloupce vlevo od žlutého pruhu** jsou interpretovány jako **klíče**. Klíče se používají k strukturě stromu v levém horním okně. Zde máme dva *klíčové* sloupce, "Název poskytovatele" a "Název úkolu". V důsledku toho stromová struktura v levém horním okně je dvě úrovně hluboké. Pokud změníte pořadí sloupců nebo přidáte nebo odeberete sloupce z oblasti klíče, změní se struktura ve stromovém zobrazení.

**Sloupce vpravo od modrého pruhu** se používají pro **zobrazení grafu** v pravém horním okně. Ve většině času se používá pouze první sloupec, ale některé režimy grafu vyžadují více sloupců dat. Aby spojinové grafy fungovaly, musí být v tomto sloupci nastaven *režim agregace.* Použijte "Avg" nebo "Max". Režim agregace se používá k určení hodnoty grafu v daném obrazovém bodu, když obrazový bod pokrývá rozsah s více událostmi. To lze pozorovat nastavením agregace na 'Sum' a pak přiblížení a oddálení.

Sloupce uprostřed nemají žádný zvláštní význam.

![Zobrazení událostí](./media/wpa-event-view.png)

V *Editoru zobrazení obecných událostí* můžete nakonfigurovat všechny sloupce, které se zobrazí, režim agregace, řazení a sloupce, které se používají jako klíče nebo pro grafování. Ve výše uvedeném příkladu je pole **2** povoleno a pole 3 - 6 je zakázáno. Pole 2 je obvykle první *vlastní datové* pole události ETW a tedy pro události ARR "FrameStatistics", které představují určitou hodnotu latence sítě. Povolte další sloupce "Pole", chcete-li zobrazit další hodnoty této události.

### <a name="presets"></a>Přednastavení

Chcete-li správně analyzovat trasování, budete muset zjistit vlastní pracovní postup a upřednostňované zobrazení dat. Abychom však mohli získat rychlý přehled o událostech specifických pro ARR, zahrneme profil platformy Windows Software Protection Platform a soubory přednastavení do složky *Nástroje/ETLProfiles*. Chcete-li načíst celý profil, vyberte *profily > použít...* na řádku nabídek WPA nebo otevřete panel *Moje přednastavení* *(Okna > moje přednastavení)* a vyberte *Importovat*. První z nich vytvoří kompletní konfiguraci WPA jako na obrázku níže. Ten bude pouze přednastavení pro různé konfigurace zobrazení k dispozici a umožňují rychle otevřít zobrazení se podívat na konkrétní část dat události ARR.

![Přednastavení](./media/wpa-arr-trace.png)

Obrázek nahoře zobrazuje zobrazení různých událostí specifických pro ARR a zobrazení celkového využití procesoru.

## <a name="next-steps"></a>Další kroky

* [Dotazy na výkon na straně serveru](../overview/features/performance-queries.md)
