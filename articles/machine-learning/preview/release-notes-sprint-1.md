---
title: "Azure ML Workbench poznámky k 1. listopadu 2017 sprintu"
description: "Tento dokument podrobně popisuje aktualizace pro verzi sprintu 1 Azure ML"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2017
---
# <a name="sprint-1---november-2017"></a>Sprint 1 – listopad 2017 

**Číslo verze: 0.1.1710.31013**

Vítá vás druhý aktualizace Azure Machine Learning Workbench. Abychom mohli pokračovat k vylepšení v oblasti zabezpečení, stability a jeho udržovatelnost aplikace workbench, rozhraní příkazového řádku a vrstva back endové služby. Děkujeme za nám pošlete úsměvy a frowns. Řadu níže aktualizace, které jsou vytvářeny jako přímý výsledky váš názor. Uchovávejte je Připravujeme!

## <a name="notable-new-features"></a>Upozorňují na důležité nové funkce
- Azure ML je nyní k dispozici ve dvou nových oblastí Azure: **západní Evropa** a **jihovýchodní Asie**. Připojí předchozí oblasti **východní USA 2**, **– Západ střední USA**, a **Austrálie – východ**, přináší celkový počet nasazení oblasti na pět.
- Jsme povoleno syntaxe kódu Python zvýraznění v aplikaci Workbench, aby bylo snazší číst a upravovat Python zdrojového kódu. 
- Teď můžete spustit vaše oblíbené IDE přímo ze souboru, nikoli z celý projekt.  Otevření souboru v Workbench a potom kliknutím na "Upravit" spustí soubor aktuální, projektu vaší IDE (aktuálně VS Code a PyCharm jsou podporované).  Můžete také kliknout na šipku vedle tlačítka Upravit upravte soubor v textovém editoru Workbench.  Soubory jsou jen pro čtení, dokud nekliknete na tlačítko Upravit, zabránit náhodným změnám.
- Oblíbené knihovny výkresu `matplotlib` verze 2.1.0 je nyní součástí aplikace Workbench.
- Jsme upgradovat na verzi .NET Core 2.0 pro přípravný modul data. Odebrat tento požadavek pro instalaci brew openssl během instalace aplikace v systému macOS. Je také dláždí zajímavější data přípravný funkcí se v blízké budoucnosti. 
- Domovské stránce specifické pro verzi aplikace jsme povolili tak, aby získat více důležité poznámky k verzi a aktualizovat výzvy na základě vaší aktuální verze aplikace.
- Pokud vaše místní uživatelské jméno má svůj prostor v něm, můžete nyní úspěšně nainstalovat aplikace. 

## <a name="detailed-updates"></a>Podrobné aktualizace
Níže je seznam podrobné aktualizací v oblasti Azure Machine Learning v této sprintu jednotlivých součástí.

### <a name="installer"></a>Instalační program
- Instalační program aplikace teď vyčistí instalační adresář vytvořený starší verzí aplikace.
- Opravit chyby, který vede získávání zablokované ve 100 % v systému macOS vysoké Sierra Instalační služby.
- Je nyní přímý odkaz na adresáře instalačního programu pro uživatele ke kontrole instalační protokoly v případě, že instalace se nezdaří.
- Nainstalujte teď funguje pro uživatele, kteří mají místo ve své uživatelské jméno.

### <a name="workbench-authentication"></a>Workbench ověřování
- Podpora pro ověřování v správce serveru Proxy.
- Teď přihlášení úspěšné, pokud uživatel je za bránou firewall. 
- Pokud má uživatel experimentování účty v několika oblastech Azure a jedné oblasti se stane nedostupný, aplikace se už zablokuje.
- Pokud ověřování neproběhne a dialogové okno ověřování je stále zobrazena, aplikace už se pokusí načíst prostoru z místní mezipaměti.

