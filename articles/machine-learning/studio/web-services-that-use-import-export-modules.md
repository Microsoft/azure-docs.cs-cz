---
title: Použití Import/Export dat ve webových službách Azure Machine Learning Studio | Dokumentace Microsoftu
description: Další informace o použití modulů Data importovat a exportovat Data odesílat a přijímat data z webové služby.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: ee7d0fb4792983099dc6192de8f85338daee357f
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52306943"
---
# <a name="deploy-azure-machine-learning-studio-web-services-that-use-data-import-and-data-export-modules"></a>Nasazení webové služby Azure Machine Learning Studio, které používají moduly Import dat a Export dat

Při vytváření prediktivní experiment přidáte obvykle webové služby vstup a výstup. Při nasazení experimentu příjemci můžou odesílat a přijímat data z webové služby prostřednictvím vstupy a výstupy. U některých aplikací může být k dispozici z datového kanálu uživatelských dat nebo se již nacházejí v externího zdroje dat jako jsou úložiště objektů Blob v Azure. V těchto případech nemusí číst a zapisovat data pomocí webové služby vstupy a výstupy. Mohou, místo toho dat ze zdroje dat pomocí modulu Import dat pomocí služby Batch Execution (BES) a bodovací výsledky zapsat do různých datových umístění pomocí modulu exportovat Data.

Import dat a Export dat moduly, můžete číst z a zapisovat do různých dat, třeba na adresu URL webu prostřednictvím protokolu HTTP, dotaz Hive, Azure SQL database, Azure Table storage, Azure Blob storage, datového kanálu poskytnout nebo místní databázi SQL.

Toto téma používá "vzorku 5: trénování, testování, vyhodnotit pro binární klasifikaci: pro dospělé datovou sadu" Ukázkový a předpokládá datová sada už je načtený do tabulky Azure SQL s názvem censusdata.

## <a name="create-the-training-experiment"></a>Vytvořit výukový experiment
Když otevřete "vzorku 5: trénování, testování, vyhodnotit pro binární klasifikaci: dospělé datovou sadu" Ukázka používá ukázkovou datovou sadou pro dospělé binární klasifikace příjmů sčítání. A na plátno experimentu bude vypadat podobně jako na následujícím obrázku:

