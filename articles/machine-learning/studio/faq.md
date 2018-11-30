---
title: Machine Learning Studio – nejčastější dotazy (FAQ) – Azure | Dokumentace Microsoftu
description: 'Azure Machine Learning Studio: Časté otázky k fakturaci, schopnostem a omezením cloudové služby pro efektivní prediktivní modelování.'
keywords: úvod ke strojovému učení, prediktivní modelování, co je strojové učení
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/02/2017
ms.openlocfilehash: c9c80da55737d0093813b84e33d3d90c5d7d4603
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309136"
---
# <a name="azure-machine-learning-studio-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Nejčastější dotazy k Azure Machine Learning Studio: fakturace, schopnosti, omezení a podpora
Zde jsou některé nejčastější dotazy (a příslušné odpovědi) týkající se cloudové služby Azure Machine Learning, která slouží k vývoji prediktivních modelů a zprovozňování řešení prostřednictvím webových služeb. Najdete tu dotazy ke způsobu používání této služby, včetně modelu fakturace, možností, omezení a podpory.

**Máte dotaz, který tady nemůžete najít?**

Azure Machine Learning má fórum na webu MSDN, kde se členové komunity odborníků přes data můžou ptát na věci související se službou Azure Machine Learning. Toto fórum monitoruje tým služby Azure Machine Learning. Přejděte na [fórum služby Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) a vyhledejte odpovědi nebo zadejte novou vlastní otázku.

## <a name="general-questions"></a>Obecné otázky
**Co je Machine Learning Studio?**

Machine Learning Studio je pracovní prostředí, ke kterému přistupujete pomocí webového prohlížeče. Machine Learning Studio nabízí mnoho modulů s vizuálním kompozičním rozhraním, které umožňuje vytvořit komplexní pracovní postup založený na datové vědě ve formě experimentu.

Další informace o nástroji Machine Learning Studio najdete v tématu [Co je Machine Learning Studio?](what-is-ml-studio.md).

**Co je služba Machine Learning API?**

Služba Machine Learning API umožňuje nasazovat prediktivní modely, jako jsou třeba modely integrované v Machine Learning Studiu, jako škálovatelné webové služby, které jsou odolné vůči chybám. Webové služby vytvořené pomocí služby Machine Learning API představují rozhraní REST API, která zajišťují komunikaci mezi externími aplikacemi a vašimi modely prediktivní analýzy.

Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).

**Kde jsou uvedené klasické webové služby? Kde jsou uvedené nové webové služby (využívající Azure Resource Manager)?**

Webové služby vytvořené pomocí modelu nasazení Classic a Webové služby vytvořené pomocí nového modelu nasazení Azure Resource Manager jsou uvedené na portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).