### <a name="workbench-app"></a>Workbench aplikace
- Zvýraznění syntaxe kódu Python je povolený v textovém editoru.
- Tlačítko Upravit v textovém editoru slouží k úpravě souboru buď v IDE (VS Code a PyCharm podporuje) nebo v editoru integrovaný text.
- Textový editor, je v režimu jen pro čtení ve výchozím nastavení. 
- Uložte změny teď tlačítko visual stavu zakázáno po aktuální soubor uložený a proto už nekonzistence.
- Uloží Workbench _všechny_ neuložené soubory při zahájení spustit.
- Workbench pamatuje, že poslední používáte prostoru v místním počítači, takže se automaticky otevře.
- Pouze jedna instance nástroje Workbench je nyní povoleno spustit. Může být dříve spustit více instancí, které způsobily problémy při fungování na stejném projektu.
- Nabídka soubor přejmenovat "... otevřít projekt" na "Přidat existující složky jako projektu..." 
- Karta přepínání je mnohem rychlejší.
- Odkazy na nápovědu se přidají do dialogového okna konfigurace IDE.
- Formulář zpětné vazby teď pamatuje e-mailovou adresu zadali naposledy.
- Úsměvy a frowns textová oblast formuláře je větší, nyní, pošlete nám další názor! 
- `--owner` Přepínač text nápovědy v `az ml workspace create` po opravě.
- Jsme přidali "O" dialogové okno s pomohou uživatele snadno zobrazení a zkopírování číslo verze aplikace.
- V nabídce Nápověda naleznete vkládá položku nabídky "Navrhnout funkce".
- Název účtu experimentování je nyní viditelné v názvu aplikace panelu předcházející "Azure Machine Learning Workbench" název aplikace.
- Domovskou stránku specifické pro verzi aplikace se zobrazí, nyní na základě verze aplikace zjištěna.

### <a name="data-preparation"></a>Příprava dat 
- Externí web můžete již nebudou načteny z Inspector mapu, aby se zabránilo potenciální problémy se zabezpečením.
- Inspektoři Histogram a počet hodnot má teď možnost zobrazit graf v logaritmické měřítko.
- Při výpočtu probíhá, kvality pruh dat nyní zobrazuje barvu signál stavu "výpočet".
- Metriky nyní zobrazit statistiky sloupce pro sloupce hodnotu představující kategorii.
- Poslední znak v názvu zdroje dat už se zkrátí.
- Přípravný balíček dat nyní zůstane otevřená, při přepínání karet, výsledná zvýšení znatelné výkonu.
- Ve zdroji dat, při přepínání mezi zobrazením dat a metriky nebudou změny pořadí sloupců teď.
- Otevírání neplatný `.dprep` nebo `.dsource` soubor už způsobí, že Workbench havárií.
- Přípravný balíček dat můžete teď používá relativní cesta pro výstup v _zapisovat do souboru CSV_ transformace.
- _Zachovat sloupec_ transformace teď umožňuje uživateli přidat další sloupce upravit.
- _Nahraďte ho_ nabídky nyní ve skutečnosti spustí _nahraďte hodnotu_ dialogové okno.
- _Nahraďte hodnotu_ transformace nyní funkce očekávaným místo zobrazení chyby.
- Při odkazování na datové soubory mimo složku projekt, což umožňuje spuštění balíčku v místní kontextu s absolutní cestu k souboru dat, přípravný datový balíček teď používá absolutní cesta.
- _Celého souboru_ strategie vzorkování nyní podporuje při použití Azure blob jako zdroj dat.
- Generuje kód Python (z dat přípravný balíčku) teď představuje znaky CR a LF, což popisný v systému Windows.
- _Zvolte metriky_ rozevírací teď skryje vlastnost při přechodu k zobrazení dat.
- Workbench můžete nyní proces parquet soubory i v případě, že používá modul Python runtime. Dřív pouze Spark můžete použít při zpracování parquet soubory. 
- Filtrování hodnot ve sloupci s _datum_ datový typ už způsobí, že data přípravný modul chyby.
- Zobrazení metriky teď respektuje vzorkování strategie aktualizace.
- Vzdálené vzorkování úlohy teď funguje správně.

