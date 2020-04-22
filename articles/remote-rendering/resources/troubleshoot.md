---
title: Řešení potíží
description: Informace o řešení potíží pro vzdálené vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b86af2ff8fad3793fc47cec9399fd499c1cabba7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681856"
---
# <a name="troubleshoot"></a>Řešení potíží

Na této stránce jsou uvedeny běžné problémy, které narušují vzdálené vykreslování Azure, a způsoby jejich řešení.

## <a name="client-cant-connect-to-server"></a>Klient se nemůže připojit k serveru

Ujistěte se, že brány firewall (na zařízení, uvnitř směrovačů atd.) neblokují následující porty:

* **50051 (TCP)** - vyžadováno pro počáteční připojení (HTTP handshake)
* **8266 (TCP+UDP)** - vyžadováno pro přenos dat
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - vyžadováno pro [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Chyba Odpojeno: VideoFormatNotavailable

Zkontrolujte, zda gpu podporuje hardwarové dekódování videa. Viz [Vývoj pc](../overview/system-requirements.md#development-pc).

Pokud pracujete na přenosném počítači se dvěma grafickými procesory, je možné, že gpu, na kterém běžíte ve výchozím nastavení, neposkytuje funkci hardwarového dekódování videa. Pokud ano, zkuste aplikaci přinutit k použití jiného grafického procesoru. To je často možné v nastavení ovladače GPU.

## <a name="h265-codec-not-available"></a>Kodek H265 není k dispozici

Existují dva důvody, proč server může odmítnout připojení k **kodeku není k dispozici** chyba.

**Kodek H265 není nainstalován:**

Nejprve se ujistěte, že jste nainstalovali **rozšíření HEVC Video Extensions,** jak je uvedeno v části [Software](../overview/system-requirements.md#software) v požadavcích na systém.

Pokud stále dochází k problémům, ujistěte se, že grafická karta podporuje H265 a máte nainstalován nejnovější grafický ovladač. Informace o dodavateli naleznete v části [Vývojový počítač](../overview/system-requirements.md#development-pc) v části Systémové požadavky.

**Kodek je nainstalován, ale nelze jej použít:**

Důvodem tohoto problému je nesprávné nastavení zabezpečení na knihovnách DLL. Tento problém se neprojevuje při pokusu o sledování videí kódovaných h265. Ani přeinstalování kodeku problém nevyřeší. Místo toho proveďte následující kroky:

1. Otevření **PowerShellu s právy správce** a spuštění

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Tento příkaz by `InstallLocation` měl výstup kodeku, něco jako:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Otevření této složky v Průzkumníkovi Windows
1. Měla by existovat podsložka **x86** a **x64.** Klikněte pravým tlačítkem myši na jednu ze složek a zvolte **Vlastnosti**
    1. Vyberte kartu **Zabezpečení** a klepněte na tlačítko **Upřesnit nastavení.**
    1. Klikněte na **Změnit** **vlastníka.**
    1. Zadejte **správce** do textového pole.
    1. Klikněte na **Zkontrolovat jména** a **OK.**
1. Opakování výše uvedených kroků pro druhou složku
1. Také opakujte výše uvedené kroky v každém souboru DLL uvnitř obou složek. Měly by existovat celkem čtyři knihovny DLL.

Chcete-li ověřit, zda jsou nastavení nyní správná, proveďte to pro každý ze čtyř knihoven DLL:

1. Vybrat **vlastnosti > zabezpečení > úpravy**
1. Projděte si seznam všech **skupin / uživatelů** a ujistěte se, že každý z nich má správnou sadu Číst & **Provést** (zaškrtnutí ve sloupci **povolit** musí být zaškrtnuto)

## <a name="low-video-quality"></a>Nízká kvalita videa

Kvalita videa může být ohrožena buď kvalitou sítě, nebo chybějícím video kodekem H265.

* Postup identifikace problémů se sítí naleznete v následujících [postupech](#unstable-holograms).
* Podívejte se na [systémové požadavky](../overview/system-requirements.md#development-pc) pro instalaci nejnovějšího grafického ovladače.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Video nahrané pomocí MRC neodráží kvalitu živého zážitku

Video lze nahrát na Hololens přes [Mixed Reality Capture (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). Výsledné video má však horší kvalitu než živá zkušenost ze dvou důvodů:
* Snímková frekvence videa je omezena na 30 Hz oproti 60 Hz.
* Video obrázky neprocházejí [pozdní fázi reprojection](../overview/features/late-stage-reprojection.md) zpracování krok, takže video se zdá být choppier.

Obě jsou inherentní omezení nahrávací techniky.

## <a name="black-screen-after-successful-model-loading"></a>Černá obrazovka po úspěšném načtení modelu

Pokud jste připojeni k vykreslování runtime a úspěšně načten model, ale pouze vidět černou obrazovku poté, pak to může mít několik různých příčin.

Před provedením podrobnější analýzy doporučujeme otestovat následující věci:

* Je kodek H265 nainstalován? Ačkoli by měl být záložní k odevzdání kodeku H264, viděli jsme případy, kdy tento záložní nefungoval správně. Podívejte se na [systémové požadavky](../overview/system-requirements.md#development-pc) pro instalaci nejnovějšího grafického ovladače.
* Při použití projektu Unity zavřete Unity, odstraňte dočasné *knihovny* a *obj* složky v adresáři projektu a načíst/sestavit projekt znovu. V některých případech data uložená v mezipaměti způsobila, že vzorek nefunguje správně bez zjevného důvodu.

Pokud tyto dva kroky nepomohly, je nutné zjistit, zda jsou snímky videa klientem přijímány či nikoli. To lze dotazovat programově, jak je vysvětleno v kapitole [dotazy na výkon na straně serveru.](../overview/features/performance-queries.md) Má `FrameStatistics struct` člen, který označuje, kolik snímků videa byly přijaty. Pokud je toto číslo větší než 0 a v průběhu času se zvyšuje, klient obdrží skutečné snímky videa ze serveru. V důsledku toho musí být problém na straně klienta.

### <a name="common-client-side-issues"></a>Běžné problémy na straně klienta

**Model není uvnitř pohledu frustum:**

V mnoha případech je model zobrazen správně, ale nachází se mimo frustum fotoaparátu. Častým důvodem je, že model byl exportován s daleko mimostředovým čepem, takže je oříznut vzdálenou ořezovou rovinou fotoaparátu. Pomáhá dotaz modelu ohraničovací rámeček programově a vizualizovat pole s Unity jako řádek pole nebo vytisknout jeho hodnoty do protokolu ladění.

Kromě toho proces převodu generuje [výstupní soubor json](../how-tos/conversion/get-information.md) spolu s převedeným modelem. Chcete-li ladit problémy s umístěním `boundingBox` modelu, stojí za to podívat se na položku v [části outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Ohraničovací rámeček `min` je `max` popsán jako a pozice v 3D prostoru, v metrech. Takže souřadnice 1000.0 znamená, že je 1 kilometr od počátku.

S tímto ohraničovacím rámečkem mohou být dva problémy, které vedou k neviditelné geometrii:
* **Pole může být daleko od středu**, takže objekt je oříznut úplně kvůli daleko rovina výstřižek. Hodnoty `boundingBox` v tomto případě bude `min = [-2000, -5,-5], max = [-1990, 5,5]`vypadat takto: , pomocí velké posun na ose x jako příklad zde. Chcete-li tento typ problému `recenterToOrigin` vyřešit, povolte tuto možnost v [konfiguraci převodu modelu](../how-tos/conversion/configure-model-conversion.md).
* **Box může být vystředěný, ale řádově příliš velký**. To znamená, že i když kamera začíná ve středu modelu, jeho geometrie je oříznuta ve všech směrech. Typické `boundingBox` hodnoty v tomto případě `min = [-1000,-1000,-1000], max = [1000,1000,1000]`by vypadat takto: . Důvodem pro tento typ problému je obvykle neshoda měřítka jednotky. Chcete-li kompenzovat, zadejte [hodnotu měřítka během převodu](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) nebo označte zdrojový model se správnými jednotkami. Změna měřítka lze také použít na kořenový uzel při načítání modelu za běhu.

**Kanál vykreslení Unity neobsahuje vykreslovací háčky:**

Azure Remote Rendering háčky do unity vykreslovat kanálu provést složení rámce s videem a provést reprojekci. Chcete-li ověřit, zda tyto háčky existují, otevřete *nabídku Windows > Analysis > Frame ladicí program*. Povolte ji a ujistěte se, že existují dvě položky `HolographicRemotingCallbackPass` pro v potrubí:

![Ladicí program rámce Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Kód jednoty pomocí rozhraní API vzdáleného vykreslování se nekompiluje

Přepněte *typ sestavení* řešení Unity na **ladění**. Při testování ARR v editoru Unity define je definice `UNITY_EDITOR` k dispozici pouze v sestaveních "Ladění". Všimněte si, že to nesouvisí s typem sestavení používané pro [nasazené aplikace](../quickstarts/deploy-to-hololens.md), kde byste měli dát přednost sestavení verze.

## <a name="unstable-holograms"></a>Nestabilní hologramy

V případě, že se zdá, že se vykreslené objekty pohybují spolu s pohyby hlavy, může se u vás vyskytnat problémy s *reprojekcí pozdní fáze* (LSR). Pokyny, jak k takové situaci přistupovat, naleznete v části [Reprojection late stage.](../overview/features/late-stage-reprojection.md)

Dalším důvodem nestabilních hologramů (kolísání, pokřivení, chvění nebo skákání hologramů) může být špatné připojení k síti, zejména nedostatečná šířka pásma sítě nebo příliš vysoká latence. Dobrým ukazatelem kvality síťového připojení je hodnota `ARRServiceStats.VideoFramesReused` [statistiky výkonu](../overview/features/performance-queries.md) . Opakované použití snímků označuje situace, kdy je třeba znovu použít starý snímek videa na straně klienta, protože nebyl k dispozici žádný nový snímek videa – například z důvodu ztráty paketů nebo z důvodu změn latence sítě. Pokud `ARRServiceStats.VideoFramesReused` je často větší než nula, to znamená problém se sítí.

Další hodnota, na `ARRServiceStats.LatencyPoseToReceiveAvg`které se můžete podívat, je . Mělo by být trvale nižší než 100 ms. Pokud se zobrazí vyšší hodnoty, znamená to, že jste připojeni k datovému centru, které je příliš daleko.

Seznam možných zmírnění rizik naleznete v [pokynech pro připojení k síti](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Další kroky

* [Požadavky na systém](../overview/system-requirements.md)
* [Síťové požadavky](../reference/network-requirements.md)
