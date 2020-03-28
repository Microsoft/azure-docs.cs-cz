---
title: Vytvoření automatizovaných klasifikačních modelů ML
titleSuffix: Azure Machine Learning
description: Zjistěte, jak trénovat & nasazení klasifikačních modelů pomocí automatizovaného rozhraní Machine Learning (automated ML) Azure Machine Learning.Learn how to train & deploy classification models with Azure Machine Learning's automated machine learning (automated ML) interface.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 03/04/2020
ms.openlocfilehash: b5a335a3f215ad5883b1b223245ca9d3f9967c3b
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366520"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Kurz: Vytvoření klasifikačního modelu s automatizovanou ml v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto kurzu se dozvíte, jak vytvořit základní klasifikační model bez psaní jediného řádku kódu pomocí automatizovaného rozhraní machine learningu Azure Machine Learning. Tento klasifikační model předpovídá, zda se klient přihlásí k upsání termínovaného vkladu u finanční instituce.

Díky automatizovanému strojovému učení můžete automatizovat časově náročné úkoly. Automatizované strojové učení rychle itetizuje mnoho kombinací algoritmů a hyperparametrů, které vám pomohou najít nejlepší model založený na metriku úspěchu podle vašeho výběru.

V tomto kurzu se dozvíte, jak provést následující úkoly:

> [!div class="checklist"]
> * Vytvořte pracovní prostor Azure Machine Learning.
> * Jak spouštět experimenty automatizovaného strojového učení.
> * Zobrazení podrobností o experimentu.
> * Nasazení modelu

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Stáhněte si datový soubor [**bankmarketing_train.csv.**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) Sloupec **y** označuje, zda se zákazník přihlásil k odběru pevně stanoveného vkladu, který je později identifikován jako cílový sloupec pro předpovědi v tomto kurzu. 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor Azure Machine Learning je základní prostředek v cloudu, který používáte k experimentování, trénování a nasazování modelů strojového učení. Vazby předplatného Azure a skupiny prostředků na snadno spotřebované objektve službě. 

Pracovní prostor vytvoříte prostřednictvím portálu Azure, webové konzoly pro správu prostředků Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Poznamenejte si pracovní **prostor** a **předplatné**. Budete je potřebovat, abyste zajistili vytvoření experimentu na správném místě. 

## <a name="create-and-run-the-experiment"></a>Vytvoření a spuštění experimentu

Můžete dokončit následující experiment set-up a spustit https://ml.azure.comkroky prostřednictvím Azure Machine learning na , konsolidované webové rozhraní, které zahrnuje nástroje strojového učení k provádění scénářů datové vědy pro odborníky datové vědy všech úrovní chrupu. Toto rozhraní není v prohlížečích Internet Explorer podporováno.

