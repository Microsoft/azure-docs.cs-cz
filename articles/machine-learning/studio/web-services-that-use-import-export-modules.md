---
title: Import/Export dat ve webových službách
titleSuffix: ML Studio (classic) - Azure
description: Další informace o použití modulů Data importovat a exportovat Data odesílat a přijímat data z webové služby.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204050"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Nasazení webových služeb Azure Machine Learning Studio (Classic), které používají moduly importu a exportu dat

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Při vytváření prediktivní experiment přidáte obvykle webové služby vstup a výstup. Při nasazení experimentu příjemci můžou odesílat a přijímat data z webové služby prostřednictvím vstupy a výstupy. U některých aplikací může být k dispozici z datového kanálu uživatelských dat nebo se již nacházejí v externího zdroje dat jako jsou úložiště objektů Blob v Azure. V těchto případech nemusí číst a zapisovat data pomocí webové služby vstupy a výstupy. Mohou, místo toho dat ze zdroje dat pomocí modulu Import dat pomocí služby Batch Execution (BES) a bodovací výsledky zapsat do různých datových umístění pomocí modulu exportovat Data.

Import dat a Export dat moduly, můžete číst z a zapisovat do různých dat, třeba na adresu URL webu prostřednictvím protokolu HTTP, dotaz Hive, Azure SQL database, Azure Table storage, Azure Blob storage, datového kanálu poskytnout nebo místní databázi SQL.

Toto téma používá "vzorku 5: trénování, testování, vyhodnotit pro binární klasifikaci: pro dospělé datovou sadu" Ukázkový a předpokládá datová sada už je načtený do tabulky Azure SQL s názvem censusdata.

## <a name="create-the-training-experiment"></a>Vytvořit výukový experiment
Když otevřete "vzorku 5: trénování, testování, vyhodnotit pro binární klasifikaci: dospělé datovou sadu" Ukázka používá ukázkovou datovou sadou pro dospělé binární klasifikace příjmů sčítání. A na plátno experimentu bude vypadat podobně jako na následujícím obrázku:

