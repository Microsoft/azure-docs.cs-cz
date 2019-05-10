---
title: Možnosti typu ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak využít možnosti Azure Sentinelu typu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 754722508de248294667ee9e923e8faf3ad67f61
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236284"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Hunt hrozby s ve verzi Preview Sentinelu Azure

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud jste vyšetřovatel, který chce být proaktivní o vyhledávání bezpečnostních hrozeb, Azure Sentinelu výkonné myslivost nástrojích Hledat a dotaz na hunt bezpečnostních hrozeb napříč zdroji dat vaší organizace. Ale vašimi systémy a zabezpečovací zařízení generovat hory data, která může být obtížné analyzovat a filtrovat do smysluplné události. Chcete-li pomoci zabezpečení analytici vyhledejte proaktivně nové anomálie, které nebyly zjištěny nástrojem zabezpečení aplikací, Azure Sentinelu "integrované typu dotazy vás do klást ty pravé otázky a najít problémy v datech už máte v síti. 

Například jeden předdefinované poskytují údaje o nejvíce neobvyklé procesů běžících ve vaší infrastruktuře – není vhodné upozornění týkající se pokaždé, když se použijí, by mohly být zcela nevinnosti, ale můžete chtít podívejte se na dotazu v některých případech, abyste zjistili, jestli th ere vaší nic neobvyklého. 



Azure Sentinelu typu, můžete využít následující možnosti:

- Předdefinované dotazy: Abyste mohli začít, počáteční stránky poskytuje příklady předem načtených dotazů, které jsou navržené tak, aby vám pomůžou začít a vás seznámí s tabulkami a dotazovací jazyk. Tyto dotazy předdefinované typu se vyvíjejí na základě zabezpečení výzkumným pracovníkům společnosti Microsoft na základě průběžné přidání nových dotazů a upřesnění existující dotazy, kde přinášejí vstupní bod pro nové detekce a zjistit, kde začít hledání začátku novými útoky. 

- Výkonný dotazovací jazyk s podporou technologie IntelliSense: Postavená na dotazovací jazyk, který vám poskytuje flexibilitu, které musíte provést typu na další úroveň.

- Vytvoření vlastních záložek: Během procesu typu můžete narazit na shody nebo zjištění, řídicí panely nebo aktivity, které vypadají neobvyklé nebo podezřelé. Aby bylo možné označit ty položky, můžete se vrátit k nim v budoucnu, použijte funkci záložku. Záložky umožňují uložit položky pro pozdější použití, který se má použít k vytvoření případu pro zkoumání. Další informace o záložky najdete v článku použití [záložky v typu].

- Použití poznámkových bloků pro automatizaci šetření: Poznámkové bloky jsou jako krok za krokem playbooky, které můžete vytvářet provede jednotlivými kroky šetření a dost možná nejlepší.  Poznámkové bloky zapouzdřovat všechny kroky typu v opakovaně použitelné playbook, který můžete sdílet s ostatními ve vaší organizaci. 
- Dotaz uložených dat: Data jsou přístupná v tabulkách, které pro vás k dotazování. Například se můžete dotazovat vytváření procesů, události DNS a mnoho dalších typů událostí.

- Odkazy na komunity: Využijte síly větší komunity najít další zdroje pro dotazy a data.
 
## <a name="get-started-hunting"></a>Začínáme myslivost

1. Na portálu Azure Sentinelu, klikněte na tlačítko **typu**.
  ![Azure Sentinel spustí typu](media/tutorial-hunting/hunting-start.png)

2. Když otevřete **typu** stránce, všechny dotazy typu se zobrazují v jedné tabulce. V tabulce jsou uvedeny všechny dotazy napsané týmem Microsoftu analytikům zabezpečení, stejně jako jakýkoli další dotaz jste vytvořili nebo upravili. Každý dotaz obsahuje popis co hunts pro a jaký druh dat je spuštěný. Tyto šablony jsou seskupeny podle jejich různé taktika – typy hrozeb, jako je například počáteční přístup, uchovávat a průsak ven kategorizace ikony na pravé straně. Tyto šablony dotazu typu pomocí libovolné pole můžete filtrovat. Jakýkoli dotaz můžete uložit do oblíbených položek. Dotaz uložíte do oblíbených položek, dotaz spustí automaticky pokaždé, když **typu** navštěvované stránky. Můžete vytvořit vlastní dotaz typu nebo klonování a přizpůsobit existující šablonu typu dotazu. 
 
2. Klikněte na tlačítko **spuštění dotazu** v typu stránce Podrobnosti dotazu můžete spouštět jakýkoli dotaz, aniž byste opustili stránku typu.  Počet shody se zobrazí v tabulce. Projděte si seznam typu dotazy a jejich odpovídající položky. Podívejte se na fázi v řetězu událostí, který je přidružen ke shodě.