1. Přihlaste se k [Azure Machine Learning](https://ml.azure.com).

1. Vyberte předplatné a pracovní prostor, který jste vytvořili.

1. Vyberte **Možnost Začínáme**.

1. V levém podokně vyberte v části **Autor** možnost **Automatizovaná ml.**

   Vzhledem k tomu, že se jedná o první automatizovaný experiment ML, zobrazí se prázdný seznam a odkazy na dokumentaci.

   ![Stránka Začínáme](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Vyberte **nový automatizovaný běh ML**. 

1. Vytvořte novou datovou sadu výběrem **Z místních souborů** z rozevíracího políčka **+Vytvořit datovou sadu.** 

    1. Ve formuláři **Základní informace** zadejte název datové sady a poskytněte volitelný popis. Automatizované rozhraní ML v současné době podporuje pouze TabularDatasets, takže typ datové sady by měl být výchozí *pro Tabulkární*.

    1. V levém dolním rohu vyberete **další.**

    1. Ve formuláři **Datastore a výběru souborů** vyberte výchozí úložiště dat, které bylo automaticky nastaveno během vytváření pracovního prostoru, **workspaceblobstore (Azure Blob Storage).** Tady nahrajete datový soubor, abyste ho zpřístupnili do pracovního prostoru.

    1. Vyberte **Procházet**.
    
    1. Zvolte soubor **bankmarketing_train.csv** v místním počítači. Jedná se o soubor, který jste stáhli jako [předpoklad](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Pojmenujte datovou sadu jedinečným názvem a poskytněte volitelný popis. 

    1. **Vlevém** na levé dolní části vyberte, chcete-li jej odeslat do výchozího kontejneru, který byl automaticky nastaven během vytváření pracovního prostoru.  
    
       Po dokončení nahrávání je formulář Nastavení a náhled předem vyplněn na základě typu souboru. 
       
    1. Ověřte, zda je formulář **Nastavení a náhled** naplněn následujícím způsobem, a vyberte možnost **Další**.
        
        Pole|Popis| Hodnota pro výuku
        ---|---|---
        Formát souboru|Definuje rozložení a typ dat uložených v souboru.| Oddělený
        Oddělovač|Jeden nebo více znaků pro určení&nbsp; hranice mezi samostatnými nezávislými oblastmi ve formátu prostého textu nebo jiných datových proudech. |Čárka
        Kódování|Určuje, jaký bit do tabulky schématu znaků použít ke čtení datové sady.| UTF-8
        Záhlaví sloupců| Označuje, jak bude zpracováno záhlaví datové sady, pokud existuje.| Všechny soubory mají stejné hlavičky
        Přeskočit řádky | Označuje, kolik řádků, pokud existuje, je v datové sadě přeskočeno.| Žádný

    1. Formulář **Schéma** umožňuje další konfiguraci dat pro tento experiment. V tomto příkladu vyberte přepínač pro **funkci day_of_week,** aby nebyl zahrnut pro tento experiment. Vyberte **další**.

        ![Konfigurace karty Náhled](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. Ve formuláři **Potvrdit podrobnosti** ověřte, zda se informace shodují s tím, co bylo dříve vyplněno ve formulářích **Základní informace** a Nastavení **a Náhled.**
    1. Vyberte **Vytvořit,** chcete-li dokončit vytvoření datové sady.
    1. Jakmile se datová sada zobrazí v seznamu, vyberte ji.
    1. Zkontrolujte **náhled dat** a ujistěte se, že jste nezahrnuli **day_of_week** pak vyberte **OK**.

    1. Vyberte **další**.

1. Formulář **Konfigurovat spuštění** naplňte následujícím způsobem:
    1. Zadejte tento název experimentu:`my-1st-automl-experiment`

    1. Vyberte **y** jako cílový sloupec, co chcete předpovědět. Tento sloupec označuje, zda se klient přihlásil k termínovanému vkladu či nikoli.
    1. Vyberte **Vytvořit nový výpočetní výkon** a nakonfigurujte svůj výpočetní cíl. Výpočetní cíl je místní nebo cloudové prostředí prostředků, které se používá ke spuštění školicího skriptu nebo k hostování nasazení služby. Pro tento experiment používáme cloudové výpočetní prostředky. 

        Pole | Popis | Hodnota pro výuku
        ----|---|---
        Název výpočetního výkonu |Jedinečný název, který identifikuje váš výpočetní kontext.|automl-compute
        Velikost&nbsp;&nbsp;virtuálního počítače| Vyberte velikost virtuálního počítače pro vaše výpočty.|Standard_DS12_V2
        Min / Max uzly (v pokročilých nastaveních)| Chcete-li daty profilu, musíte zadat 1 nebo více uzlů.|Min uzly: 1<br>Maximální počet uzlů: 6
  
        1. Vyberte **Vytvořit,** chcete-li získat výpočetní cíl. 

            **To trvá několik minut na dokončení.** 

        1. Po vytvoření vyberte nový výpočetní cíl z rozevíracího seznamu.

    1. Vyberte **další**.

1. Ve formuláři **Typ úkolu a nastavení** vyberte **Klasifikace** jako typ úlohy strojového učení.

    1. Vyberte **Zobrazit další nastavení konfigurace** a vyplňte pole následujícím způsobem. Tato nastavení mají lépe řídit úlohu školení. V opačném případě jsou výchozí hodnoty použity na základě výběru experimentu a dat.

        >[!NOTE]
        > V tomto kurzu nenastavíte metrické skóre nebo maximální hodnoty jader na prahovou hodnotu iterací. Ani nebudete blokovat algoritmy z testování.
   
        Další&nbsp;konfigurace|Popis|Hodnota&nbsp;&nbsp;pro výuku
        ------|---------|---
        Primární metrika| Metrika vyhodnocení, podle které bude algoritmus strojového učení měřen.|AUC_weighted
        Automatická featurizace| Povolí předběžné zpracování. To zahrnuje automatické čištění dat, přípravu a transformaci pro generování syntetických funkcí.| Povolení
        Blokované algoritmy | Algoritmy, které chcete vyloučit z tréninkové úlohy| Žádný
        Kritérium ukončení| Pokud je splněna kritéria, je úloha školení zastavena. |Doba&nbsp;&nbsp;tréninku (hodiny): 1 <br> Prahová&nbsp;hodnota metriky:&nbsp;Žádná
        Ověřování | Zvolte typ křížového ověření a počet testů.|Typ ověření:<br>&nbsp;k-fold&nbsp;křížové ověření <br> <br> Počet ověření: 2
        Souběžnost| Maximální počet paralelních iterací provedených na iteraci| Maximální&nbsp;počet&nbsp;souběžných iterací: 5
        
        Vyberte **Uložit**.

1. Chcete-li experiment spustit, vyberte **možnost Dokončit.** Otevře se obrazovka **Podrobnosti běhu** se **stavem Spustit** v horní části při zahájení přípravy experimentu.

>[!IMPORTANT]
> Příprava trvá **10-15 minut,** než se připraví experiment.
> Po spuštění trvá **2-3 minuty více pro každou iteraci**.  
> Chcete-li pravidelně sledovat stav spuštění v průběhu experimentu, pravidelně vyberte možnost **Aktualizovat.**
>
> Ve výrobě byste pravděpodobně na chvíli odešel. Ale pro účely tohoto kurzu doporučujeme začít zkoumat testované algoritmy na kartě **Modely,** když se dokončí, zatímco ostatní jsou stále spuštěny. 

##  <a name="explore-models"></a>Prozkoumejte modely

Přejděte na kartu **Modely** a podívejte se na testované algoritmy (modely). Ve výchozím nastavení jsou modely seřazeny podle metriky skóre, jak je dokončí. Pro účely tohoto kurzu model, který skóre nejvyšší na základě zvolené **metriky AUC_weighted** je v horní části seznamu.

Zatímco čekáte na dokončení všech modelů experimentu, vyberte **název algoritmu** dokončeného modelu a prozkoumejte podrobnosti o jeho výkonu. 

Následující procházet **podrobnosti modelu** a **vizualizace** karty pro zobrazení vlastností vybraného modelu, metriky a grafy výkonu. 

![Spustit podrobnosti iterace](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Nasazení nejlepšího modelu

Automatizované rozhraní strojového učení umožňuje nasadit nejlepší model jako webovou službu v několika krocích. Nasazení je integrace modelu, takže může předpovídat nová data a identifikovat potenciální oblasti příležitostí. 

Pro tento experiment nasazení do webové služby znamená, že finanční instituce má nyní iterativní a škálovatelné webové řešení pro identifikaci potenciálních zákazníků s pevným termínem vkladu. 

Po dokončení běhu přejděte zpět na stránku **Spustit podrobnosti** a vyberte kartu **Modely.**

V tomto kontextu experimentu **VotingEnsemble** je považován za nejlepší model, na základě **metriky AUC_weighted.**  Tento model nasazujeme, ale upozorňujeme, že dokončení nasazení trvá přibližně 20 minut. Proces nasazení zahrnuje několik kroků, včetně registrace modelu, generování prostředků a jejich konfigurace pro webovou službu.

1. V levém dolním rohu vyberte tlačítko **Nasadit nejlepší model.**

1. Naplnit **podokno nasazení modelu** takto:

    Pole| Hodnota
    ----|----
    Název nasazení| moje-automl-deploy
    Popis nasazení| Moje první nasazení experimentu automatizovaného strojového učení
    Typ výpočtu | Výběr výpočetní instance Azure (ACI)
    Povolení ověřování| Zakázat. 
    Použití vlastních nasazení| Zakázat. Umožňuje automatické generování výchozího souboru ovladače (bodovacího skriptu) a souboru prostředí. 
    
    V tomto příkladu použijeme výchozí hodnoty uvedené v nabídce *Upřesnit.* 

1. Vyberte **Nasadit**.  

    Zelená zpráva o úspěchu se zobrazí v horní části obrazovky **Spustit** a v podokně **Doporučený model** se v části **Stav nasazení**zobrazí stavová zpráva . **Chcete-li** pravidelně kontrolovat stav nasazení, vyberte pravidelně aktualizovat.
    
Nyní máte provozní webové služby pro generování předpovědi. 

Pokračujte [**k dalším krokům,**](#next-steps) kde se dozvíte víc o tom, jak využít novou webovou službu, a otestujte své předpovědi pomocí integrované podpory Azure Machine Learning u Power BI.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než soubory dat a experimentů, takže jejich uložení bude stát více. Odstraňte pouze soubory nasazení, abyste minimalizovali náklady na svůj účet, nebo pokud chcete zachovat pracovní prostor a soubory experimentů. V opačném případě odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-the-deployment-instance"></a>Odstranění instance nasazení

Odstraňte pouze instanci nasazení z\/Azure Machine Learning na https: /ml.azure.com/, pokud chcete zachovat skupinu prostředků a pracovní prostor pro další kurzy a průzkum. 

1. Přejděte na [Azure Machine Learning](https://ml.azure.com/). Přejděte do pracovního prostoru a vlevo v podokně **Datové zdroje** vyberte **Koncové body**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **Pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto automatizovaném kurzu strojového učení jste k vytvoření a nasazení klasifikačního modelu použili automatizované rozhraní ML Azure Machine Learning. Další informace a další kroky naleznete v těchto článcích:

> [!div class="nextstepaction"]
> [Využívání webové služby](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).
+ Další informace o metrikách klasifikace a grafech najdete v článku [Vysvětlení výsledků automatizovaného strojového učení.+](how-to-understand-automated-ml.md#classification) Další informace o [featurization](how-to-use-automated-ml-for-ml-models.md#featurization).
+ Další informace o [profilování dat](how-to-use-automated-ml-for-ml-models.md#profile).


>[!NOTE]
> Tato datová sada bankovního marketingu je k dispozici pod [licencí Creative Commons (CCO: Public Domain).](https://creativecommons.org/publicdomain/zero/1.0/) Veškerá práva k individuálnímu obsahu databáze jsou licencována pod [licencí Database Contents License](https://creativecommons.org/publicdomain/zero/1.0/) a dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada byla původně k dispozici v [databázi Strojového učení UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. „A Data-Driven Approach to Predict the Success of Bank Telemarketing“ (Předpovídání úspěchu bankovního telemarketingu na základě dat). Decision Support Systems, Elsevier, 62:22-31, červen 2014
