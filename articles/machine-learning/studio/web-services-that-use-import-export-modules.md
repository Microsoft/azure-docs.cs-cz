---
title: Import nebo export dat ve webových službách
titleSuffix: ML Studio (classic) - Azure
description: Přečtěte si, jak pomocí modulů Importovat data a Exportovat data k odesílání a přijímání dat z webové služby.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 144a3bc0d9e0499a238e4033d37d5e4d3fa61e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204050"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Nasazení webových služeb Azure Machine Learning Studio (klasické), které používají moduly importu dat a exportu dat

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Při vytváření prediktivní experiment, obvykle přidat vstup webové služby a výstup. Při nasazení experimentu mohou spotřebitelé odesílat a přijímat data z webové služby prostřednictvím vstupů a výstupů. Pro některé aplikace mohou být data spotřebitele dostupná z datového kanálu nebo se už nacházejí v externím zdroji dat, jako je úložiště objektů blob Azure. V těchto případech nepotřebují číst a zapisovat data pomocí vstupů a výstupů webové služby. Místo toho mohou pomocí služby bes (Batch Execution Service) číst data ze zdroje dat pomocí modulu Import data a zapsat výsledky vyhodnocování do jiného umístění dat pomocí modulu Export dat.

Moduly Importovat data a exportovat data můžou číst a zapisovat do různých datových umístění, jako je webová adresa URL přes HTTP, dotaz Hive, databáze Azure SQL, úložiště tabulek Azure, úložiště objektů Blob Azure, poskytuje datový kanál nebo místní databáze SQL.

Toto téma používá ukázku "Ukázka 5: Trénování, Test, Vyhodnotit pro binární klasifikace: Dospělá datová sada" ukázka a předpokládá, že datová sada již byla načtena do tabulky Azure SQL s názvem data sčítání.

## <a name="create-the-training-experiment"></a>Vytvoření tréninkového experimentu
Když otevřete ukázku "Vzorek 5: Vlak, Test, Vyhodnotit pro binární klasifikace: Dospělá datová sada" vzorek dospělé sčítání příjmů binární klasifikace datové sady. A experiment na plátně bude vypadat podobně jako následující obrázek:

