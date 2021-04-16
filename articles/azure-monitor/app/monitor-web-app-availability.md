---
title: Monitorování dostupnosti a odezvy jakéhokoli webu-Azure Monitor
description: Nastavte testy pro příkazy testu a testování v Application Insights. Zasílání upozornění, pokud web přestane být k dispozici nebo reaguje pomalu.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: ecfd4ffee3582ff37411e59c75d8be8fca5e945f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516593"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorování dostupnosti jakéhokoli webu

Název "test příkazu" URL testu adresy URL je bitovou příponou misnomer. Aby bylo jasné, že tyto testy nebudou používat protokol ICMP (Internet Control Message Protocol) ke kontrole dostupnosti vašeho webu. Místo toho používají pokročilejší funkci požadavků HTTP k ověření, zda koncový bod reaguje. Také měří výkon spojený s touto odpovědí a přidávají možnost nastavit vlastní kritéria úspěchu v kombinaci s pokročilejšími funkcemi, jako je analýza závislých požadavků, a povolení pro opakování.

K dispozici jsou dva typy testů adres URL typu URL, které můžete použít k testování testů, Basic a standard pro testování.

> [!NOTE]
> Testy na úrovni Basic a Standard jsou momentálně ve verzi Public Preview. Tyto verze Preview jsou poskytovány bez smlouvy o úrovni služeb. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.

Základní vs Standard:

- Úroveň Basic je omezena na pět míst na jeden test.
- Standardní testy mohou mít vlastní záhlaví nebo tělo požadavku.
- Standardní testy můžou používat jakoukoli metodu požadavku HTTP, zatímco základní může používat jenom `GET` .
- Doba životnosti certifikátu SSL: po uplynutí doby, než vyprší platnost certifikátu, se zobrazí upozornění na nastavený čas.
- Standardní testy jsou placené funkce.

> [!NOTE]
> Pro standardní testy testovacího testu pro testování funkcí verze Preview se momentálně neúčtují žádné další poplatky. Ceny pro funkce, které jsou ve verzi Preview, budou v budoucnu ohlášeny a oznámení poskytované před zahájením fakturace. Pokud se rozhodnete, že budete po období oznámení používat standardní testy pro testování pomocí příkazu, bude se vám účtovat příslušná sazba.

## <a name="create-a-url-ping-test"></a>Vytvoření testu adresy URL pomocí příkazu Ping

Aby bylo možné vytvořit test dostupnosti, je třeba použít existující prostředek Application Insights nebo [vytvořit prostředek Application Insights](create-new-resource.md).

Pokud chcete vytvořit svou první žádost o dostupnost, otevřete podokno dostupnost a vyberte vytvořit test & zvolte Test SKU.

:::image type="content" source="./media/monitor-web-app-availability/create-basic-test.png" alt-text="Snímek obrazovky vytvoření testu na základě požadavku na základní adresu URL na webu Azure Portal":::

|Nastavení | Vysvětlení |
|--------|-------------|
|**Adresa URL** |  Adresa URL může být libovolná webová stránka, kterou chcete otestovat, ale musí být viditelná z veřejného internetu. Adresa URL může obsahovat řetězec dotazu. To znamená, že můžete také trochu vyzkoušet svou databázi. Pokud se adresa URL přeloží na přesměrování, budeme ji sledovat až po 10 přesměrování.|
|**Analyzovat závislé požadavky**| Test žádostí o obrázky, skripty, soubory stylu a další soubory, které jsou součástí testované webové stránky. Zaznamenaná doba odezvy zahrnuje i čas potřebný k získání těchto souborů. Test se nezdaří, pokud některý z těchto prostředků nelze úspěšně stáhnout v časovém limitu pro celý test. Pokud tato možnost není zaškrtnutá, test si vyžádá pouze soubor na zadané adrese URL. Povolení této možnosti má za následek přísnější kontrolu. Test se nezdařil pro případy, které nemusí být při ručním procházení lokality patrné. |
|**Povolit opakování**| V případě, že se test nezdařil, bude opakován po krátkém intervalu. Selhání je nahlášeno pouze v případě tří po sobě jdoucích neúspěšných pokusů. Následné testy jsou pak provedeny s obvyklou frekvencí testu. Opakování je dočasně pozastaveno do dalšího úspěchu. Toto pravidlo platí nezávisle na každém umístění testu. **Tuto možnost doporučujeme**. V průměru přibližně 80 % selhání při opakování zmizí.|
| **Test ověřování certifikátu SSL** | Certifikát SSL můžete na svém webu ověřit, abyste se ujistili, že je správně nainstalovaný, platný, důvěryhodný a neposkytuje žádné chyby pro žádné z vašich uživatelů. |
| **Proaktivní kontroly životního cyklu** | To vám umožní definovat nastavené časové období, než vyprší platnost certifikátu SSL. Po vypršení platnosti se test nezdaří. |
|**Frekvence testování**| Nastaví, jak často se test spouští z každého umístění testu. S výchozí pětiminutovou frekvencí a pěti testovanými místy bude váš web testován v průměru každou minutu.|
|**Testovací umístění**| Jsou místa, odkud naše servery odesílají webové požadavky na adresu URL. **Náš minimální počet doporučených testovacích umístění je pět** , abyste měli jistotu, že můžete odlišit problémy na webu od problémů se sítí. Můžete vybrat více než pět umístění se standardním testem a až 16 umístění.|

