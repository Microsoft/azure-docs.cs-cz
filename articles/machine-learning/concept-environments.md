---
title: Informace o prostředích Azure Machine Learning
titleSuffix: Azure Machine Learning
description: V tomto článku se seznámíte s výhodami strojového učení, které umožňují reprodukovatelná, Auditovaná a přenosné definice závislostí strojového učení napříč různými výpočetními cíli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 07/08/2020
ms.openlocfilehash: a37a09d971ee80d05f9e028ece1adc7962c0c1a0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905714"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co jsou Azure Machine Learning prostředí?


Azure Machine Learning prostředí jsou zapouzdření prostředí, ve kterém se vaše školení ve službě Machine Learning stane. Určují balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů. Určují také časy spuštění (Python, Spark nebo Docker). Prostředí jsou spravovaná a entitami se správou verzí v rámci vašeho Machine Learning pracovního prostoru, které umožňují reprodukovatelné pracovní postupy, které lze auditovat a přenosné strojové učení napříč různými výpočetními cíli.

`Environment`Objekt na místním výpočetním prostředí můžete použít k těmto akcím:
* Vytvořte školicí skript.
* Používejte stejné prostředí v Azure Machine Learning COMPUTE pro modelové školení ve velkém měřítku.
* Nasaďte model se stejným prostředím.
* Přečtěte si prostředí, ve kterém byl existující model vyškolený.

Následující diagram znázorňuje, jak můžete použít jeden `Environment` objekt jak v konfiguraci spuštění (pro školení), tak v konfiguraci nasazení a nasazení (pro nasazení webových služeb).

![Diagram prostředí v pracovním postupu Machine Learning](./media/concept-environments/ml-environment.png)

Prostředí, cíl výpočtů a školicí skript tvoří konfiguraci spuštění: úplnou specifikaci školicího běhu.

## <a name="types-of-environments"></a>Typy prostředí

Prostředí se můžou v podstatě rozdělit do tří *kategorií: řízená*, *uživatelsky spravovaná*a *spravovaná systémem*.

Dodaná prostředí jsou poskytována Azure Machine Learning a jsou ve výchozím nastavení k dispozici ve vašem pracovním prostoru. Určené k použití jako je, obsahují kolekce balíčků a nastavení Pythonu, které vám pomůžou začít s různými architekturami strojového učení. Tato předem vytvořená prostředí také umožňují rychlejší nasazení. Úplný seznam najdete v článku o zobrazených [prostředích](resource-curated-environments.md).

V prostředích spravovaných uživatelem zodpovídáte za nastavení vašeho prostředí a instalaci každého balíčku, který váš školicí skript potřebuje na výpočetním cíli. Conda nekontroluje vaše prostředí ani neinstaluje cokoli za vás. Pokud definujete vlastní prostředí, musíte `azureml-defaults` vytvořit seznam s verzí `>= 1.0.45` jako závislostí PIP. Tento balíček obsahuje funkce, které jsou potřeba pro hostování modelu jako webové služby.

