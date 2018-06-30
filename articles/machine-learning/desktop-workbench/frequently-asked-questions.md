---
title: Azure Machine Learning 2017 Preview – nejčastější dotazy | Microsoft Docs
description: Tento článek obsahuje nejčastější dotazy a odpovědi pro Azure Machine Learning funkce verze preview
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
ms.openlocfilehash: 43da51e389fa899d912a60bfe90e7b4e39390382
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115929"
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Nejčastější dotazy k Azure Machine Learning

Azure Machine Learning je plně spravovaná služba Azure, která umožňuje vytváření, testování, správu a nasazení strojového učení a AI modelů. Našich služeb a ke stažení aplikace nabízejí přístup první kód, který využívá cloud, místní a hraniční síti, aby poskytují vlaku, nasazovat a spravovat a monitorovat modelů pomocí možnosti, rychlost a pružnost. Alternativně Azure Machine Learning Studio nabízí založené na prohlížeči visual přetažení myší pro tvorbu prostředí a kde není vyžadováno žádné kódování. 

## <a name="general-product-questions"></a>Obecné otázky

**Jaké jinými službami Azure jsou potřeba?**

Azure Blob Storage a kontejner registru Azure používají Azure Machine Learning. Kromě toho bude muset zřídit výpočetní prostředky, jako je například cluster virtuálních počítačů vědecké účely dat nebo HDInsight. Výpočty a hostování vyžadují se i při nasazování webových služeb, jako například [Azure Container Service](https://docs.microsoft.com/azure/aks).

**Jak Azure Machine Learning vztahují k Microsoft Machine Learning Services v SQL serveru 2017?**   

V SQL serveru 2017 služby Machine Learning je rozšiřitelný, škálovatelná platforma pro integraci úkoly strojového učení do databáze pracovních postupů. Je vzájemně přizpůsobit pro scénáře, kdy je potřeba, například kde přesun dat je nákladné nebo untenable místní řešení. Naproti tomu Cloudová nebo hybridní úlohy jsou skvělý přizpůsobit pro naše nové služby Azure. 

**Podporujete Python a R? Co o jinými programovací jazyky jako jazyk C++**

Momentálně podporujeme aplikace Python jenom. Jsme práce na integraci R a očekávají, že ho mít dostupný brzy k dispozici. 

**Jak Azure Machine Learning souvisí s Microsoft Machine Learning pro Spark?**

MMLSpark poskytuje přímý učení a datové vědy nástroje pro Apache Spark, s důrazem na produktivitu, usnadňují experimentování a stavu techniky algoritmů. MMLSpark nabízí integraci kanály Spark Machine Learning s Microsoft kognitivní Toolkit a OpenCV. Můžete vytvořit výkonné a vysoce škálovatelného modely prediktivní a analytické pro data bitové kopie a text. MMLSpark je k dispozici v části licenci open source a je součástí AML Workbench jako sada použití modelů a algoritmy. Další informace o MMLSpark najdete na naší dokumentaci produktu. 

**Jaké verze Spark podporuje nové nástroje a služby?**

Workbench aktuálně obsahuje a podporuje MMLSpark verze 0,8, který je kompatibilní s Apache Spark 2.1. Máte také možnost použít bitovou kopii Docker grafický procesor s podporou systému MMLSpark 0,8 na virtuální počítače s Linuxem.

## <a name="experimentation-service"></a>Služba experimentování

**Co je služba experimenty Azure Machine Learning?**

Služba experimentování je spravovaná služba Azure, která přebírá machine learning experimentování na další úroveň. Experimenty se dají vytvářet místně nebo v cloudu. Rychlé prototypu na plochu, pak škálování virtuálních počítačů nebo clustery Spark. Virtuální počítače Azure s nejnovější technologie GPU umožňují vykonávat hloubkové učení rychle a efektivně. Těsná integrace s Gitem jsme také zahrnuli, můžete snadno připojit k existující pracovní postupy pro kód pro sledování, konfigurace a spolupráci. 

**Jak můžu odečte pro službu experimentování?**

První dva uživatele přidružené služby Azure Machine Learning experimenty jsou volné. Další uživatelé se bude účtovat poplatek ve verzi Public Preview výši $50 za měsíc. Další informace o cenách a fakturace najdete na naší stránce cena.

**I odečte závislosti na tom, kolik experimenty spustit?**

Ne, služba experimentování povolí tolik experimenty, jako je třeba a poplatky pouze podle počtu uživatelů. Výpočetní prostředky Experimentování se účtují samostatně. Doporučujeme provádět více experimenty vyhledávat nejlépe vyhovují model pro vaše řešení.   

**Toho, jaké konkrétní typy prostředků výpočetního prostředí a úložiště můžete použít?**

Službu experimentování může spustit experimentů na místních počítačích (přímého nebo na základě Docker), [virtuální počítače Azure](https://azure.microsoft.com/services/virtual-machines/), a [HDInsight](https://azure.microsoft.com/services/hdinsight/). Služba také přistupuje [Azure Storage](https://azure.microsoft.com/services/storage/) účtu pro ukládání výstupy spuštění a může využívat [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) účet pro správu verzí a úložiště Git. Všimněte si, že vám budou dostávat nezávisle pro žádné spotřebované výpočetní a úložné prostředky, na základě jednotlivých ceny.  


## <a name="model-management"></a>Správa modelů

**Co je Azure Machine Learning modelu Management?**

Azure Machine Learning modelu správy je spravovaná služba Azure, která umožňuje data vědců a vývojářů ops týmy a spolehlivě nasazovat prediktivní modely do širokou škálu prostředí. Úložiště Git a Docker kontejnery poskytují sledovatelnost a opakovatelnosti. Modely se dá nasadit spolehlivě v cloudu, místní nebo okraj. Jakmile v produkčním prostředí, můžete spravovat výkonu modelu, pak proaktivně přeučování Pokud snižuje výkon. Modely na místních počítačích, můžete nasadit na [virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/), Spark na [HDInsight](https://azure.microsoft.com/services/hdinsight/) nebo orchestrovat Kubernetes [Azure Container Service](https://azure.microsoft.com/services/container-service/) clustery.  

**Co je "model"?**

Model je výstup experimentování, spuštění, který se použije pro model správy. Model, který je zaregistrován v hostitelských účet se počítá proti plánu, včetně modely aktualizovat přes iterace retraining nebo verzi.

**Co je "spravované model?"**

Model je výstupem procesu trénování a jedná se o aplikování algoritmu machine learningu na trénovací data. Model správy umožňuje nasazovat modely jako webové služby, spravovat různé verze vaší modelů a monitorovat jejich výkonu a metriky. "Spravovaný" modely jsou zaregistrovaní s účtem Azure Machine Learning modelu správy. Představte si například scénář, kdy se pokoušíte o prognózu prodeje. Během fáze experimentování generování mnoho modelů pomocí různých datových sad nebo algoritmy. Vygenerování čtyři modely s různými přesnostmi ale zaregistrujete pouze modelu s nejvyšší přesnost. Model, který je zaregistrován se změní na první spravované modelu.
 
**Co je "nasazení?"**

Model správy umožňuje nasadit modely jako kontejnery zabalené webové služby v Azure. Tyto webové služby lze vyvolat pomocí rozhraní REST API. Každý webové služby se počítá jako jedno nasazení, a celkový počet aktivní nasazení, se počítají směrem k plánu. Pomocí prognózy například při nasazení vaší nejvýkonnější modelu prodeje, váš plán se zvýší o jedno nasazení. Pokud pak přeučování a nasadíte jinou verzi, máte dvě nasazení. Pokud zjistíte, že novější modelu je lepší a odstranit původní spočítat nasazení se odečte o jednu.  

**Jaké konkrétní výpočetní prostředky jsou k dispozici pro moje nasazení?** 

Model správy můžete spustit vaše nasazení, jako kontejnery Docker zaregistrované [Azure Container Service](https://azure.microsoft.com/services/container-service/), jako [virtuální počítače Azure](https://azure.microsoft.com/services/virtual-machines/), nebo na místních počítačích. Za chvíli se přidají další nasazení cíle. Všimněte si, že vám budou dostávat nezávisle pro všechny spotřebované výpočetní prostředky, na základě jednotlivých ceny.     

**K nasazení modely vytvořené pomocí jiných nástrojích než službu experimentování, používat Model správy Azure Machine Learning?**

Bude docházet k dosažení co nejlepších výsledků při nasazování modelů vytvořených pomocí služby experimenty. Můžete však nasadit modely vytvořené pomocí jiných rozhraní a nástroje. Podporujeme mnoha včetně MMLSpark, TensorFlow, Microsoft kognitivní Toolkit, scikit-další Keras atd. 

**Můžete použít vlastní prostředky Azure?**

Ano, služba experimentování a modelu správy práce ve spojení s více úložišti dat Azure, výpočetní úlohy a dalších služeb. Najdete na naší technické dokumentace pro další informace o požadované služby Azure.

**Podpora místní a cloudové scénáře nasazení?**

Ano. Jsme podporu místní a cloudové scénáře nasazení prostřednictvím Docker kontejnery. Zahrnout místní provádění cílů: nasazení Docker jeden uzel, [Microsoft SQL Server službou ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop nebo Spark. Také podporujeme cloudové nasazení prostřednictvím Docker, včetně: nasazení v clusteru prostřednictvím Azure Container Service a Kubernetes, HDInsight nebo Spark clusterů. Okraj scénáře jsou podporované pomocí Docker kontejnery a Azure IOT okraj. 

**Můžete spustit Docker bitovou kopii, která byla vytvořena v jiném hostiteli pomocí rozhraní příkazového řádku Azure Machine Learning?**

Ano. Můžete vytvořit bitovou kopii jako webovou službu na každém hostiteli docker tak dlouho, dokud hostitel má dostatečná výpočetní prostředky pro hostování docker bitové kopie.

**Podporujete přeučení modelů nasazené?**

Ano, můžete nasadit několik verzí stejného modelu. Model správy bude podporovat aktualizace služby pro všechny aktualizované modely a bitové kopie.

## <a name="workbench"></a>Workbench

**Co je Azure Machine Learning Workbench?**

Azure Machine Learning Workbench je doprovodné aplikace vytvořené pro odborníky v oblasti datových vědců. K dispozici pro systém Windows a Mac, Machine Learning Workbench poskytuje přehled, Správa a řízení pro machine learning řešení. Nástroje Machine Learning Workbench patří přístup k rozhraní AI špičkových od společnosti Microsoft a komunitou s otevřeným zdrojem. Jsme zahrnuli nejoblíbenější vědecké účely sadách dat včetně TensorFlow, Microsoft kognitivní Toolkit, Spark ML, scikit-Další informace a další. Také jsme jste povolili integraci s vědecké zpracování dat oblíbených integrovaného vývojového prostředí například poznámkové bloky Jupyter, PyCharm a Visual Studio Code. Nástroje Machine Learning Workbench má integrovanou data přípravy schopnosti rychle ukázkové, pochopit a připravit data, zda strukturovaných nebo nestrukturovaných. Naše nová data nástroj pro přípravu, názvem [PROSE](https://microsoft.github.io/prose/), je založená na špičkové technologie z Microsoft Research.  

**Je Workbench IDE?**

Ne. Nástroje Machine Learning Workbench jsou určeny jako doprovodné do oblíbených integrovaného vývojového prostředí, například Jupyter Notebooks, Visual Studio Code a PyCharm, ale není plně funkční IDE. Nástroje Workbench Machine Learning nabízí některé základní text možností pro úpravy, ale ladění, intellisense a dalších nejčastěji používaných možností IDE nejsou podporovány. Doporučujeme používat vaše oblíbená rozhraní IDE pro vývoj kódu, úprav a ladění. Také je možné pokusit [kódu nástroje sady Visual Studio pro AI](https://www.visualstudio.com/downloads/ai-tools-vscode).

**Je k dispozici zdarma pro použití nástroje Workbench Azure Machine Learning?**

Ne. Azure Machine Learning Workbench je bezplatná aplikace. Můžete ji stáhnout na tolik počítačů a pro tolik uživatelů, kolik potřebujete. K používání aplikace Azure Machine Learning Workbench potřebujete účet Experimentování. .  

**Podporuje možnosti příkazového řádku?**

Ano, Azure Machine Learning nabízí úplné rozhraní CLI. Rozhraní příkazového řádku Machine Learning je nainstalována ve výchozím nastavení s Azure Machine Learning Workbench. To je k dispozici jako součást vědecké zpracování dat Linux virtuálního počítače na Azure a budete integrovat do [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**Můžete použít Jupyter Notebooks s Workbench?**

Ano! Poznámkové bloky Jupyter v Workbench s Workbench jako hostitelskou aplikaci klienta, můžete spustit stejně, jako by použít prohlížeč jako klient. 

**Které Poznámkový blok Jupyter jádra jsou podporovány?**

Aktuální verze Jupyter součástí Workbench spouští Python 3 jádra a další jádra pro každý soubor "runconfig" ve složce aml_config. Podporované konfigurace zahrnují:
- Místní Python
- Python v místním nebo vzdáleném Docker

## <a name="data-formats-and-capabilities"></a>Formáty dat a možnosti

**Které formáty souborů jsou aktuálně podporovány pro přijímání dat v Workbench?**

Nástroje pro přípravu dat v Workbench aktuálně podporují přijímání z následujících formátů: 
- Soubory s oddělovači například CSV, TSV atd.  
- Soubory s pevnou šířkou
- Soubory ve formátu prostého textu
- Aplikace Excel (XLS nebo xlsx)
- Soubory JSON
- Parquet soubory 
- Vlastní soubory (skripty), pokud vaše řešení vyžaduje přijímat data z dalších zdrojů, kód Python můžete použít k... 

**Která umístění úložiště dat se aktuálně podporuje?**

Pro verzi public preview podporuje Workbench přijímání dat od: 
- Místní pevný disk nebo namapované síťové umístění úložiště
- Azure BLOB nebo Azure Storage (vyžaduje předplatné služby Azure)
- Azure SQL Server
- Microsoft SQL Server


**Jaké druhy dat wrangling, přípravy a transformace jsou k dispozici?**

Pro verzi public preview podporuje Workbench "Odvozena sloupec podle příkladu", "Sloupec rozdělení příklad", "Text Clustering", "Zpracování chybějící hodnoty" a mnohé další.  Workbench také podporuje převod typů dat, agregace dat (počet, střední, odchylka atd.) a komplexní datová spojení. Úplný seznam podporovaných schopností najdete na adrese dokumentaci produktu. 

**Existují nějaká omezení dat vynuceny Azure Machine Learning Workbench, experimentování nebo Model správy?**

Ne, nových služeb neukládají žádné omezení data. Existují však omezení zavedených v prostředí, ve kterém provádíte Příprava dat, školení modelu, experimentování nebo nasazení. Například pokud cílíte na místním prostředí pro školení, je omezen pomocí dostupné místo v pevném disku. Pokud cílíte na HDInsight, nebo jsou omezena jakékoli přidružené velikosti nebo výpočetní omezení. 

## <a name="algorithms-and-libraries"></a>Algoritmy a knihovny

**Jaké algoritmy jsou podporovány v nástroji Azure Machine Learning Workbench?**

Naše náhled produktů a služeb zahrnují nejvhodnější komunitou s otevřeným zdrojem. Podporujeme širokou škálu algoritmy a knihovny, včetně TensorFlow, scikit – zjistěte, Apache Spark a kognitivní nástrojů Microsoft. Také balíčky Azure Machine Learning Workbench [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) balíčku.

**Jak Azure Machine Learning vztahují k kognitivní nástrojů Microsoft?**

[Kognitivní nástrojů Microsoft](https://www.microsoft.com/cognitive-toolkit/) je jedním z mnoha rozhraní podporovaných funkcí našeho nových nástrojů a služeb. Kognitivní Toolkit je sada jednotná nástrojů přímým učení vám umožní využívat a kombinovat oblíbených modelů strojového učení informačního kanálu dopředného hluboké Neuronové sítě, Convolutional sítěmi, včetně pořadí pořadí a opakující sítě. Další informace o Microsoft kognitivní Toolkit, navštivte naše [dokumentaci k produktu](https://docs.microsoft.com/cognitive-toolkit/). 