Klasické webové služby jsou uvedené také na kartě **Webové služby** v [Machine Learning Studiu](http://studio.azureml.net).

## <a name="azure-machine-learning-questions"></a>Dotazy ke službě Azure Machine Learning
**Co jsou webové služby Azure Machine Learning?**

Webové služby Machine Learning poskytují rozhraní mezi aplikací a modelem Machine Learning pro vyhodnocování pracovních postupů. Pomocí služby Azure Machine Learning může externí aplikace komunikovat v reálném čase s modelem Machine Learning pro vyhodnocování pracovních postupů. Volání webové služby Machine Learning vrací do externí aplikace predikované výsledky. Webovou službu můžete volat předáním klíče rozhraní API, který se vytvořil při nasazení této webové služby. Webová služba Machine Learning je založená na architektuře REST, která je u programátorských projektů na webu oblíbenou volbou.

Azure Machine Learning zahrnuje dva typy webových služeb:

* Služba Request-Response (RRS): Služba s nízkou latencí, která je vysoce škálovatelná a poskytuje rozhraní pro bezstavové modely, které byly vytvořené a nasazené pomocí Machine Learning Studia.
* Služba Batch Execution (BES): Asynchronní služba pro vyhodnocování dávek datových záznamů.

Existuje několik způsobů, jak využít REST API a komunikovat s webovou službou. Můžete například napsat aplikaci v jazyce C#, R nebo Python s pomocí ukázkového kódu, který se vygeneroval při nasazení webové služby.

Ukázkový kód je dostupný na těchto místech:
- Stránka o využívání webových služeb na portálu Azure Machine Learning Web Services
- Stránka nápovědy k rozhraní API na řídicím panelu webové služby v Machine Learning Studiu

Můžete také použít ukázkový sešit v Microsoft Excelu, který se pro vás vygeneroval a který je dostupný na řídicím panelu webové služby v Machine Learning Studiu.

**Jaké jsou hlavní aktualizace služby Azure Machine Learning?**

Nejnovější aktualizace najdete v tématu [Novinky ve službě Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="machine-learning-studio-questions"></a>Otázky k nástroji Machine Learning Studio
### <a name="import-and-export-data-for-machine-learning"></a>Import a export dat pro Machine Learning
**Jaké zdroje dat Machine Learning podporuje?**

Data můžete do experimentu Machine Learning Studio stáhnout třemi způsoby:

- Nahrání místního souboru jako datové sady
- Použití modulu k importu dat z cloudových datových služeb
- Import datové sady uložené z jiného experimentu

Další informace o podporovaných formátech souborů najdete v tématu o [importu trénovacích dat do nástroje Machine Learning Studio](import-data.md).

#### <a id="ModuleLimit"></a>Jak velká může být datová sada pro moje moduly?
Moduly v nástroji Machine Learning Studio podporují datové sady, které obsahují až 10 GB hustých číselných dat pro běžné případy použití. Pokud modul přijímá víc než jeden vstup, celková velikost všech vstupních velikostí je 10 GB. Větší datové sady je před ingestováním možné vzorkovat pomocí dotazů Hive nebo Azure SQL Database. Je možné také použít předzpracování metodou Learning by Counts.  

Během normalizace funkcí je možné následující typy dat rozšířit do větších datových sad. Tyto typy jsou omezené na méně než 10 GB:

* Řídké
* Kategorické
* Řetězce
* Binární data

Následující moduly jsou omezené na datové sady menší než 10 GB:

* Doporučené moduly
* Modul SMOTE (Synthetic Minority Oversampling Technique)
* Skriptovací moduly: R, Python, SQL
* Moduly, kde velikost výstupních dat může být větší než velikost vstupních dat, třeba Join nebo Feature Hashing
* Pro velmi velký počet iterací Cross-validation, Tune Model Hyperparameters, Ordinal Regression a One-vs-All Multiclass

#### <a id="UploadLimit"></a>Jaké jsou limity pro nahrávání dat?
Pro datové sady větší než několik GB byste měli nahrát data do Azure Storage nebo Azure SQL Database, případně použít HDInsight, a nenahrávat přímo z místního souboru.

**Je možné číst data z Amazonu S3?**

Pokud máte malé množství dat a chcete je zveřejnit přes adresu URL protokolu HTTP, můžete použít modul [Import dat][import-data]. Jakákoli větší množství dat nejdřív přeneste do Azure Storage a potom použijte modul [Import dat][import-data], abyste je připojili k experimentu.
<!--

<SEE CLOUD DS PROCESS>
-->

**Je k dispozici integrovaná funkce pro obrazový vstup?**

Informace o funkci obrazového vstupu najdete v referenci [Import obrázků][image-reader].

### <a name="modules"></a>Moduly
**V Azure Machine Learning Studiu není algoritmus, zdroj dat, formát dat nebo operace transformace dat, kterou hledám. Jaké mám možnosti?**

Můžete navštívit [fórum pro zpětnou vazbu uživatelů](https://go.microsoft.com/fwlink/?LinkId=404231), kde najdete žádosti o funkce, které sledujeme. Pokud už existuje žádost o funkci, kterou sami hledáte, přidejte k ní svůj hlas. Pokud funkce, kterou hledáte, neexistuje, vytvořte novou žádost. Na tomto fóru můžete také sledovat stav své žádosti. Tento seznam pečlivě sledujeme a často aktualizujeme stav dostupnosti funkce. Navíc díky integrované podpoře R a Pythonu můžete podle potřeby vytvářet vlastní transformace.

**Můžu převést svůj existující kód do nástroje Machine Learning Studio?**

Ano, svůj existující kód R nebo Python můžete do nástroje Machine Learning Studio převést, spustit jej v tom stejném experimentu pomocí inteligentních algoritmů Azure Machine Learning a přes Azure Machine Learning nasadit řešení jako webovou službu. Další informace najdete v tématech o [rozšíření experimentů pomocí R](extend-your-experiment-with-r.md) a o [spouštění pythonových skriptů strojového učení v nástroji Azure Machine Learning Studio](execute-python-scripts.md).

**Je možné definovat model pomocí něčeho takového, jako je jazyk [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)?**

Ne, jazyk PMML (Predictive Model Markup Language) se nepodporuje. K definování modulu můžete využít vlastní kód v R a Pythonu.

**Kolik modulů je možné v experimentu paralelně spustit?**  

V jednom experimentu můžete paralelně spustit až čtyři moduly.

### <a name="data-processing"></a>Zpracování dat
**Je k možné v rámci experimentu využívat interaktivní vizualizaci dat (kromě vizualizací R)?**

Kliknutím na výstup modulu můžete vizualizovat data a získat statistiky.

**Při prohlížení náhledu výsledků nebo dat v prohlížeči je počet řádků a sloupců omezený. Proč?**

Protože do prohlížeče je možné odesílat velké objemy dat, je velikost dat omezená, aby se Machine Learning Studio nezpomalovalo. Pokud chcete vizualizovat všechna data nebo výsledky, je vhodnější data stáhnout a použít Excel nebo jiný nástroj.

### <a name="algorithms"></a>Algoritmy
**Jaké stávající algoritmy podporuje Machine Learning Studio?**

Machine Learning Studio poskytuje nejmodernější algoritmy, například škálovatelné vylepšené rozhodovací stromy, systémy bayesovského rozhodování, hluboké neuronové sítě a rozhodovací džungle vyvinuté v Microsoft Research. Dostupné jsou i škálovatelné open-sourcové balíčky pro strojové učení, třeba Vowpal Wabbit. Machine Learning Studio podporuje algoritmy strojového učení pro binární klasifikaci a klasifikaci s více třídami, regresi a clustering. Podívejte se na úplný seznam [modulů služby Machine Learning][machine-learning-modules].

**Navrhnete mi automaticky algoritmus Machine Learning vhodný pro moje data?**

Ne, ale Machine Learning Studio nabízí různé způsoby, jak porovnat výsledky jednotlivých algoritmů a určit, který z nich je pro váš problém nejvhodnější.

**Lze obecně poradit, jak z poskytovaných algoritmů upřednostnit jeden před jiným?**

Přečtěte si téma [Jak vybrat algoritmus](algorithm-choice.md).

**Jsou poskytované algoritmy napsané v R nebo Pythonu?**

Ne, tyto algoritmy jsou většinou napsané v kompilovaných jazycích, aby poskytovaly vyšší výkon.

**Jsou k dispozici nějaké podrobnosti o algoritmech?**

Určité informace o algoritmech jsou uvedené v dokumentaci. Pro optimalizaci algoritmů pro vaše použití jsou popsané parametry, pomocí kterých je možné činnost algoritmů upravit.  

**Podporuje se online učení?**

Ne, v tuto chvíli je podporováno jenom programové přeučení.

**Je možné vizualizovat vrstvy modelu neuronové sítě pomocí integrovaného modulu?**

Ne.

**Je možné vytvářet vlastní moduly v jazyce C# nebo v nějakém jiném jazyce?**

V současnosti k vytvoření nových vlastních modulů můžete využít jenom R.

### <a name="r-module"></a>Modul R
**Jaké balíčky R jsou k dispozici v nástroji Machine Learning Studio?**

K dnešnímu dni Machine Learning Studio podporuje přes 400 balíčků CRAN R. Všechny zahrnuté balíčky jsou uvedené v [aktuálním seznamu](http://az754797.vo.msecnd.net/docs/RPackages.xlsx). Pokud chcete zjistit, jak tento seznam získat sami, přečtěte si také téma o [rozšíření experimentů pomocí R](extend-your-experiment-with-r.md). Pokud požadovaný balíček není v seznamu, uveďte název balíčku na [fóru pro zpětnou vazbu uživatelů](https://go.microsoft.com/fwlink/?LinkId=404231).

**Je možné vytvořit vlastní modul R?**

Ano, další informace najdete v tématu o [vytváření vlastních modulů R ve službě Azure Machine Learning](custom-r-modules.md).

**Je pro R k dispozici prostředí REPL?**

Ne, ve studiu není žádné prostředí REPL (Read-Eval-Print-Loop) pro R.

### <a name="python-module"></a>Modul Python
**Je možné vytvořit vlastní modul Python?**

V této chvíli ne, ale stejného výsledku je možné dosáhnout použitím jednoho nebo více modulů [Execute Python Script][python].

**Je pro Python k dispozici prostředí REPL?**

V nástroji Machine Learning Studio můžete použít Jupyter Notebooks. Další informace najdete v tématu [Úvod do aplikace Jupyter Notebooks v Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Webová služba
### <a name="retrain"></a>Přeučování
**Jak je možné programově přeučit modely Azure Machine Learning?**

Použijte rozhraní API pro přeučení. Další informace najdete v tématu o [programovém přeučení modelů Machine Learning](retrain-models-programmatically.md). V [ukázce přeučování v Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/) je k dispozici i ukázkový kód.

### <a name="create"></a>Vytvořit
**Je možné model nasadit lokálně nebo do aplikace, která nemá připojení k internetu?**

Ne.

**Existuje základní latence, kterou je možné očekávat u všech webových služeb?**

Přečtěte si téma [Limity předplatného Azure](../../azure-subscription-service-limits.md).

### <a name="use"></a>Použití
**Kdy je žádoucí spustit prediktivní model jako službu Batch Execution a kdy jako službu Request Response?**

Služba Request Response (RRS) je vysoce škálovatelná webová služba s nízkou latencí, pomocí které se poskytuje rozhraní pro bezstavové modely vytvořené a nasazené z prostředí experimentů. Služba Batch Execution (BES) je služba pro asynchronní vyhodnocování dávek datových záznamů. Vstup pro BES je podobný datové vstupu, který využívá RRS. Hlavním rozdílem je to, že BES čte blok záznamů z několika různých zdrojů, jako je třeba Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (dotaz Hive) a zdroje HTTP. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).

**Jak je možné aktualizovat model pro nasazenou webovou službu?**

Pokud chcete aktualizovat prediktivní model pro už nasazenou službu, upravte experiment, který jste použili k vytvoření a uložení natrénovaného modelu, a spusťte ho znovu. Jakmile je dostupná nová verze vytrénovaného modelu, Machine Learning Studio se vás zeptá, jestli chcete aktualizovat webovou službu. Podrobnosti o tom, jak aktualizovat nasazenou webovou službu, najdete v tématu o [nasazení webové služby Machine Learning](publish-a-machine-learning-web-service.md).

Kromě toho můžete použít i rozhraní Retraining API.
Další informace najdete v tématu o [programovém přeučení modelů Machine Learning](retrain-models-programmatically.md). V [ukázce přeučování v Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/) je k dispozici i ukázkový kód.

**Jak je možné sledovat webovou službu nasazenou do produkčního prostředí?**

Po nasazení prediktivního modelu ji můžete sledovat z portálu Azure Machine Learning Web Services. Každá nasazená služba má svůj vlastní řídicí panel, kde můžete sledovat informace z monitorování dané služby. Další informace o správě nasazené webové služby najdete v tématech [Správa webové služby s použitím portálu Azure Machine Learning Web Services](manage-new-webservice.md) a [Správa pracovního prostoru Azure Machine Learning](manage-workspace.md).

**Je možné si někde zobrazit výstup RRS nebo BES?**

V případě RRS je místem, kde uvidíte výsledky, zpravidla odpověď webové služby. Můžete je zapsat také do Azure Blob Storage. U BES je výstup standardně zapisován do objektu blob. Pomocí modulu [Export dat][export-data] je možné výstup zapsat i do databáze nebo tabulky.

**Webové služby je možné vytvářet jenom z modelů, které byly vytvořené v Machine Learning Studiu?**

Ne, webové služby je možné vytvářet i přímo pomocí Jupyter Notebooks a RStudia.

**Kde najdu informace o chybových kódech?**

Seznam chybových kódů a jejich popisy najdete v tématu o [chybových kódech modulů Machine Learning](https://msdn.microsoft.com/library/azure/dn905910.aspx).

## <a name="scalability"></a>Škálovatelnost
**Jaká je škálovatelnost webové služby?**

V tuto chvíli je výchozí koncový bod zřizován s 20 souběžnými požadavky RRS na jeden koncový bod. Jak je popsáno v tématu [Škálování webové služby](scaling-webservice.md), kapacitu je možné rozšířit na 200 souběžných požadavků na jeden koncový bod a každou webovou službu jde škálovat na 10 000 koncových bodů. Pro BES každý koncový bod může zpracovat 40 požadavků najednou, požadavky nad těchto 40 požadavků se zařazují do fronty. Požadavky ve frontě se budou spouštět automaticky podle toho, jak se fronta vyprazdňuje.

**Jsou úlohy R rozdělené mezi uzly?**

Ne.  

**Kolik dat je možné použít k trénování?**

Moduly v nástroji Machine Learning Studio podporují datové sady, které obsahují až 10 GB hustých číselných dat pro běžné případy použití. Pokud modul přijímá více než jeden vstup, celková velikost všech vstupů je 10 GB. Větší datové sady je před ingestováním možné vzorkovat pomocí dotazů Hivu, dotazů Azure SQL Database nebo předzpracováním pomocí modulů [učení na základě počtů][counts].  

Během normalizace funkcí je možné následující typy dat rozšířit do větších datových sad. Tyto typy jsou omezené na méně než 10 GB:

* Řídké
* Kategorické
* Řetězce
* Binární data

Následující moduly jsou omezené na datové sady menší než 10 GB:

* Doporučené moduly
* Modul SMOTE (Synthetic Minority Oversampling Technique)
* Skriptovací moduly: R, Python, SQL
* Moduly, kde velikost výstupních dat může být větší než velikost vstupních dat, třeba Join nebo Feature Hashing
* Pro velmi velký počet iterací Cross-Validate, Tune Model Hyperparameters, Ordinal Regression a One-vs-All Multiclass

Pro datové sady větší než několik GB nahrajte data do úložiště Azure nebo Azure SQL Database, případně použijte HDInsight, a nenahrávejte přímo z místního souboru.

**Existují nějaká omezení velikosti vektoru?**

Řádky i sloupce jsou omezeny na omezení Max Int v rozhraní .NET: 2 147 483 647.

**Je možné upravit velikost virtuálního počítače, na kterém běží webová služba?**

Ne.  

## <a name="security-and-availability"></a>Zabezpečení a dostupnost
**Kdo má ve výchozím nastavení přístup ke koncovému bodu HTTP pro webovou službu? Jak omezím přístup ke koncovému bodu?**

Po nasazení webové služby je pro tuto službu vytvořen koncový bod. Výchozí koncový bod je možné volat pomocí klíče rozhraní API. Další koncové body s vlastními klíči je možné přidat na portálu webových služeb nebo programově pomocí rozhraní API pro správu webových služeb. Pro volání do webové služby se vyžadují přístupové klíče. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).

**Co se stane, když se můj účet Azure Storage nenajde?**

Machine Learning Studio při provádění pracovního postupu využívá k ukládání pracovních dat uživatelem zadaný účet Azure Storage. Tento účet úložiště se do nástroje Machine Learning Studio zadává při vytváření pracovního prostoru. Pokud po vytvoření pracovního prostoru dojde k odstranění účtu úložiště a už se nedá najít, pracovní prostor přestane fungovat a všechny experimenty v něm selžou.

Pokud jste účet úložiště omylem odstranili, znovu vytvořte účet úložiště se stejným názvem a ve stejné oblasti jako odstraněný účet úložiště. Potom znovu synchronizujte přístupový klíč.

**Co se stane, když přístupový klíč účtu úložiště není synchronizovaný?**

Machine Learning Studio při provádění pracovního postupu využívá k ukládání pracovních dat uživatelem zadaný účet Azure Storage. Tento účet úložiště se do nástroje Machine Learning Studio zadává při vytváření pracovního prostoru. Přístupové klíče se přiřadí k tomuto pracovnímu prostoru. Pokud se přístupové klíče po vytvoření pracovního prostoru změní, pracovní prostor už nebude mít přístup k účtu úložiště. Přestane proto fungovat a všechny experimenty v něm selžou.

Pokud jste změnili přístupové klíče účtu úložiště, znovu je v pracovním prostoru synchronizujte přes portál Azure.  

## <a name="support-and-training"></a>Podpora a školení
**Kde získám školení pro Azure Machine Learning?**

V [centru dokumentace Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) najdete videokurzy a návody. Tyto podrobné návody představují jednotlivé služby a popisují životní cyklus vycházející z datové vědy a sestávající z importu dat, jejich čištění, vytváření prediktivních modelů a jejich nasazování do provozu pomocí Azure Machine Learningu.

Průběžně do centra pro Machine Learning přidáváme nové materiály. Na [fóru pro zpětnou vazbu uživatelů](https://windowsazure.uservoice.com/forums/257792-machine-learning) můžete zaslat žádost o další výukové materiály v centru pro Machine Learning.

Školení najdete i v [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Jak je možné získat podporu pro Azure Machine Learning?**

Pokud potřebujete technickou podporu pro Azure Machine Learning, přejděte k [podpoře Azure](https://azure.microsoft.com/support/options/) a vyberte **Machine Learning**.

Azure Machine Learning má i fórum komunity na webu MSDN, kde se můžete ptát na věci související se službou Azure Machine Learning. Toto fórum monitoruje tým služby Azure Machine Learning. Navštivte [fórum Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Dotazy k fakturaci
**Jak se Machine Learning fakturuje?**

Azure Machine Learning má dvě komponenty: Machine Learning Studio a webové služby Machine Learning.

Dokud vyhodnocujete využívání Machine Learning Studia, nemusíte za něj na úrovni Free platit. Úroveň Free mimo jiné umožňuje nasadit webovou službu Classic s omezenou kapacitou.

Pokud si ověříte, že služba Azure Machine Learning splňuje vaše požadavky, můžete se zaregistrovat k úrovni Standard. K tomu ale musíte mít předplatné Microsoft Azure.

Na úrovni Standard se vám bude účtovat měsíční poplatek za každý pracovní prostor, který v Machine Learning Studiu vytvoříte. Experimenty spouštěné v nástroji Studio se účtují podle využitých výpočetních prostředků. Co se týče nasazování webových služeb Classic, transakce a výpočetní hodiny se účtují na bázi průběžných plateb (Pay As You Go).

S novými webovými službami (založenými na Resource Manageru) přichází fakturační plány, se kterými lépe odhadnete výsledné náklady. Odstupňované ceny umožňují snížit ceny zákazníkům, kteří potřebují vysokou kapacitu.

Po vytvoření plánu se zavazujete hradit fixní částku, do které je zahrnuto určité množství výpočetních hodin a transakcí API. Pokud byste potřebovali zahrnout vyšší množství, stačí do plánu doplnit instance. Pokud budete vyžadovat podstatně víc výpočetního času a transakcí, můžete si vybrat vyšší úroveň, která kromě toho nabízí i výhodnější sazbu.

Po vyčerpání množství zahrnutého v existujících instancích se další využití účtuje sazbou za nadlimitní využití, která je přiřazená danému fakturačnímu plánu.

> [!NOTE]
Zahrnuté množství se znovu přiděluje každých 30 dní a nevyčerpané prostředky se do dalšího období nepřevádějí.

Další informace o fakturaci a cenách najdete v tématu [Machine Learning – ceny](https://azure.microsoft.com/pricing/details/machine-learning/).

**Nabízí Machine Learning bezplatnou zkušební verzi?**

 Azure Machine Learning nabízí možnost bezplatného předplatného, které je vysvětlené v tématu [Podrobnosti o cenách za Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). Machine Learning Studio nabízí osmihodinovou zkušební verzi pro rychlé vyhodnocení, která je dostupná po přihlášení do [Machine Learning Studia](https://studio.azureml.net/?selectAccess=true&o=2).

 Když se navíc zaregistrujete k bezplatné zkušební verzi Azure, získáte možnost vyzkoušet si všechny služby Azure po dobu jednoho měsíce. Další informace o bezplatné zkušební verzi Azure najdete v [nejčastějších dotazech k této verzi](https://azure.microsoft.com/pricing/free-trial-faq/).

**Co je transakce?**

Transakcí se rozumí volání rozhraní API, na které odpoví služba Azure Machine Learning. Transakce, které proběhnou po volání služeb Request-Response (RRS) a Batch Execution (BES), se sčítají a potom účtují podle vašeho plánu.

**Můžu objemy transakcí zahrnuté v plánu využít pro transakce RRS i BES?**

Ano, transakce RRS a BES se sčítají dohromady a účtují podle vašeho plánu.

**Co je výpočetní čas rozhraní API?**

Výpočetní hodina rozhraní API je fakturační jednotka pro zúčtování času, který spotřebují volání rozhraní API s využitím výpočetních prostředků Machine Learningu. Všechna volání se vám pro účely fakturace započítávají dohromady.

**Jak dlouho obvykle trvá volání rozhraní API produkčního prostředí?**

Časy volání rozhraní API produkčního prostředí se mohou výrazně lišit, obecně od stovek milisekund až po několik sekund. Některá volání rozhraní API mohou vyžadovat i několik minut, v závislosti na složitosti modelu strojového učení a zpracování dat. Dobu volání API produkčního prostředí nejlépe odhadnete tak, že ve službě Machine Learning model otestujete.

**Co je výpočetní hodina Studia?**

Výpočetní hodina Studia je jednotka pro zúčtování celkového času, po který vaše experimenty využívaly výpočetní prostředky ve Studiu.

**K čemu u nových webových služeb (využívajících Azure Resource Manager) slouží úroveň pro vývoj a testování?**

Webové služby využívající Azure Resource Manager nabízí několik úrovní fakturačního plánu. Cenová úroveň pro vývoj a testování zahrnuje omezené množství transakcí a výpočetního času, který využijete k otestování experimentu jako nové webové služby bez dalších nákladů. Službu si můžete vyzkoušet, abyste zjistili, jak funguje.

**Platí se úložiště odděleně?**

Úroveň Free služby Machine Learning nevyžaduje, ale ani neumožňuje využívat oddělené úložiště. Uživatelé služby Machine Learning na úrovni Standard se naopak neobejdou bez účtu Azure Storage. Služba Azure Storage se [fakturuje zvlášť](https://azure.microsoft.com/pricing/details/storage/).

**Podporuje služba Machine Learning vysokou dostupnost?**

Ano. Podrobnosti najdete v tématu [Machine Learning – ceny](https://azure.microsoft.com/pricing/details/machine-learning/) u popisu smlouvy SLA.

**Jaké výpočetní prostředky konkrétně zpracovávají volání rozhraní API produkčního prostředí?**

Služba Machine Learning je víceklientská. Skutečné výpočetní prostředky, které se používají na back-endu, se liší a jsou optimalizované z hlediska výkonu a předvídatelnosti.

### <a name="management-of-new-resource-manager-based-web-services"></a>Správa nových webových služeb (využívajících Resource Manager)
**Co se stane, když odstraním plán?**

Plán vám odebereme z předplatného a služby naúčtujeme podle průběžného využití.

> [!NOTE]
Plán, který právě používá nějaká webová služba, se nedá odstranit. Pokud chcete takový plán odstranit, musíte webové službě přiřadit nový plán nebo nejdřív odstranit ji.

**Co je instance plánu?**

Instance plánu je jednotka množství zahrnutého času a transakcí, které si můžete přidávat do fakturačního plánu. Po výběru úrovně obsahuje plán jednu instanci. Když budete potřebovat zahrnout větší množství, stačí do plánu doplnit dodatečné instance vybrané úrovně.

**Kolik instancí plánu je možné přidat?**

Ve svém předplatném můžete mít jednu instanci cenové úrovně pro vývoj a testování.

Úrovně Standard S1, Standard S2 a Standard S3 můžete přidávat podle potřeby.

> [!NOTE]
V závislosti na tom, jaké využití očekáváte, může být výhodnější upgradovat na úroveň s větším zahrnutým množstvím než přidávat instance do aktuální úrovně.

**Co se stane po změně úrovně plánu (po upgradu nebo downgradu)?**

Původní plán odstraníme a aktuální využití se účtuje průběžně. Na zbytek období je sestaven nový plán obsahující veškeré zahrnuté množství upgradované či downgradované úrovně.

> [!NOTE]
Zahrnuté množství se přiděluje na jednotlivá období a nevyužité prostředky se do dalších období nepřevádějí.

**Co se stane po zvýšení počtu instancí v plánu?**

Množství se zahrnuje na základě průběžné sazby a proces zahrnutí může trvat až 24 hodin.

**Co se stane po odstranění instance plánu?**

Instanci vám odebereme z předplatného a služby účtujeme podle průběžného využití.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Registrace k novým plánům Web Service (které využívající Resource Manager)
**Jak se mám k plánu zaregistrovat?**

Fakturační plán lze vytvořit dvěma způsoby.

Při prvním nasazení webové služby využívající Resource Manager můžete vybrat existující plán nebo vytvořit nový.

Plány vytvořené tímto způsobem spadají do vaší výchozí oblasti, kam se taky nasazuje webová služba.

Pokud chcete službu nasadit v jiné než výchozí oblasti, bude možná vhodné definovat fakturační plány, ještě než službu nasadíte.

V takovém případě se můžete přihlásit k portálu Azure Machine Learning Web Services a přejít na stránku s plány. Odtud můžete plány přidávat, odstraňovat a upravovat.

**S jakým plánem je nejvhodnější začít?**

Doporučujeme začít s úrovní Standard S1 a monitorovat využití prostředků. Pokud zjistíte, že zahrnuté množství transakcí a času rychle vyčerpáte, můžete doplnit instance nebo přejít na vyšší úroveň s výhodnější sazbou. Fakturační plán můžete podle potřeby upravovat kdykoliv během zúčtovacího období.

**Ve kterých oblastech jsou nové plány dostupné?**

Nové fakturační plány jsou dostupné ve třech produkčních oblastech, kde podporujeme nové webové služby:

* Střed USA – jih
* Západní Evropa
* Jihovýchodní Asie

**Webové služby využívám v několika různých oblastech. Potřebuji plán pro každou oblast?**

Ano. Ceny plánů se podle oblasti liší. Pokud webovou službu nasadíte do jiné oblasti, je potřeba k ní přiřadit plán určený právě pro tuto oblast. Další informace najdete v tématu [Dostupné produkty v jednotlivých oblastech]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>Nové webové služby: nadlimitní využití
**Jak můžu ověřit, jestli webové služby nevyužívám nadlimitně?**

Využití všech plánů najdete na stránce Plány na portálu Azure Machine Learning Web Services. Přihlaste se k portálu a v nabídce klikněte na možnost **Plány**.

V tabulce se ve sloupcích pro **Transakce** a **Compute** zobrazí množství zahrnuté v plánu a procento využití.

**Co se stane po vyčerpání množství zahrnutého v cenové úrovni pro vývoj a testování?**

Služby, ke kterým je přiřazená cenová úroveň pro vývoj a testování, se zastaví, dokud nezačne další období nebo dokud je nepřesunete do placené úrovně.

**Jak se počítají ceny úloh RRS (Request Response) a BES (Batch) v případě klasických webových služeb a nadlimitního využití nových webových služeb (využívajících Resource Manager)?**

Co se týče úloh RRS, účtujeme každé volání transakce API a zároveň výpočetní čas, který tyto žádosti spotřebují. Cena za transakce RRS v rozhraní API produkčního prostředí se tak rovná celkovému počtu volání API vynásobenému cenou za 1 000 transakcí (poměrně přepočítané na jednotlivé transakce). Cena za výpočetní čas RRS v rozhraní API produkčního prostředí se rovná době spuštění jednotlivých volání API vynásobené celkovým počtem transakcí API a cenou za výpočetní hodinu v rozhraní API produkčního prostředí.

Podívejme se na příklad z nadlimitního využití plánu Standard S1. 1 000 000 transakcí API, z nichž každá probíhá 0,72 sekundy, by stálo (1 000 000 * 0,50 USD / 1 000 transakcí API) 500 dolarů v nákladech za transakce API produkčního prostředí a (1 000 000 * 0,72 s * 2 USD/hod) 400 dolarů v nákladech za výpočetní hodiny API produkčního prostředí, takže celkem 900 dolarů.

Úlohy BES se účtují stejně. Ale náklady na transakce API představují počet odeslaných dávkových úloh a náklady na výpočetní kapacitu představují s nimi spojený výpočetní čas. Cena za transakce BES v rozhraní API produkčního prostředí se tak rovná celkovému počtu odeslaných úloh vynásobenému cenou za 1 000 transakcí (poměrně přepočítané na jednotlivé transakce). Cena za výpočetní čas BES v rozhraní API produkčního prostředí se rovná době potřebné pro spuštění jednotlivých řádků úlohy vynásobené celkovým počtem řádků v úloze, celkovým počtem úloh a cenou za výpočetní čas API produkčního prostředí. Počítadlo transakcí v kalkulačce Machine Learning představuje počet úloh, které se chystáte odeslat, a pole s časem na transakci představuje celkovou dobu potřebnou ke spuštění všech řádků v jednotlivých úlohách.

Podívejme se na příklad nadlimitního využití úrovně Standard S1. Předpokládejme, že odešlete 100 úloh za den, z nichž se každá skládá z 500 řádků trvajících 0,72 sekundy. Měsíční náklady za nadlimitní využití potom dosáhnou výše (100 úloh za den = 3 100 úloh za měsíc * 0,50 USD / 1 000 transakcí API) 1,55 USD za transakce API produkčního prostředí a (500 řádků * 0,72 s * 3 100 úloh * 2 USD / hod) 620 USD za výpočetní čas API produkčního prostředí, takže celkem 621,55 USD.

### <a name="azure-machine-learning-classic-web-services"></a>Klasické webové služby Azure Machine Learning
**Je dál možné využívat průběžné platby (Pay As You Go)?**

Ano, klasické webové služby jsou ve službě Azure Machine Learning stále dostupné.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Azure Machine Learning – úrovně Free a Standard
**Co je ve službě Azure Machine Learning součástí úrovně Free?**

Úroveň Free služby Azure Machine Learning slouží vás má detailně uvést do nástroje Azure Machine Learning Studio. K registraci stačí jen účet Microsoft. Úroveň Free zahrnuje bezplatný přístup do jednoho pracovního prostoru Azure Machine Learning Studio ke každému [účtu Microsoft](https://account.microsoft.com/account). Na této úrovni můžete využít až 10 GB úložiště a z modelů vytvářet rozhraní API přípravného prostředí. Úlohy úrovně Free nejsou předmětem smlouvy SLA a jsou určeny jenom pro vývoj a osobní užití. 

Pracovní prostory úrovně Free mají následující omezení:

* Úlohy nemůžou přistupovat k datům tak, že se připojí k místnímu serveru se spuštěným SQL Serverem.
* Nejde nasadit nové webové služby využívajících Resource Manager.


**Co je ve službě Azure Machine Learning součástí úrovně a plánů Standard?**

Úroveň Standard služby Azure Machine Learning je placená produkční verze nástroje Azure Machine Learning Studio. Měsíční poplatek za Azure Machine Learning Studio účtujeme po pracovních prostorech a měsících a za částečné měsíce se účtuje poměrná část. Hodiny experimentování se službou Azure Machine Learning Studio se účtují za výpočetní čas aktivního experimentování. Načaté hodiny se fakturují podle průběžného využívání.  

Služba Azure Machine Learning API se účtuje s ohledem na to, jestli jde o klasickou nebo novou webovou službu (využívající Resource Manager).

Následující poplatky se v rámci předplatného započítávají za jednotlivé pracovní prostory.

* Předplatné za pracovní prostor Machine Learning: Předplatné za pracovní prostor Machine Learning je měsíční poplatek, který umožňuje přístup k pracovnímu prostoru Machine Learning Studia. Zároveň se bez něho neobejdete, pokud chcete spouštět experimenty ve Studiu nebo využívat rozhraní API produkčního prostředí.
* Hodiny experimentování se službou ML Studio: Sčítá veškeré poplatky za výpočetní kapacitu související se spouštěním experimentů v Machine Learning Studiu nebo voláním produkčního rozhraní API v přípravném prostředí.
* Pro účely trénování a hodnocení ve vašich modelech využijte pro přístup k datům připojení k místnímu serveru se spuštěným SQL Serverem.
* U klasických webových služeb:
  * Výpočetní hodiny v rozhraní API produkčního prostředí: Měří poplatky za výpočetní kapacitu využitou webovými službami spuštěnými v produkčním prostředí.
  * Transakce v rozhraní API produkčního prostředí (v tisících): Měří poplatky za volání webové služby v produkčním prostředí.

Vedle výše uvedených poplatků se v případě webových služeb využívajících Resource Manager poplatky souhrnně započítávají do vybraného plánu:

* Plán API úrovně Standard S1/S2/S3 (jednotky): Reprezentuje typ instance vybraný pro webové služby využívajících Resource Manager.
* Nadlimitní výpočetní hodiny API úrovně Standard S1/S2/S3: Zahrnuje poplatky za výpočetní kapacitu, kterou webové služby využívající Resource Manager a spuštěné v produkčním prostředí využijí potom, co se vyčerpá množství zahrnuté v existujících instancích. Další využívání se účtuje podle nadlimitní sazby přiřazené k plánům úrovně S1/S2/S3.
* Nadlimitní transakce plánu API úrovně Standard S1/S2/S3 (v tisících): Zahrnuje poplatky za volání webové služby využívající Resource Manager v produkčním prostředí potom, co se vyčerpá množství zahrnuté v existujících instancích. Další využívání se účtuje podle nadlimitní sazby přiřazené k plánům úrovně S1/S2/S3.
* Zahrnuté množství výpočetních hodin API: U webových služeb využívajících Resource Manager představuje zahrnuté množství výpočetních hodin API.
* Zahrnuté množství transakcí API (v tisících): U webových služeb využívajících Resource Manager představuje zahrnuté množství transakcí API.

**Jak se ve službě Azure Machine Learning zaregistrovat k úrovni Free?**

Stačí vám jen účet Microsoft. Přejděte na [domovskou stránku Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) a potom klikněte na možnost **Začít**. Jen co se přihlásíte pomocí účtu Microsoft, vytvoří se vám pracovní prostor v úrovni Free. Okamžitě si můžete prostředí prohlédnout a vytvořit ve službě Machine Learning první experimenty.

**Jak se ve službě Azure Machine Learning zaregistrovat k úrovni Standard?**

Pokud chcete vytvořit pracovní prostor Machine Learning úrovně Standard, musíte mít přístup k předplatnému Azure. Nejdřív si můžete vyzkoušet 30denní bezplatnou verzi Azure a později upgradovat na placené předplatné Azure. Nebo si placenou verzi Azure můžete pořídit hned. S přístupem k předplatnému potom můžete pracovní prostor Machine Learning vytvořit na portálu Microsoft Azure. Projděte si prosím [podrobný návod](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

Nebo vás do pracovního prostoru může pozvat vlastník pracovního prostoru Machine Learning úrovně Standard.

**Můžu pro úroveň Free zadat vlastní účet Azure Blob Storage?**

Ne, ekvivalentem verze služby Machine Learning, která byla dostupná před zavedením úrovní, je úroveň Standard.

**Je v úrovni Free možné nasadit modely strojového učení jako API?**

Ano, součástí úrovně Free je možnost zprovoznit modely strojového učení jako služby API přípravného prostředí. Abyste službu API přípravného prostředí přesunuli do produkce a získali ke zprovozněné službě produkční koncový bod, je potřeba využít úroveň Standard.

**Jaký je rozdíl mezi bezplatnou zkušební verzí Azure a úrovní Free služby Azure Machine Learning?**

[Bezplatná zkušební verze Microsoft Azure](https://azure.microsoft.com/free/) obsahuje kredit, který můžete použít v jakékoliv službě Azure na dobu jednoho měsíce. Úroveň Free ve službě Azure Machine Learning nabízí stálý přístup jenom k této službě a její využití pro neprodukční úlohy.

**Jak přesunu experiment z úrovně Free do úrovně Standard?**

Postup přesunutí experimentu z úrovně Free do úrovně Standard:

1. Přihlaste se do Azure Machine Learning Studia a zkontrolujte, jestli se pod selektorem pracovního prostoru na horním navigačním panelu zobrazuje pracovní prostor Free i Standard.
2. Pokud jste v pracovním prostoru Standard, přepněte na možnost Free.
3. V zobrazení seznamu experimentů vyberte experiment, který chcete zkopírovat, a klikněte na příkazové tlačítko **Kopírovat**.
4. V dialogovém okně, které se otevře, vyberte pracovní prostor Standard a potom klikněte na tlačítko **Kopírovat**.
   Společně s experimentem se do pracovního prostoru Standard zkopírují i všechny přiřazené datové sady, natrénované modely atd.
5. V pracovním prostoru Standard bude potřeba experiment znovu spustit a webovou službu znovu publikovat.

### <a name="studio-workspace"></a>Pracovní prostor Studia
**Fakturují se jednotlivé pracovní prostory zvlášť?**

Poplatky za pracovní prostory se rozepisují do jednotlivých měřených kategorií na jedné faktuře.

**Jaké konkrétní výpočetní prostředky se používají ke spouštění experimentů?**

Služba Machine Learning je víceklientská. Skutečné výpočetní prostředky, které se používají na back-endu, se liší a jsou optimalizované z hlediska výkonu a předvídatelnosti.

### <a name="guest-access"></a>Přístup hosta
**Co je přístup hosta ke službě Azure Machine Learning Studio?**

Přístup hosta je omezený zkušební přístup. Umožňuje vytvářet a spouštět experimenty v Azure Machine Learning Studiu zdarma a bez ověřování. Relace hosta jsou dočasné (nejdou ukládat) a omezené na 8 hodin. Mezi další omezení patří chybějící podpora jazyků R a Python, chybějící rozhraní API přípravného prostředí a omezená velikost datové sady a úložiště. Naproti tomu uživatelé, kteří se přihlásí pomocí účtu Microsoft, získají v Machine Learning Studiu plný přístup k úrovni Free, která je popsaná výše a která zahrnuje stálý pracovní prostor a pokročilejší možnosti. Je na vás, jak službu Machine Learning bezplatně vyzkoušíte. Na webu [https://studio.azureml.net](https://studio.azureml.net) klikněte na **Začínáme** a vyberte **Přístup hosta** nebo se přihlaste pomocí účtu Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