**Pokud vaše adresa URL není viditelná z veřejného Internetu, můžete vybrat možnost selektivně otevřít bránu firewall, aby povolovala pouze testovací transakce**. Další informace o výjimkách brány firewall pro testovací agenty dostupnosti najdete v [Průvodci IP adresou](./ip-addresses.md#availability-tests).

> [!NOTE]
> Důrazně doporučujeme testování z více umístění s **minimálně pěti umístěními**. K tomu je potřeba zabránit falešným poplachům, které mohou být způsobeny přechodnými problémy s konkrétním umístěním. Kromě toho jsme zjistili, že optimální konfigurace má mít **počet testovacích umístění stejný jako prahová hodnota umístění výstrahy + 2**.

## <a name="standard-test"></a>Standardní test

:::image type="content" source="./media/monitor-web-app-availability/standard-test-post.png" alt-text="Snímek karty standardního testovacího údaje" border="false":::

|Nastavení | Vysvětlení |
|--------|-------------|
| **Vlastní hlavičky** | Páry klíč-hodnota, které definují parametry operačního systému. |
| **Operace požadavku HTTP** | Určete, jakou akci chcete s vaší žádostí provést. Pokud vaše zvolená operace není v uživatelském rozhraní k dispozici, můžete nasadit standardní test pomocí Sledování prostředků Azure s požadovanou volbou. |
| **Text žádosti** | Vlastní data přidružená k požadavku HTTP Do svého obsahu můžete nahrát typ vlastní soubory, nebo tuto funkci zakázat. Pro obsah nezpracovaného textu podporujeme TEXT, JSON, HTML, XML a JavaScript. |

## <a name="success-criteria"></a>Kritéria úspěchu

|Nastavení| Vysvětlení
|----|----|----|
| **Časový limit testu** |Snižte tuto hodnotu, aby se zobrazila výstraha týkající se pomalých odpovědí. Test se počítá jako selhání, pokud během tohoto období nebyly přijaty odpovědí z webu. Pokud jste vybrali možnost **Analyzovat závislé požadavky**, potom všechny image, soubory stylů, skripty a další závislé prostředky musejí být přijaty během tohoto období.|
| **Odpověď HTTP** | Vrácený stavový kód, který se počítá jako úspěch. 200 je kód, který označuje, že byla vrácena normální webová stránka.|
| **Shoda obsahu** | Řetězec, například "Welcome!" U každé odpovědi testujeme výskyt přesné shody (s rozlišováním velkých a malých písmen). Musí být prostý řetězec bez zástupných znaků. Nezapomeňte, že pokud se obsah vaší stránka změní, bude pravděpodobně nutné jej aktualizovat. **U shody obsahu se podporují jenom anglické znaky.** |

## <a name="alerts"></a>Výstrahy

|Nastavení| Vysvětlení
|----|----|----|
|**Téměř v reálném čase (Preview)** | Doporučujeme používat upozornění téměř v reálném čase. Konfigurace tohoto typu upozornění se provádí po vytvoření testu dostupnosti.  |
|**Prahová hodnota umístění výstrahy**|Doporučujeme minimálně 3/5 umístění. Optimální vztah mezi prahovou hodnotou umístění výstrahy a počtem testovacích umístění je **prahová hodnota pro umístění upozornění**  =  **v umístění testovacích umístění – 2, minimálně pět umístění testu.**|

## <a name="location-population-tags"></a>Umístění značek naplnění

Po nasazení testu adresy URL dostupnosti pomocí Azure Resource Manager se dá pro atribut geografické polohy použít následující Tagy naplnění.

#### <a name="azure-gov"></a>Gov Azure

| Zobrazovaný název   | Název souboru     |
|----------------|---------------------|
| USGov Virginie | usgov – VA – AZR        |
| USGov Arizona  | usgov-PHX-AZR       |
| USGov Texas    | usgov-TX-AZR        |
| USDoD východ     | usgov-ddeast-AZR    |
| USDoD – střed  | usgov-ddcentral-AZR |

#### <a name="azure"></a>Azure

| Zobrazovaný název                           | Název souboru   |
|----------------------------------------|-------------------|
| Austrálie – východ                         | Evropa – au – SYD – Edge  |
| Brazílie – jih                           | LATAM-br – Gru – Edge |
| USA – střed                             | US-FL – Mia – Edge    |
| Východní Asie                              | APAC-HK-hkn-AZR   |
| East US                                | US-VA-popel – AZR     |
| Francie – jih (dříve Francie – střed) | oblast EMEA-ch-ZRH-Edge  |
| Francie – střed                         | Evropa – fr – pra – Edge  |
| Japonsko – východ                             | APAC – JP – KAW – Edge  |
| Severní Evropa                           | oblast EMEA-GB-DB3-AZR   |
| USA – středosever                       | US-Il-CH1-AZR     |
| Středojižní USA                       | US-TX-SN1-AZR     |
| Southeast Asia                         | APAC-SG-Sin-AZR   |
| Spojené království – západ                                | oblast EMEA-se-ši-Edge  |
| West Europe                            | Evropa – nl – AMS – AZR   |
| USA – západ                                | US-CA-SJC-AZR     |
| Spojené království – jih                               | Evropa – ru – MSA – Edge  |

## <a name="see-your-availability-test-results"></a>Zobrazení výsledků testu dostupnosti

Výsledky testů dostupnosti lze vizuálně zobrazit pomocí spojnicových i bodových zobrazení.

Po několika minutách kliknutím na **aktualizovat** zobrazíte výsledky testů.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Snímek obrazovky zobrazující stránku dostupnosti se zvýrazněným tlačítkem aktualizovat":::

Zobrazení scatterplot zobrazuje ukázky výsledků testů, které obsahují podrobné informace o testovacím kroku testu. V případě testů obsahujících selhání ukládá testovací modul diagnostické informace. U úspěšných testů se diagnostické informace ukládají pro celou dílčí sadu provedení. Pokud chcete zobrazit test, název testu a umístění, najeďte myší na kteroukoli zelenou/červenou tečku.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Řádkové zobrazení." border="false":::

Výběrem určitého testu nebo umístění nebo zkrácením časového období zobrazíte více výsledků v požadovaném časovém období. V Průzkumníku služby Hledání můžete zobrazit výsledky všech provedení a pomocí dotazů služby Analytics můžete na těchto datech spouštět vlastní sestavy.

## <a name="inspect-and-edit-tests"></a> Kontrola a úprava testů

Chcete-li upravit, dočasně zakázat nebo odstranit test, klikněte na tři tečky vedle názvu testu. Může trvat až 20 minut, než se změny konfigurace rozšíří do všech testovacích agentů po provedení změny.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Zobrazit podrobnosti o testu. Upravte a zakažte webový test." border="false":::

Při provádění údržby služby může být vhodné zakázat testy dostupnosti nebo pravidla upozornění, která jsou s nimi spojená.

## <a name="if-you-see-failures"></a>Pokud vidíte selhání

Vyberte červenou tečku.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Snímek obrazovky s kartou s podrobnostmi o transakcích na konci" border="false":::

Z výsledku testu dostupnosti můžete zobrazit podrobnosti transakce napříč všemi komponentami. Tady můžete:

* Zkontrolujte zprávu o řešení potíží, abyste zjistili, co mohlo způsobit selhání testu, ale vaše aplikace je stále k dispozici.
* Kontrolovat odpověď přijatou ze serveru.
* Při zpracování testu dostupnosti došlo k chybě při diagnostice na straně serveru shromážděné korelaceí.
* Zaprotokoluje problém nebo pracovní položku v Gitu nebo Azure Boards pro sledování problému. Chyba bude obsahovat odkaz na tuto událost.
* Otevřít výsledek webového testu v sadě Visual Studio.

Další informace o prostředí diagnostiky koncových transakcí najdete v [dokumentaci k diagnostice](./transaction-diagnostics.md)transakcí.

Kliknutím na řádek výjimky zobrazíte podrobnosti o výjimce na straně serveru, která způsobila selhání testu syntetické dostupnosti. Můžete také získat [snímek ladění](./snapshot-debugger.md) pro bohatší diagnostiku úrovně kódu.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostika na straně serveru.":::

Kromě nezpracovaných výsledků můžete také zobrazit dvě klíčové metriky dostupnosti v [Průzkumník metrik](../essentials/metrics-getting-started.md):

1. Dostupnost: procento testů, které proběhly úspěšně, vzhledem k celkovému počtu provedení testu.
2. Doba trvání testu: průměrná doba trvání u všech provedení testu.

## <a name="automation"></a>Automation

* [Automatické nastavení testu dostupnosti pomocí skriptů PowerShell](./powershell.md#add-an-availability-test).
* Nastavení [webhook](../alerts/alerts-webhooks.md), který je volán při vydání výstrahy.


## <a name="next-steps"></a>Další kroky

* [Výstrahy dostupnosti](availability-alerts.md)
* [Webové testy s více kroky](availability-multistep.md)
* [Řešení potíží](troubleshoot-availability.md)
