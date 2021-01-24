---
title: 'Příklad: vytvoření a nasazení vlastní dovednosti pomocí Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: Tento příklad ukazuje, jak použít Azure Machine Learning k sestavení a nasazení vlastní dovednosti pro kanál rozšíření AI pro Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745630"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Příklad: sestavení a nasazení vlastní dovednosti pomocí Azure Machine Learning 

V tomto příkladu použijete [datovou sadu přezkoumání hotelu](https://www.kaggle.com/datafiniti/hotel-reviews) (distribuovanou v rámci licence Creative-4,0 License [CC-NC-SA](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) a vytvoříte [vlastní dovednost](./cognitive-search-aml-skill.md) pomocí Azure Machine Learning k extrakci mínění založených na aspektech z revizí. To umožňuje, aby přiřazení pozitivních a záporných mínění v rámci stejné revize bylo správně přiřazené k identifikovaným entitám, jako jsou například zaměstnanci, místnosti, předsálí nebo fondy.

Pro výuku modelu mínění založeného na aspektech v Azure Machine Learning budete používat [úložiště recepty NLP](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa). Model se pak nasadí jako koncový bod v clusteru Azure Kubernetes. Po nasazení se koncový bod přidá do kanálu pro rozšíření jako AML dovednost pro použití službou Kognitivní hledání.

Jsou k dispozici dvě datové sady. Pokud chcete model naučit sami sebe, je vyžadován soubor hotel_reviews_1000.csv. Chcete přeskočit krok školení? Stáhněte hotel_reviews_100.csv.

> [!div class="checklist"]
> * Vytvoření instance služby Azure Kognitivní hledání
> * Vytvořte pracovní prostor Azure Machine Learning (vyhledávací služba a pracovní prostor by měly být ve stejném předplatném).
> * Výuka a nasazení modelu do clusteru Azure Kubernetes
> * Propojení kanálu rozšíření AI s nasazeným modelem
> * Ingestovat výstup z nasazeného modelu jako vlastní dovednosti

> [!IMPORTANT] 
> Tato dovednost je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V tuto chvíli není podporovaná žádná podpora sady .NET SDK.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Získejte [bezplatné předplatné](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Služba Kognitivní hledání](./search-get-started-arm.md)
* [Prostředek Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Účet služby Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Pracovní prostor služby Azure Machine Learning](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>Nastavení

* Naklonujte nebo Stáhněte obsah [ukázkového úložiště](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill).
* Extrahuje obsah, pokud je stahování souborem zip. Ujistěte se, že jsou soubory pro čtení i zápis.
* Při nastavování účtů a služeb Azure zkopírujte názvy a klíče do snadno dostupného textového souboru. Názvy a klíče budou přidány do první buňky v poznámkovém bloku, kde jsou definovány proměnné pro přístup ke službám Azure.
* Pokud nejste obeznámeni s Azure Machine Learning a jejími požadavky, budete si chtít tyto dokumenty před začátkem začít:
 * [Konfigurace vývojového prostředí pro Azure Machine Learning](../machine-learning/how-to-configure-environment.md)
 * [Vytváření a Správa pracovních prostorů Azure Machine Learning v Azure Portal](../machine-learning/how-to-manage-workspace.md)
 * Při konfiguraci vývojového prostředí pro Azure Machine Learning zvažte použití [cloudové výpočetní instance](../machine-learning/how-to-configure-environment.md#compute-instance) pro rychlost a usnadnění práce v části Začínáme.
* Nahrajte soubor DataSet do kontejneru v účtu úložiště. Větší soubor je nutný, pokud chcete provést krok školení v poznámkovém bloku. Pokud budete chtít přeskočit krok školení, je doporučeno zmenšit soubor.

## <a name="open-notebook-and-connect-to-azure-services"></a>Otevřete Poznámkový blok a připojte se ke službám Azure.

1. Zadejte všechny požadované informace pro proměnné, které umožní přístup ke službám Azure v první buňce a spustí buňku.
1. Po spuštění druhé buňky se potvrdí, že jste se připojili ke službě vyhledávání pro vaše předplatné.
1. Oddíly 1,1 – 1,5 vytvoří úložiště dat služby Search, dovednosti, index a indexer.

V tomto okamžiku se můžete rozhodnout, že budete chtít přeskočit kroky k vytvoření sady školicích dat a experimentovat Azure Machine Learning a přeskočit přímo k registraci dvou modelů, které jsou k dispozici ve složce modely úložiště GitHub. Pokud přeskočíte tyto kroky, přejděte v poznámkovém bloku do části 3,5 a zapište si skript bodování. Ušetří se čas; dokončení kroků stažení a nahrání dat může trvat až 30 minut.

## <a name="creating-and-training-the-models"></a>Vytváření a školení modelů

Oddíl 2 obsahuje šest buněk, které stáhnou soubor vkládání šetrnější z úložiště recepty NLP. Po stažení se soubor nahraje do úložiště dat Azure Machine Learning. Soubor. zip má asi 2G a při provádění těchto úkolů bude nějakou dobu trvat. Po nahrání se pak vyextrahují školicí data a teď jste připraveni přejít na oddíl 3.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>Vyškolení modelu mínění založeného na aspektech a nasazení koncového bodu

Část 3 poznámkového bloku bude vytvářet modely vytvořené v části 2, registrovat tyto modely a nasazovat je jako koncový bod v clusteru Azure Kubernetes. Pokud nejste obeznámeni s Azure Kubernetes, důrazně doporučujeme před pokusem o vytvoření clusteru odvození zkontrolovat následující články:

* [Přehled služby Azure Kubernetes](../aks/intro-kubernetes.md)
* [Základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)](../aks/concepts-clusters-workloads.md)
* [Kvóty, omezení velikosti virtuálních počítačů a dostupnost oblastí ve službě Azure Kubernetes Service (AKS)](../aks/quotas-skus-regions.md)

Vytvoření a nasazení clusteru odvození může trvat až 30 minut. Při testování webové služby před přechodem k posledním krokům se doporučuje aktualizovat dovednosti a spustit indexer.

## <a name="update-the-skillset"></a>Aktualizace dovednosti

Oddíl 4 v poznámkovém bloku obsahuje čtyři buňky, které aktualizují dovednosti a indexer. Alternativně můžete pomocí portálu vybrat a použít novou dovednost na dovednosti a pak spustit indexer a aktualizovat vyhledávací službu.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

Na portálu přejít na dovednosti a vyberte odkaz definice dovednosti (JSON). Portál zobrazí kód JSON vašeho dovednosti, který byl vytvořen v první buňce poznámkového bloku. Napravo od zobrazení je rozevírací nabídka, ve které můžete vybrat šablonu definice dovednosti. Vyberte šablonu Azure Machine Learning (AML). Zadejte název pracovního prostoru Azure ML a koncový bod modelu nasazeného do odvozeného clusteru. Šablona bude aktualizována pomocí identifikátoru URI a klíče koncového bodu.

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="Šablona definice dovednosti":::

Zkopírujte šablonu dovednosti z okna a vložte ji do definice dovednosti na levé straně. Upravte šablonu, aby poskytovala chybějící hodnoty pro:

* Název
* Popis
* Kontext
* název a zdroj vstupů
* název a cílový_název pro výstupy

Uložte dovednosti.

Po uložení dovednosti přejít na indexer a vybrat odkaz na definici indexeru (JSON). Portál zobrazí kód JSON indexeru, který byl vytvořen v první buňce poznámkového bloku. Mapování polí výstup bude nutné aktualizovat pomocí dalších mapování polí, aby bylo zajištěno, že indexer bude schopen pracovat a správně předat. Uložte změny a pak vyberte spustit. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontrola webového rozhraní API](./cognitive-search-custom-skill-web-api.md) 
>  pro vlastní dovednosti [Další informace o přidání vlastních dovedností do kanálu pro obohacení](./cognitive-search-custom-skill-interface.md)