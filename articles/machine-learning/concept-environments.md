---
title: O prostředích Azure Machine Learning
titleSuffix: Azure Machine Learning
description: V tomto článku se dozvíte výhody prostředí strojového učení, které umožňují reprodukovatelné, auditovatelné a přenosné definice závislostí strojového učení napříč různými výpočetními cíli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064189"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co jsou prostředí Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Prostředí Azure Machine Learning určují balíčky Pythonu, proměnné prostředí a nastavení softwaru kolem vašich školicích a vyhodnocovacích skriptů. Určují také časy spuštění (Python, Spark nebo Docker). Prostředí jsou spravované a verze entity v rámci pracovního prostoru Machine Learning, které umožňují reprodukovatelné, auditovatelné a přenosné pracovní postupy strojového učení napříč různými výpočetními cíli.

Objekt v `Environment` místním výpočetním prostředí můžete použít k:
* Vytvořte si tréninkový skript.
* Znovu použít stejné prostředí na Azure Machine Learning Compute pro školení modelu ve velkém měřítku.
* Nasaďte model se stejným prostředím.

Následující diagram znázorňuje, jak `Environment` můžete použít jeden objekt v konfiguraci spuštění, pro školení a odvození a konfigurace nasazení pro nasazení webových služeb.

![Diagram prostředí v pracovním postupu strojového učení](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typy prostředí

Prostředí lze obecně rozdělit do tří kategorií: *kurátor ,* *spravovaný uživatelem*a *systémově spravovaný*.

Kurátorská prostředí jsou dostupná od Azure Machine Learning a ve výchozím nastavení jsou dostupná ve vašem pracovním prostoru. Obsahují kolekce balíčků a nastavení Pythonu, které vám pomůžou začít s různými architekturami strojového učení. 

V prostředích spravovaných uživateli jste zodpovědní za nastavení prostředí a instalaci každého balíčku, který váš školicí skript potřebuje na výpočetní cíl. Conda nekontroluje vaše prostředí ani nic neinstaluje za vás. Pokud definujete vlastní prostředí, musíte uvést `azureml-defaults` s `>= 1.0.45` verzí jako závislost pip. Tento balíček obsahuje funkce, které jsou potřebné k hostování modelu jako webové služby.

Systémově spravovaná prostředí se používají, když chcete, aby [Conda](https://conda.io/docs/) spravoval prostředí Pythonu a závislosti skriptů za vás. Služba předpokládá tento typ prostředí ve výchozím nastavení, protože jeho užitečnost na vzdálené výpočetní cíle, které nejsou ručně konfigurovatelné.

## <a name="create-and-manage-environments"></a>Vytváření a správa prostředí

Prostředí můžete vytvářet takto:

* Definování nových `Environment` objektů, buď pomocí kurátorované prostředí nebo definovánívlastní závislosti.
* Použití `Environment` existujících objektů z pracovního prostoru. Tento přístup umožňuje konzistenci a reprodukovatelnost s vašimi závislostmi.
* Import z existující definice prostředí Anaconda.
* Použití cli Azure Machine Learning

Pro konkrétní ukázky kódu, naleznete v části "Vytvořit prostředí" [znovu použít prostředí pro školení a nasazení](how-to-use-environments.md#create-an-environment). Prostředí se také snadno spravují prostřednictvím pracovního prostoru. Zahrnují následující funkce:

* Prostředí se automaticky zaevidují do pracovního prostoru při odeslání experimentu. Mohou být také ručně registrovány.
* Můžete načíst prostředí z pracovního prostoru pro školení nebo nasazení nebo provádět úpravy definice prostředí.
* Pomocí správy verzí můžete v průběhu času zobrazit změny prostředí, což zajišťuje reprodukovatelnost.
* Můžete vytvářet image Dockeru automaticky z vašeho prostředí.

Ukázky kódu naleznete v části Správa prostředí [v prostředích opakovaného použití pro školení a nasazení](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Vytváření prostředí, ukládání do mezipaměti a opětovné použití

Služba Azure Machine Learning vytváří definice prostředí do ibi dockerů a prostředí conda. Také ukládá do mezipaměti prostředí, takže je lze znovu použít v následných spuštěních školení a nasazení koncového bodu služby.

### <a name="building-environments-as-docker-images"></a>Vytváření prostředí jako image Dockeru

Obvykle při prvním odeslání spustit pomocí prostředí, služba Azure Machine Learning vyvolá [úlohu sestavení ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) na registru kontejnerů Azure (ACR) přidružené k pracovního prostoru. Vytvořená bitová kopie Dockeru je pak uložena do mezipaměti v acr pracovního prostoru. Na začátku spuštění spuštění bitové kopie je načten a výpočetní cíl.

Sestavení bitové kopie se skládá ze dvou kroků:

 1. Stažení základní bitové kopie a provedení všech kroků Dockeru
 2. Vytváření prostředí conda podle conda závislostí zadaných v definici prostředí.

Druhý krok je vynechán, pokud zadáte [závislosti spravované uživatelem](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). V takovém případě jste zodpovědní za instalaci všech balíčků Pythonu, jejich zahrnutím do základní bitové kopie nebo zadáním vlastních kroků Dockeru v prvním kroku. Jste také zodpovědní za určení správného umístění spustitelného souboru Pythonu.

### <a name="image-caching-and-reuse"></a>Ukládání obrázků do mezipaměti a opakované použití

Pokud použijete stejnou definici prostředí pro jiné spuštění, služba Azure Machine Learning znovu použije image uloženou v mezipaměti z acr pracovního prostoru. 

Chcete-li zobrazit podrobnosti o bitové kopii uložené v mezipaměti, použijte metodu [Environment.get_image_details.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)

Chcete-li zjistit, zda chcete znovu použít bitovou kopii uloženou v mezipaměti nebo vytvořit novou, vypočítá služba [hodnotu hash](https://en.wikipedia.org/wiki/Hash_table) z definice prostředí a porovná ji s hodnotami hash existujících prostředí. Hash je založen na:
 
 * Hodnota vlastnosti základního obrázku
 * Hodnota vlastnosti Vlastní docker kroky
 * Seznam balíčků Pythonu v definici Conda
 * Seznam balíčků v definici Spark 

Hash nezávisí na názvu prostředí nebo verzi. Změny definice prostředí, jako je například přidání nebo odebrání balíčku Pythonu nebo změna verze balíčku, způsobí změnu hodnoty hash a spustí opětovné sestavení bitové kopie. Pokud však jednoduše přejmenujete prostředí nebo vytvoříte nové prostředí s přesnými vlastnostmi a balíčky existujícího prostředí, zůstane hodnota hash stejná a bude použita bitová kopie uložená v mezipaměti.

Podívejte se na následující diagram, který znázorňuje tři definice prostředí. Dva z nich mají jiný název a verzi, ale identické základní image a balíčky Pythonu. Mají stejnou hodnotu hash, a proto odpovídají stejnému obrázku uloženému v mezipaměti. Třetí prostředí má různé balíčky a verze Pythonu, a proto odpovídá jiné image uložené v mezipaměti.

![Diagram ukládání do mezipaměti prostředí jako imitace Dockeru](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Pokud vytvoříte prostředí s nepřipnutým balíčkem závislost, například ```numpy```, že prostředí bude nadále používat verzi balíčku _nainstalovanou v době vytvoření prostředí_. Také jakékoli budoucí prostředí s odpovídající definicí bude nadále používat starou verzi. 

Chcete-li balíček aktualizovat, zadejte číslo verze, které vynutí opětovné sestavení obrazu, například ```numpy==1.18.1```. Všimněte si, že nové závislosti, včetně vnořených ty budou nainstalovány, které by mohly přerušit dříve pracovní scénář.

> [!WARNING]
>  [Metoda Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) znovu vytvoří bitovou kopii uloženou v mezipaměti s možným vedlejším účinkem aktualizace nepřipnutých balíčků a rozdělení reprodukovatelnosti pro všechny definice prostředí odpovídající této bitové kopii uložené v mezipaměti.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [vytvářet a používat prostředí](how-to-use-environments.md) v Azure Machine Learning.
* Podívejte se na referenční dokumentaci sady Python SDK pro [třídu prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Viz referenční dokumentace sady R SDK pro [prostředí](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