### <a name="job-execution"></a>Provádění úlohy
- Argument je nyní zahrnutá v záznamu historie spouštění.
- Úlohy spuštěna rozhraní příkazového řádku se zobrazí v panelu spustit historie úlohy automaticky.
- Panel úloh nyní zobrazuje úloh vytvořených produktem uživatele typu Host přidá do klienta AAD.
- Panel úlohu zrušit a akce odstranění jsou stabilnější.
- Když kliknete na tlačítko spustit, je nyní spuštěna chybovou zprávu, pokud konfigurační soubory, které jsou v nesprávném formátu.
- Ukončování aplikace už naruší úloh spuštěna v rozhraní příkazového řádku.
- Úlohy spuštěna CLI nyní nadále rozdělit standardní výstup i po jedné hodině provádění.
- Lepší chybové zprávy se zobrazují, když se nezdaří spustit přípravný balíček dat v Pythonu nebo PySpark.
- `az ml experiment clean`Nyní vyčistí imagí Dockeru do vzdáleného virtuálního počítače.
- `az ml experiment clean`nyní pracuje správně pro místní cíl v systému macOS.
- Chybové zprávy při cílení na místním nebo vzdáleném Docker spuštění vyčištěním nahoru a snadněji číst.
- Lepší chybová zpráva se zobrazí, když při připojené jako cíl provádění není správně naformátovaný název hlavního uzlu clusteru HDInsight.
- Lepší chybová zpráva se zobrazí, pokud tajný klíč nebyl nalezen ve službě přihlašovacích údajů. 
- Knihovna MMLSpark upgradován na podporu Apache Spark 2.2.
- MMLSpark nyní zahrnují subjektu kódování transformace (OK kódování) pro lékařské dokumenty.
- `matplotlib`verze 2.1.0 je nyní uvidíte na více systémů v pole s Workbench.

### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
- Vyhledávání názvu poznámkového bloku nyní pracuje správně v zobrazení poznámkových bloků.
- Nyní můžete odstranit Poznámkový blok v zobrazení poznámkových bloků.
- Nové magic `%upload_artifact` je přidána pro nahrávání souborů vytvořil v prostředí pro spuštění poznámkového bloku do úložiště dat historie spouštění.
- Chyby jádra jsou nyní prezentované v stav úlohy poznámkového bloku pro snazší ladění.
- Jupyter nyní správně vypnutí serveru při přihlášení uživatele mimo aplikaci.

### <a name="azure-portal"></a>portál Azure
- Účet experimentování a účet Model správy mohou být vytvořeny teď v dvě nové oblasti Azure: západní Evropa a jihovýchodní Asie.
- Model správy účtu DevTest plán nyní je k dispozici pouze pokud je první z nich má být vytvořen v rámci předplatného. 
- Odkaz Nápověda v portálu Azure je aktualizována tak, aby odkazoval na stránce dokumentace správné.
- Pole popisu se odebere ze stránce s podrobnostmi o Docker bitové kopie, protože není použitelný.
- Podrobnosti, včetně nastavení AppInsights a automatického škálování jsou přidány na stránku podrobností webové služby.
- Model správy stránky teď vykreslí i v případě, že soubory cookie třetích stran nejsou v prohlížeči. 

### <a name="operationalization"></a>Operationalization
- Webové služby s "skóre" v jeho název už se nezdaří.
- Uživatel nyní můžete vytvořit prostředí nasazení právě Přispěvatel přístup k skupinu prostředků Azure nebo předplatné. Vlastník přístup k celé předplatné již není potřeba.
- Operationalization CLI nyní požívá karta Automatické dokončování v systému Linux.
- Služba vytváření bitové kopie teď podporuje vytváření bitové kopie pro služby nebo zařízení Azure IoT.

### <a name="sample-projects"></a>Ukázkové projekty
- [_Klasifikace Iris_ ](./tutorial-classifying-iris-part-1.md) ukázkového projektu:
    - `iris_pyspark.py`je přejmenován na `iris_spark.py`.
    - `iris_score.py`je přejmenován na `score_iris.py`.
    - `iris.dprep`a `iris.dsource` jsou aktualizovány tak, aby odrážela nejnovější aktualizace dat přípravný modul.
    - `iris.ipynb`Poznámkového bloku se mění pro práci v clusteru HDInsight.
    - Historie spouštění zapnutý `iris.ipynb` Poznámkový blok buněk.
- [_Rozšířená Data Prep pomocí dat sdílené složky kolo_ ](./tutorial-bikeshare-dataprep.md) ukázkový projekt "Zpracování chyby hodnota" krok pevné.
- [_MMLSpark na datech pro dospělé úplné zjišťování_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) ukázkový projekt `docker.runconfig` aktualizováno z JSON na YAML formátu.
- [_Distribuované ladění Hyperparameter_ ](./scenario-distributed-tuning-of-hyperparameters.md) ukázkový projekt`docker.runconfig` aktualizováno z JSON na YAML formátu.
- Nový Projekt Ukázka [ _klasifikace bitovou kopii pomocí CNTK_](./scenario-image-classification-using-cntk.md).