![Počáteční konfigurace experimentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Čtení dat z tabulky Azure SQL:

1. Odstraňte modul datové sady.
2. Do vyhledávacího pole komponent zadejte import.
3. Ze seznamu výsledků přidejte modul *Importovat data* na plátno experimentu.
4. Připojte výstup modulu *Import data* ke vstupu modulu Vyčistit *chybějící data.*
5. V podokně vlastností vyberte V rozevíracím seznamu **Zdroj dat** vyberte Azure **SQL Database.**
6. Do polí **Název databázového serveru**, **Název databáze**, Uživatelské **jméno**a **Heslo** zadejte příslušné informace pro databázi.
7. Do pole Dotaz databáze zadejte následující dotaz.

     vyberte [věk],

        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     z údajů dbo.census;
8. V dolní části plátna experimentu klikněte na **Spustit**.

## <a name="create-the-predictive-experiment"></a>Vytvoření prediktivního experimentu
Dále nastavíte prediktivní experiment, ze kterého nasadíte webovou službu.

1. V dolní části plátna experimentu klepněte na **položku Nastavit webovou službu** a vyberte **možnost Prediktivní webová služba [Doporučeno]**.
2. Odeberte moduly *vstup webové služby* a *výstup webové služby* z prediktivního experimentu.
3. Do vyhledávacího pole komponent zadejte export.
4. Ze seznamu výsledků přidejte modul *Exportdat* na plátno experimentu.
5. Připojte výstup modulu *Model skóre* vstup modulu *Export dat.*
6. V podokně vlastností vyberte **Azure SQL Database** v rozevíracím seznamu cíl dat.
7. Do polí **Název databázového serveru**, **Název databáze**, Název **uživatelského účtu serveru**a Heslo **uživatelského účtu serveru** zadejte příslušné informace pro databázi.
8. V **seznamu sloupců, které mají být uloženy, v seznamu sloupců, které mají být uloženy,** zadejte Popisky s vyhodženým skóre.
9. Do **pole Název tabulky Dat**zadejte dbo. ScoredLabels. Pokud tabulka neexistuje, je vytvořena při spuštění experimentu nebo volání webové služby.
10. V **poli Čárka oddělené ho pole sloupce datové tabulky** zadejte ScoredLabels.

Při psaní aplikace, která volá konečnou webovou službu, můžete zadat jiný vstupní dotaz nebo cílovou tabulku za běhu. Chcete-li tyto vstupy a výstupy nakonfigurovat, nastavte pomocí funkce Parametry webové služby vlastnost *Import ovat datový* *zdroj* data a cílovou vlastnost Exportovat data v režimu *dat.*  Další informace o parametrech webové služby najdete v [tématu Azure Machine Learning Studio parametry webových služeb položky](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) na Cortana Intelligence a Machine Learning blog.

Postup konfigurace parametrů webové služby pro importovaný dotaz a cílovou tabulku:

1. V podokně vlastností modulu *Importovat data* klepněte na ikonu v pravém horním rohu pole **dotazu databáze** a vyberte **parametr Nastavit jako webovou službu**.
2. V podokně vlastností modulu *Exportovat data* klepněte na ikonu v pravém horním rohu pole **Název tabulky dat** a vyberte Nastavit jako parametr webové **služby**.
3. V dolní části podokna Vlastnosti modulu *Exportovat data* klikněte v části **Parametry webové služby** na dotaz databáze a přejmenujte jej na Dotaz.
4. Klikněte na **Název tabulky dat** a přejmenujte ji **tabulka**.

Po dokončení by experiment měl vypadat podobně jako na následujícím obrázku:

![Konečný pohled experimentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Nyní můžete experiment nasadit jako webovou službu.

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit do klasické nebo nové webové služby.

### <a name="deploy-a-classic-web-service"></a>Nasazení klasické webové služby
Nasazení jako klasické webové služby a vytvoření aplikace, která ji bude využívat:

1. V dolní části plátna experimentu klikněte na Spustit.
2. Po dokončení spuštění klepněte na **tlačítko Nasadit webovou službu** a vyberte **možnost Nasadit webovou službu [Classic]**.
3. Na řídicím panelu webové služby vyhledejte klíč rozhraní API. Zkopírujte a uložte jej, abyste jej později použili.
4. V **tabulce Výchozí koncový bod** kliknutím na odkaz **Dávkové spuštění** otevřete stránku nápovědy rozhraní API.
5. V sadě Visual Studio vytvořte konzolovou aplikaci Jazyka C#: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework).**
6. Na stránce nápovědy rozhraní API najdete oddíl **Ukázkový kód** v dolní části stránky.
7. Zkopírujte a vložte ukázkový kód C# do souboru Program.cs a odeberte všechny odkazy na úložiště objektů blob.
8. Aktualizujte hodnotu proměnné *apiKey* pomocí dříve uloženého klíče rozhraní API.
9. Vyhledejte deklaraci požadavku a aktualizujte hodnoty parametrů webové služby, které jsou předány modulům *Import dat* a *Export dat.* V takovém případě použijete původní dotaz, ale definujte nový název tabulky.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Spusťte aplikaci.

Po dokončení spuštění je do databáze přidána nová tabulka obsahující výsledky hodnocení.

### <a name="deploy-a-new-web-service"></a>Nasazení nové webové služby

> [!NOTE]
> Chcete-li nasadit novou webovou službu, musíte mít dostatečná oprávnění v předplatném, do kterého nasadíte webovou službu. Další informace najdete [v tématu Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).

Nasazení jako nové webové služby a vytvoření aplikace, která ji bude využívat:

1. V dolní části plátna experimentu klikněte na **Spustit**.
2. Po dokončení spuštění klepněte na **tlačítko Nasadit webovou službu** a vyberte **možnost Nasadit webovou službu [New]**.
3. Na stránce Deploy Experiment zadejte název webové služby a vyberte cenový plán a klikněte na **Nasadit**.
4. Na stránce **Rychlý start** klikněte na **položku Spotřebovat**.
5. V části **Ukázkový kód** klikněte na **dávky**.
6. V sadě Visual Studio vytvořte konzolovou aplikaci Jazyka C#: **New** > **Project** > **Visual C#** > **Windows Classic Desktop** > **Console App (.NET Framework).**
7. Zkopírujte a vložte ukázkový kód C# do Program.cs souboru.
8. Aktualizujte hodnotu proměnné *apiKey* s **primárním klíčem** umístěným v části **Základní informace o spotřebě.**
9. Vyhledejte *deklaraci scoreRequest* a aktualizujte hodnoty parametrů webové služby, které jsou předány modulům *Import dat* a Export *dat.* V takovém případě použijete původní dotaz, ale definujte nový název tabulky.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Spusťte aplikaci.