Prostředí spravovaná systémem se používají, když chcete, aby [conda](https://conda.io/docs/) spravovalo prostředí Pythonu a závislosti skriptů za vás. Nové prostředí conda je postavené na základě objektu závislosti conda. Služba Azure Machine Learning ve výchozím nastavení předpokládá tento typ prostředí, protože se jedná o užitečnost vzdálených výpočetních cílů, které nebudete konfigurovat ručně.

## <a name="create-and-manage-environments"></a>Vytváření a Správa prostředí

Prostředí můžete vytvořit pomocí:

* Definování nových `Environment` objektů, a to buď pomocí spravovaného prostředí, nebo definováním vlastních závislostí.
* Použití existujících `Environment` objektů z pracovního prostoru. Tento přístup umožňuje konzistenci a reprodukovatelnosti s vašimi závislostmi.
* Probíhá import z existující definice prostředí Anaconda.
* Použití rozhraní příkazového řádku Azure Machine Learning
* [Použití rozšíření VS Code](how-to-manage-resources-vscode.md#create-environment)

Konkrétní ukázky kódu najdete v části Vytvoření prostředí v tématu [Jak používat prostředí](how-to-use-environments.md#create-an-environment). Prostředí je taky možné snadno spravovat přes pracovní prostor. Zahrnují tyto funkce:

* Prostředí se při odeslání experimentu automaticky zaregistrují do vašeho pracovního prostoru. Můžou se taky registrovat ručně.
* Můžete načíst prostředí z pracovního prostoru, která se mají použít pro školení nebo nasazení, nebo upravit definici prostředí.
* Se správou verzí můžete zobrazit změny prostředí v průběhu času, což zajišťuje reprodukovatelnost.
* Image Docker můžete vytvářet automaticky z prostředí.

Ukázky kódu najdete v části "Správa prostředí" tématu [použití prostředí](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Sestavení, ukládání do mezipaměti a opakované použití prostředí

Služba Azure Machine Learning sestaví definice prostředí do prostředí Docker images a conda. Také ukládá do mezipaměti prostředí, aby je bylo možné znovu použít v následných školicích běhůch a nasazeních koncových bodů služby. Vzdálené spuštění školicího skriptu vyžaduje vytvoření image Docker, zatímco místní běh může přímo používat prostředí conda. 

### <a name="submitting-a-run-using-an-environment"></a>Odeslání běhu pomocí prostředí

Při prvním odeslání vzdáleného spuštění pomocí prostředí služba Azure Machine Learning vyvolá [úlohu sestavení ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) na Azure Container Registry (ACR), která je přidružena k pracovnímu prostoru. Vytvořená image Docker se pak uloží do mezipaměti v pracovním prostoru ACR. V prostředích Docker, která jsou ukládána do mezipaměti v globálním ACR, jsou uložená prostředí. Na začátku spuštění běhu se image načte z příslušného ACR do cílového výpočetního prostředí.

Pro místní spuštění se vytvoří Docker nebo prostředí conda na základě definice prostředí. Skripty se pak spustí na cílovém výpočetním prostředí – místní běhové prostředí nebo místní modul Docker.

### <a name="building-environments-as-docker-images"></a>Sestavování prostředí jako imagí Docker

Pokud definice prostředí v pracovním prostoru ACR ještě neexistuje, vytvoří se nový obrázek. Sestavení image se skládá ze dvou kroků:

 1. Stažení základní image a provedení všech kroků Docker
 2. Vytvoření prostředí conda podle závislostí conda zadaných v definici prostředí.

Druhý krok se vynechá, pokud zadáte [závislosti spravované uživatelem](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true). V tomto případě zodpovídáte za instalaci jakýchkoli balíčků Pythonu, zahrnutím do základní Image nebo zadáním vlastních kroků Docker v prvním kroku. Zodpovídáte také za určení správného umístění spustitelného souboru Pythonu. Je také možné použít [vlastní základní image Docker](how-to-deploy-custom-docker-image.md).

### <a name="image-caching-and-reuse"></a>Ukládání obrázků do mezipaměti a opakované použití

Použijete-li stejnou definici prostředí pro jiný běh, služba Azure Machine Learning znovu použije obrázek uložený v mezipaměti z pracovního prostoru ACR. 

Chcete-li zobrazit podrobnosti obrázku v mezipaměti, použijte metodu [Environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#&preserve-view=trueget-image-details-workspace-) .

Aby bylo možné určit, zda znovu použít bitovou kopii v mezipaměti nebo vytvořit novou, služba vypočítá [hodnotu hash](https://en.wikipedia.org/wiki/Hash_table) z definice prostředí a porovná ji s hodnotami hash stávajících prostředí. Hodnota hash je založena na:
 
 * Základní hodnota vlastnosti Image
 * Hodnota vlastnosti vlastní kroky Docker
 * Seznam balíčků Pythonu v definici conda
 * Seznam balíčků v definici Sparku 

Hodnota hash není závislá na názvu prostředí nebo verzi – Pokud přejmenujete prostředí nebo vytvoříte nové prostředí s přesnou vlastností a balíčky existující instance, hodnota hash zůstane stejná. Změny definice prostředí, jako je například přidání nebo odebrání balíčku Pythonu nebo změna verze balíčku, způsobují změnu hodnoty hash. Změna pořadí závislostí nebo kanálů v prostředí bude mít za následek nové prostředí, takže bude vyžadovat nové sestavení image. Je důležité si uvědomit, že jakákoli změna v takovém prostředí způsobí, že hodnota hash neověřuje a výsledkem je nové "nespravované" prostředí.

Vypočítaná hodnota hash je porovnána s hodnotami v pracovním prostoru a globálním ACR (nebo v cíli výpočetní služby pro místní spuštění). Pokud se shoduje, pak je načtena bitová kopie uložená v mezipaměti, jinak se spustí sestavení obrázku. Doba, po kterou má být vypsána bitová kopie uložená v mezipaměti, zahrnuje dobu stahování, zatímco doba vyžádat si nově vytvořenou bitovou kopii zahrnuje i čas sestavení i dobu stahování. 

Následující diagram znázorňuje tři definice prostředí. Dva z nich mají různé názvy a verze, ale stejné základní image a balíčky Pythonu. Ale mají stejnou hodnotu hash, takže odpovídají stejné imagi uložené v mezipaměti. Třetí prostředí má různé balíčky a verze Pythonu a proto odpovídá jinému obrázku v mezipaměti.

![Diagram ukládání do mezipaměti prostředí jako imagí Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Pokud vytvoříte prostředí s nepřipojenou závislostí balíčku, například ```numpy``` Toto prostředí bude používat verzi balíčku nainstalovanou _v době vytváření prostředí_. I jakékoli budoucí prostředí s vyhovující definicí bude dál používat starou verzi. 

Chcete-li aktualizovat balíček, zadejte číslo verze pro vynucení opětovného sestavení bitové kopie, například ```numpy==1.18.1``` . Budou nainstalovány nové závislosti, včetně vnořených, které mohou přerušit předchozí pracovní scénář. 

> [!WARNING]
>  Metoda [prostředí. Build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#&preserve-view=truebuild-workspace--image-build-compute-none-) znovu sestaví bitovou kopii v mezipaměti s případným vedlejším účinkem aktualizace nepřipnutých balíčků a zásadní reprodukovatelnosti pro všechny definice prostředí odpovídající tomuto obrázku v mezipaměti.

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet a používat prostředí](how-to-use-environments.md) v Azure Machine Learning.
* Viz Referenční dokumentace sady Python SDK pro [třídu prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py&preserve-view=true).
* Podívejte se na referenční dokumentaci sady R SDK pro [prostředí](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
