---
title: Sledování dostupnosti a odezvy libovolných webů | Dokumentace Microsoftu
description: Nastavení testů webu ve službě Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303738"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorování dostupnosti všechny weby

Po nasazení webové aplikace nebo webu, můžete nastavit opakovanou testy ke sledování dostupnosti a odezvy. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. To vás může upozornit, pokud vaše aplikace nereaguje nebo zda stránka reaguje příliš pomalu.

Testy dostupnosti můžete nastavit pro libovolný koncový bod HTTP nebo HTTPS, který je přístupný z veřejného internetu. Není nutné provádět žádné změny k webu, který testujete. Ve skutečnosti ještě nemá být lokalitě, kterou vlastníte. Rozhraní REST API, které vaše služba závisí na dostupnosti můžete otestovat.

### <a name="types-of-availability-tests"></a>Typy testů dostupnosti:

Existují tři druhy testů dostupnosti:

* [Testování ping adresy URL](#create-a-url-ping-test): jednoduchý test, který můžete vytvořit na portálu Azure.
* [Vícekrokový webový test](availability-multistep.md): Záznam posloupnost webových požadavků, které se dají přehrávat na testovat složitější scénáře. Vícekrokové webové testy jsou vytvořené v sadě Visual Studio Enterprise a odeslat na portál pro spuštění.
* [Testy dostupnosti vlastní sledování](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): `TrackAvailability()` Metodu je možné vytvořit testy dostupnosti vlastní.

**Můžete vytvořit až 100 testů dostupnosti pro každý prostředek Application Insights.**

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Pokud chcete vytvořit test dostupnosti, musíte nejprve vytvořit prostředek Application Insights. Pokud již jste vytvořili prostředek, pokračujte k další části a [vytvoření testu adresy URL příkazem Ping](#create-a-url-ping-test).

Na webu Azure Portal, vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights** a [vytvoření Prostředek služby Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Vytvoření testu adresy URL pomocí příkazu Ping

Název "adresa URL příkazem ping test" je hodně misnomer. Aby bylo jasno, není tento test provádění veškeré jeho používání protokolu ICMP (Internet Control Message Protocol) ke kontrole dostupnosti vašeho webu. Místo toho používá pokročilejší funkce požadavku HTTP k ověření, zda je koncový bod reagovat. Také měří výkonnost přidružené odpověď a umožňuje nastavit kritéria úspěchu vlastní s pokročilejší funkce, jako jsou analýzy závislé požadavky a umožňuje opakovaných pokusů s velkou provázaností.

Pokud chcete vytvořit první žádosti o dostupnosti, otevřete podokno dostupnosti a vyberte **vytvoření testu**.

![Vyplňte alespoň adresu URL webu](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Vytvoření testu

|Nastavení| Vysvětlení
|----|----|----|
|**Adresa URL** |  Adresa URL může být jakékoli webové stránky, kterou chcete testovat, ale musí být viditelná z veřejného Internetu. Adresa URL může obsahovat řetězec dotazu. To znamená, že můžete také trochu vyzkoušet svou databázi. Pokud se adresa URL přeloží na přesměrování, budeme ji sledovat až po 10 přesměrování.|
|**Analyzovat závislé požadavky**| Test si vyžádá obrázky, skripty, soubory stylu a další soubory, které jsou součástí testované webové stránky. Zaznamenaná doba odezvy zahrnuje i čas potřebný k získání těchto souborů. Pokud některý z těchto prostředků nelze úspěšně stáhnout v časovém limitu pro celý test, test se nezdaří. Pokud tato možnost není zaškrtnutá, test si vyžádá pouze soubor na zadané adrese URL. Povoluje se tato možnost za následek větší kontrolu. Test může selhat pro případy, které nemusí být patrné při procházení webu ručně.
|**Povolit opakované pokusy**|Pokud se test nezdaří, zopakuje se za krátkou dobu. Selhání je nahlášeno pouze v případě tří po sobě jdoucích neúspěšných pokusů. Následné testy jsou pak provedeny s obvyklou frekvencí testu. Opakování je dočasně pozastaveno do dalšího úspěchu. Toto pravidlo platí nezávisle na každém umístění testu. **Tuto možnost doporučujeme**. V průměru přibližně 80 % selhání při opakování zmizí.|
|**Frekvence testování**| Nastaví, jak často test spustí z umístění každého testu. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.|
|**Místa testování**| Jsou místa, ze kterých naše servery odesílají webové požadavky na adresu URL. **Naše minimální počet umístění doporučené testu je pět** aby bylo možné zajistit, že možné rozlišit problémy ve vašem webu od problémů se sítí. Můžete vybrat až 16 umístění.

**Pokud vaše adresa URL není viditelná z veřejného Internetu, můžete selektivně otevře bránu firewall a povolit pouze testovací transakce prostřednictvím**. Další informace o výjimkách brány firewall pro náš testovací agenty dostupnosti, najdete [IP adres průvodce](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Důrazně doporučujeme testování z více míst s **minimálně pět umístění**. Toto je zabránit falešných poplachů, které mohou být způsobeny přechodné problémy s konkrétní umístění. Kromě toho zjistili jsme, že je optimální konfiguraci, aby **počet umístění testu být rovna mezní hodnota umístění upozornění + 2**.

### <a name="success-criteria"></a>Kritéria úspěchu

|Nastavení| Vysvětlení
|----|----|----|
| **Časový limit testu** |Zmenšete tuto hodnotu pro upozornění pomalé odezvy. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.|
| **Odpověď HTTP** | Vrácený kód stavu, který se počítá jako úspěšný. 200 je kód, který označuje, že byla vrácena normální webová stránka.|
| **Shoda obsahu** | Řetězec, například "Vítejte!" U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat. **Shody obsahu podporují pouze anglické znaky** |

### <a name="alerts"></a>Výstrahy

|Nastavení| Vysvětlení
|----|----|----|
|**Near-realtime (Preview)** | Doporučujeme použít v téměř reálném čase výstrahy. Po vytvoření testu dostupnosti se provádí konfigurace tohoto typu výstrahy.  |
|**Classic** | Už se nedoporučuje používat klasických upozornění pro nové testy dostupnosti.|
|**Mezní hodnota umístění upozornění**|Doporučujeme aspoň 3 nebo 5 umístění. Optimální vztah mezi mezní hodnota umístění upozornění a počet umístění testu je **mezní hodnota umístění upozornění** = **počet umístění testu - 2, přičemž minimum pěti testovanými místy.**|

## <a name="see-your-availability-test-results"></a>Zobrazení výsledků testu dostupnosti

Dostupnost výsledků testů lze vizualizovat pomocí řádku a bodové vykreslení zobrazení.

Po několika minutách, klikněte na tlačítko **aktualizovat** k zobrazení výsledků testu.

![Řádky – zobrazení](./media/monitor-web-app-availability/availability-refresh-002.png)

Zobrazení diagnostického obsahuje ukázky výsledků testu, které mají v nich Podrobnosti testu. V případě testů obsahujících selhání ukládá testovací modul diagnostické informace. U úspěšných testů se diagnostické informace ukládají pro celou dílčí sadu provedení. Podržením ukazatele zelených/červených tečkách naleznete v tématu test, test název a umístění.

![Řádky – zobrazení](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Výběrem určitého testu nebo umístění nebo zkrácením časového období zobrazíte více výsledků v požadovaném časovém období. V Průzkumníku služby Hledání můžete zobrazit výsledky všech provedení a pomocí dotazů služby Analytics můžete na těchto datech spouštět vlastní sestavy.

## <a name="inspect-and-edit-tests"></a>Kontrola a úprava testů

Upravit, dočasně zakázat nebo odstranit test kliknutím na tři tečky vedle názvu testu. Může trvat až 20 minut, než se změny konfigurace, které rozšíří do všech testovacích agentů, po provedení změny.

![Zobrazit podrobnosti o testu. Upravit a zakázat webový test](./media/monitor-web-app-availability/edit.png)

Při provádění údržby služby může být vhodné zakázat testy dostupnosti nebo pravidla upozornění, která jsou s nimi spojená.

## <a name="if-you-see-failures"></a>Pokud vidíte selhání

Klikněte na červenou tečku.

![Klikněte na červenou tečku](./media/monitor-web-app-availability/open-instance-3.png)

V výsledek testu dostupnosti můžete zobrazit podrobnosti o transakci napříč všemi komponentami. Zde můžete:

* Kontrolovat odpověď přijatou ze serveru.
* Diagnostikujte selhání pomocí korelační telemetrická data na straně serveru získané při zpracování test dostupnosti se nezdařilo.
* Protokolovat chyby nebo pracovní položky v Gitu nebo panely Azure kvůli sledování problému. Chyba bude obsahovat odkaz na tuto událost.
* Otevřít výsledek webového testu v sadě Visual Studio.

Přečtěte si další informace o Diagnostika transakcí kompletní prostředí [tady](../../azure-monitor/app/transaction-diagnostics.md).

Klikněte na řádku výjimky a zobrazit podrobnosti o výjimce na straně serveru, který způsobil selhání testu dostupnosti syntetického. Můžete získat také [snímek ladění](../../azure-monitor/app/snapshot-debugger.md) pro bohatší úrovně diagnostiky kódu.

![Diagnostika na straně serveru](./media/monitor-web-app-availability/open-instance-4.png)

Vedle nezpracovaných výsledků, můžete také zobrazit dvě metriky dostupnosti klíče v [Průzkumníka metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Dostupnost: Procento testů, které proběhly úspěšně, všechny provádění testů.
2. Doba trvání testu: Průměrná doba trvání u všech provedení testu.

## <a name="automation"></a>Automation

* [Automatické nastavení testu dostupnosti pomocí skriptů PowerShell](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Nastavení [webhook](../../azure-monitor/platform/alerts-webhooks.md), který je volán při vydání výstrahy.

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazené [článek pro řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další postup

* [Upozornění na dostupnost](availability-alerts.md)
* [Vícekrokové webové testy](availability-multistep.md)


