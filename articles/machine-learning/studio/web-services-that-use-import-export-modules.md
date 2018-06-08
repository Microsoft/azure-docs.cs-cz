---
title: Pomocí importu a exportu dat v Azure Machine Learning webové služby | Microsoft Docs
description: Další informace o použití modulů Import Data a Export dat k odesílat a přijímat data z webové služby.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
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
ms.openlocfilehash: 27873930ebef75923088f8bf2170c8e6a383cfa8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837408"
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Nasazování webových služeb Azure ML používajících moduly Import dat a Export dat

Když vytvoříte prediktivní experiment, obvykle přidat vstup webové služby a výstup. Když nasadíte experiment, příjemci můžete odesílat a přijímat data z webové služby prostřednictvím vstupy a výstupy. Některé aplikace může být k dispozici z datového kanálu uživatelských dat nebo se již nacházejí v zdroj externích dat, jako je například úložiště objektů Blob v Azure. V těchto případech nemusí čtení a zápisu dat pomocí webové služby vstupy a výstupy. Mohou, místo toho čtení dat ze zdroje dat pomocí modul Import dat pomocí služby Batch provádění (BES) a vyhodnocování výsledky zapsat do různých datových umístění pomocí modul exportovat Data.

Umožňuje importovat Data a Export dat moduly, můžete číst z a zapisovat do různých dat, které poskytují umístění, jako je například adresa URL webového prostřednictvím protokolu HTTP, dotaz Hive, databázi Azure SQL, Azure Table storage, Azure Blob storage, datového kanálu nebo místní databázi SQL.

Toto téma používá "vzorku 5: Train, testovací, Evaluate pro binární klasifikaci: pro dospělé datovou sadu" ukázkové a předpokládá datovou sadu již byla načtena do tabulky Azure SQL s názvem censusdata.

## <a name="create-the-training-experiment"></a>Vytvoření experimentu školení
Při otevření "vzorku 5: Train, testovací, Evaluate pro binární klasifikaci: pro dospělé datovou sadu" Ukázka používá ukázkovou datovou sadu pro dospělé úplné zjišťování příjem binární klasifikace. A na plátno experimentu bude vypadat podobně jako na následujícím obrázku:

