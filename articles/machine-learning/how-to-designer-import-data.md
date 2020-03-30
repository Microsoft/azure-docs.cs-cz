---
title: Import dat
titleSuffix: Azure Machine Learning
description: Zjistěte, jak importovat data do návrháře Azure Machine Learning z různých zdrojů dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: bd1c2ca182ae8be8425246f691dca805bf38637b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064022"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Import dat do návrháře Azure Machine Learning (preview)

V tomto článku se dozvíte, jak importovat vlastní data v návrháři k vytvoření vlastních řešení. Data lze do návrháře importovat dvěma způsoby: 

* **Datové sady Azure Machine Learning** – zaregistrujte datové [sady](concept-data.md#datasets) v Azure Machine Learning, abyste povolili pokročilé funkce, které vám pomůžou spravovat vaše data.
* **Modul Importovat data** – Pomocí modulu [Import dat](algorithm-module-reference/import-data.md) můžete přímo přistupovat k datům z online zdrojů dat.

## <a name="use-azure-machine-learning-datasets"></a>Použití datových sad Azure Machine Learning

Doporučujeme použít [datové sady](concept-data.md#datasets) k importu dat do návrháře. Při registraci datové sady můžete plně využívat pokročilé funkce dat, jako je [správa verzí a sledování](how-to-version-track-datasets.md) verzí a monitorování [dat](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrace datové sady

Existující datové sady můžete programově zaregistrovat [pomocí sady SDK](how-to-create-register-datasets.md#use-the-sdk) nebo [vizuálně ve studiu Azure Machine Learning](how-to-create-register-datasets.md#use-the-ui).

Můžete také zaregistrovat výstup pro libovolný modul návrháře jako datovou sadu.

1. Vyberte modul, který vyvezuje data, která chcete registrovat.

1. V podokně vlastností vyberte**Dataset Registru** **výstupů** > .

    ![Snímek obrazovky znázorňující, jak přejít na možnost Registrovat datovou sadu](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Použití datové sady

Vaše registrované datové sady najdete v paletě modulů v části **Datové sady** > **Moje datové sady**. Chcete-li použít datovou sadu, přetáhněte ji na plátno kanálu. Potom připojte výstupní port datové sady k jiným modulům v paletě.

![Snímek obrazovky s umístěním uložených datových sad v paletě návrháře](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Návrhář aktuálně podporuje pouze zpracování [tabulkových datových sad](how-to-create-register-datasets.md#dataset-types). Pokud chcete použít [datové sady souborů](how-to-create-register-datasets.md#dataset-types), použijte Azure Machine Learning SDK, který je k dispozici pro Python a R.

## <a name="import-data-using-the-import-data-module"></a>Import dat pomocí modulu Importovat data

I když doporučujeme používat datové sady k importu dat, můžete také použít modul [Importovat data.](algorithm-module-reference/import-data.md) Modul Importovat data přeskočí registraci datové sady v Azure Machine Learning a importuje data přímo z [úložiště dat](concept-data.md#datastores) nebo http adresy URL.

Podrobné informace o použití modulu Import dat naleznete na [stránce Reference importu dat](algorithm-module-reference/import-data.md).

> [!NOTE]
> Pokud vaše datová sada má příliš mnoho sloupců, může dojít k následující chybě: "Ověření se nezdařilo z důvodu omezení velikosti". Chcete-li tomu zabránit, [zaregistrujte datovou sadu v rozhraní datových sad](how-to-create-register-datasets.md#use-the-ui).

## <a name="supported-sources"></a>Podporované zdroje

V této části jsou uvedeny zdroje dat podporované návrhářem. Data přicházejí do návrháře z úložiště dat nebo z [tabulkové datové sady](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Zdroje úložiště dat
Seznam podporovaných zdrojů úložiště dat najdete [v tématu Přístup ovádata ve službách úložiště Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Zdroje tabulkových datových sad

Návrhář podporuje tabulkové datové sady vytvořené z následujících zdrojů:
 * Oddělované soubory
 * Soubory JSON
 * Soubory Parquet
 * Dotazy SQL

## <a name="data-types"></a>Typy dat

Návrhář interně rozpozná následující datové typy:

* Řetězec
* Integer
* Desetinné číslo
* Logická hodnota
* Datum

Návrhář používá interní datový typ k předání dat mezi moduly. Data můžete explicitně převést do formátu tabulky dat pomocí modulu [Převést na datovou sadu.](algorithm-module-reference/convert-to-dataset.md) Jakýkoli modul, který přijímá formáty jiné než vnitřní formát převede data tiše před předáním do dalšího modulu.

## <a name="data-constraints"></a>Omezení dat

Moduly v návrháři jsou omezeny velikostí výpočetního cíle. Pro větší datové sady byste měli použít větší výpočetní prostředek Azure Machine Learning. Další informace o výpočetních náčtech Azure Machine Learning najdete v tématu [Co jsou výpočetní cíle v Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Další kroky

Naučte se základy návrháře s [Tutorial: Předpovědět cenu automobilu s designérem](tutorial-designer-automobile-price-train-score.md).
