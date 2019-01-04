---
title: Azure Machine Learning verze Preview 2017 – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje nejčastější dotazy a odpovědi pro funkce Azure Machine Learning ve verzi preview
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ROBOTS: NOINDEX
ms.openlocfilehash: f573cda9f06837bf44ee39a680b207af1ba0fe07
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973226"
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Nejčastější dotazy k Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Služba Azure Machine Learning je plně spravovaná služba Azure, která umožňuje vytvářet, testovat, spravovat a nasadit modely AI a strojové učení. Naše služby a aplikace ke stažení nabízejí přístup založeno na kódu, který využívá cloud, místní a hraničními zařízeními a poskytují vlaku, nasazovat, spravovat a monitorovat modelů pomocí power, rychlost a pružnost. Případně Azure Machine Learning Studio nabízí založené na prohlížeči visual a přetahování pro vytváření prostředí a kde není vyžadováno žádné kódování. 

## <a name="general-product-questions"></a>Obecné dotazy

**Jaké další služby Azure jsou vyžadována?**

Azure Blob Storage a Azure Container Registry se používají technologii Azure Machine Learning. Kromě toho musíte ke zřízení výpočetních prostředků, jako je například virtuální počítač pro datové vědy nebo HDInsight clusteru. Výpočetní funkce a hostování se rovněž vyžadují při nasazování webových služeb, jako například [Azure Container Service](https://docs.microsoft.com/azure/aks).

**Jak Azure Machine Learning souvisí Microsoft Machine Learning Services v SQL serveru 2017?**   

Machine Learning Services v SQL serveru 2017 je rozšiřitelný, škálovatelnou platformu pro integraci úlohy strojového učení do databáze pracovních postupů. To se skvěle hodí pro scénáře, ve kterém jsou vyžadována pro příklad, ve kterém přesun dat je nákladné nebo untenable místní řešení. Naproti tomu Cloudová nebo hybridní úlohy se skvěle hodí pro naše nové služby Azure. 

**Jak Azure Machine Learning souvisí Microsoft Machine Learning pro Spark?**

MMLSpark poskytuje obsáhlého learningu a nástrojů pro datové vědy pro Apache Spark, s důrazem na produktivitu, snadné experimentování a stav nejmodernější algoritmy. MMLSpark nabízí integraci kanálů Spark Machine Learning pomocí sady Microsoft Cognitive Toolkit a OpenCV. Můžete vytvořit výkonné a vysoce škálovatelné prediktivní a analytické modely pro data obrázků a textu. MMLSpark k dispozici v licenci open source a je zahrnuté v aplikaci Workbench AML jako sada spotřební modely a algoritmy. Další informace o MMLSpark projděte si naši dokumentaci produktu. 

**Nové nástroje a služby jsou podporovány verzí Spark?**

Aplikace Workbench aktuálně obsahuje a podporuje MMLSpark verzi 0.8, které je kompatibilní s Apache Sparkem 2.1. Máte také možnost k použití image Dockeru s podporou grafického procesoru z MMLSpark 0,8 na virtuální počítače s Linuxem.

## <a name="experimentation-service"></a>Služba experimentování ve službě

**Co je služba experimentování ve službě Azure Machine Learning?**

Služba experimentování ve službě je spravovaná služba Azure, která přebírá experimentování ve službě machine learning na další úroveň. Experimenty se dají místně nebo v cloudu. Rychle prototypy na desktopu, pak škálovat, aby virtuální počítače ani clustery Spark. Virtuální počítače Azure s nejnovějšími technologiemi GPU umožňují zapojit do obsáhlý learning rychle a efektivně. Také zahrnuli jsme těsnou integraci s Git, můžete snadno připojit do stávajících pracovních postupů pro kód pro sledování, konfigurace a spolupráci. 

**Jak se mi účtovat služba experimentování?**

První dva uživatele přidružené k vaší služby experimentování ve službě Azure Machine Learning je zdarma. Další uživatelé bude účtovat ve výši 50 USD/měsíc ve verzi Public Preview. Další informace o cenách a fakturaci najdete na naší stránce s cenami.

**Se mi účtovat podle počtu spuštěných experimentů?**

Ne, služba experimentování umožňuje tolik experimentů potřebujete a účtuje se pouze podle počtu uživatelů. Výpočetní prostředky Experimentování se účtují samostatně. Doporučujeme vám, abyste mohli vyhledat nejlepší přizpůsobení modelu pro vaše řešení provádět více experimentů.

**Jaké konkrétní výpočetní prostředky a prostředky úložiště, které můžete použít**

Služba experimentování ve službě může vaše experimenty provádět na místních počítačích (přímý nebo v dockeru), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), a [HDInsight](https://azure.microsoft.com/services/hdinsight/). Také přistupuje k službě [služby Azure Storage](https://azure.microsoft.com/services/storage/) účet pro ukládání výstupů spuštění a můžete využít [Azure DevOps](https://azure.microsoft.com/services/devops/) účet pro správu verzí a úložiště Git. Všimněte si, že se vám bude účtovat nezávisle na sobě žádné spotřebované výpočetní prostředky a prostředky úložiště podle jejich příslušných cen.

## <a name="model-management"></a>Správa modelů

**Co je Správa modelů Azure Machine Learning?**

Správa modelů Azure Machine Learning je spravovaná služba Azure, který umožňuje data vědců a vývojářů týmům spolehlivě nasazovat prediktivní modely v nejrůznějších prostředích. Úložiště Git a kontejnery Dockeru poskytují sledovatelnost a opakovatelnost. V cloudu, v místním nebo hraničním je možné spolehlivě nasadit modely. Jakmile se v produkčním prostředí, budete moct spravovat výkon modelů, pak proaktivně přeučování Pokud výkon sníží, přetrénujte. Můžete nasazovat modely na místních počítačích, na [virtuální počítače Azure](https://azure.microsoft.com/services/virtual-machines/), Spark u [HDInsight](https://azure.microsoft.com/services/hdinsight/) nebo orchestrované Kubernetes [Azure Container Service](https://azure.microsoft.com/services/container-service/) clustery.

**Co je "model"?**

Model je, že se použije jako výstupní experimentování ve službě spuštění, který pro správu modelů. Model, který je registrován v hostitelský účet se počítá podle vašeho plánu, včetně modelů aktualizovat prostřednictvím přetrénování nebo verze iterace.

**Co je "spravovaný model?"**

Model je výstupem procesu trénování a jedná se o aplikování algoritmu machine learningu na trénovací data. Správa modelů umožňuje nasazovat modely jako webové služby, správa různých verzí vašich modelů a monitorujte jejich výkon a metriky. "Spravovaný" modely jsou zaregistrovány pomocí účtu správy modelů Azure Machine Learning. Představte si například scénář, kdy se pokoušíte o prognózu prodeje. V průběhu fáze experimentování vygenerujete mnoho modelů s použitím různých datových sad nebo algoritmů. Jste vygenerovali čtyři modely s různou přesností, ale rozhodnout registrovat pouze model s nejvyšší přesností. Model, který je registrovaný se stane první spravovaný model.
 
**Co je "nasazení?"**

Správa modelů umožňuje nasazovat modely jako zabalené kontejnery webové služby v Azure. Tyto webové služby je možné vyvolat pomocí rozhraní REST API. Každá webová služba se počítá jako jedno nasazení a celkový počet aktivních nasazení se započítává do plánu. Použití příklad Prognózování prodeje, když nasazujete nejvýkonnějšího modelu, váš plán je zvýšen o jedno nasazení. Přeučování a nasadit jinou verzi, máte dvě nasazení. Pokud zjistíte, že je lepší novější model a odstranit původní, počet vašich nasazení se sníží o jedna.

**Jaké konkrétní výpočetní prostředky jsou k dispozici pro mé nasazení?** 

Správa modelů ve službě může vaše nasazení spouštět jako kontejnery Dockeru zaregistrovaný [Azure Container Service](https://azure.microsoft.com/services/container-service/), jako [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), nebo na místních počítačích. Cíle nasazení dalších přidáme zanedlouho. Všimněte si, že vám budou nezávisle účtovat veškeré spotřebované výpočetní prostředky, na základě jejich příslušných cen.

**Můžete použít Správa modelů Azure Machine Learning nasazovat modely sestavené pomocí jiných nástrojů než služba experimentování?**

Získáte dosažení co nejlepších výsledků při nasazování modelů vytvořených pomocí služby experimentování. Však můžete nasazovat modely sestavené pomocí jiných architekturami a nástroji. Podporujeme různé modely včetně MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-informace, Keras atd. 

**Můžete použít vlastní prostředky Azure?**

Ano, služba experimentování a správa modelů ve službě fungování ve spojení s více úložišť dat Azure, výpočetní úlohy a dalších služeb. V naší technické dokumentaci pro další informace o požadovaných služeb Azure.

**Podpora místních a cloudových scénáře nasazení?**

Ano. Podporujeme místní a cloudové scénáře nasazení zařízení prostřednictvím kontejnerů Dockeru. Místní spuštění cíle zahrnují: nasazení Docker jedním uzlem, [Microsoft SQL serverem služby ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop a Spark. Podporujeme také cloudové nasazení prostřednictvím Dockeru, včetně: nasazení v clusteru prostřednictvím služby Azure Container Service a Kubernetes a HDInsight Spark clustery. Edge scénáře jsou podporované prostřednictvím kontejnerů Dockeru a Azure IOT Edge. 

**Můžete spustit image Dockeru, který byl vytvořen pomocí rozhraní příkazového řádku Azure Machine Learning na jiného hostitele?**

Ano. Image můžete použít jako webové služby na libovolného hostitele dockeru jako hostitel má dostatečný výpočetní prostředky pro hostování image dockeru.

**Podporujete přetrénování modelů nasazené?**

Ano, můžete nasadit několik verzí stejného modelu. Správa modelů ve službě bude podporovat aktualizace služby pro všechny aktualizované modely a obrázky.

## <a name="workbench"></a>Workbench

**Co je Azure Machine Learning Workbench?**

Azure Machine Learning Workbench je aplikace, která doplňuje vytvořené pro profesionální datové vědce. K dispozici pro Windows a Mac v aplikaci Machine Learning Workbench poskytuje přehled, Správa a řízení pro řešení strojového učení. Machine Learning Workbench zahrnuje přístup až po špičkové architektur AI od Microsoftu i open source komunity. Přidali jsme nejoblíbenější data science sady nástrojů, včetně TensorFlow, Microsoft Cognitive Toolkit, Spark ML a scikit-informace a provádění dalších akcí. Povolili jsme také integrace s oblíbenými datové vědy Integrovaná vývojová prostředí jako je například poznámkových bloků Jupyter, PyCharm a Visual Studio Code. Machine Learning Workbench má integrovaný možnosti přípravy rychle ukázkový, pochopit a přípravě dat, ať už strukturovaných nebo nestrukturovaných. Náš nový nástroj pro přípravu dat, volá [PROSE](https://microsoft.github.io/prose/), je založená na špičkové technologie od Microsoft Research.

**Aplikace Workbench je integrované vývojové prostředí?**

Ne. Machine Learning Workbench byly navržené jako pomocníka pro oblíbená prostředí IDE, jako jsou poznámkové bloky Jupyter, Visual Studio Code a PyCharm ale není plně funkční prostředí IDE. Machine Learning Workbench nabízí některé základní text možností pro úpravy, ale ladění, technologie intellisense a dalších běžně používané funkce integrovaného vývojového prostředí se nepodporují. Doporučujeme používat své oblíbené prostředí IDE pro vývoj kódu, úpravy a ladění. Možná budete také chtít zkuste [Visual Studio Code Tools pro AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

**Platí se za použití Azure Machine Learning Workbench?**

Ne. Azure Machine Learning Workbench je bezplatná aplikace. Můžete ji stáhnout na tolik počítačů a pro tolik uživatelů, kolik potřebujete. K používání aplikace Azure Machine Learning Workbench potřebujete účet Experimentování.

**Podporuje možnosti příkazového řádku?**

Ano, Azure Machine Learning nabízí úplné rozhraní příkazového řádku. Rozhraní příkazového řádku Machine Learning je nainstalovaný ve výchozím nastavení se aplikace Azure Machine Learning Workbench. Je také součástí virtuálního počítače pro datové vědy pro Linux v Azure a bude se integrovat do [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**Můžete použít Jupyter Notebooks pomocí aplikace Workbench?**

Ano! Poznámkové bloky Jupyter v aplikaci Workbench pomocí aplikace Workbench jako hostitelské aplikace klienta, můžete spustit stejným způsobem, jako by prohlížeč jako klient. 

**Které jádra aplikace Jupyter Notebook jsou podporovány?**

Aktuální verze zahrnuté v aplikaci Workbench Jupyter spustí jádra Python 3 a další jádra pro každý soubor "runconfig" ve složce aml_config. Podporované konfigurace zahrnují:
- Místní Pythonu
- Python v místním nebo vzdáleném Dockeru

## <a name="data-formats-and-capabilities"></a>Formáty dat a možnosti

**Které formáty souborů jsou aktuálně podporovány pro příjem dat v aplikaci Workbench?**

Nástroje pro přípravu dat v aplikaci Workbench aktuálně podporují příjem z následujících formátů: 
- Soubory s oddělovači, jako je například CSV, TSV, atd.
- Soubory Pevná šířka
- Místo textových souborů
- Excel (.xls/xlsx)
- Soubory JSON
- Soubory parquet 
- Vlastní soubory (skriptů), pokud vaše řešení nevyžaduje příjem dat z dalších zdrojů, kódu Pythonu můžete použít k... 

**Umístění úložiště dat se aktuálně podporují?**

Aplikace Workbench ve veřejné verzi preview podporuje příjem dat ze: 
- Místní pevný disk nebo namapované síťové umístění úložiště
- Azure BLOB nebo služby Azure Storage (vyžaduje předplatné Azure)
- Azure SQL Server
- Microsoft SQL Server


**Jaké druhy tahání dat, přípravy a transformace jsou k dispozici?**

Aplikace Workbench ve veřejné verzi preview podporuje "Odvodit sloupec podle příkladu", "Rozdělit sloupec podle příkladu", "Text Clustering", "Zpracování chybějící hodnoty" a mnohé další. Aplikace Workbench také podporuje převod typu dat, agregace dat (počet, průměr, odchylka atd.) a komplexních datových spojení. Úplný seznam podporovaných schopností projděte si naši dokumentaci produktu. 

**Existují nějaká omezení velikosti dat vynucuje Azure Machine Learning Workbench, služby experimentování ve službě nebo Správa modelů ve službě?**

Ne, tyto nové služby nějaká omezení data neukládají. Existuje ale omezení zavedených v prostředí, ve kterém provádíte přípravu dat, trénování modelu, služby experimentování ve službě nebo nasazení. Například pokud se zaměřujete na místních prostředí pro školení, které se uplatňuje limit vycházející dostupné místo v pevném disku. Pokud se zaměřujete na HDInsight, případně se uplatňuje limit vycházející jakékoli přidružené velikosti nebo výpočetní omezení. 

## <a name="algorithms-and-libraries"></a>Algoritmy a knihovny

**Jaké algoritmy jsou podporovány v aplikaci Azure Machine Learning Workbench?**

Naše produkty ve verzi preview a služeb zahrnují nejlepší open source komunity. Podporujeme širokou škálu algoritmy a knihoven, včetně TensorFlow, scikit-informace, Apache Spark a Microsoft Cognitive Toolkit. Také balíčky Azure Machine Learning Workbench [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) balíčku.

**Jak Azure Machine Learning souvisí Microsoft Cognitive Toolkit?**

[Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) je jednou z mnoha architektur podporuje našich nových nástrojů a služeb. Sada Cognitive Toolkit je jednotný nástrojů pro obsáhlého learningu a umožňuje využívat a kombinovat Oblíbené modelů strojového učení, včetně kanál dopředné hluboké Neuronové sítě, Konvoluční sítě pořadí pořadí a Rekurentní sítě. Další informace o Microsoft Cognitive Toolkit, navštivte naši [dokumentaci k produktu](https://docs.microsoft.com/cognitive-toolkit/). 
