---
title: Importovat data do návrháře (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se importovat data do návrháře Azure Machine Learning (Preview) z různých zdrojů dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: d977c8e13ce75eb276c8fdb11e9dd40e40a923ad
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495367"
---
# <a name="import-data-into-azure-machine-learning-designer-preview"></a>Import dat do návrháře Azure Machine Learning (Preview)

V tomto článku se dozvíte, jak v Návrháři importovat vlastní data, abyste mohli vytvářet vlastní řešení. Existují dva způsoby, jak můžete importovat data do návrháře: 

* **Azure Machine Learning DataSets** – zaregistrujete datové [sady](concept-data.md#datasets) v Azure Machine Learning a povolíte tak pokročilé funkce, které vám pomůžou se správou vašich dat.
* **Importovat modul dat** – pomocí modulu [Import dat](algorithm-module-reference/import-data.md) můžete získat přímý přístup k datům z online zdrojů dat.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Použití Azure Machine Learning datových sad

Pro import dat do návrháře doporučujeme použít datové [sady](concept-data.md#datasets) . Když zaregistrujete datovou sadu, můžete plně využít výhod pokročilých datových funkcí, jako je [Správa verzí a sledování](how-to-version-track-datasets.md) a [monitorování dat](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrace datové sady

Existující datové sady [programově se sadou SDK](how-to-create-register-datasets.md#datasets-sdk) můžete registrovat nebo [vizuálně v Azure Machine Learning Studiu](how-to-create-register-datasets.md#datasets-ui).

Výstup můžete také zaregistrovat pro libovolný modul návrháře jako datovou sadu.

1. Vyberte modul, který vypíše data, která chcete zaregistrovat.

1. V podokně Vlastnosti vyberte možnost **výstupy**pro  >  **registrační datovou sadu**.

    ![Snímek obrazovky ukazující, jak přejít na možnost Registrovat datovou sadu](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Použití datové sady

Vaše registrované datové sady se dají najít v paletě modulu **v části datové sady**  >  **My Datasets**. Pokud chcete datovou sadu použít, přetáhněte ji na plátno kanálu. Pak připojte výstupní port datové sady k jiným modulům v paletě.

![Snímek obrazovky znázorňující umístění uložených datových sad v paletě návrháře](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Návrhář aktuálně podporuje pouze zpracování [tabelárních datových sad](how-to-create-register-datasets.md#dataset-types). Pokud chcete použít [souborové sady](how-to-create-register-datasets.md#dataset-types), použijte sadu SDK Azure Machine Learning dostupnou pro Python a R.

## <a name="import-data-using-the-import-data-module"></a>Import dat pomocí modulu import dat

I když doporučujeme k importu dat použít datové sady, můžete také použít modul [Import dat](algorithm-module-reference/import-data.md) . Modul Import dat přeskočí registraci datové sady v Azure Machine Learning a importuje data přímo z [úložiště](concept-data.md#datastores) dat nebo adresy URL protokolu HTTP.

Podrobné informace o tom, jak používat modul import dat, najdete na [stránce s referenčními informacemi k importu dat](algorithm-module-reference/import-data.md).

> [!NOTE]
> Pokud vaše datová sada obsahuje příliš mnoho sloupců, může dojít k následující chybě: "ověření se nezdařilo z důvodu omezení velikosti". Pokud se tomu chcete vyhnout, [Zaregistrujte datovou sadu v rozhraní datových sad](how-to-create-register-datasets.md#datasets-ui).

## <a name="supported-sources"></a>Podporované zdroje

Tato část obsahuje seznam zdrojů dat, které Návrhář podporuje. Data přicházejí do návrháře z úložiště dat nebo z [tabulkové sady dat](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Zdroje úložiště dat
Seznam podporovaných zdrojů úložiště [dat najdete v tématu přístup k datům ve službě Azure Storage](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Zdroje tabelární datové sady

Návrhář podporuje tabulkové datové sady vytvořené z následujících zdrojů:
 * Soubory s oddělovači
 * Soubory JSON
 * Soubory Parquet
 * Dotazy SQL

## <a name="data-types"></a>Typy dat

Návrhář interně rozpoznává následující typy dat:

* Řetězec
* Celé číslo
* Desetinné číslo
* Logická hodnota
* Datum

Návrhář používá interní datový typ k předávání dat mezi moduly. Data můžete explicitně převést do formátu tabulky dat pomocí modulu [převést na datovou sadu](algorithm-module-reference/convert-to-dataset.md) . Libovolný modul, který přijímá jiné formáty než interní formát, převede data v tichém režimu předtím, než je předáte do dalšího modulu.

## <a name="data-constraints"></a>Omezení dat

Moduly v návrháři jsou omezeny velikostí cíle výpočtů. U větších datových sad byste měli použít větší Azure Machine Learning výpočetní prostředky. Další informace o Azure Machine Learning COMPUTE najdete v tématu [co jsou výpočetní cíle v Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Přístup k datům ve virtuální síti

Pokud je váš pracovní prostor ve virtuální síti, musíte provést další kroky konfigurace, aby bylo možné vizualizovat data v návrháři. Další informace o tom, jak používat úložiště dat a datové sady ve virtuální síti, najdete v tématu [izolace sítě během školení & odvození s privátními virtuálními sítěmi](how-to-enable-virtual-network.md#machine-learning-studio).

## <a name="next-steps"></a>Další kroky

Seznamte se se základy návrháře pomocí [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md).