![Počáteční konfigurace testu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

K načtení dat z tabulky Azure SQL:

1. Odstraňte modul datovou sadu.
2. Do vyhledávacího pole komponenty typ importu.
3. Ze seznamu výsledků přidat *Import dat* modulů na plátno experimentu.
4. Propojte výstup z *Import dat* modulu vstupu z *vyčištění chybějících dat* modulu.
5. V podokně vlastností, vyberte **Azure SQL Database** v **zdroj dat** rozevíracího seznamu.
6. V **název databázového serveru**, **název_databáze**, **uživatelské jméno**, a **heslo** polí zadejte příslušné informace pro váš databáze.
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
8. V dolní části na plátno experimentu klikněte na tlačítko **spustit**.

## <a name="create-the-predictive-experiment"></a>Vytvoření prediktivního experimentu
Další nastavíte prediktivní experiment, ze kterého nasazujete webovou službu.

1. V dolní části na plátno experimentu klikněte na tlačítko **nastavení webové služby** a vyberte **prediktivní webové služby (doporučeno)**.
2. Odeberte *vstup webové služby* a *webové služby výstupní moduly* z prediktivního experimentu. 
3. Do pole Hledat součásti zadejte exportu.
4. Ze seznamu výsledků přidat *exportovat Data* modulů na plátno experimentu.
5. Propojte výstup z *Score Model* modulu vstupu z *exportovat Data* modulu. 
6. V podokně vlastností, vyberte **Azure SQL Database** v rozevírací nabídce Cíl data.
7. V **název databázového serveru**, **název_databáze**, **název uživatelského účtu serveru**, a **heslo uživatelského účtu serveru** pole, zadejte příslušné informace pro vaši databázi.
8. V **čárkami oddělený seznam sloupců, které se má uložit** zadejte popisky vyhodnocení.
9. V **pole název tabulky dat**, zadejte dbo. ScoredLabels. Pokud tabulka neexistuje, vytvoří se při spuštění experimentu nebo webová služba je volána.
10. V **čárkami oddělený seznam sloupců do tabulky datatable** pole, zadejte ScoredLabels.

Při psaní aplikace, která volá poslední webovou službu, můžete zadat jiný dotaz vstupní nebo cílové tabulky v době běhu. Při konfiguraci těchto vstupy a výstupy, funkce parametrů webové služby používá k nastavení *Import dat* modulu *zdroj dat* vlastnost a *exportovat Data* data režimu cílové vlastnosti.  Další informace o parametry webové služby, najdete v článku [vstupní parametry webové služby Azure ml](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) v Cortana Intelligence a Machine Learning Blog.

Konfigurace parametrů webové služby pro import dotazu a cílová tabulka:

1. V podokně vlastností *Import dat* modulu, klikněte na ikonu v horní části napravo od **databázový dotaz** pole a vyberte **nastavit jako parametr webové služby**.
2. V podokně vlastností *exportovat Data* modulu, klikněte na ikonu v horní části napravo od **název tabulky dat** pole a vyberte **nastavit jako parametr webové služby**.
3. V dolní části *exportovat Data* podokno vlastností modulu v **parametrů webové služby** části, klikněte na tlačítko databázového dotazu a dotaz přejmenovat.
4. Klikněte na tlačítko **název tabulky dat** a přejmenujte jej **tabulky**.

Jakmile budete hotovi, experiment by měl vypadat podobně jako na následujícím obrázku:

![Poslední vzhled experimentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Teď můžete nasadit experiment jako webové služby.

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit na klasickém nebo novou webovou službu.

### <a name="deploy-a-classic-web-service"></a>Nasadit klasickou webovou službou
Nasadit jako webovou službu Classic a vytvořte aplikaci pro použít:

1. V dolní části na plátno experimentu klikněte na tlačítko spustit.
2. Po dokončení spuštění, klikněte na tlačítko **nasadit webovou službu** a vyberte **nasazení webové služby [Classic]**.
3. Na řídicím panelu webové služby vyhledejte klíč rozhraní API. Zkopírujte a uložte ho pro pozdější použití.
4. V **výchozí koncový bod** tabulku, klikněte na tlačítko **Batch Execution** odkaz k otevření stránce nápovědy k API.
5. V sadě Visual Studio, vytvořit C# konzolové aplikace: **nový** > **projektu** > **Visual C#**   >   **Klasická plocha Windows** > **aplikace konzoly (.NET Framework)**.
6. Na stránce nápovědy k API, vyhledejte **ukázkový kód** části v dolní části stránky.
7. Zkopírujte a vložte C# ukázkový kód do souboru Program.cs a odeberte všechny odkazy na úložiště objektů blob.
8. Aktualizujte hodnotu *apiKey* proměnné s klíčem rozhraní API předtím uložili.
9. Vyhledejte prohlášení požadavku a aktualizujte hodnoty parametrů webové služby, které jsou předány *Import dat* a *exportovat Data* moduly. V takovém případě použijte původní dotaz ale definovat nový název tabulky.
   
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

1. V dolní části na plátno experimentu klikněte na tlačítko **spustit**.
2. Po dokončení spuštění, klikněte na tlačítko **nasadit webovou službu** a vyberte **nasazení [nové] webová služba**.
3. Na stránce experimentu nasazení zadejte název pro webovou službu a vybrat cenový plán, pak klikněte na tlačítko **nasadit**.
4. Na **rychlý Start** klikněte na **spotřebovat**.
5. V **ukázkový kód** klikněte na tlačítko **Batch**.
6. V sadě Visual Studio, vytvořit C# konzolové aplikace: **nový** > **projektu** > **Visual C#**   >   **Klasická plocha Windows** > **aplikace konzoly (.NET Framework)**.
7. Zkopírujte a vložte C# ukázkový kód do souboru Program.cs.
8. Aktualizujte hodnotu *apiKey* proměnné s **primární klíč** umístěné v **informace o základní spotřeby** oddílu.
9. Vyhledejte *scoreRequest* prohlášení a aktualizujte hodnoty parametrů webové služby, které jsou předány *Import dat* a *exportovat Data* moduly. V takovém případě použijte původní dotaz ale definovat nový název tabulky.
   
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

