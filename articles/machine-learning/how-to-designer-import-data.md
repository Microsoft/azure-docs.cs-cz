---
title: Import dat do návrháře
titleSuffix: Azure Machine Learning
description: Naučte se, jak importovat data do návrháře Azure Machine Learning pomocí datových sad Azure Machine Learning a modulu import dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: a2cc0840b7ba4b26cf9f5b1219fc189230870774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97739854"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Import dat do návrháře Azure Machine Learning

V tomto článku se dozvíte, jak v Návrháři importovat vlastní data, abyste mohli vytvářet vlastní řešení. Existují dva způsoby, jak můžete importovat data do návrháře: 

* **Azure Machine Learning DataSets** – zaregistrujete datové [sady](concept-data.md#datasets) v Azure Machine Learning a povolíte tak pokročilé funkce, které vám pomůžou se správou vašich dat.
* **Importovat modul dat** – pomocí modulu [Import dat](algorithm-module-reference/import-data.md) můžete získat přímý přístup k datům z online zdrojů dat.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Použití Azure Machine Learning datových sad

Pro import dat do návrháře doporučujeme použít datové [sady](concept-data.md#datasets) . Když zaregistrujete datovou sadu, můžete plně využít výhod pokročilých datových funkcí, jako je [Správa verzí a sledování](how-to-version-track-datasets.md) a [monitorování dat](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrace datové sady

Existující datové sady [programově se sadou SDK](how-to-create-register-datasets.md#datasets-sdk) můžete registrovat nebo [vizuálně v Azure Machine Learning Studiu](how-to-connect-data-ui.md#create-datasets).

Výstup můžete také zaregistrovat pro libovolný modul návrháře jako datovou sadu.

1. Vyberte modul, který vypíše data, která chcete zaregistrovat.

1. V podokně Vlastnosti vyberte **výstupy + protokoly**  >  **Registrovat datovou sadu**.

    ![Snímek obrazovky ukazující, jak přejít na možnost Registrovat datovou sadu](media/how-to-designer-import-data/register-dataset-designer.png)

Pokud jsou výstupní data modulu v tabulkovém formátu, je nutné se rozhodnout zaregistrovat výstup jako **datovou sadu** nebo **tabulkovou datovou sadu**.

 - **Datová sada souborů** registruje výstupní složku modulu jako datovou sadu souboru. Výstupní složka obsahuje datový soubor a meta soubory, které Návrhář používá interně. Tuto možnost vyberte, pokud chcete i nadále používat registrovanou datovou sadu v návrháři. 

 - **Tabulková datová sada** registruje pouze výstupní datový soubor modulu jako tabelární datovou sadu. Tento formát je snadno využíván jinými nástroji, například v automatizovaných Machine Learning nebo v sadě Python SDK. Tuto možnost vyberte, pokud plánujete použít registrovanou datovou sadu mimo Návrhář.  
 

### <a name="use-a-dataset"></a>Použití datové sady

Vaše registrované datové sady lze nalézt v paletě modulu v části **datové sady**. Pokud chcete datovou sadu použít, přetáhněte ji na plátno kanálu. Pak připojte výstupní port datové sady k ostatním modulům na plátně. 

Pokud zaregistrujete datovou sadu souborů, je typ výstupního portu datové sady **AnyDirectory**. Pokud zaregistrujete tabulkovou sadu, typ výstupního portu pro datovou sadu, pokud **DataFrameDirectory**. Všimněte si, že pokud připojíte výstupní port datové sady k jiným modulům v návrháři, je nutné zarovnat typ portu datových sad a modulů.

![Snímek obrazovky znázorňující umístění uložených datových sad v paletě návrháře](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Návrhář podporuje [správu verzí datových sad](how-to-version-track-datasets.md). Zadejte verzi datové sady na panelu vlastností modulu DataSet.

### <a name="limitations"></a>Omezení 

- V současné době je možné vizualizovat pouze tabelární datovou sadu v návrháři. Pokud zaregistrujete souborovou sadu mimo návrháře, nemůžete ji vizualizovat na plátně návrháře.
- Vaše datová sada je uložená ve virtuální síti (VNet). Chcete-li vizualizovat, je nutné povolit správu pracovního prostoru s identitou úložiště dat.
    1. Přejděte na související úložiště dat a klikněte na **aktualizovat** přihlašovací údaje 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="aktualizovat"::: přihlašovací údaje.
    1. Vyberte **Ano** , pokud chcete povolit spravovanou identitu pracovního prostoru.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Povolit spravovanou identitu pracovního prostoru":::

## <a name="import-data-using-the-import-data-module"></a>Import dat pomocí modulu import dat

I když doporučujeme k importu dat použít datové sady, můžete také použít modul [Import dat](algorithm-module-reference/import-data.md) . Modul Import dat přeskočí registraci datové sady v Azure Machine Learning a importuje data přímo z [úložiště](concept-data.md#datastores) dat nebo adresy URL protokolu HTTP.

Podrobné informace o tom, jak používat modul import dat, najdete na [stránce s referenčními informacemi k importu dat](algorithm-module-reference/import-data.md).

> [!NOTE]
> Pokud vaše datová sada obsahuje příliš mnoho sloupců, může dojít k následující chybě: "ověření se nezdařilo z důvodu omezení velikosti". Pokud se tomu chcete vyhnout, [Zaregistrujte datovou sadu v rozhraní datových sad](how-to-connect-data-ui.md#create-datasets).

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
* Integer
* Decimal
* Logická hodnota
* Datum

Návrhář používá interní datový typ k předávání dat mezi moduly. Data můžete explicitně převést do formátu tabulky dat pomocí modulu [převést na datovou sadu](algorithm-module-reference/convert-to-dataset.md) . Libovolný modul, který přijímá jiné formáty než interní formát, převede data v tichém režimu předtím, než je předáte do dalšího modulu.

## <a name="data-constraints"></a>Omezení dat

Moduly v návrháři jsou omezeny velikostí cíle výpočtů. U větších datových sad byste měli použít větší Azure Machine Learning výpočetní prostředky. Další informace o Azure Machine Learning COMPUTE najdete v tématu [co jsou výpočetní cíle v Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Přístup k datům ve virtuální síti

Pokud je váš pracovní prostor ve virtuální síti, musíte provést další kroky konfigurace, aby bylo možné vizualizovat data v návrháři. Další informace o tom, jak používat úložiště dat a datové sady ve virtuální síti, najdete v tématu [použití Azure Machine Learning studia ve službě Azure Virtual Network](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Další kroky

Naučte se základy pro návrháře v tomto [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md).
