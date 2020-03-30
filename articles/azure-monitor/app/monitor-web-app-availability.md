---
title: Sledování dostupnosti a odezvy libovolných webů | Dokumentace Microsoftu
description: Nastavení testů webu ve službě Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670028"
---
# <a name="monitor-the-availability-of-any-website"></a>Sledování dostupnosti webových stránek

Po nasazení webové aplikace nebo webu můžete nastavit opakované testy pro sledování dostupnosti a odezvy. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. Může vás upozornit, pokud vaše aplikace neodpovídá nebo pokud reaguje příliš pomalu.

Testy dostupnosti můžete nastavit pro libovolný koncový bod HTTP nebo HTTPS, který je přístupný z veřejného internetu. Na webu, který testujete, nemusíte provádět žádné změny. Ve skutečnosti to ani nemusí být web, který vlastníte. Můžete otestovat dostupnost rozhraní REST API, na kterém závisí vaše služba.

### <a name="types-of-availability-tests"></a>Typy testů dostupnosti:

Existují tři typy testů dostupnosti:

* [Testování ping adresy URL](#create-a-url-ping-test): jednoduchý test, který můžete vytvořit na portálu Azure.
* [Vícekrokový webový test](availability-multistep.md): Záznam posloupnosti webových požadavků, které lze přehrát a otestovat složitější scénáře. Vícekrokové webové testy se vytvářejí v sadě Visual Studio Enterprise a nahrávají se na portál k provedení.
* [Vlastní testy dostupnosti stop:](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)Pokud se rozhodnete vytvořit vlastní `TrackAvailability()` aplikaci pro spuštění testů dostupnosti, lze metodu použít k odeslání výsledků do application insights.

**Můžete vytvořit až 100 testů dostupnosti na prostředek Application Insights.**

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Chcete-li vytvořit test dostupnosti, musíte nejprve vytvořit prostředek Application Insights. Pokud jste již zdroj vytvořili, pokračujte v další části [a vytvořte test příkazu Ping adresy URL](#create-a-url-ping-test).

Na portálu Azure vyberte **Vytvořit zdroj** > **nástroje pro vývojáře** > **Application Insights** a [vytvořte prostředek Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Vytvoření testu adresy URL pomocí příkazu Ping

Název "URL ping test" je trochu nesprávné pojmenování. Aby bylo jasno, tento test nevyužívá protokol ICMP (Internet Control Message Protocol) ke kontrole dostupnosti webu. Místo toho používá pokročilejší funkce požadavku HTTP k ověření, zda koncový bod reaguje. Také měří výkon přidružený k této odpovědi a přidává možnost nastavit vlastní kritéria úspěchu spolu s pokročilejší funkce, jako je analýza závislé požadavky a umožňuje opakování.

Chcete-li vytvořit první žádost o dostupnost, otevřete podokno Dostupnost a vyberte **vytvořit test**.

![Vyplňte alespoň adresu URL webu](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Vytvoření testu

|Nastavení| Vysvětlení
|----|----|----|
|**Adresa URL** |  Adresa URL může být libovolná webová stránka, kterou chcete otestovat, ale musí být viditelná z veřejného internetu. Adresa URL může obsahovat řetězec dotazu. To znamená, že můžete také trochu vyzkoušet svou databázi. Pokud se adresa URL přeloží na přesměrování, budeme ji sledovat až po 10 přesměrování.|
|**Analyzovat závislé požadavky**| Test požaduje obrázky, skripty, soubory stylů a další soubory, které jsou součástí testované webové stránky. Zaznamenaná doba odezvy zahrnuje i čas potřebný k získání těchto souborů. Test se nezdaří, pokud některý z těchto prostředků nelze úspěšně stáhnout v rámci časového času pro celý test. Pokud tato možnost není zaškrtnutá, test si vyžádá pouze soubor na zadané adrese URL. Povolení této možnosti má za následek přísnější kontrolu. Test může selhat v případech, které nemusí být patrné při ručním procházení webu.
|**Povolit opakování**|pokud se test nezdaří, je po krátkém intervalu zopakován. Selhání je nahlášeno pouze v případě tří po sobě jdoucích neúspěšných pokusů. Následné testy jsou pak provedeny s obvyklou frekvencí testu. Opakování je dočasně pozastaveno do dalšího úspěchu. Toto pravidlo platí nezávisle na každém umístění testu. **Doporučujeme tuto možnost**. V průměru přibližně 80 % selhání při opakování zmizí.|
|**Četnost zkoušek**| Nastavuje, jak často je test spuštěn z každého testovacího umístění. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.|
|**Testovací místa**| Jsou místa, odkud naše servery posílají webové požadavky na vaši adresu URL. **Náš minimální počet doporučených testovacích míst je pět,** abychom se ujistili, že můžete odlišit problémy na svých webových stránkách od problémů se sítí. Můžete vybrat až 16 umístění.

**Pokud vaše adresa URL není viditelná z veřejného internetu, můžete selektivně otevřít bránu firewall a povolit pouze testovací transakce prostřednictvím aplikace**. Další informace o výjimkách brány firewall pro naše agenty testování dostupnosti naleznete v [průvodci IP adresami](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Důrazně doporučujeme testování z více míst s **minimálně pěti místy**. Toto je zabránit falešným poplachům, které mohou být důsledkem přechodných problémů s určitým umístěním. Kromě toho jsme zjistili, že optimální konfigurace je mít **počet testovacích míst se rovná prahovou hodnotu umístění výstrahy + 2**.

### <a name="success-criteria"></a>Kritéria úspěchu

|Nastavení| Vysvětlení
|----|----|----|
| **Časový čas testu** |Snižte tuto hodnotu, abyste byli upozorněni na pomalé odpovědi. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.|
| **Odpověď HTTP** | Vrácený stavový kód, který se počítá jako úspěšný. 200 je kód, který označuje, že byla vrácena normální webová stránka.|
| **Shoda obsahu** | Řetězec, jako "Vítejte!" U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat. **Pouze anglické znaky jsou podporovány s obsahem odpovídající** |

### <a name="alerts"></a>Výstrahy

|Nastavení| Vysvětlení
|----|----|----|
|**Téměř v reálném čase (náhled)** | Doporučujeme používat výstrahy v reálném čase. Konfigurace tohoto typu výstrahy se provádí po vytvoření testu dostupnosti.  |
|**Classic** | Už nedoporučujeme používat klasické výstrahy pro nové testy dostupnosti.|
|**Prahová hodnota umístění výstrahy**|Doporučujeme minimálně 3/5 lokalit. Optimální vztah mezi prahovou hodnotou umístění výstrahy a počtem testovacích míst je **výstražný počet prahových míst** = **umístění testovacích umístění - 2, s minimálně pěti testovacími místy.**|

## <a name="see-your-availability-test-results"></a>Zobrazení výsledků testu dostupnosti

Výsledky testu dostupnosti lze vizualizovat pomocí zobrazení čáry i bodového vykreslování.

Po několika minutách klikněte na **Aktualizovat** a zověžte výsledky testů.

![Zobrazení čáry](./media/monitor-web-app-availability/availability-refresh-002.png)

Zobrazení bodový graf zobrazuje ukázky výsledků testu, které mají diagnostické test-krok podrobnosti v nich. V případě testů obsahujících selhání ukládá testovací modul diagnostické informace. U úspěšných testů se diagnostické informace ukládají pro celou dílčí sadu provedení. Najeďte nad některou ze zelených/červených tečů, abyste viděli test, název testu a umístění.

![Zobrazení čáry](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Výběrem určitého testu nebo umístění nebo zkrácením časového období zobrazíte více výsledků v požadovaném časovém období. V Průzkumníku služby Hledání můžete zobrazit výsledky všech provedení a pomocí dotazů služby Analytics můžete na těchto datech spouštět vlastní sestavy.

## <a name="inspect-and-edit-tests"></a> Kontrola a úprava testů

Chcete-li upravit, dočasně zakázat nebo odstranit test, klepněte na tři tečky vedle názvu testu. Může trvat až 20 minut, než se změny konfigurace rozšíří na všechny testovací agenty po provedené změně.

![Zobrazit podrobnosti testu. Úprava a zakázání webového testu](./media/monitor-web-app-availability/edit.png)

Při provádění údržby služby může být vhodné zakázat testy dostupnosti nebo pravidla upozornění, která jsou s nimi spojená.

## <a name="if-you-see-failures"></a>Pokud vidíte selhání

Klikněte na červenou tečku.

![Klikněte na červenou tečku](./media/monitor-web-app-availability/open-instance-3.png)

Z výsledku testu dostupnosti můžete zobrazit podrobnosti o transakci ve všech součástech. Zde můžete:

* Kontrolovat odpověď přijatou ze serveru.
* Diagnostikovat selhání s korelační telemetrie na straně serveru shromážděné při zpracování testu dostupnosti se nezdařilo.
* Protokolovat problém nebo pracovní položku v Git nebo Azure Boards sledovat problém. Chyba bude obsahovat odkaz na tuto událost.
* Otevřít výsledek webového testu v sadě Visual Studio.

Další informace o prostředí diagnostiky transakcí od konce [najdete zde](../../azure-monitor/app/transaction-diagnostics.md).

Kliknutím na řádek výjimky zobrazíte podrobnosti o výjimce na straně serveru, která způsobila selhání testu syntetické dostupnosti. Můžete také získat [snímek ladění](../../azure-monitor/app/snapshot-debugger.md) pro bohatší diagnostiku úrovně kódu.

![Diagnostika na straně serveru](./media/monitor-web-app-availability/open-instance-4.png)

Kromě nezpracovaných výsledků můžete také zobrazit dvě klíčové metriky dostupnosti v [Průzkumníku metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Dostupnost: procento testů, které proběhly úspěšně, vzhledem k celkovému počtu provedení testu.
2. Doba trvání testu: průměrná doba trvání u všech provedení testu.

## <a name="automation"></a>Automatizace

* [Automatické nastavení testu dostupnosti pomocí skriptů PowerShell](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Nastavení [webhook](../../azure-monitor/platform/alerts-webhooks.md), který je volán při vydání výstrahy.

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek o řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další kroky

* [Upozornění na dostupnost](availability-alerts.md)
* [Vícekrokové webové testy](availability-multistep.md)


