---
title: Sledování dostupnosti a odezvy libovolných webů | Dokumentace Microsoftu
description: Nastavení testů webu ve službě Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: a5bee2da5059213e85e03d5a0e4df0ef88c26b03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986026"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorování dostupnosti jakéhokoli webu

Po nasazení webové aplikace nebo webu můžete nastavit opakované testy pro monitorování dostupnosti a odezvy. [Azure Application Insights](./app-insights-overview.md) odesílá do vaší aplikace webové požadavky v pravidelných intervalech z bodů po celém světě. Může vás upozornit, pokud vaše aplikace nereaguje nebo pokud reaguje příliš pomalu.

Testy dostupnosti můžete nastavit pro libovolný koncový bod HTTP nebo HTTPS, který je přístupný z veřejného internetu. Na webu, který testujete, nemusíte dělat žádné změny. Ve skutečnosti to ani nemusí být lokalita, kterou vlastníte. Můžete testovat dostupnost REST API, na kterém je vaše služba závislá.

### <a name="types-of-availability-tests"></a>Typy testů dostupnosti:

Existují tři typy testů dostupnosti:

* [Testování ping adresy URL](#create-a-url-ping-test): jednoduchý test, který můžete vytvořit na portálu Azure.
* [Webový test s více kroky](availability-multistep.md): záznam sekvence webových požadavků, které je možné přehrát zpětně testovat složitější scénáře. Webové testy s více kroky jsou vytvořeny v Visual Studio Enterprise a nahrány na portál pro provádění.
* [Testy dostupnosti vlastních stop](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): Pokud se rozhodnete vytvořit vlastní aplikaci pro spuštění testů dostupnosti, `TrackAvailability()` lze použít metodu k odeslání výsledků do Application Insights.

**Pro každý prostředek Application Insights můžete vytvořit testy dostupnosti až 100.**

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Aby bylo možné vytvořit test dostupnosti, musíte nejprve vytvořit prostředek Application Insights. Pokud jste už vytvořili prostředek, přejděte k další části a [vytvořte test adresy URL pomocí testu URL](#create-a-url-ping-test).

V Azure Portal vyberte **vytvořit prostředek**  >  **vývojářské nástroje**  >  **Application Insights** a [vytvořte prostředek Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Vytvoření testu adresy URL pomocí příkazu Ping

Název "test příkazu" URL testu adresy URL je bitovou příponou misnomer. Aby bylo jasné, že tento test neprovádí žádné použití protokolu ICMP (Internet Control Message Protocol) ke kontrole dostupnosti vašeho webu. Místo toho používá pokročilejší funkci požadavku HTTP k ověření, zda koncový bod reaguje. Měří také výkon spojený s touto odpovědí a přidává možnost nastavit vlastní kritéria úspěchu v kombinaci s pokročilejšími funkcemi, jako je například analýza závislých požadavků a umožnění opakování.

Pokud chcete vytvořit svou první žádost o dostupnost, otevřete podokno dostupnost a vyberte **vytvořit test**.

![Vyplňte alespoň adresu URL webu](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Vytvořit test

|Nastavení| Vysvětlení
|----|----|----|
|**Adresa URL** |  Adresa URL může být libovolná webová stránka, kterou chcete otestovat, ale musí být viditelná z veřejného internetu. Adresa URL může obsahovat řetězec dotazu. To znamená, že můžete také trochu vyzkoušet svou databázi. Pokud se adresa URL přeloží na přesměrování, budeme ji sledovat až po 10 přesměrování.|
|**Analyzovat závislé požadavky**| Test žádostí o obrázky, skripty, soubory stylu a další soubory, které jsou součástí testované webové stránky. Zaznamenaná doba odezvy zahrnuje i čas potřebný k získání těchto souborů. Test se nezdaří, pokud některý z těchto prostředků nelze úspěšně stáhnout v časovém limitu pro celý test. Pokud tato možnost není zaškrtnutá, test si vyžádá pouze soubor na zadané adrese URL. Povolení této možnosti má za následek přísnější kontrolu. Test se nezdařil pro případy, které nemusí být při ručním procházení lokality patrné.
|**Povolit opakování**|v případě, že se test nezdařil, bude opakován po krátkém intervalu. Selhání je nahlášeno pouze v případě tří po sobě jdoucích neúspěšných pokusů. Následné testy jsou pak provedeny s obvyklou frekvencí testu. Opakování je dočasně pozastaveno do dalšího úspěchu. Toto pravidlo platí nezávisle na každém umístění testu. **Tuto možnost doporučujeme**. V průměru přibližně 80 % selhání při opakování zmizí.|
|**Frekvence testování**| Nastaví, jak často se test spouští z každého umístění testu. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.|
|**Testovací umístění**| Jsou místa, odkud naše servery odesílají webové požadavky na adresu URL. **Náš minimální počet doporučených testovacích umístění je pět** , abyste měli jistotu, že můžete odlišit problémy na webu od problémů se sítí. Můžete vybrat až 16 umístění.

**Pokud vaše adresa URL není viditelná z veřejného Internetu, můžete vybrat možnost selektivně otevřít bránu firewall, aby povolovala pouze testovací transakce**. Další informace o výjimkách brány firewall pro testovací agenty dostupnosti najdete v [Průvodci IP adresou](./ip-addresses.md#availability-tests).

> [!NOTE]
> Důrazně doporučujeme testování z více umístění s **minimálně pěti umístěními**. K tomu je potřeba zabránit falešným poplachům, které mohou být způsobeny přechodnými problémy s konkrétním umístěním. Kromě toho jsme zjistili, že optimální konfigurace má mít **počet testovacích umístění stejný jako prahová hodnota umístění výstrahy + 2**.

### <a name="success-criteria"></a>Kritéria úspěchu

|Nastavení| Vysvětlení
|----|----|----|
| **Časový limit testu** |Snižte tuto hodnotu, aby se zobrazila výstraha týkající se pomalých odpovědí. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.|
| **Odpověď HTTP** | Vrácený stavový kód, který se počítá jako úspěch. 200 je kód, který označuje, že byla vrácena normální webová stránka.|
| **Shoda obsahu** | Řetězec, například "Welcome!" U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat. **U shody obsahu se podporují jenom anglické znaky.** |

### <a name="alerts"></a>Výstrahy

|Nastavení| Vysvětlení
|----|----|----|
|**Téměř v reálném čase (Preview)** | Doporučujeme používat upozornění téměř v reálném čase. Konfigurace tohoto typu upozornění se provádí po vytvoření testu dostupnosti.  |
|**Klasický** | Pro nové testy dostupnosti už nedoporučujeme používat klasické výstrahy.|
|**Prahová hodnota umístění výstrahy**|Doporučujeme minimálně 3/5 umístění. Optimální vztah mezi prahovou hodnotou umístění výstrahy a počtem testovacích umístění je **prahová hodnota pro umístění upozornění**  =  **v umístění testovacích umístění – 2, minimálně pět umístění testu.**|

## <a name="see-your-availability-test-results"></a>Zobrazení výsledků testu dostupnosti

Výsledky testů dostupnosti lze vizuálně zobrazit pomocí spojnicových i bodových zobrazení.

Po několika minutách kliknutím na **aktualizovat** zobrazíte výsledky testů.

![Snímek obrazovky zobrazující stránku dostupnosti se zvýrazněným tlačítkem aktualizovat](./media/monitor-web-app-availability/availability-refresh-002.png)

Zobrazení scatterplot zobrazuje ukázky výsledků testů, které obsahují podrobné informace o testovacím kroku testu. V případě testů obsahujících selhání ukládá testovací modul diagnostické informace. U úspěšných testů se diagnostické informace ukládají pro celou dílčí sadu provedení. Pokud chcete zobrazit test, název testu a umístění, najeďte myší na kteroukoli zelenou/červenou tečku.

![Řádkové zobrazení](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Výběrem určitého testu nebo umístění nebo zkrácením časového období zobrazíte více výsledků v požadovaném časovém období. V Průzkumníku služby Hledání můžete zobrazit výsledky všech provedení a pomocí dotazů služby Analytics můžete na těchto datech spouštět vlastní sestavy.

## <a name="inspect-and-edit-tests"></a> Kontrola a úprava testů

Chcete-li upravit, dočasně zakázat nebo odstranit test, klikněte na tři tečky vedle názvu testu. Může trvat až 20 minut, než se změny konfigurace rozšíří do všech testovacích agentů po provedení změny.

![Zobrazit podrobnosti o testu. Úprava a zakázání webového testu](./media/monitor-web-app-availability/edit.png)

Při provádění údržby služby může být vhodné zakázat testy dostupnosti nebo pravidla upozornění, která jsou s nimi spojená.

## <a name="if-you-see-failures"></a>Pokud vidíte selhání

Klikněte na červenou tečku.

![Klikněte na červenou tečku](./media/monitor-web-app-availability/open-instance-3.png)

Z výsledku testu dostupnosti můžete zobrazit podrobnosti transakce napříč všemi komponentami. Tady můžete:

* Kontrolovat odpověď přijatou ze serveru.
* Při zpracování testu dostupnosti došlo k chybě při diagnostice na straně serveru shromážděné korelaceí.
* Zaprotokoluje problém nebo pracovní položku v Gitu nebo Azure Boards pro sledování problému. Chyba bude obsahovat odkaz na tuto událost.
* Otevřít výsledek webového testu v sadě Visual Studio.

Další informace o prostředí diagnostiky koncových transakcí [najdete tady](./transaction-diagnostics.md).

Kliknutím na řádek výjimky zobrazíte podrobnosti o výjimce na straně serveru, která způsobila selhání testu syntetické dostupnosti. Můžete také získat [snímek ladění](./snapshot-debugger.md) pro bohatší diagnostiku úrovně kódu.

![Diagnostika na straně serveru](./media/monitor-web-app-availability/open-instance-4.png)

Kromě nezpracovaných výsledků můžete také zobrazit dvě klíčové metriky dostupnosti v [Průzkumník metrik](../platform/metrics-getting-started.md):

1. Dostupnost: procento testů, které proběhly úspěšně, vzhledem k celkovému počtu provedení testu.
2. Doba trvání testu: průměrná doba trvání u všech provedení testu.

## <a name="automation"></a>Automation

* [Automatické nastavení testu dostupnosti pomocí skriptů PowerShell](./powershell.md#add-an-availability-test).
* Nastavení [webhook](../platform/alerts-webhooks.md), který je volán při vydání výstrahy.

## <a name="troubleshooting"></a>Řešení potíží

Vyhrazený [článek týkající se řešení potíží](troubleshoot-availability.md).

## <a name="next-steps"></a>Další kroky

* [Výstrahy dostupnosti](availability-alerts.md)
* [Vícekrokové webové testy](availability-multistep.md)

