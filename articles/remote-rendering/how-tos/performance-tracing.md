---
title: Vytvoření trasování výkonu na straně klienta
description: Osvědčené postupy pro profilaci výkonu na straně klienta pomocí WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204091"
---
# <a name="create-client-side-performance-traces"></a>Vytvoření trasování výkonu na straně klienta

Existuje mnoho důvodů, proč výkon vzdáleného vykreslování Azure nemusí být podle potřeby vhodný. Kromě čistého výkonu vykreslování na cloudovém serveru mají obzvláště kvalita síťového připojení výrazný vliv na prostředí. Pokud chcete Profilovat výkon serveru, přečtěte si téma [dotazy na výkon na straně serveru](../overview/features/performance-queries.md).

Tato kapitola se zaměřuje na to, jak identifikovat potenciální kritické body na straně klienta prostřednictvím *:::no-loc text="performance traces":::* .

## <a name="getting-started"></a>Začínáme

Pokud s funkcemi systému Windows začínáte :::no-loc text="performance tracing"::: , Tato část uvádí nejdůležitější výrazy a aplikace, které vám pomohou začít.

### <a name="installation"></a>Instalace

Aplikace, které se používají k trasování pomocí ARR, jsou nástroje pro obecné účely, které se dají použít pro vývoj pro Windows. Jsou k dispozici prostřednictvím sady [Windows Performance Toolkit](/windows-hardware/test/wpt/). Tuto sadu nástrojů získáte stažením [sady Windows Assessment and Deployment Kit](/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologie

Při hledání informací o trasování výkonu se nebudete nevyhnutelně pohybovat v rámci celé řady podmínek. Nejdůležitější jsou tyto:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**Trasování událostí pro Windows** představuje [ **E**-indows **pro** **t**](/windows/win32/etw/about-event-tracing). Je to jednoduše název přestavění efektivního sledovacího zařízení na úrovni jádra, které je součástí systému Windows. Nazývá se trasování *událostí* , protože aplikace, které podporují ETW, generují události pro protokolování akcí, které mohou pomoci při sledování problémů s výkonem. Ve výchozím nastavení operační systém již generuje události pro věci, jako jsou přístup k disku, přepínače úlohy a takové. Aplikace, jako je třeba šipka, navíc generují vlastní události, např. odhozené snímky, prodlevu sítě atd.

**ETL** představuje pro **e-mail E**-Park **T**rasy **L**ogging. Jednoduše to znamená, že trasování bylo shromážděno (zaznamenáno), a proto se obvykle používá jako Přípona souboru pro soubory, které ukládají data trasování. Proto když provedete trasování, obvykle budete mít \* soubor. ETL později.

**WPR** představuje [ **W**indows **P**ERFORMANCE **R**ecorder](/windows-hardware/test/wpt/windows-performance-recorder) a je název aplikace, která spustí a zastaví záznam trasování událostí. WPR převezme soubor profilu ( \* . wprp), který nakonfiguruje, které přesné události se mají protokolovat. Takový `wprp` soubor je k dispozici v sadě ARR SDK. Při trasování na stolním počítači můžete spustit WPR přímo. Když provádíte trasování HoloLens, obvykle místo toho procházíte webovým rozhraním.

**WPA** představuje pro [ **W**indows **P**ERFORMANCE **a**nalyzer](/windows-hardware/test/wpt/windows-performance-analyzer) a je název aplikace grafického uživatelského rozhraní, která se používá k otevírání \* souborů. ETL a ke zjištění problémů s výkonem prostřednictvím dat. WPA umožňuje řadit data podle různých kritérií, zobrazovat data několika způsoby, dig je v podrobnostech a korelovat informace.

I když se trasování ETL dá vytvořit na jakémkoli zařízení s Windows (místní počítač, HoloLens, cloudový server atd.), obvykle se ukládá na disk a analyzuje se přes WPA na stolním počítači. Soubory ETL je možné odeslat ostatním vývojářům, aby vypadaly. Mějte na paměti, že citlivé informace, jako jsou například cesty k souborům a IP adresaem, mohou být zachyceny v trasování ETL, ale. Trasování událostí pro Windows můžete použít dvěma způsoby: k záznamu trasování nebo k analýze trasování. Záznam trasování je přímý a vyžaduje minimální nastavení. Analýza trasování na druhé straně vyžaduje dát porozumění nástroji WPA a problému, který zkoumáte. Obecný materiál pro výuku WPA se bude nacházet níže a také pokyny k interpretaci trasování specifických pro použití ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Záznam trasování na místním počítači

Chcete-li zjistit problémy s výkonem na základě výkonu, měli byste preferovat trasování přímo na HoloLens, protože to je jediný způsob, jak získat snímek skutečných charakteristik výkonu. Pokud však konkrétně chcete provést trasování bez omezení výkonu HoloLens nebo chcete pouze zjistit, jak používat protokol WPA a nepotřebujete reálné trasování, zde je uveden postup.

### <a name="wpr-configuration"></a>Konfigurace WPR

1. [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder)Z *nabídky Start*spusťte.
1. Rozbalit **Další možnosti**
1. Klikněte na **Přidat profily...**
1. Vyberte soubor *AzureRemoteRenderingNetworkProfiling. wprp*. Tento soubor najdete v sadě ARR SDK v nabídce *nástroje/ETLProfiles*.
   Profil se teď v WPR zobrazí v části *vlastní měření*. Ujistěte se, že se jedná o jediný povolený profil.
1. Rozbalte položku *třídění první úrovně*:
    * Pokud je vše, co chcete udělat, zachytit rychlou trasu událostí sítě ARR, **zakažte** tuto možnost.
    * Pokud potřebujete korelovat události sítě ARR s dalšími charakteristikami systému, jako je využití procesoru nebo paměti, tuto možnost **Povolte** .
    * Pokud tuto možnost povolíte, bude mít toto trasování největší velikost více gigabajtů a uložení a otevření v WPA může trvat dlouhou dobu.

Konfigurace WPR by pak měla vypadat nějak takto:

![Konfigurace WPR](./media/wpr.png)

### <a name="recording"></a>Nahrávání

Kliknutím na **Spustit** zahájíte zaznamenávání trasování. Nahrávání můžete kdykoli spustit a zastavit. před tím, než to uděláte, nemusíte aplikaci zavřít. Jak vidíte, nemusíte určit, která aplikace se má trasovat, protože trasování událostí pro Windows bude vždycky nahrávat trasování pro celý systém. `wprp`Soubor určuje, které typy událostí mají být zaznamenávány.

Kliknutím na **Uložit** zastavte nahrávání a určete, kam se má uložit soubor ETL.

Nyní máte soubor ETL, který můžete buď otevřít přímo v WPA, nebo poslat někomu jinému.

## <a name="recording-a-trace-on-a-hololens"></a>Záznam trasování na HoloLens

Pokud chcete zaznamenat trasování na HoloLens, spusťte zařízení a zadejte jeho IP adresu do prohlížeče a otevřete tak *portál zařízení*.

![Portál zařízení](./media/wpr-hl.png)

1. Na levé straně přejděte na *výkon > sledování výkonu*.
1. Vybrat **vlastní profily**
1. Klepněte **:::no-loc text="Browse...":::**
1. Vyberte soubor *AzureRemoteRenderingNetworkProfiling. wprp*. Tento soubor najdete v sadě ARR SDK v nabídce *nástroje/ETLProfiles*.
1. Klikněte na **Spustit trasování** .
1. HoloLens nyní zaznamenává trasování. Nezapomeňte aktivovat problémy s výkonem, které chcete prozkoumat. Pak klikněte na **Zastavit trasování**.
1. Trasování bude uvedeno v dolní části webové stránky. Kliknutím na ikonu disku na pravé straně Stáhněte soubor ETL.

Nyní máte soubor ETL, který můžete buď otevřít přímo v WPA, nebo poslat někomu jinému.

## <a name="analyzing-traces-with-wpa"></a>Analýza trasování pomocí WPA

### <a name="wpa-basics"></a>Základy WPA

Windows Performance Analyzer je standardní nástroj pro otevírání souborů ETL a kontrolu trasování. Vysvětlení, jak WPA funguje, je pro tento článek mimo rozsah. Začněte tím, že se podíváte na tyto prostředky:

* Podívejte se na [úvodní videa](/windows-hardware/test/wpt/windows-performance-analyzer) , kde si můžete prohlédnout první přehled.
* WPA sám obsahuje kartu *Začínáme* , která vysvětluje běžné kroky. Podívejte se na témata, která jsou k dispozici. Zejména v části "zobrazení dat" získáte rychlý Úvod k vytváření grafů pro konkrétní data.
* [Na tomto webu](https://randomascii.wordpress.com/2015/09/24/etw-central/)jsou skvělé informace, ale ne všechny jsou relevantní pro začátečníky.

### <a name="graphing-data"></a>Vytváření grafů dat

Chcete-li začít s trasováním pomocí ARR, je dobré znát následující části.

![Graf výkonu](./media/wpa-graph.png)

Výše uvedený obrázek ukazuje tabulku dat trasování a reprezentace grafu se stejnými daty.

V dolní části tabulky si poznamenejte žlutý (Zlatý) pruh a modrý pruh. Tyto panely můžete přetáhnout a umístit je na libovolné místo.

Všechny **sloupce nalevo od žlutého pruhu** jsou interpretovány jako **klíče**. Klíče se používají ke strukturování stromu v levém horním okně. Tady máme dva *klíčové* sloupce, "název zprostředkovatele" a "název úlohy". Stromová struktura v levém horním okně je proto na dvě úrovně hluboko. Pokud změníte pořadí sloupců nebo přidáte nebo odeberete sloupce z oblasti klíče, struktura ve stromovém zobrazení se změní.

**Sloupce napravo od modrého pruhu** se použijí pro **zobrazení grafu** v pravém horním okně. Ve většině případů je použit pouze první sloupec, ale některé režimy grafu vyžadují více sloupců dat. Aby spojnicové grafy fungovaly, musí být nastaven *režim agregace* v tomto sloupci. Použijte AVG nebo max. Režim agregace se používá k určení hodnoty grafu v daném pixelu, když pixel pokrývá rozsah s více událostmi. To může být pozorováno nastavením agregace na hodnotu SUM a následným přiblížením nebo oddálení.

Sloupce uprostřed nemají žádný zvláštní význam.

![Zobrazení událostí](./media/wpa-event-view.png)

V *editoru zobrazení obecných událostí* můžete nakonfigurovat všechny sloupce, které se mají zobrazit, režim agregace, řazení a které sloupce se používají jako klíče nebo pro vytváření grafů. V předchozím příkladu je **pole 2** povolené a pole 3-6 je zakázané. Pole 2 je obvykle první *vlastní datové* pole události ETW, což znamená události FrameStatistics, což představuje určitou hodnotu latence sítě. Pokud chcete zobrazit další hodnoty této události, povolte jiné sloupce "Field".

### <a name="presets"></a>Žádném

Chcete-li správně analyzovat trasování, budete muset zjistit vlastní pracovní postup a preferované zobrazení dat. Abychom ale mohli získat rychlý přehled o událostech specifických pro ARR, zahrneme profil platformy Windows Software Protection a předdefinované soubory do složky *nástroje/ETLProfiles*. Pokud chcete načíst úplný profil, vyberte *profily > použít...* z řádku nabídek WPA nebo otevřete panel *moje předvolby* (*okno > moje předvolby*) a vyberte *importovat*. V předchozí části se nastaví kompletní konfigurace WPA, jako na obrázku níže. Ta pak provede předvolby jenom pro různé dostupné konfigurace zobrazení a umožní vám rychle otevřít zobrazení a podívat se na konkrétní data události ARR.

![Žádném](./media/wpa-arr-trace.png)

Obrázek výše zobrazuje zobrazení různých událostí specifických pro ARR a přehled o celkovém využití procesoru.

## <a name="next-steps"></a>Další kroky

* [Dotazy na výkon na straně serveru](../overview/features/performance-queries.md)