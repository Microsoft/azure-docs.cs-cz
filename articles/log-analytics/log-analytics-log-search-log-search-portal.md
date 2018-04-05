---
title: Pomocí portálu hledání protokolů v Azure Log Analytics | Microsoft Docs
description: Tento článek obsahuje kurz, který popisuje, jak vytvořit protokolu hledání a analyzovat data uložená v pracovním prostoru analýzy protokolů pomocí portálu hledání protokolů.  Tento kurz zahrnuje spuštění několika jednoduchých dotazů, které vrátí různé typy dat, a analýzu výsledků.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 37213012e817f0fae21a47a4334a519bbbca206b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Vytvoření protokolu hledání v Azure Log Analytics pomocí portálu hledání protokolů

> [!NOTE]
> Tento článek popisuje portálu hledání protokolů v Azure Log Analytics jazykem nový dotaz.  Můžete získat další informace o nový jazyk a získat postup upgradu pracovního prostoru v [upgradu pracovní prostor analýzy protokolů Azure na nové hledání protokolu](log-analytics-log-search-upgrade.md).  
>
> Pokud pracovní prostor nebyla upgradována, aby nové dotazovací jazyk, se seznamte s [najít data pomocí protokolu hledání v analýzy protokolů](log-analytics-log-searches.md) informace o aktuální verzi portálu hledání protokolů.

