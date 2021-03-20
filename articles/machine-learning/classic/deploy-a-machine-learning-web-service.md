---
title: 'ML Studio (Classic): nasazení webové služby – Azure'
description: Jak převést školicí experiment na prediktivní experiment, připravit ho pro nasazení a pak ho nasadit jako webovou službu Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 018e292abc7050f5d71c6bf9065618fecf8b5668
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520438"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Nasazení webové služby Azure Machine Learning Studio (Classic)

**platí pro:** ![ Toto je značka zaškrtnutí, což znamená, že se tento článek týká Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ Toto je X, což znamená, že se tento článek týká Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Azure Machine Learning Studio (Classic) umožňuje sestavit a otestovat prediktivní analytické řešení. Pak můžete řešení nasadit jako webovou službu.

Webové služby Machine Learning Studio (Classic) poskytují rozhraní mezi aplikací a modelem vyhodnocování pracovního postupu pro Machine Learning Studio (Classic). Externí aplikace může komunikovat s modelem vyhodnocování pracovního postupu Machine Learning Studio (Classic) v reálném čase. Volání webové služby Machine Learning Studio (Classic) vrátí výsledky předpovědi do externí aplikace. Webovou službu můžete volat předáním klíče rozhraní API, který se vytvořil při nasazení této webové služby. Webová služba Machine Learning Studio (Classic) je založená na REST, na základě oblíbené architektury pro projekty webového programování.

Azure Machine Learning Studio (Classic) má dva typy webových služeb:

* Služba Request-Response (RR): nízká latence, vysoce škálovatelná služba, která vyhodnotí jeden datový záznam.
* Služba batch execution (BES): asynchronní služba, která vyrovnává dávku datových záznamů.

Vstup pro BES je podobný datové vstupu, který využívá RRS. Hlavním rozdílem je to, že BES čte blok záznamů z několika různých zdrojů, jako je třeba Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (dotaz Hive) a zdroje HTTP.

Z hlediska vysoké úrovně si model nasadíte ve třech krocích:

* **[Vytvoření výukového experimentu]** – v studiu (Classic) můžete vytvářet a testovat model prediktivní analýzy pomocí školicích dat, která zadáte, a to pomocí rozsáhlé sady integrovaných algoritmů strojového učení.
* **[Převeďte je na prediktivní experiment]** – Jakmile se model vyškole pomocí stávajících dat a jste připraveni ho použít k vyhodnocení nových dat, připravte a Zjednodušte svůj experiment pro předpovědi.
* **Nasazení** jako **[nové webové služby]** nebo **[klasické webové služby]** – při nasazení prediktivního experimentu jako webové služby Azure mohou uživatelé odesílat data do modelu a přijímat předpovědi modelu.

## <a name="create-a-training-experiment"></a>Vytvoření školicí experimentu

K výuce modelu prediktivní analýzy použijte Azure Machine Learning Studio (Classic) k vytvoření školicího experimentu, který obsahuje různé moduly pro načtení školicích dat, přípravu dat podle potřeby, použití algoritmů strojového učení a vyhodnocení výsledků. Můžete iterovat na experiment a vyzkoušet různé algoritmy strojového učení pro porovnání a vyhodnocení výsledků.

Proces vytváření a správy pokusů o školení se podrobněji pokryje jinde. Další informace najdete v těchto článcích:

* [Vytvoření jednoduchého experimentu v Azure Machine Learning Studio (Classic)](create-experiment.md)
* [Vývoj prediktivního řešení s využitím Azure Machine Learning Studio (Classic)](tutorial-part1-credit-risk.md)
* [Import školicích dat do Azure Machine Learning Studio (Classic)](import-data.md)
* [Správa iterací experimentů v Azure Machine Learning Studio (Classic)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod školicího experimentu na prediktivní experiment

Jakmile svůj model provedete, budete připraveni převést svůj školicí experiment na prediktivní experiment, který bude vyhodnocovat skóre nových dat.

Převodem na prediktivní experiment získáte vyškolený model připravený k nasazení jako webovou službu bodování. Uživatelé webové služby mohou odesílat vstupní data do modelu a váš model vrátí zpět výsledky předpovědi. Při převodu na prediktivní experiment mějte na paměti, jak očekáváte, že váš model budou používat jiní uživatelé.

Proces převodu školicího experimentu na prediktivní experiment zahrnuje tři kroky:

1. Nahraďte moduly algoritmu strojového učení vašim vyškolenou modelem.
2. Zkraťte experiment jenom na ty moduly, které jsou potřeba pro vyhodnocování. Školicí experiment zahrnuje řadu modulů, které jsou nezbytné pro školení, ale nejsou potřeba, když je model vyškolený.
3. Definujte, jak bude model přijímat data od uživatele webové služby a jaká data budou vrácena.

> [!TIP]
> Ve školicím experimentu jste se rozhodli o školení a vyhodnocování modelu pomocí vašich vlastních dat. Ale po nasazení budou uživatelé do modelu posílat nová data a výsledky předpovědi budou vracet. Takže při převodu školicího experimentu na prediktivní experiment, který je připravený pro nasazení, pamatujte na to, jak model budou používat jiní uživatelé.

![Převést na experiment bodování](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Tlačítko nastavit webovou službu
Po spuštění experimentu (klikněte na **Run (spustit** ) v dolní části plátna experimentu), klikněte na tlačítko **nastavit webovou službu** (vyberte možnost **prediktivní webová služba** ). **Nastavení webové služby** provede za vás tři kroky převodu školicího experimentu na prediktivní experiment:

1. Tento model se uloží do části **Proučené modely** v paletě modulu (nalevo od plátna experimentu). Pak nahrazuje algoritmus strojového učení a moduly [modelu vlaků][train-model] s uloženým školicím modelem.
2. Analyzuje experiment a odebírá moduly, které byly jasně používány pouze pro školení a již není potřeba.
3. Vloží vstupní a _výstupní_ moduly _webové služby_ do výchozích umístění v experimentu (tyto moduly přijímají a vrátí data uživatelů).

Například následující experiment navlakuje pomocí ukázkového sčítání dat modelem zesíleného rozhodovacího stromu:

![Experiment školení](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduly v tomto experimentu provádějí základní čtyři různé funkce:

![Funkce modulu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Když převedete tento experiment školení na prediktivní experiment, některé z těchto modulů už nejsou potřebné nebo teď budou sloužit k jinému účelu:

* **Data** – data v této ukázkové datové sadě se při vyhodnocování nepoužijí – uživatel webové služby dodá data, která se mají určit skóre. Nicméně metadata z této datové sady, jako jsou například datové typy, používá trained model. Proto je třeba zachovat datovou sadu ve prediktivním experimentu, aby mohla tato metadata poskytnout.

* **Příprava** – v závislosti na uživatelských datech, která budou odeslána pro vyhodnocování, mohou být tyto moduly nebo nemusí být nezbytné ke zpracování příchozích dat. Tlačítko **nastavit webovou službu** je nedotykové – musíte se rozhodnout, jak je chcete zpracovat.
  
    Například v tomto příkladu může mít ukázková datová sada chybějící hodnoty, takže modul [Vyčištění chybějících dat][clean-missing-data] byl zahrnut k tomu, aby se s nimi mohla pracovat. Ukázková datová sada také obsahuje sloupce, které nejsou nutné pro výuku modelu. Proto byl zahrnutý modul [Výběr sloupců v datové sadě][select-columns] , aby vyloučil tyto nadbytečné sloupce z toku dat. Pokud víte, že data, která budou odeslána pro bodování prostřednictvím webové služby, nebudou obsahovat hodnoty, můžete odebrat modul [Vyčištění chybějících dat][clean-missing-data] . Vzhledem k tomu, že modul [Výběr sloupců v datové sadě][select-columns] pomáhá definovat sloupce dat, které vyškolený model očekává, musí tento modul zůstat.

* **Výuka** – tyto moduly se používají ke školení modelu. Když kliknete na **nastavit webovou službu**, tyto moduly se nahradí jedním modulem, který obsahuje model, který jste vyškole. Tento nový modul je uložený v části s **výukou modelů** v paletě modulu.

* **Skóre** – v tomto příkladu se modul [rozdělení dat][split] používá k rozdělení datového proudu do testovacích dat a dat školení. Ve prediktivním experimentu už nebudeme školením moct odebrat [rozdělená data][split] . Podobně modul s druhým [modelem skóre][score-model] a modul [vyhodnocení modelu][evaluate-model] slouží k porovnání výsledků z testovacích dat, takže tyto moduly nejsou v prediktivním experimentu potřeba. Modul zbývajícího [modelu skóre][score-model] je však potřeba k vrácení výsledku skóre prostřednictvím webové služby.

Tady je postup, jak náš příklad vypadá po kliknutí na **nastavit webovou službu**:

![Převedený prediktivní experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Práce, kterou provedete pomocí **nastavení webové služby** , může být dostačující k přípravě experimentu, který chcete nasadit jako webovou službu. Můžete ale chtít udělat další práci, která je specifická pro váš experiment.

#### <a name="adjust-input-and-output-modules"></a>Upravit vstupní a výstupní moduly
Ve školicím experimentu jste použili sadu školicích dat a pak nějaké zpracování využívali k získání dat ve formuláři, který algoritmus strojového učení potřebuje. Pokud data, která očekáváte pro příjem prostřednictvím webové služby, nebude potřeba zpracovat, můžete ji obejít: připojit výstup **vstupního modulu webové služby** k jinému modulu v experimentu. Data uživatele se nyní dostanou do modelu v tomto umístění.

Například ve výchozím nastavení umístí **Webová služba** **Vstupní modul webové služby** v horní části toku dat, jak je znázorněno na obrázku výše. Ale můžeme ručně umístit **vstup webové služby** za moduly zpracování dat:

![Přesun vstupu webové služby](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Vstupní data poskytnutá prostřednictvím webové služby teď budou předávat přímo do modulu bodového modelu bez jakéhokoli předběžného zpracování.

Podobně výchozí nastavení **webové služby** umístí modul výstupu webové služby do dolní části toku dat. V tomto příkladu se webová služba vrátí uživateli výstup modulu určení [skóre modelu][score-model] , který zahrnuje kompletní vstupní datový vektor a výsledky bodování.
Pokud ale dáváte přednost vrácení jiné položky, můžete přidat další moduly před **výstupní modul webové služby** . 

Například chcete-li vrátit pouze výsledky bodování a ne celý vektor vstupních dat, přidejte modul [Výběr sloupců v datové sadě][select-columns] , který vyloučí všechny sloupce kromě výsledků bodování. Pak přesuňte modul **výstupu webové služby** na výstup modulu [Výběr sloupců v datové sadě][select-columns] . Experiment vypadá takto:

![Přesunutí výstupu webové služby](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Přidat nebo odebrat další moduly zpracování dat
Pokud máte v experimentu více modulů, které vás při bodování nebudou potřebovat, můžete je odebrat. Protože jsme například přesunuli modul **vstupu webové služby** do bodu za moduly zpracování dat, můžeme z prediktivního experimentu odebrat modul [Vyčištění chybějících dat][clean-missing-data] .

Náš prediktivní experiment teď vypadá takto:

![Odebírá se další modul.](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Přidat volitelné parametry webové služby
V některých případech můžete chtít uživateli webové služby změnit chování modulů, když je služba k dispozici. Tato možnost umožňuje použít *parametry webové služby* .

Běžným příkladem je nastavení modulu [Import dat][import-data] , aby uživatel nasazené webové služby mohl při použití webové služby zadat jiný zdroj dat. Nebo nakonfigurujte modul [exportu dat][export-data] tak, aby bylo možné zadat jiný cíl.

Můžete definovat parametry webové služby a přidružit je k jednomu nebo více parametrům modulu a můžete určit, jestli jsou povinné nebo volitelné. Uživatel webové služby poskytne hodnoty pro tyto parametry, když je služba k dispozici, a akce modulu se upraví odpovídajícím způsobem.

Další informace o tom, jaké parametry webové služby jsou a jak je používat, najdete v tématu [použití Azure Machine Learningch parametrů webové služby][webserviceparameters].

Následující kroky popisují Nasazení prediktivního experimentu jako nové webové služby. Experiment můžete nasadit také jako klasickou webovou službu.

## <a name="deploy-it-as-a-new-web-service"></a>Nasadit jako novou webovou službu

Teď, když je připravený experiment připraven, můžete ho nasadit jako novou (Správce prostředků) webovou službu Azure. Pomocí webové služby mohou uživatelé odesílat data do modelu a model vrátí jeho předpovědi.

Pokud chcete nasadit prediktivní experiment, klikněte na **Spustit** v dolní části plátna experimentu. Po skončení běhu experimentu klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [nové]**.  Otevře se stránka nasazení portálu webové služby Machine Learning Studio (Classic).

> [!NOTE] 
> Nasazení nové webové služby vyžaduje dostatečná oprávnění v předplatném, na které nasazujete webovou službu. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Stránka experimentu nasazení portálu webové služby

Na stránce nasazení experimentu zadejte název webové služby.
Vyberte Cenový tarif. Pokud máte existující Cenový tarif, můžete ho vybrat, jinak musíte pro službu vytvořit nový cenový plán.

1. V rozevíracím seznamu **cenový plán** vyberte existující plán nebo vyberte možnost **vybrat nový plán** .
2. Do pole **název plánu** zadejte název, který bude identifikovat plán na faktuře.
3. Vyberte jednu z **úrovní měsíčního plánu**. Plány plánu se ve výchozím nastavení naplánují pro výchozí oblast a vaše webová služba se v této oblasti nasadí.

Klikněte na **nasadit** a otevře se stránka pro **rychlý Start** pro webovou službu.

Stránka pro rychlý Start webové služby vám poskytne přístup a doprovodné materiály k nejběžnějším úlohám, které budete provádět po vytvoření webové služby. Odtud můžete snadno přistupovat ke stránce test a k využití stránky.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Otestování nové webové služby

Pokud chcete otestovat novou webovou službu, klikněte v části běžné úlohy na **Testovat webovou službu** . Na stránce test můžete otestovat webovou službu jako službu Request-Response (RR) nebo službu Batch Execution (BES).

Na stránce testování záznamů se zobrazí vstupy, výstupy a všechny globální parametry, které jste pro experiment definovali. K otestování webové služby můžete ručně zadat příslušné hodnoty pro vstupy nebo zadat formátovaný soubor s hodnotami oddělenými čárkou (CSV) obsahující testovací hodnoty.

Chcete-li testovat pomocí prostředku, v režimu zobrazení seznamu zadejte příslušné hodnoty pro vstupy a klikněte na **testovat požadavek-odpověď**. Výsledky předpovědi se zobrazí ve sloupci Output vlevo.

![Zadejte odpovídající hodnoty pro otestování webové služby.](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Pokud chcete otestovat BES, klikněte na **Batch**. Na stránce test dávky klikněte na Procházet pod vaším vstupem a vyberte soubor CSV obsahující příslušné vzorové hodnoty. Pokud nemáte soubor CSV a vytvořili jste prediktivní experiment pomocí Machine Learning Studio (Classic), můžete si stáhnout datovou sadu pro prediktivní experiment a použít ji.

Chcete-li stáhnout datovou sadu, otevřete Machine Learning Studio (Classic). Otevřete prediktivní experiment a klikněte pravým tlačítkem na vstup pro váš experiment. V místní nabídce vyberte **datová sada** a pak vyberte **Stáhnout**.

![Stáhněte si datovou sadu z plátna studia (Classic).](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klikněte na tlačítko **test**. Stav úlohy spuštění dávky se zobrazí vpravo v části **testovací úlohy dávky**.

![Otestování úlohy provádění dávky na portálu webové služby](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na stránce **Konfigurace** můžete změnit popis, název, aktualizovat klíč účtu úložiště a povolit ukázková data pro webovou službu.

![Konfigurace webové služby](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Přístup k nové webové službě

Po nasazení webové služby z Machine Learning Studio (Classic) můžete odesílat data do služby a programově přijímat odpovědi.

Stránka **využití** poskytuje všechny informace, které potřebujete pro přístup k webové službě. Například klíč rozhraní API je k dispozici pro povolení oprávněného přístupu ke službě.

Další informace o přístupu k webové službě Machine Learning Studio (Classic) najdete v tématu [Jak používat webové služby Azure Machine Learning Studio (Classic)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Správa nové webové služby

Nové webové služby můžete spravovat pomocí portálu Web Services Machine Learning Studio (Classic). Na [hlavní stránce portálu](https://services.azureml.net/)klikněte na **webové služby**. Na stránce webové služby můžete odstranit nebo zkopírovat službu. Chcete-li monitorovat konkrétní službu, klikněte na službu a pak klikněte na **řídicí panel**. Chcete-li monitorovat dávkové úlohy přidružené k webové službě, klikněte na položku **protokol žádosti o dávku**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Nasazení nové webové služby do více oblastí

Novou webovou službu můžete snadno nasadit do několika oblastí, aniž byste potřebovali víc předplatných nebo pracovních prostorů.

Ceny jsou specifické pro oblast, takže je potřeba definovat plán fakturace pro každou oblast, ve které budete webovou službu nasazovat.

#### <a name="create-a-plan-in-another-region"></a>Vytvoření plánu v jiné oblasti

1. Přihlaste se k [Microsoft Azure Machine Learning webové služby](https://services.azureml.net/).
2. Klikněte na možnost nabídky **plány** .
3. Na stránce plány pro zobrazení klikněte na **Nový**.
4. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém se nový plán bude nacházet.
5. V rozevíracím seznamu **oblast** vyberte oblast pro nový plán. Možnosti plánu pro vybranou oblast se zobrazí v části **Možnosti plánu** stránky.
6. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků pro daný plán. Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. V části **plán název** zadejte název plánu.
8. V části **Možnosti plánu** klikněte na úroveň fakturace nového plánu.
9. Klikněte na **Vytvořit**.

#### <a name="deploy-the-web-service-to-another-region"></a>Nasazení webové služby do jiné oblasti

1. Na stránce Microsoft Azure Machine Learning webové služby klikněte na možnost nabídky **webové služby** .
2. Vyberte webovou službu, kterou nasazujete, do nové oblasti.
3. Klikněte na tlačítko **Kopírovat**.
4. Do pole **název webové služby** zadejte nový název webové služby.
5. Do pole **Popis webové služby** zadejte popis webové služby.
6. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém bude nová webová služba umístěna.
7. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků pro webovou službu. Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. V rozevíracím seznamu **oblast** vyberte oblast, do které chcete nasadit webovou službu.
9. V rozevíracím seznamu **účet úložiště** vyberte účet úložiště, do kterého chcete uložit webovou službu.
10. V rozevíracím seznamu **cenový plán** vyberte plán v oblasti, kterou jste vybrali v kroku 8.
11. Klikněte na tlačítko **Kopírovat**.

## <a name="deploy-it-as-a-classic-web-service"></a>Nasaďte ji jako klasickou webovou službu.

Teď, když je prediktivní experiment dostatečně připravený, můžete ho nasadit jako klasickou webovou službu Azure. Pomocí webové služby mohou uživatelé odesílat data do modelu a model vrátí jeho předpovědi.

Pokud chcete nasadit prediktivní experiment, klikněte na **Spustit** v dolní části plátna experimentu a potom klikněte na **nasadit webovou službu**. Webová služba je nastavená a nachází se na řídicím panelu webové služby.

![Nasazení webové služby z studia (Classic)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testování klasické webové služby

Webovou službu můžete otestovat buď na portálu Machine Learning Studio (Classic) webových služeb, nebo v Machine Learning Studio (Classic).

Webovou službu Request response otestujete kliknutím na tlačítko **test** na řídicím panelu webové služby. Zobrazí se dialogové okno s žádostí o zadání vstupních dat pro službu. Toto jsou sloupce očekávané experimentem bodování. Zadejte sadu dat a pak klikněte na **OK**. Výsledky vygenerované webovou službou se zobrazí v dolní části řídicího panelu.

Kliknutím na odkaz **test** Preview můžete službu otestovat na portálu Azure Machine Learning Studio (Classic) Web Services, jak je uvedeno dříve v části Nová webová služba.

Chcete-li otestovat službu spuštění dávky, klikněte na odkaz **test** Preview. Na stránce test dávky klikněte na Procházet pod vaším vstupem a vyberte soubor CSV obsahující příslušné vzorové hodnoty. Pokud nemáte soubor CSV a vytvořili jste prediktivní experiment pomocí Machine Learning Studio (Classic), můžete si stáhnout datovou sadu pro prediktivní experiment a použít ji.

![Otestování webové služby](./media/publish-a-machine-learning-web-service/figure-3.png)

Na stránce **Konfigurace** můžete změnit zobrazovaný název služby a zadat pro něj popis. Název a popis se zobrazí v [Azure Portal](https://portal.azure.com/) , kde spravujete své webové služby.

Zadáním řetězce pro každý sloupec v části **vstupní schéma**, **výstupní schéma** a **parametr webové služby** můžete zadat popis pro vstupní data, výstupní data a parametry webové služby. Tyto popisy se používají v dokumentaci k ukázkovému kódu, která je k dispozici pro webovou službu.

Protokolování můžete povolit, chcete-li diagnostikovat všechny chyby, které vidíte, když máte k dispozici webovou službu. Další informace najdete v tématu [Povolení protokolování pro webové služby Machine Learning Studio (Classic)](web-services-logging.md).

![Povolit protokolování na portálu Web Services](./media/publish-a-machine-learning-web-service/figure-4.png)

Koncovým bodům webové služby můžete také nakonfigurovat na portálu Azure Machine Learning Web Services podobným postupem, který jste použili dříve v části Nová webová služba. Možnosti se liší, můžete přidat nebo změnit popis služby, povolit protokolování a povolit ukázková data pro testování.

### <a name="access-your-classic-web-service"></a>Přístup k klasické webové službě

Po nasazení webové služby z Azure Machine Learning Studio (Classic) můžete odesílat data do služby a programově přijímat odpovědi.

Řídicí panel poskytuje všechny informace, které potřebujete pro přístup k webové službě. Například klíč rozhraní API je k dispozici pro povolení oprávněného přístupu ke službě a k dispozici jsou stránky s nápovědami k rozhraní API, které vám pomohou začít psát kód.

Další informace o přístupu k webové službě Machine Learning Studio (Classic) najdete v tématu [Jak používat webové služby Azure Machine Learning Studio (Classic)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Správa klasické webové služby

Existují různé akce, které můžete provést k monitorování webové služby. Můžete ho aktualizovat a odstranit. Kromě výchozího koncového bodu, který je vytvořen při jeho nasazení, můžete také přidat další koncové body do klasické webové služby.

Další informace najdete v tématech [Správa pracovního prostoru Azure Machine Learning Studio (klasický)](manage-workspace.md) a [Správa webové služby pomocí portálu Web Services Azure Machine Learning Studio (Classic)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualizace webové služby
Můžete provádět změny webové služby, jako je třeba aktualizace modelu pomocí dalších školicích dat, a znovu ho nasadit a přepsat původní webovou službu.

Chcete-li aktualizovat webovou službu, otevřete původní prediktivní experiment, který jste použili k nasazení webové služby, a proveďte upravitelnou kopii kliknutím na možnost **Uložit jako**. Proveďte změny a potom klikněte na **nasadit webovou službu**.

Vzhledem k tomu, že jste tento experiment nasadili dřív, zobrazí se dotaz, jestli chcete přepsat existující službu (klasickou webovou službu) nebo aktualizovat (novou webovou službu). Kliknutím na **Ano** nebo **aktualizovat** zastavíte existující webovou službu a nasadíme nový prediktivní experiment, který se nasadí na místo.

> [!NOTE]
> Pokud jste v původní webové službě provedli změny konfigurace, například zadáním nového zobrazovaného názvu nebo popisu, budete muset tyto hodnoty zadat znovu.

Jednou z možností aktualizace webové služby je přeučení modelu prostřednictvím kódu programu. Další informace najdete v tématu [přeučení modelů Machine Learning Studio (Classic) prostřednictvím kódu programu](./retrain-machine-learning-model.md).

## <a name="next-steps"></a>Další kroky

* Další technické informace o tom, jak funguje nasazení, najdete v tématu [jak model Machine Learning Studio (klasický) postupuje z experimentu na provozní webovou službu](model-progression-experiment-to-web-service.md).

* Podrobnosti o tom, jak model připravit k nasazení, najdete v tématu [Příprava modelu pro nasazení v Azure Machine Learning Studio (Classic)](deploy-a-machine-learning-web-service.md).

* Existuje několik způsobů, jak využít REST API a komunikovat s webovou službou. Podívejte [se, jak využívat webovou službu Azure Machine Learning Studio (Classic)](consume-web-services.md).

<!-- internal links -->
[Vytvoření školicí experimentu]: #create-a-training-experiment
[Převést na prediktivní experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Nová webová služba]: #deploy-it-as-a-new-web-service
[Klasická webová služba]: #deploy-it-as-a-classic-web-service
[New]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data