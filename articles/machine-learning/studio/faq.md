---
title: Machine Learning Studio – nejčastější dotazy (FAQ)
titleSuffix: Azure Machine Learning Studio
description: 'Azure Machine Learning Studio: Časté otázky k fakturaci, schopnostem a omezením cloudové služby pro efektivní prediktivní modelování.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462275"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Azure Machine Learning Studio – nejčastější dotazy: možnosti a omezení
Zde jsou některé nejčastější dotazy (a příslušné odpovědi) týkající se cloudové služby Azure Machine Learning, která slouží k vývoji prediktivních modelů a zprovozňování řešení prostřednictvím webových služeb. 

## <a name="general-questions"></a>Obecné otázky
**Co je Machine Learning Studio?**

Machine Learning Studio je prostředí a přetahování plátno, které přistupujete pomocí webového prohlížeče. Machine Learning Studio nabízí mnoho modulů s vizuálním kompozičním rozhraním, které umožňuje vytvořit komplexní pracovní postup založený na datové vědě ve formě experimentu.

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

* Služba Request-Response (RRS): Nízká latence, vysoce škálovatelná služba, která poskytuje rozhraní pro bezstavové modely vytvořené a nasazené pomocí Machine Learning Studio.
* Služba batch Execution (BES): Asynchronní služba pro vyhodnocování dávek datových záznamů.

Existuje několik způsobů, jak využít REST API a komunikovat s webovou službou. Můžete například napsat aplikaci v jazyce C#, R nebo Python s pomocí ukázkového kódu, který se vygeneroval při nasazení webové služby.

Ukázkový kód je dostupný na těchto místech:
- Stránka o využívání webových služeb na portálu Azure Machine Learning Web Services
- Stránka nápovědy k rozhraní API na řídicím panelu webové služby v Machine Learning Studiu

Můžete také použít ukázkový sešit v Microsoft Excelu, který se pro vás vygeneroval a který je dostupný na řídicím panelu webové služby v Machine Learning Studiu.

**Jaké jsou hlavní aktualizace služby Azure Machine Learning?**

Nejnovější aktualizace najdete v tématu [Novinky ve službě Azure Machine Learning](../../active-directory/fundamentals/whats-new.md).

## <a name="import-and-export-data-for-machine-learning"></a>Import a export dat pro Machine Learning
**Jaké zdroje dat Machine Learning podporuje?**

Data můžete do experimentu Machine Learning Studio stáhnout třemi způsoby:

- Nahrání místního souboru jako datové sady
- Použití modulu k importu dat z cloudových datových služeb
- Import datové sady uložené z jiného experimentu

Další informace o podporovaných formátech souborů najdete v tématu o [importu trénovacích dat do nástroje Machine Learning Studio](import-data.md).

### <a id="ModuleLimit"></a>Jak velká může být datová sada pro moje moduly?
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

### <a id="UploadLimit"></a>Jaké jsou limity pro nahrávání dat?
Pro datové sady větší než několik GB byste měli nahrát data do Azure Storage nebo Azure SQL Database, případně použít HDInsight, a nenahrávat přímo z místního souboru.

**Je možné číst data z Amazonu S3?**

Pokud máte malé množství dat a chcete je zveřejnit přes adresu URL protokolu HTTP, můžete použít modul [Import dat][import-data]. Jakákoli větší množství dat nejdřív přeneste do Azure Storage a potom použijte modul [Import dat][import-data], abyste je připojili k experimentu.
<!--

<SEE CLOUD DS PROCESS>
-->

**Je k dispozici integrovaná funkce pro obrazový vstup?**

Informace o funkci obrazového vstupu najdete v referenci [Import obrázků][image-reader].

## <a name="modules"></a>Moduly
**V Azure Machine Learning Studiu není algoritmus, zdroj dat, formát dat nebo operace transformace dat, kterou hledám. Jaké mám možnosti?**