Tento článek obsahuje kurz, který popisuje, jak vytvořit protokolu hledání a analyzovat data uložená v pracovním prostoru analýzy protokolů pomocí portálu hledání protokolů.  Tento kurz zahrnuje spuštění několika jednoduchých dotazů, které vrátí různé typy dat, a analýzu výsledků.  Zaměřuje se na funkce na portálu hledání protokolů pro úpravy dotazu a nikoli změny přímo.  Podrobnosti o přímou úpravou dotazu, najdete v článku [referenční příručka jazyka dotazů](https://go.microsoft.com/fwlink/?linkid=856079).

Vytvoření vyhledávání v portálu pokročilé analýzy místo portálu vyhledávání protokolu naleznete v tématu [Začínáme s portálu analýza](https://go.microsoft.com/fwlink/?linkid=856587).  Oba Portály pomocí stejné dotazovací jazyk přístup ke stejným datům v pracovním prostoru analýzy protokolů.

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že už máte pracovní prostor analýzy protokolů s alespoň jeden připojený zdroj, který generuje data pro dotazy k analýze.  

- Pokud nemáte pracovní prostor, můžete vytvořit volné jeden pomocí postupu v [začít pracovat s pracovní prostor analýzy protokolů](log-analytics-get-started.md).
- Připojit aspoň jeden [agenta Windows](log-analytics-windows-agent.md) nebo jednu [agenta systému Linux](log-analytics-linux-agents.md) do pracovního prostoru.  

## <a name="open-the-log-search-portal"></a>Otevření portálu pro prohledávání protokolů
Začněte otevřením portálu pro prohledávání protokolů. 

1. Otevřete web Azure Portal.
2. Přejděte k analýze protokolů a vyberte pracovní prostor.
3. Vyberte **protokolu vyhledávání**.

![Tlačítko vyhledat protokolu](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>Vytvoření jednoduchého hledání
Nejrychlejší způsob načtení nějakých dat, se kterými můžeme pracovat, je použít tento jednoduchý dotaz, který vrátí všechny záznamy v tabulce.  Pokud ke svému pracovnímu prostoru máte připojené nějaké klienty Windows nebo Linuxu, budete mít data v tabulce Event (Windows) nebo Syslog (Linux).

Zadejte do vyhledávacího pole jeden z následujících dotazů a klikněte na tlačítko Vyhledat.  

```
Event
```
```
Syslog
```

Data se vrátí ve výchozím zobrazení seznamu a zobrazí se celkový počet vrácených záznamů.

![Jednoduchý dotaz](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Zobrazí se pouze několik prvních vlastností každého záznamu.  Kliknutím na **zobrazit více** zobrazíte všechny vlastnosti konkrétního záznamu.

![Podrobnosti o záznamu](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Nastavit obor čas
Každý záznam shromažďují analýzy protokolů má **TimeGenerated** vlastnost, která obsahuje datum a čas, který byl vytvořen záznam.  Dotaz na portálu hledání protokolů pouze vrátí záznamy s **TimeGenerated** v rámci oboru čas, který se zobrazí na levé straně obrazovky.  

Filtr času můžete změnit tak, že vyberete rozevíracího seznamu nebo změnou posuvníku.  Posuvník Zobrazí pruhový graf, který se zobrazuje číslo relativní záznamů pro každý segment čas v rozsahu.  Tento segment se bude lišit v závislosti na rozsahu.

Rozsah výchozí doba je **1 den**.  Změna této hodnoty na **7 dní**, a celkový počet záznamů měli zvýšit.

![Datum čas oboru](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrování výsledků dotazu
Na levé straně obrazovky je podokno filtru, ve kterém můžete do dotazu přidat filtrování, aniž byste ho upravovali přímo.  Několik vlastností vrácené záznamy jsou zobrazit s jejich hodnoty prvních deset s jejich počet záznamů.

Pokud pracujete s tabulkou **Event**, zaškrtněte políčko vedle textu **Error** (Chyba) v části **EVENTLEVELNAME** (NÁZEV ÚROVNĚ UDÁLOSTI).   Pokud pracujete s tabulkou **Syslog**, zaškrtněte políčko vedle textu **err** (Chyba) v části **SEVERITYLEVEL** (ÚROVEŇ ZÁVAŽNOSTI).  Tím se dotaz změní na jeden následujících a výsledky se omezí na chybové události.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtr](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Přidejte do podokna filtru vlastnosti tím, že v nabídce vlastností jednoho ze záznamů vyberete **Přidat do filtrů**.

![Nabídka Přidat do filtrů](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Stejný filtr můžete nastavit výběrem možnosti **Filtrovat** v nabídce vlastností záznamu s hodnotou, kterou chcete filtrovat.  

Můžete mít pouze **filtru** možnost Vlastnosti s jejich název modře.  To jsou *prohledávatelná* pole, která se indexují pro vyhledávací podmínky.  Pole zobrazená šedě jsou pole *vhodná pro volnotextové prohledávání*, u kterých je dostupná pouze možnost **Zobrazit odkazy**.  Tato možnost vrátí záznamy obsahující danou hodnotu v jakékoli vlastnosti.

![Filtr nabídky](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Výsledky můžete seskupit podle jedné vlastnosti výběrem možnosti **Seskupit podle** v nabídce záznamu.  Tím se do vašeho dotazu přidá operátor [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator), který zobrazí výsledky v grafu.  Seskupení můžete provést podle více než jedné vlastnosti, museli byste však upravit dotaz přímo.  Vyberte nabídku záznam vedle **počítače** vlastnost a vyberte **Seskupit podle "Počítač"**.  

![Seskupení podle počítače](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Práce s výsledky
Portál pro prohledávání protokolů obsahuje řadu funkcí pro práci s výsledky dotazu.  Výsledky můžete řadit, filtrovat a seskupovat a díky tomu můžete analyzovat data, aniž byste upravovali samotný dotaz.  Výsledky dotazu ve výchozím nastavení nejsou seřazené.

Pokud chcete data zobrazit v podobě tabulky, která nabízí další možnosti filtrování a řazení, klikněte na **Tabulka**.  

![Zobrazení tabulky](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Kliknutím na šipku vedle záznamu zobrazíte podrobnosti o daném záznamu.

![Řazení výsledků](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Výsledky můžete řadit podle jakéhokoli pole kliknutím na záhlaví příslušného sloupce.

![Řazení výsledků](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Výsledky můžete filtrovat podle konkrétní hodnoty ve sloupci kliknutím na tlačítko filtru a zadáním podmínky filtru.

![Filtrování výsledků](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Výsledky můžete seskupit podle konkrétního sloupce přetažením záhlaví sloupce do horní části výsledků.  Přetažením více sloupců do horní části můžete výsledky seskupit podle více polí.

![Seskupení výsledků](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Práce s daty výkonu
Data výkonu agentů Windows i Linuxu se ukládají v pracovním prostoru Log Analytics v tabulce **Perf**.  Zaznamenává výkonu vypadají stejně jako jakýkoli jiný záznam a jsme může zapisovat jednoduchý dotaz, který vrátí že všechny záznamy výkonu stejně jako s událostmi.

```
Perf
```

![Data výkonu](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Vracení milionů záznamů pro všechny objekty a čítače výkonu však není moc užitečné.  Pomocí stejných metod jako výše můžete data filtrovat nebo zadejte následující dotaz přímo do pole prohledávání protokolů.  Tento dotaz vrátí pouze záznamy o využití procesoru pro počítače s Windows i Linuxem.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Využití procesoru](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Tím se data omezí na konkrétní čítač, ale stále se nezobrazují v užitečném formátu.  Data můžete zobrazit ve spojnicovém grafu, ale nejprve je potřeba je seskupit podle polí Computer (Počítač) a TimeGenerated (Čas vygenerování).  Abyste mohli data seskupit podle více polí, musíte dotaz upravit přímo, takže ho upravte na následující dotaz.  Tento dotaz používá funkci [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) s vlastností **CounterValue** (Hodnota čítače) k výpočtu průměrných hodinových hodnot.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Graf dat výkonu](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Když jsou teď data vhodně seskupená, můžete je zobrazit ve vizuálním grafu přidáním operátoru [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Spojnicový graf](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Další postup

- Další informace o analýzy protokolů dotazovací jazyk v [Začínáme s portálu analýza](https://go.microsoft.com/fwlink/?linkid=856079).
- Provede kurz pomocí [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587) která umožňuje spouštět stejné dotazy a přístup ke stejným datům jako portál hledání protokolů.
