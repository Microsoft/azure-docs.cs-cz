---
title: Vlastní pole v Azure Log Analytics | Microsoft Docs
description: Vlastní pole funkci analýzy protokolů můžete vytvořit vlastní prohledávatelné pole ze záznamů analýzy protokolů, které přidávají do vlastností shromážděných záznamu.  Tento článek popisuje postup, jak vytvořit vlastní pole a poskytuje podrobný návod s událost vzorku.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: f0f3311f35f954f81560cad21e7f0e3bc850a094
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28030689"
---
# <a name="custom-fields-in-log-analytics"></a>Vlastní pole v analýzy protokolů
**Vlastní pole** funkce analýzy protokolů můžete rozšířit existující záznamy v analýzy protokolů přidat vlastní prohledávatelné pole.  Vlastní pole se vyplní automaticky ze dat extrahovaných z dalších vlastností ve stejném záznamu.

![Vlastní pole – přehled](media/log-analytics-custom-fields/overview.png)

Například následující ukázkový záznam obsahuje užitečné data schovaný v popisu události.  Extrahování tato data do samostatné vlastností, tak bude dostupný pro činnosti, jako je řazení a filtrování.

![Tlačítko vyhledat protokolu](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> Ve verzi Preview jsou omezeny na 100 vlastní pole, v pracovním prostoru.  Tento limit bude rozšířena, když tato funkce dosáhne obecné dostupnosti.
> 
> 

## <a name="creating-a-custom-field"></a>Vytváření vlastních polí
Když vytvoříte vlastní pole, analýzy protokolů musíte pochopit datových sloužící k naplnění jeho hodnotu.  Používá technologii služby Microsoft Research názvem FlashExtract rychle identifikovat tato data.  Místo bylo třeba zadávat explicitní pokyny, analýzy protokolů dozví o data, která mají být extrahovány z příklady, které zadáte.

Postup pro vytváření vlastních polí v následujících částech.  V dolní části tohoto článku je návod, ukázka extrakce.

> [!NOTE]
> Při přidávání záznamů odpovídající zadaným kritériím k analýze protokolů, takže se zobrazí pouze na záznamy shromážděných po vytvoření vlastních polí, zadá vlastní pole.  Vlastní pole nepřidají záznamy, které jsou již v úložišti dat při jeho vytvoření.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1 – určete, které bude mít vlastní pole
Prvním krokem je identifikovat záznamy, které získají vlastní pole.  Spustit s [standardní protokol hledání](log-analytics-log-searches.md) a pak vyberte záznam tak, aby fungoval jako model, který analýzy protokolů se dozvíte z.  Pokud určíte, že chcete extrahovat data do vlastních polí, **Průvodce extrakce pole** je otevřen, kde můžete ověřit a upřesněte kritéria.

1. Přejděte na **hledání protokolů** a použít [dotaz pro načtení záznamů](log-analytics-log-searches.md) , bude mít vlastní pole.
2. Vyberte záznam, který bude používat analýzy protokolů tak, aby fungoval jako model pro extrahování dat k vyplnění pole vlastní.  Identifikujete data, která mají být extrahovány z tento záznam a analýzy protokolů bude tyto informace slouží k určení logiku k vyplnění pole vlastní všechny podobné záznamy.
3. Klikněte na tlačítko nalevo od žádnou vlastnost text záznam a vyberte **rozbalte pole z**.
4. **Pole extrakce průvodce otevřete**, a záznam, který jste vybrali se zobrazí v **hlavní příklad** sloupce.  Vlastní pole bude určena pro tyto záznamy se stejnými hodnotami ve vlastnostech, které jsou vybrány.  
5. Pokud je výběr přesně co chcete použít, vyberte další pole upřesněte kritéria.  Chcete-li změnit hodnoty polí pro kritéria, musíte zrušit a vyberte jiný záznam odpovídající kritériím, které chcete.

### <a name="step-2---perform-initial-extract"></a>Krok 2 – provádět počáteční extrakce.
Jakmile jste zformulovali záznamy, které budou mít vlastní pole, můžete identifikovat data, která mají být extrahovány.  Analýzy protokolů bude tyto informace slouží k identifikaci vzorů podobně jako v podobných záznamů.  V kroku po to bude možné ověřit výsledky a poskytnout další podrobnosti o analýzy protokolů pro použití v jeho analýzu.

1. Zvýrazněte text ve vzorku záznam, který chcete naplnit vlastní pole.  Potom zobrazí se dialogové okno, zadejte název pro pole a provádět počáteční extract.  Znaky  **\_CR** se automaticky připojí.
2. Klikněte na tlačítko **extrahovat** provést analýzu shromážděných záznamů.  
3. **Souhrn** a **výsledky hledání** části zobrazit výsledky extract, takže si můžete prohlédnout její přesnost.  **Souhrn** zobrazuje kritéria použít k identifikaci záznamů a počtu pro každou z hodnot dat identifikovat.  **Výsledky hledání** poskytuje podrobný seznam záznamů, které odpovídají kritériím.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 – ověřte správnost extract a vytvořit vlastní pole
Po provedení počáteční extrakce, analýzy protokolů se zobrazí její výsledky na základě dat, které již byly zjištěny.  Pokud výsledky vypadají přesné můžete vytvořit vlastní pole s žádné další práci.  Pokud ne, pak zpřesnění výsledků, aby analýzy protokolů může zlepšit svou logikou.

1. Pokud všechny hodnoty v počáteční extract nejsou správné, klikněte **upravit** Ikona vedle nesprávné záznamů a vyberte možnost **upravit tento zvýraznění** k úpravě výběru.
2. Položka se zkopíruje do **Další příklady** části pod **hlavní příklad**.  Zvýraznění Zde můžete upravit pomohou analýzy protokolů pochopení toho, co by měl provedli.
3. Klikněte na tlačítko **extrahovat** do této nové informace slouží k vyhodnocení všechny existující záznamy.  Pro záznamy než ten, který jste právě provedli změnu podle této nové intelligence může upravit výsledky.
4. Pokračujte pro přidání opravy, dokud všechny záznamy v extract správně identifikovat data k naplnění nové vlastní pole.
5. Klikněte na tlačítko **uložit extrahovat** po s výsledky spokojeni.  Vlastní pole je nyní definována, ale ještě nebude přidáno žádné záznamy.
6. Počkejte, než pro nové záznamy odpovídající zadaným kritériím shromažďovat a poté znovu spusťte hledání protokolů. Nové záznamy by měl mít vlastní pole.
7. Vlastní pole jako ostatní záznamů vlastnost použijte.  Můžete ji použít k data agregační a skupiny a používat i k vytvoření nové statistiky.

## <a name="viewing-custom-fields"></a>Zobrazení vlastních polí
Můžete zobrazit seznam všech vlastních polí ve vaší skupině pro správu z **Upřesnit nastavení** nabídce pracovního prostoru analýzy protokolů na portálu Azure.  Vyberte **Data** a potom **vlastní pole** seznam všech vlastních polí v pracovním prostoru.  

![Vlastní pole](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Odebrání vlastního pole
Existují dva způsoby, jak odebrat vlastní pole.  První je **odebrat** možnost pro každé pole při zobrazení seznamu dokončení, jak je popsáno výše.  Další metodou je načíst záznam a klikněte na tlačítko vlevo od pole.  V nabídce bude mít možnost odebrat vlastní pole.

## <a name="sample-walkthrough"></a>Ukázka návod
V následující části vás provede kompletní příklad, jak vytváření vlastních polí.  Tento příklad extrahuje názvu služby v systému Windows události, které označují změna stavu služby.  To závisí na události vytvořené pomocí Správce řízení služeb v protokolu systému na počítačích se systémem Windows.  Pokud chcete v tomto příkladu, musí být [shromažďování událostí informace v systémovém protokolu](log-analytics-data-sources-windows-events.md).

Jsme zadejte následující dotaz, který vrátí všechny události ze Správce řízení služeb s ID události z 7036, které je událost, která označuje, spuštění nebo zastavení služby.

![Dotaz](media/log-analytics-custom-fields/query.png)

Zvolte jsme žádný záznam se události ID 7036.

![Zdrojový záznam](media/log-analytics-custom-fields/source-record.png)

Chceme, aby název služby, který se zobrazí v **RenderedDescription** vlastnost a vyberte tlačítko vedle této vlastnosti.

![Rozbalte pole](media/log-analytics-custom-fields/extract-fields.png)

**Pole extrakce průvodce** otevření a **protokolu událostí** a **EventID** pole jsou vybrána v **hlavní příklad** sloupce.  To znamená, že bude pro události v systémovém protokolu s ID události 7036 definována vlastní pole.  To je dostatečná, takže není třeba vybrat jiné pole.

![Hlavní příklad](media/log-analytics-custom-fields/main-example.png)

Jsme zvýrazněte název služby **RenderedDescription** vlastnost a použití **služby** k identifikaci názvu služby.  Vlastní pole bude volána **Service_CF**.

![Název pole](media/log-analytics-custom-fields/field-title.png)

Vidíte, název služby je pro některé záznamy, ale ne pro ostatní identifikovány správně.   **Výsledky hledání** zobrazit část názvu **adaptér výkonu služby WMI** nebyla vybrána.  **Souhrn** ukazuje, že čtyři zaznamenává s **DPRMA** služby zahrnuty nesprávně navíc word a dva záznamy identifikovat **instalační program moduly** místo **Instalační služby systému Windows moduly**.  

![Výsledky hledání](media/log-analytics-custom-fields/search-results-01.png)

Začneme s **adaptér výkonu služby WMI** záznamu.  Kliknete na ikonu pro úpravu a potom **upravit tento zvýraznění**.  

![Upravit zvýraznění](media/log-analytics-custom-fields/modify-highlight.png)

Jsme zvýšit zvýraznění zahrnout slovo **WMI** a poté znovu spusťte extract.  

![Další příklad](media/log-analytics-custom-fields/additional-example-01.png)

Jsme uvidíte, že položky pro **adaptér výkonu služby WMI** opravení, a analýzy protokolů také používají tyto informace pro opravte záznamy pro **Instalační služby systému Windows modulu**.  Vidíme v **Souhrn** části ale který **DPMRA** není právě nadále identifikovány správně.

![Výsledky hledání](media/log-analytics-custom-fields/search-results-02.png)

Přejděte na záznam se služba DPMRA jsme použít stejný postup k odstranění záznamů.

![Další příklad](media/log-analytics-custom-fields/additional-example-02.png)

 Při extrahování spustíme, jsme vidět, že všechny naše výsledky jsou nyní správné.

![Výsledky hledání](media/log-analytics-custom-fields/search-results-03.png)

Jsme uvidíte, že **Service_CF** je vytvořen, ale ještě není přidáno žádné záznamy.

![Počáteční počet](media/log-analytics-custom-fields/initial-count.png)

Po určité době uplynutí tak nové události jsou shromažďovány, jsme uvidíte, že, **Service_CF** pole teď se přidává do záznamy, které odpovídají naše kritériím.

![Konečné výsledky](media/log-analytics-custom-fields/final-results.png)

Jsme teď můžete použít vlastní pole jako libovolné jiné vlastnosti záznamu.  Pro znázornění je se nám vytvořit dotaz, který seskupuje novými **Service_CF** pole, které chcete zkontrolovat, které služby jsou nejvíce aktivní.

![Seskupit podle dotazu](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Další postup
* Další informace o [protokolu hledání](log-analytics-log-searches.md) k vytvoření dotazů pomocí vlastních polí pro kritéria.
* Monitorování [vlastní protokol soubory](log-analytics-data-sources-custom-logs.md) , analyzovat pomocí vlastních polí.