![Počáteční konfigurace experimentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Přečíst data z tabulky Azure SQL:

1. Odstraňte modul datovou sadu.
2. Do pole Hledat součásti typ importu.
3. V seznamu výsledků přidat *importovat Data* modulu na plátno experimentu.
4. Připojit výstup *importovat Data* modulu vstup z *vyčištění chybějících dat* modulu.
5. V podokně vlastnosti, vyberte **Azure SQL Database** v **zdroj dat** rozevíracího seznamu.
6. V **název databázového serveru**, **název databáze**, **uživatelské jméno**, a **heslo** pole, zadejte příslušné informace pro vaše databáze.
7. V poli dotazu databázi zadejte následující dotaz.
   
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
8. V dolní části plátna experimentu, klikněte na tlačítko **spustit**.

## <a name="create-the-predictive-experiment"></a>Vytvořit prediktivní experiment
Další nastavíte prediktivní experiment, ze kterého nasadíte webovou službu.

1. V dolní části plátna experimentu, klikněte na tlačítko **nastavit webové služby** a vyberte **prediktivní webové služby (doporučeno)**.
2. Odeberte *vstup webové služby* a *webové služby výstupní moduly* z prediktivní experiment. 
3. Do pole Hledat součásti typu export.
4. V seznamu výsledků přidat *Export dat* modulu na plátno experimentu.
5. Připojit výstup *Score Model* modulu vstup z *Export dat* modulu. 
6. V podokně vlastnosti, vyberte **Azure SQL Database** v rozevírací nabídce cílový data.
7. V **název databázového serveru**, **název databáze**, **název uživatelského účtu serveru**, a **heslo uživatelského účtu serveru** pole, zadejte příslušné informace pro vaši databázi.
8. V **čárkami oddělený seznam sloupce, které chcete uložit** zadejte popisky vyhodnocení.
9. V **pole název tabulky dat**, zadejte dbo. ScoredLabels. Pokud tabulka neexistuje, vytvoří se při spuštění experimentu nebo se nazývá webovou službu.
10. V **čárkami oddělený seznam sloupců datatable** pole, zadejte ScoredLabels.

Při psaní aplikace, která volá konečné webovou službu, můžete zadat různé vstupní dotaz nebo cílové tabulky v době běhu. Při konfiguraci těchto vstupy a výstupy, funkce parametry webové služby používá k nastavení *importovat Data* modulu *zdroj dat* vlastnost a *Export dat* data režimu Vlastnost cílové.  Další informace o parametry webové služby, najdete v článku [vstupní parametry webové služby AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) na webu Cortana Intelligence a Machine Learning blogu.

Konfigurovat parametry webové služby pro import dotazu a cílové tabulky:

1. V podokně vlastností *importovat Data* modulu, klikněte na ikonu v horní části napravo od **databázový dotaz** pole a vyberte **nastavit jako parametr webové služby**.
2. V podokně vlastností *Export dat* modulu, klikněte na ikonu v horní části napravo od **název tabulky dat** pole a vyberte **nastavit jako parametr webové služby**.
3. V dolní části *Export dat* podokno vlastností modulu v **parametry webové služby** části, klikněte na databázový dotaz a přejmenujte ji dotazu.
4. Klikněte na tlačítko **název tabulky dat** a přejmenujte ji **tabulky**.

Až skončíte, experimentu by měl vypadat podobně jako na následujícím obrázku:

![Poslední vzhled experimentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Experiment teď můžete nasadit jako webovou službu.

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit na klasickém nebo novou webovou službu.

### <a name="deploy-a-classic-web-service"></a>Nasazení Classic webové služby
Nasadit jako webovou službu, Classic a vytvoření aplikace ho zpracovat:

1. V dolní části plátna experimentu klikněte na tlačítko spustit.
2. Po dokončení spuštění, klikněte na tlačítko **nasazení webové služby** a vyberte **nasazení webové služby [Classic]**.
3. Na řídicím panelu webové služby vyhledejte klíč rozhraní API. Zkopírujte a uložte jej pro pozdější použití.
4. V **výchozí koncový bod** tabulky, klikněte **Batch Execution** odkazu k otevření stránce nápovědy k rozhraní API.
5. Ve Visual Studiu Vytvořte konzolovou aplikaci C#: **nový** > **projektu** > **Visual C#** > **Windows Klasický desktopový** > **konzoly aplikace (rozhraní .NET Framework)**.
6. Na stránce nápovědy k rozhraní API najít **ukázkový kód** v dolní části stránky.
7. Zkopírujte a vložte ukázkový kód C# do souboru Program.cs a odeberte všechny odkazy na úložiště objektů blob.
8. Aktualizujte hodnotu *apiKey* proměnné s klíčem rozhraní API předtím uložili.
9. Vyhledejte deklaraci požadavku a aktualizujte hodnoty parametry webové služby, které jsou předávány *importovat Data* a *Export dat* moduly. V takovém případě použijte původní dotaz ale definovat nový název tabulky.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Spusťte aplikaci. 

Při dokončení spuštění, se přidá novou tabulku k databázi obsahující vyhodnocování výsledky.

### <a name="deploy-a-new-web-service"></a>Nasadit novou webovou službu

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které, můžete nasazení webové služby. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

Nasadit jako novou webovou službu a vytvořte aplikaci pro ho využívají:

1. V dolní části plátna experimentu, klikněte na tlačítko **spustit**.
2. Po dokončení spuštění, klikněte na tlačítko **nasazení webové služby** a vyberte **nasazení [nové] webové služby**.
3. Na stránce experimentu nasazení zadejte název vaší webové služby a vybrat cenový plán, a pak klikněte na **nasadit**.
4. Na **rychlý Start** klikněte na tlačítko **spotřebě**.
5. V **ukázkový kód** klikněte na tlačítko **Batch**.
6. Ve Visual Studiu Vytvořte konzolovou aplikaci C#: **nový** > **projektu** > **Visual C#** > **Windows Klasický desktopový** > **konzoly aplikace (rozhraní .NET Framework)**.
7. Zkopírujte a vložte ukázkový kód C# do souboru Program.cs.
8. Aktualizujte hodnotu *apiKey* proměnné s **primární klíč** umístěný v **informace o základní spotřeby** části.
9. Vyhledejte *scoreRequest* deklarace a aktualizujte hodnoty parametry webové služby, které jsou předávány *importovat Data* a *Export dat* moduly. V takovém případě použijte původní dotaz ale definovat nový název tabulky.
   
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