![Počáteční konfigurace testu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

K načtení dat z tabulky Azure SQL:

1. Odstraňte modul datovou sadu.
2. Do vyhledávacího pole komponenty typ importu.
3. V seznamu výsledků přidejte modul *Import dat* na plátno experimentu.
4. Připojte výstup modulu *Import dat* do vstupu modulu *Vyčištění chybějících dat* .
5. V podokně Vlastnosti vyberte v rozevíracím seznamu **zdroj dat** možnost **Azure SQL Database** .
6. Do polí **název databázového serveru**, **název databáze**, **uživatelské jméno**a **heslo** zadejte příslušné informace pro vaši databázi.
7. Do pole databáze dotazu zadejte následující dotaz.

     Vyberte [stáří]

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
     z dbo.censusdata;
8. V dolní části plátna experimentu klikněte na **Spustit**.

## <a name="create-the-predictive-experiment"></a>Vytvoření prediktivního experimentu
Další nastavíte prediktivní experiment, ze kterého nasazujete webovou službu.

1. V dolní části plátna experimentu klikněte na **nastavit webovou službu** a vyberte **prediktivní webovou službu [doporučeno]** .
2. Odeberte *výstupní moduly* vstupu a výstupu *webové služby* z prediktivního experimentu.
3. Do pole Hledat součásti zadejte exportu.
4. V seznamu výsledků přidejte modul *Export dat* na plátno experimentu.
5. Připojte výstup modulu určení *skóre modelu* se vstupem modulu *Export dat* .
6. V podokně Vlastnosti vyberte v rozevíracím seznamu cíl data možnost **Azure SQL Database** .
7. Do polí **název**databázového serveru, název **databáze**, **název účtu uživatele serveru**a **heslo účet uživatele serveru** zadejte příslušné informace pro vaši databázi.
8. V **seznamu sloupců, které mají být uloženy, do pole oddělených čárkami** zadejte popisky s skóre.
9. Do **pole název tabulky dat**zadejte dbo. ScoredLabels. Pokud tabulka neexistuje, vytvoří se při spuštění experimentu nebo webová služba je volána.
10. Do **textového pole seznam sloupců DataTable** zadejte ScoredLabels.

Při psaní aplikace, která volá poslední webovou službu, můžete zadat jiný dotaz vstupní nebo cílové tabulky v době běhu. Chcete-li nakonfigurovat tyto vstupy a výstupy, použijte funkci parametry webové služby a nastavte vlastnost *zdroj dat* modulu *Import dat* a vlastnost cíl pro *Export* dat v režimu dat.  Další informace o parametrech webové služby najdete v tématu [Azure Machine Learning Studio položky parametrů webové služby](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) na blogu Cortana Intelligence a Machine Learning.

Konfigurace parametrů webové služby pro import dotazu a cílová tabulka:

1. V podokně vlastnosti modulu *importovat data* klikněte na ikonu v pravém horním rohu pole **databázový dotaz** a vyberte možnost **nastavit jako parametr webové služby**.
2. V podokně vlastnosti modulu *exportovat data* klikněte na ikonu v pravém horním rohu pole **název tabulky dat** a vyberte možnost **nastavit jako parametr webové služby**.
3. V dolní části podokna Vlastnosti modulu *exportu dat* klikněte v části **parametry webové služby** na dotaz databáze a přejmenujte dotaz na něj.
4. Klikněte na **název tabulky dat** a přejmenujte ji na **tabulka**.

Jakmile budete hotovi, experiment by měl vypadat podobně jako na následujícím obrázku:

![Poslední vzhled experimentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Teď můžete nasadit experiment jako webové služby.

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit na klasickém nebo novou webovou službu.

### <a name="deploy-a-classic-web-service"></a>Nasadit klasickou webovou službou
Nasadit jako webovou službu Classic a vytvořte aplikaci pro použít:

1. V dolní části na plátno experimentu klikněte na tlačítko spustit.
2. Po dokončení běhu klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [Classic]** .
3. Na řídicím panelu webové služby vyhledejte klíč rozhraní API. Zkopírujte a uložte ho pro pozdější použití.
4. V tabulce **výchozích koncových bodů** klikněte na odkaz **spuštění dávky** a otevřete stránku s usnadněníem rozhraní API.
5. V aplikaci Visual Studio C# vytvořte konzolovou aplikaci: **Nový** > **projekt** > **Visual C#**  > **Windows Classic Desktop** > **Konzolová aplikace (.NET Framework)** .
6. Na stránce s nápovědě k rozhraní API vyhledejte část **vzorový kód** v dolní části stránky.
7. Zkopírujte a vložte C# ukázkový kód do souboru Program.cs a odeberte všechny odkazy na úložiště objektů blob.
8. Aktualizujte hodnotu proměnné *apiKey* pomocí klíče rozhraní API, který jste uložili dříve.
9. Vyhledejte deklaraci žádosti a aktualizujte hodnoty parametrů webové služby, které jsou předány do datových modulů *Import dat* a *Export* . V takovém případě použijte původní dotaz ale definovat nový název tabulky.

        var request = new BatchExecutionRequest()
        {
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Spusťte aplikaci.

Po dokončení spuštění se přidá novou tabulku k databázi obsahující výsledky hodnocení.

### <a name="deploy-a-new-web-service"></a>Nasazení nové webové služby

> [!NOTE]
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).

Nasadit jako novou webovou službu a vytvořte aplikaci pro použít:

1. V dolní části plátna experimentu klikněte na **Spustit**.
2. Po dokončení běhu klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [New]** .
3. Na stránce nasazení experimentu zadejte název vaší webové služby a vyberte cenový tarif a klikněte na **nasadit**.
4. Na stránce **rychlý Start** klikněte na možnost **spotřebovat**.
5. V části **vzorový kód** klikněte na **Batch**.
6. V aplikaci Visual Studio C# vytvořte konzolovou aplikaci: **Nový** > **projekt** > **Visual C#**  > **Windows Classic Desktop** > **Konzolová aplikace (.NET Framework)** .
7. Zkopírujte a vložte C# ukázkový kód do souboru Program.cs.
8. Aktualizujte hodnotu proměnné *apiKey* pomocí **primárního klíče** , který je umístěný v části **informace o základní spotřebě** .
9. Vyhledejte deklaraci *scoreRequest* a aktualizujte hodnoty parametrů webové služby, které jsou předány do datových modulů *Import dat* a *Export* . V takovém případě použijte původní dotaz ale definovat nový název tabulky.

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