Můžete navštívit [fórum pro zpětnou vazbu uživatelů](https://go.microsoft.com/fwlink/?LinkId=404231), kde najdete žádosti o funkce, které sledujeme. Pokud už existuje žádost o funkci, kterou sami hledáte, přidejte k ní svůj hlas. Pokud funkce, kterou hledáte, neexistuje, vytvořte novou žádost. Na tomto fóru můžete také sledovat stav své žádosti. Tento seznam pečlivě sledujeme a často aktualizujeme stav dostupnosti funkce. Navíc díky integrované podpoře R a Pythonu můžete podle potřeby vytvářet vlastní transformace.

**Můžu převést svůj existující kód do nástroje Machine Learning Studio?**

Ano, svůj existující kód R nebo Python můžete do nástroje Machine Learning Studio převést, spustit jej v tom stejném experimentu pomocí inteligentních algoritmů Azure Machine Learning a přes Azure Machine Learning nasadit řešení jako webovou službu. Další informace najdete v tématech o [rozšíření experimentů pomocí R](extend-your-experiment-with-r.md) a o [spouštění pythonových skriptů strojového učení v nástroji Azure Machine Learning Studio](execute-python-scripts.md).

**Je možné definovat model pomocí něčeho takového, jako je jazyk [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)?**

Ne, jazyk PMML (Predictive Model Markup Language) se nepodporuje. K definování modulu můžete využít vlastní kód v R a Pythonu.

**Kolik modulů je možné v experimentu paralelně spustit?**  

V jednom experimentu můžete paralelně spustit až čtyři moduly.

## <a name="data-processing"></a>Zpracování dat
**Je k možné v rámci experimentu využívat interaktivní vizualizaci dat (kromě vizualizací R)?**

Kliknutím na výstup modulu můžete vizualizovat data a získat statistiky.

**Při prohlížení náhledu výsledků nebo dat v prohlížeči je počet řádků a sloupců omezený. Proč?**

Protože do prohlížeče je možné odesílat velké objemy dat, je velikost dat omezená, aby se Machine Learning Studio nezpomalovalo. Pokud chcete vizualizovat všechna data nebo výsledky, je vhodnější data stáhnout a použít Excel nebo jiný nástroj.

## <a name="algorithms"></a>Algoritmy
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

## <a name="r-module"></a>Modul R
**Jaké balíčky R jsou k dispozici v nástroji Machine Learning Studio?**

K dnešnímu dni Machine Learning Studio podporuje přes 400 balíčků CRAN R. Všechny zahrnuté balíčky jsou uvedené v [aktuálním seznamu](http://az754797.vo.msecnd.net/docs/RPackages.xlsx). Pokud chcete zjistit, jak tento seznam získat sami, přečtěte si také téma o [rozšíření experimentů pomocí R](extend-your-experiment-with-r.md). Pokud požadovaný balíček není v seznamu, uveďte název balíčku na [fóru pro zpětnou vazbu uživatelů](https://go.microsoft.com/fwlink/?LinkId=404231).

**Je možné vytvořit vlastní modul R?**

Ano, další informace najdete v tématu o [vytváření vlastních modulů R ve službě Azure Machine Learning](custom-r-modules.md).

**Je pro R k dispozici prostředí REPL?**

Ne, ve studiu není žádné prostředí REPL (Read-Eval-Print-Loop) pro R.

## <a name="python-module"></a>Modul Python
**Je možné vytvořit vlastní modul Python?**

V této chvíli ne, ale stejného výsledku je možné dosáhnout použitím jednoho nebo více modulů [Execute Python Script][python].

**Je pro Python k dispozici prostředí REPL?**

V nástroji Machine Learning Studio můžete použít Jupyter Notebooks. Další informace najdete v tématu [Úvod do aplikace Jupyter Notebooks v Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).

## <a name="web-service"></a>Webová služba

**Jak je možné programově přeučit modely Azure Machine Learning?**

Použijte rozhraní API pro přeučení. Další informace najdete v tématu o [programovém přeučení modelů Machine Learning](retrain-models-programmatically.md). V [ukázce přeučování v Microsoft Azure Machine Learning](https://azuremlretrain.codeplex.com/) je k dispozici i ukázkový kód.

**Je možné model nasadit lokálně nebo do aplikace, která nemá připojení k internetu?**

Ne.

**Existuje základní latence, kterou je možné očekávat u všech webových služeb?**

Přečtěte si téma [Limity předplatného Azure](../../azure-subscription-service-limits.md).

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

Řádky i sloupce jsou každý omezeny na omezení Max Int rozhraní .NET: 2,147,483,647.

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


## <a name="billing-questions"></a>Dotazy k fakturaci

Informace o fakturaci a cenách najdete v tématu [Machine Learning – ceny](https://azure.microsoft.com/pricing/details/machine-learning/).


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
