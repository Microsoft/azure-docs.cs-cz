---
title: Pomocí portálu pro prohledávání protokolu ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek obsahuje kurz popisující vytváření prohledávání protokolů a analýzu dat uložených ve vašem pracovním prostoru Log Analytics pomocí portálu pro prohledávání protokolů.  Tento kurz zahrnuje spuštění několika jednoduchých dotazů, které vrátí různé typy dat, a analýzu výsledků.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 532df20a7639f42d8ba1c840a5fd19f0ad0e4042
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246329"
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Vytváření prohledávání protokolů v Azure Log Analytics pomocí portálu pro prohledávání protokolů

Tento článek obsahuje kurz popisující vytváření prohledávání protokolů a analýzu dat uložených ve vašem pracovním prostoru Log Analytics pomocí portálu pro prohledávání protokolů.  Tento kurz zahrnuje spuštění několika jednoduchých dotazů, které vrátí různé typy dat, a analýzu výsledků.  Zaměřuje se na funkce na portálu pro prohledávání protokolů pro úpravy dotazu a nikoli ho upravovali přímo.  Podrobnosti o přímo upravit dotaz, najdete v článku [dotazovací jazyk odkaz](https://go.microsoft.com/fwlink/?linkid=856079).

Pokud chcete vytvořit vyhledávání v portálu pro pokročilou analýzu místo portál pro prohledávání protokolů, najdete v článku [Začínáme s portálem Analytics](https://go.microsoft.com/fwlink/?linkid=856587).  Oba portály používat stejný jazyk dotazu pro přístup ke stejným datům v pracovním prostoru Log Analytics.

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že už máte pracovní prostor Log Analytics se aspoň jeden připojený zdroj, který generuje data pro dotazy pro analýzy.  

- Pokud nemáte pracovní prostor, můžete vytvořit pomocí postupu v bezplatné předplatné [Začínáme s pracovním prostorem Log Analytics](log-analytics-get-started.md).
- Připojit nejméně jeden [agenta Windows](log-analytics-windows-agent.md) nebo jeden [agenta pro Linux](log-analytics-linux-agents.md) do pracovního prostoru.  

## <a name="open-the-log-search-portal"></a>Otevření portálu pro prohledávání protokolů
Začněte otevřením portálu pro prohledávání protokolů. 

1. Otevřete web Azure Portal.
2. Přejděte do Log Analytics a vyberte svůj pracovní prostor.
3. Vyberte **Protokoly**.


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

## <a name="set-the-time-scope"></a>Nastavte časový rozsah
Má každý záznam shromážděná službou Log Analytics **TimeGenerated** vlastnost, která obsahuje datum a čas, který byl vytvořen záznam.  Dotaz na portálu pro prohledávání protokolů vrátí pouze záznamy s **TimeGenerated** v tomto časovém rozsahu, který se zobrazí na levé straně obrazovky.  

Filtr času můžete změnit tak, že vyberete rozevírací seznam nebo úpravou posuvníku.  Posuvník zobrazuje pruhový graf, který zobrazuje relativní několik záznamů pro každý segment času v rozsahu.  Tento segment bude lišit v závislosti na rozsahu.

Výchozí časový rozsah je **1 den**.  Změňte tuto hodnotu na **7 dní**, a měli byste zvýšit celkový počet záznamů.

![Datum časový rozsah](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrování výsledků dotazu
Na levé straně obrazovky je podokno filtru, ve kterém můžete do dotazu přidat filtrování, aniž byste ho upravovali přímo.  Prvních deset hodnoty s jejich počet záznamů se zobrazí několik vlastností vrácených záznamů.

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

Stačí **filtr** možnost vlastnosti, jejich název je modře.  To jsou *prohledávatelná* pole, která se indexují pro vyhledávací podmínky.  Pole zobrazená šedě jsou pole *vhodná pro volnotextové prohledávání*, u kterých je dostupná pouze možnost **Zobrazit odkazy**.  Tato možnost vrátí záznamy obsahující danou hodnotu v jakékoli vlastnosti.

![Nabídka filtru](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Výsledky můžete seskupit podle jedné vlastnosti výběrem možnosti **Seskupit podle** v nabídce záznamu.  Tím se do vašeho dotazu přidá operátor [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator), který zobrazí výsledky v grafu.  Seskupení můžete provést podle více než jedné vlastnosti, museli byste však upravit dotaz přímo.  Vyberte nabídku záznamu vedle vlastnosti **Computer** (Počítač) a vyberte **Seskupit podle Computer**.  

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
Data výkonu agentů Windows i Linuxu se ukládají v pracovním prostoru Log Analytics v tabulce **Perf**.  Podívejte se stejně jako jakýkoli jiný záznam záznamů o výkonu a napíšeme jednoduchý dotaz, který vrátí že všechny záznamy výkon stejně jako s událostmi.

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

- Další informace o dotazovací jazyk Log Analytics na [Začínáme s portálem Analytics](https://go.microsoft.com/fwlink/?linkid=856079).
- Provede pomocí kurzů [portálu pro pokročilou analýzu](https://go.microsoft.com/fwlink/?linkid=856587) tomu můžete spouštět dotazy stejným a přístup ke stejným datům jako portál pro prohledávání protokolů.