3. Provést stručné shrnutí základního dotazu v podokně podrobností dotazu nebo klikněte na tlačítko **zobrazení výsledku dotazu** otevřete dotaz v Log Analytics. V dolní části Zkontrolujte shody pro dotaz.

4.  Klikněte na řádek a vyberte **přidat záložku** pro přidání řádků, které chcete prošetřit – můžete udělat pro všechno, co, které vypadají podezřele. 

5. Pak přejděte zpátky na hlavní **typu** stránky a klikněte na tlačítko **záložky** kartu pro zobrazení všech podezřelých aktivit. 

6. Vyberte záložku a pak klikněte na tlačítko **prošetření** otevřít možnosti pro zkoumání. Záložky můžete filtrovat. Například pokud zkoumáte kampaně, můžete vytvořit značku u kampaně a vyfiltrujte všechny záložky kampani.

1. Po zjištění typu dotazu, který poskytuje vysokou hodnotu přehledy o možných útoků můžete také vytvořit vlastní zjišťování pravidla založená na dotazu a zpracujte tyto poznatky jako oznámení na vaše respondéry incidentů zabezpečení.

 

## <a name="query-language"></a>Dotazovací jazyk 

Podle typu v Azure Sentinelu dotazovací jazyk Azure Log Analytics. Další informace o podporovaných operátory a dotazovací jazyk, naleznete v tématu [referenční informace k jazyku dotazu](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Úložiště GitHub veřejného typu dotazu

Podívejte se [úložiště typu dotazu](https://github.com/Azure/Orion). Přidávat a použít Příklady dotazů sdílet našich zákazníků.

 

## <a name="sample-query"></a>Ukázkový dotaz

Typický dotaz začíná název tabulky, za nímž následuje řadu operátorů oddělené \|.

V příkladu výše, začíná v tabulce Název SecurityEvent a podle potřeby přidejte potrubím elementy.

1. Definujte filtr času ke kontrole pouze záznamy z posledních sedmi dnů.

2. Přidání filtru v dotazu, aby se zobrazily pouze události ID 4688.

3. Přidání filtru v dotazu na příkazový řádek tak, aby obsahovala pouze instance podtypu cscript.exe.

4. Projekt pouze sloupce zajímá zkoumání a omezit rozsah výsledků na 1000 a klikněte na tlačítko **spuštění dotazu**.
5. Kliknutím na zelený trojúhelník a spusťte dotaz. Můžete otestovat dotaz a spusťte ho, abyste vyhledejte neobvyklé chování.

## <a name="useful-operators"></a>Užitečné operátory

Výkonný dotazovací jazyk a má k dispozici mnoho operátorů, tady jsou uvedené některé užitečné operátory:

**kde** -tabulku na podmnožinu řádků, které splňují predikát filtru.

**Shrnutí** – vytvořit tabulku, která agreguje obsah ve vstupní tabulce.

**připojení k** -sloučí řádky dvou tabulek a vytvoří novou tabulku porovnáním hodnoty zadané sloupce z každé tabulky.

**počet** – vrátí počet záznamů ve vstupní sadě záznamů.

**horní** -vrácení prvních N záznamů seřazených podle zadaných sloupců.

**limit** – vrátí až po zadaný počet řádků.

**projekt** – vyberte sloupce, které chcete zahrnout, přejmenovat nebo vyřadit a vložit nové vypočítané sloupce.

**rozšíření** – vytvoření počítaných sloupců a připojit k sadě výsledků.

**makeset** -vrátit dynamická pole (JSON) sadu jedinečných hodnot, které přebírá výraz ve skupině

**Najít** -vyhledání řádků, které splňují predikát mezi sadu tabulek.

## <a name="save-a-query"></a>Uložit dotaz

Můžete vytvořit nebo upravit dotaz a uložte ho jako vlastní dotaz nebo ho sdílet s uživateli, kteří jsou ve stejném tenantovi.

   ![Uložit dotaz](./media/tutorial-hunting/save-query.png)

Vytvořte nový dotaz typu:

1. Klikněte na tlačítko **nový dotaz** a vyberte **Uložit**.
2. Vyplňte všechna pole prázdné a vyberte **Uložit**.

   ![Nový dotaz](./media/tutorial-hunting/new-query.png)

Klonovat a upravit existující dotaz typu:

1. Výběr typu dotazu v tabulce, kterou chcete upravit.
2. Vyberte tři tečky (...) na řádku dotazu, kterou chcete upravit a možnost **klonovat dotaz**.

   ![klonovat dotaz](./media/tutorial-hunting/clone-query.png)
 

3. Úprava dotazu a vyberte **vytvořit**.

   ![vlastní dotaz](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak spustit šetření typu s Sentinelu Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:


- [Použití poznámkových bloků ke spuštění automatizovaných typu kampaně](notebooks.md)
- [Použití záložek ke uložit zajímavé informace při typu](bookmarks.md)