---
title: Co je nového v Azure Machine Learning
description: Tento dokument podrobně popisuje aktualizace Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: 9d16606665bf043e094bebdfbbce973910135f1a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Novinky ve službě Azure Machine Learning

V tomto článku, další informace o nové verze pro [služby Azure Machine Learning](overview-what-is-azure-ml.md). 

## <a name="2018-03-sprint-4"></a>2018-03 (sprintu 4)
**Číslo verze**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([najít vaší verzí](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Vítá vás páté aktualizaci vytvoří nástroje Azure Machine Learning Workbench. Řadu následující aktualizace jsou vytvářeny jako přímý výsledky váš názor. Uchovávejte je Připravujeme!

**Upozorňují na důležité nové funkce a změny**

- Podpora pro spouštění skriptů na vzdálené virtuálních počítačů Ubuntu nativně ve svém vlastním prostředí kromě vzdáleného docker na základě spuštění.
- Nové prostředí prostředí v aplikaci Workbench umožňuje vytvářet výpočetní cíle a spouštět konfigurace kromě našich zkušeností založené na rozhraní příkazového řádku.
![Karta prostředí](media/azure-machine-learning-release-notes/environment-page.png)
- Sestavy o historii přizpůsobitelné spustit ![obrázek nové spuštění sestavy historie](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Podrobné aktualizace**

Následuje seznam podrobné aktualizací v oblasti Azure Machine Learning v této sprintu jednotlivých součástí.

### <a name="workbench-ui"></a>Workbench uživatelského rozhraní
- Upravitelné spustit historie sestavy
  - Konfiguraci vylepšené grafu pro spuštění historie sestavy
    - Použít entrypoints lze změnit.
    - Nejvyšší úrovně filtry můžete přidat a upravit ![přidat filtry](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Grafy a statistiky můžete přidat nebo upravit (a přetažení myší přeskupení).
    ![Vytváření nových grafů](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD pro spuštění historie sestavy
  - Přesunout všechny existující zobrazení seznamu historie spouštění konfigurací na straně serveru sestav, který se chová jako kanály na spuštění z bodů vybranou položku.

- Karta prostředí
  - Můžete snadno přidávat nová cílová výpočetní a spustit konfigurační soubory do projektu ![nová cílová výpočetní](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Spravovat a aktualizovat konfiguračních souborů pomocí UX jednoduchý, založené na formulářích
  - Tlačítko Nový při přípravě vašeho prostředí pro spuštění

- Vylepšení výkonu do seznamu souborů na bočním panelu

### <a name="data-preparation"></a>Příprava dat 
- Azure Machine Learning Workbench teď umožňuje prohledávat pomocí známých sloupec názvu pro sloupec.


### <a name="experimentation"></a>Experimentování
- Azure Machine Learning Workbench teď podporuje nativní systémem skripty vlastního prostředí python nebo pyspark. Pro tuto funkci uživatel vytvoří a spravuje vlastní prostředí na vzdálený počítač a pomocí Azure Machine Learning Workbench provést jejich skripty na tomto cíli. Najdete v tématu [konfigurace služby Azure Machine Learning experimentování](experimentation-service-configuration.md) 

### <a name="model-management"></a>Správa modelů
- Podpora pro přizpůsobení nasazení kontejnerů: umožňuje přizpůsobení bitové kopie kontejneru tím, že se instalace externí knihovny pomocí výstižný get atd. Je již omezena na pip instalovatelných knihovny. Najdete v článku [dokumentace](model-management-custom-container.md) Další informace.
  - Použití `--docker-file myDockerStepsFilename` příznak a název souboru s manifestu, image nebo příkazy vytváření služby.
  - Upozorňujeme, že bitová kopie je Ubuntu a nemůže být upraven.
  - Příklad: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>2018-01 (sprintu 3) 
**Číslo verze**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([najít vaší verzí](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Dále jsou aktualizace a vylepšení v této sprintu. Řadu tyto aktualizace jsou vytvářeny jako přímý výsledek zpětnou vazbu od uživatelů. 


Následuje seznam podrobné aktualizací v oblasti Azure Machine Learning v této sprintu jednotlivých součástí.

- Aktualizace zásobníku ověřování vynutí přihlášení a účet výběru při spuštění

### <a name="workbench"></a>Workbench
- Možnost instalace nebo odinstalace aplikace z panelu Přidat nebo odebrat programy
- Aktualizace zásobníku ověřování vynutí přihlášení a účet výběr na spuštění
- Vylepšené prostředí pro jednotné přihlašování na (SSO) v systému Windows
- Uživatelé, kteří patří do více klientů s odlišnými pověřeními teď bude moci přihlásit do Workbench

### <a name="ui"></a>UI
- Obecná vylepšení a opravy chyb

### <a name="notebooks"></a>Poznámkové bloky
- Obecná vylepšení a opravy chyb

### <a name="data-preparation"></a>Příprava dat 
- Vylepšené automatické návrhy při provádění podle příkladu transformace
- Vylepšený algoritmus pro vzor frekvence inspector
- Schopnost posílání ukázkových dat a zpětnou vazbu při provádění transformací podle příkladu ![odvozena obrázek odeslat zpětnou vazbu odkaz na sloupec transformace](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Vylepšení Spark modulu Runtime
- Scala nahradili Pyspark
- Opravené neschopnost zavřete dat nelze použít pro nástroj Inspector řady čas 
- Pevné zablokování dobu provádění Data Prep pro HDI

### <a name="model-management-cli-updates"></a>Aktualizace modelu správy rozhraní příkazového řádku 
  - Vlastnictví předplatného se už nevyžaduje pro zřizování prostředků. Přispěvatel přístup do skupiny prostředků jsou dostatečné pro nastavení prostředí pro nasazení.
  - Povolené místní prostředí instalace zdarma odběrů 

## <a name="2017-12-sprint-2-qfe"></a>2017 – 12 (úbytek sprintu 2 QFE) 
**Číslo verze**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([najít vaší verzí](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Toto je verze QFE (Quick Fix Engineering), vedlejší verzi. Ho řeší problémy několik telemetrie a pomáhá produktový tým, abyste lépe pochopili, jak je produkt používán. Ve znalostní bázi můžete přejít do budoucích úsilí o zlepšování zkušeností produktu. 

Kromě toho existují dvě důležité aktualizace:

- Pevná chyby ve prep data, která zabránila inspector řady čas ze zobrazení v dat příprava balíčků.
- V nástroji příkazového řádku už nemusí být vlastníkem předplatného Azure zřídit clustery ACS výpočetní Machine Learning. 

## <a name="2017-12-sprint-2"></a>2017 – 12 (sprintu 2)
**Číslo verze**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([najít vaší verzí](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Vítá vás třetí aktualizace Azure Machine Learning. Tato aktualizace obsahuje vylepšení v aplikaci workbench, rozhraní příkazového řádku (CLI) a back endové služby. Děkujeme pro odesílání úsměvy a frowns. Řadu následující aktualizace jsou vytvářeny jako přímý výsledky váš názor. 

**Upozorňují na důležité nové funkce**
- [Podpora pro SQL Server a databázi SQL Azure jako zdroj dat](data-prep-appendix2-supported-data-sources.md#types) 
- [Hloubkové Learning na Spark s podporou GPU pomocí MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Všechny kontejnery AML jsou kompatibilní s Azure IoT hraniční zařízení po nasazení (žádné další kroky požadované)](http://aka.ms/aml-iot-edge-blog)
- Seznam registrovaných modelu a podrobnosti zobrazení k dispozici portál Azure
- Přístup k výpočetní cílů pomocí ověřování na základě klíčů SSH kromě přístupu pomocí uživatelského jména nebo hesla. 
- Nový nástroj Inspector vzor frekvence v datech rychlá příprava prostředí. 

**Podrobné aktualizace** tady je seznam podrobné aktualizací v oblasti Azure Machine Learning v této sprintu jednotlivých součástí.

### <a name="installer"></a>Instalační program
- Instalační program může vlastní aktualizace, která chyb opravy a nové funkce, může být podporovaný bez nutnosti znovu ji nainstalujte uživatele

### <a name="workbench-authentication"></a>Workbench ověřování
- Více opravy pro ověřování systému. Dejte nám vědět, pokud se stále setkáváte přihlášení problémy.
- Změny uživatelského rozhraní, které bylo snazší najít nastavení správce serveru Proxy.

### <a name="workbench"></a>Workbench
- Zobrazení jen pro čtení souboru má nyní světla modré pozadí
- Vpravo zjistitelnost více přesunutý tlačítko Upravit.
- formáty souborů "ipynb", "dprep" a "dsource" lze vykreslit nyní ve formátu raw textu
- Nástroje workbench má nyní nové prostředí pro úpravy, které mohou uživatelé směrem pomocí externí integrovaného vývojového prostředí upravit skripty a používat Workbench jenom pro typy souborů, které mají bohaté prostředí úpravy (například poznámkových bloků, zdroje dat, Data přípravy balíčky) upravit
- Načítání seznamu pracovních prostorů a projektů, které má uživatel přístup k je výrazně rychlejší nyní

### <a name="data-preparation"></a>Příprava dat 
- Vzor Inspector frekvence zobrazíte vzory řetězec ve sloupci. Můžete také filtrovat svá data pomocí vzorce. To ukazuje zobrazení podobně jako hodnota počty inspector. Rozdílem je, že vzor frekvence zobrazuje počet jedinečný vzory dat, než počet jedinečných data. Můžete také filtrovat příchozí nebo odchozí všechny řádky splňující určité vzor.

![Obrázek vzor frekvence inspector na číslo produktu](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Vylepšení výkonu při doporučujeme edge případech zkontrolujte v transformaci 'odvozena sloupec podle příkladu.

- [Podpora pro SQL Server a databázi SQL Azure jako zdroj dat](data-prep-appendix2-supported-data-sources.md#types) 

![Obrázek vytvoříte nový zdroj dat serveru SQL](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Povolit zobrazení "na první pohled" počty řádků a sloupců

![Obrázek sloupce počet řádků v odkladu](media/azure-machine-learning-release-notes/row-col-count.png)

- Příprava dat je povolena v kontextech všechny výpočetní
- Zdroje dat, které používají databázi systému SQL Server jsou povolené všechny výpočetní kontexty
- Data připravená data mřížky sloupce je možné filtrovat podle typu dat
- Opravené problém s převodem více sloupců k datu
- Pevné problém tento uživatel může vybrat výstupního sloupce jako zdroj v odvozena sloupec podle příkladu Pokud uživatel změnil název výstupního sloupce v rozšířeném režimu.

### <a name="job-execution"></a>Provádění úlohy
Teď můžete vytvářet a přístup k remotedocker nebo clusteru cíli typu výpočetní s SSH klíč ověřování pomocí následujících kroků:
- Připojte cíl výpočetní pomocí následujícího příkazu v rozhraní příkazového řádku

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>-k (nebo--použití azureml-ssh-key) možnost v příkazu určuje pro vygenerování a použití klíče SSH.

- Azure ML Workbench bude generovat veřejný klíč a výstup, v konzole. Přihlaste se k cíli výpočetní pomocí stejného uživatelského jména a soubor ~/.ssh/authorized_keys připojit k tomuto veřejnému klíči.

- Můžete připravit tento cíl výpočetní a použít jej pro spuštění a Azure ML Workbench budou používat tento klíč pro ověřování.  

Další informace o vytváření výpočetní cíle, najdete v části [konfigurace služby Azure Machine Learning experimentování](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools pro AI
- Přidání podpory pro [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)
- Přidat `az ml datasource create` příkaz umožňuje vytváření zdroje dat z příkazového řádku

### <a name="model-management-and-operationalization"></a>Model správy a Operationalization
- [Všechny kontejnery AML jsou kompatibilní s Azure IoT hraniční zařízení při operationalized (žádné další kroky požadované)](http://aka.ms/aml-iot-edge-blog) 
- Vylepšení v rozhraní příkazového řádku o16n chybové zprávy
- Opravy chyb v portálu pro správu modelu UX  
- Konzistentní písmeno velká a malá písmena pro atributy model správy na stránce podrobností
- V reálném čase vyhodnocování časový limit volání nastavena na 60 sekund
- Registrovaný modelu seznamu a podrobností zobrazení k dispozici na portálu Azure

![Podrobné informace o modelu portálu](media/azure-machine-learning-release-notes/model-list.jpg)

![Přehled modelu portálu](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Učení na Spark s přímým [podporu GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Podpora pro správce prostředků šablony pro nasazení snadno prostředků
- Podpora pro ekosystému SparklyR
- [Integrace AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Ukázkové projekty
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) a [MMLSpark](https://github.com/Azure/mmlspark) ukázky aktualizovat pomocí nové verze sady SDK Azure ML

### <a name="breaking-changes"></a>Nejnovější změny
- Povýší `--type` přepínač ve `az ml computetarget attach` na dílčí příkaz a. 

    - `az ml computetarget attach --type remotedocker` je nyní `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` je nyní `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>2017-11 (sprintu 1) 
**Číslo verze**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([najít vaší verzí](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

V této verzi jsme provedli jsme vylepšení v oblasti zabezpečení, stability a udržovatelnosti v aplikaci workbench, rozhraní příkazového řádku a vrstva back endové služby. Děkujeme za nám pošlete úsměvy a frowns. Řadu níže aktualizace, které jsou vytvářeny jako přímý výsledky váš názor. Zachovat jejich Připravujeme!

### <a name="notable-new-features"></a>Upozorňují na důležité nové funkce
- Azure ML je nyní k dispozici ve dvou nových oblastí Azure: **západní Evropa** a **jihovýchodní Asie**. Připojí předchozí oblasti **východní USA 2**, **– Západ střední USA**, a **Austrálie – východ**, přináší celkový počet nasazení oblasti na pět.
- Jsme povoleno syntaxe kódu Python zvýraznění v aplikaci Workbench, aby bylo snazší číst a upravovat Python zdrojového kódu. 
- Teď můžete spustit vaše oblíbené IDE přímo ze souboru, nikoli z celý projekt.  Otevření souboru v Workbench a potom kliknutím na "Upravit" spustí soubor aktuální, projektu vaší IDE (aktuálně VS Code a PyCharm jsou podporované).  Můžete také kliknout na šipku vedle tlačítka Upravit upravte soubor v textovém editoru Workbench.  Soubory jsou jen pro čtení, dokud nekliknete na tlačítko Upravit, zabránit náhodným změnám.
- Oblíbené knihovny výkresu `matplotlib` verze 2.1.0 je nyní součástí aplikace Workbench.
- Jsme upgradovat na verzi .NET Core 2.0 pro přípravný modul data. Odebrat tento požadavek pro instalaci brew openssl během instalace aplikace v systému macOS. Je také dláždí zajímavější data přípravný funkcí se v blízké budoucnosti. 
- Domovské stránce specifické pro verzi aplikace jsme povolili tak, aby získat více důležité poznámky k verzi a aktualizovat výzvy na základě vaší aktuální verze aplikace.
- Pokud vaše místní uživatelské jméno má svůj prostor v něm, můžete nyní úspěšně nainstalovat aplikace. 

### <a name="detailed-updates"></a>Podrobné aktualizace
Níže je seznam podrobné aktualizací v oblasti Azure Machine Learning v této sprintu jednotlivých součástí.

#### <a name="installer"></a>Instalační program
- Instalační program aplikace teď vyčistí instalační adresář vytvořený starší verzí aplikace.
- Opravit chyby, který vede získávání zablokované ve 100 % v systému macOS vysoké Sierra Instalační služby.
- Je nyní přímý odkaz na adresáře instalačního programu pro uživatele ke kontrole instalační protokoly v případě, že instalace se nezdaří.
- Nainstalujte teď funguje pro uživatele, kteří mají místo ve své uživatelské jméno.

#### <a name="workbench-authentication"></a>Workbench ověřování
- Podpora pro ověřování v správce serveru Proxy.
- Teď přihlášení úspěšné, pokud uživatel je za bránou firewall. 
- Pokud má uživatel experimentování účty v několika oblastech Azure a jedné oblasti se stane nedostupný, aplikace se už zablokuje.
- Pokud ověřování neproběhne a dialogové okno ověřování je stále zobrazena, aplikace už se pokusí načíst prostoru z místní mezipaměti.

#### <a name="workbench-app"></a>Workbench aplikace
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

#### <a name="data-preparation"></a>Příprava dat 
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

#### <a name="job-execution"></a>Provádění úlohy
- Argument je nyní zahrnutá v záznamu historie spouštění.
- Úlohy spuštěna rozhraní příkazového řádku se zobrazí v panelu spustit historie úlohy automaticky.
- Panel úloh nyní zobrazuje úloh vytvořených produktem uživatele typu Host přidá do klienta Azure AD.
- Panel úlohu zrušit a akce odstranění jsou stabilnější.
- Když kliknete na tlačítko spustit, je nyní spuštěna chybovou zprávu, pokud konfigurační soubory, které jsou v nesprávném formátu.
- Ukončování aplikace už naruší úloh spuštěna v rozhraní příkazového řádku.
- Úlohy spuštěna CLI nyní nadále rozdělit standardní výstup i po jedné hodině provádění.
- Lepší chybové zprávy se zobrazují, když se nezdaří spustit přípravný balíček dat v Pythonu nebo PySpark.
- `az ml experiment clean` Nyní vyčistí imagí Dockeru do vzdáleného virtuálního počítače.
- `az ml experiment clean` nyní pracuje správně pro místní cíl v systému macOS.
- Chybové zprávy při cílení na místním nebo vzdáleném Docker spuštění vyčištěním nahoru a snadněji číst.
- Lepší chybová zpráva se zobrazí, když při připojené jako cíl provádění není správně naformátovaný název hlavního uzlu clusteru HDInsight.
- Lepší chybová zpráva se zobrazí, pokud tajný klíč nebyl nalezen ve službě přihlašovacích údajů. 
- Knihovna MMLSpark upgradován na podporu Apache Spark 2.2.
- MMLSpark nyní zahrnují subjektu kódování transformace (OK kódování) pro lékařské dokumenty.
- `matplotlib` verze 2.1.0 je nyní uvidíte na více systémů v pole s Workbench.

#### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
- Vyhledávání názvu poznámkového bloku nyní pracuje správně v zobrazení poznámkových bloků.
- Nyní můžete odstranit Poznámkový blok v zobrazení poznámkových bloků.
- Nové magic `%upload_artifact` je přidána pro nahrávání souborů vytvořil v prostředí pro spuštění poznámkového bloku do úložiště dat historie spouštění.
- Chyby jádra jsou nyní prezentované v stav úlohy poznámkového bloku pro snazší ladění.
- Jupyter nyní správně vypnutí serveru při přihlášení uživatele mimo aplikaci.

#### <a name="azure-portal"></a>Azure Portal
- Účet experimentování a účet Model správy mohou být vytvořeny teď v dvě nové oblasti Azure: západní Evropa a jihovýchodní Asie.
- Model správy účtu DevTest plán nyní je k dispozici pouze pokud je první z nich má být vytvořen v rámci předplatného. 
- Odkaz Nápověda v portálu Azure je aktualizována tak, aby odkazoval na stránce dokumentace správné.
- Pole popisu se odebere ze stránce s podrobnostmi o Docker bitové kopie, protože není použitelný.
- Podrobnosti, včetně nastavení AppInsights a automatického škálování jsou přidány na stránku podrobností webové služby.
- Model správy stránky teď vykreslí i v případě, že soubory cookie třetích stran nejsou v prohlížeči. 

#### <a name="operationalization"></a>Operationalization
- Webové služby s "skóre" v jeho název už se nezdaří.
- Uživatel nyní můžete vytvořit prostředí nasazení právě Přispěvatel přístup k skupinu prostředků Azure nebo předplatné. Vlastník přístup k celé předplatné již není potřeba.
- Operationalization CLI nyní požívá karta Automatické dokončování v systému Linux.
- Služba vytváření bitové kopie teď podporuje vytváření bitové kopie pro služby nebo zařízení Azure IoT.

#### <a name="sample-projects"></a>Ukázkové projekty
- [_Klasifikace Iris_ ](./tutorial-classifying-iris-part-1.md) ukázkového projektu:
    - `iris_pyspark.py` je přejmenován na `iris_spark.py`.
    - `iris_score.py` je přejmenován na `score_iris.py`.
    - `iris.dprep` a `iris.dsource` jsou aktualizovány tak, aby odrážela nejnovější aktualizace dat přípravný modul.
    - `iris.ipynb` Poznámkového bloku se mění pro práci v clusteru HDInsight.
    - Historie spouštění zapnutý `iris.ipynb` Poznámkový blok buněk.
- [_Rozšířená Data Prep pomocí dat sdílené složky kolo_ ](./tutorial-bikeshare-dataprep.md) ukázkový projekt "Zpracování chyby hodnota" krok pevné.
- [_MMLSpark na datech pro dospělé úplné zjišťování_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) ukázkový projekt `docker.runconfig` aktualizováno z JSON na YAML formátu.
- [_Distribuované ladění Hyperparameter_ ](./scenario-distributed-tuning-of-hyperparameters.md) ukázkový projekt`docker.runconfig` aktualizováno z JSON na YAML formátu.
- Nový Projekt Ukázka [ _klasifikace bitovou kopii pomocí CNTK_](./scenario-image-classification-using-cntk.md).


## <a name="2017-10-sprint-0"></a>2017 10 (sprintu 0) 
**Číslo verze**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([najít vaší verzí](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Vítá vás Azure Machine Learning Workbench následující naše počáteční verze public preview konference Microsoft Ignite 2017 první aktualizace. Hlavní aktualizace v této verzi jsou spolehlivost a ustálení opravy.  Mezi důležité problémy, které jsme řešili patří:

### <a name="new-features"></a>Nové funkce
- systému macOS vysoké Sierra se teď podporuje.

### <a name="bug-fixes"></a>Opravy chyb
#### <a name="workbench-experience"></a>Workbench prostředí
- Přetažení souboru do Workbench způsobí, že Workbench došlo k chybě.
- Okno terminálu v produktu VS Code nakonfigurované jako rozhraní IDE pro Workbench nerozpoznává _az ml_ příkazy.

#### <a name="workbench-authentication"></a>Workbench ověřování
Jsme provedli několik aktualizací pro zlepšení různé problémy přihlášení a ověření nahlásí.
- Okno ověřování zachová odebrání up, zejména při připojení k Internetu není stabilní.
- Problémy s lepší spolehlivostí kolem ověřování vypršení platnosti tokenu.
- V některých případech ověřování zobrazí se okno dvakrát.
- Hlavní okno Workbench stále zobrazí zpráva "ověřování" po dokončení procesu ověřování a pole automaticky otevíraný dialog už se zavře.
- Pokud není k dispozici žádné připojení k Internetu, ověřovací dialog se zobrazí prázdnou obrazovku.

#### <a name="data-preparation"></a>Příprava dat 
- Když je filtrován určitou hodnotu, chyb a chybějící hodnoty jsou také vyfiltrovány.
- Změna strategie vzorkování odebere následné existující operace spojení.
- Nahrazující hodnota chybějící transformace není zohledňují NaN.
- Odvození typu datum vyvolá výjimku, když je zjištěna hodnota null.

#### <a name="job-execution"></a>Provádění úlohy
- Při provádění úlohy se nepodaří odeslat složky projektu, protože překročila limit velikosti neexistuje žádná zrušte chybová zpráva.
- Pokud se skript v jazyce Python uživatele změní pracovní adresář, nejsou sledovány soubory zapisovat do složky výstupy. 
- Pokud aktivní předplatné Azure je jiný než ten, který patří aktuální projekt, výsledků úlohy chyby 403.
- Když Docker není k dispozici, žádná zrušte chybová zpráva se vrátí, pokud se uživatel pokusí použít Docker jako cíl provádění.
- .runconfig soubor se neuloží automaticky, když uživatel klikne na _spustit_ tlačítko.

#### <a name="jupyter-notebook"></a>Poznámkový blok Jupyter
- Poznámkový blok serveru nelze spustit, pokud uživatel používá s určitými typy přihlášení.
- Poznámkový blok serveru chybové zprávy není surface protokolů, které jsou viditelné pro uživatele.

#### <a name="azure-portal"></a>Azure Portal
- Když vyberete tmavým motivem portálu Azure, budou okna Správa modelu zobrazuje jako černé políčko.

#### <a name="operationalization"></a>Operationalization
- Opětovné použití manifestu aktualizovat webovou službu způsobí, že novou bitovou kopii Docker vytvořené s náhodným názvem.
- Protokoly webové služby nelze načíst z Kubernetes clusteru.
- Zavádějící chybová zpráva je vytištěna, když uživatel se pokusí vytvořit Model správy účet nebo účet ML výpočetní a zaznamená problémy s oprávněními.

## <a name="next-steps"></a>Další postup

Přečtěte si přehled pro [Azure Machine Learning](overview-what-is-azure-ml.md).