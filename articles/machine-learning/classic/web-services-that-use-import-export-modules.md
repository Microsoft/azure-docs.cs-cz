---
title: 'ML Studio (Classic): data importu/exportu ve webových službách – Azure'
description: Naučte se, jak používat importovaná data a exportovat datové moduly pro posílání a přijímání dat z webové služby.
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a963a9f10ee23c50f50e66191e92f0839c457d9c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362836"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Nasazení webových služeb Azure Machine Learning Studio (Classic), které používají moduly importu a exportu dat

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Při vytváření prediktivní experimentu obvykle přidáte vstup a výstup webové služby. Při nasazení experimentu mohou uživatelé odesílat a přijímat data z webové služby prostřednictvím vstupů a výstupů. U některých aplikací mohou být data spotřebitele k dispozici z datového kanálu nebo již nacházejí v externím zdroji dat, jako je například úložiště objektů BLOB v Azure. V těchto případech nepotřebují číst a zapisovat data pomocí vstupů a výstupů webové služby. Můžou místo toho použít službu Batch Execution Service (BES) ke čtení dat ze zdroje dat pomocí modulu import dat a zápis výsledků bodování do jiného umístění dat pomocí modulu export dat.

Datové moduly importu dat a exportu mohou číst a zapisovat do různých umístění dat, jako je webová adresa URL prostřednictvím protokolu HTTP, dotazu na podregistr, databáze v Azure SQL Database, Azure Table Storage, Azure Blob Storage, datového kanálu nebo databáze SQL Server.

V tomto tématu se používá Ukázka 5: výuka, test, vyhodnocení binární klasifikace: datová sada pro dospělé a předpokládá, že datová sada již byla načtena do tabulky Azure SQL s názvem censusdata.

## <a name="create-the-training-experiment"></a>Vytvoření školení experimentu
Když otevřete ukázku 5: výuka, test, vyhodnocení pro binární klasifikaci: datová sada pro dospělé, používá ukázkovou datovou sadu binární klasifikace pro dospělé. A experiment na plátně bude vypadat podobně jako na následujícím obrázku:

![Počáteční konfigurace experimentu.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Čtení dat z tabulky Azure SQL:

1. Odstraňte modul DataSet.
2. Do vyhledávacího pole součásti zadejte import.
3. V seznamu výsledků přidejte modul *Import dat* na plátno experimentu.
4. Připojte výstup modulu *Import dat* do vstupu modulu *Vyčištění chybějících dat* .
5. V podokně Vlastnosti vyberte v rozevíracím seznamu **zdroj dat** možnost **Azure SQL Database** .
6. Do polí **název databázového serveru**, **název databáze**, **uživatelské jméno**a **heslo** zadejte příslušné informace pro vaši databázi.
7. Do pole databázový dotaz zadejte následující dotaz.

    ```tsql
     select [age],
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
     from dbo.censusdata;
    ```
8. V dolní části plátna experimentu klikněte na **Spustit**.

## <a name="create-the-predictive-experiment"></a>Vytvoření prediktivního experimentu
Dále nastavíte prediktivní experiment, ze kterého nasadíte webovou službu.

1. V dolní části plátna experimentu klikněte na **nastavit webovou službu** a vyberte **prediktivní webovou službu [doporučeno]**.
2. Odeberte *výstupní moduly* vstupu a výstupu *webové služby* z prediktivního experimentu.
3. Do vyhledávacího pole součásti zadejte export.
4. V seznamu výsledků přidejte modul *Export dat* na plátno experimentu.
5. Připojte výstup modulu určení *skóre modelu* se vstupem modulu *Export dat* .
6. V podokně Vlastnosti vyberte v rozevíracím seznamu cíl data možnost **Azure SQL Database** .
7. Do polí **název**databázového serveru, název **databáze**, **název účtu uživatele serveru**a **heslo účet uživatele serveru** zadejte příslušné informace pro vaši databázi.
8. V **seznamu sloupců, které mají být uloženy, do pole oddělených čárkami** zadejte popisky s skóre.
9. Do **pole název tabulky dat**zadejte dbo. ScoredLabels. Pokud tabulka neexistuje, vytvoří se při spuštění experimentu nebo volání webové služby.
10. Do **textového pole seznam sloupců DataTable** zadejte ScoredLabels.

Při psaní aplikace, která volá konečnou webovou službu, můžete v době běhu zadat jiné vstupní dotazy nebo cílovou tabulku. Chcete-li nakonfigurovat tyto vstupy a výstupy, použijte funkci parametry webové služby a nastavte vlastnost *zdroj dat* modulu *Import dat* a vlastnost cíl pro *Export* dat v režimu dat.  Další informace o parametrech webové služby najdete v tématu [Azure Machine Learning Studio položky parametrů webové služby](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) na blogu Cortana Intelligence a Machine Learning.

Konfigurace parametrů webové služby pro dotaz import a cílovou tabulku:

1. V podokně vlastnosti modulu *importovat data* klikněte na ikonu v pravém horním rohu pole **databázový dotaz** a vyberte možnost **nastavit jako parametr webové služby**.
2. V podokně vlastnosti modulu *exportovat data* klikněte na ikonu v pravém horním rohu pole **název tabulky dat** a vyberte možnost **nastavit jako parametr webové služby**.
3. V dolní části podokna Vlastnosti modulu *exportu dat* klikněte v části **parametry webové služby** na dotaz databáze a přejmenujte dotaz na něj.
4. Klikněte na **název tabulky dat** a přejmenujte ji na **tabulka**.

Až skončíte, váš experiment by měl vypadat podobně jako na následujícím obrázku:

![Konečný pohled experimentu.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Nyní můžete experiment nasadit jako webovou službu.

## <a name="deploy-the-web-service"></a>Nasazení webové služby
Můžete nasadit na klasickou nebo novou webovou službu.

### <a name="deploy-a-classic-web-service"></a>Nasazení klasické webové služby
Nasazení jako klasické webové služby a vytvoření aplikace pro její využívání:

1. V dolní části plátna experimentu klikněte na spustit.
2. Po dokončení běhu klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [Classic]**.
3. Na řídicím panelu webové služby Najděte svůj klíč rozhraní API. Zkopírujte a uložte ho pro pozdější použití.
4. V tabulce **výchozích koncových bodů** klikněte na odkaz **spuštění dávky** a otevřete stránku s usnadněníem rozhraní API.
5. V aplikaci Visual Studio vytvořte konzolovou aplikaci v jazyce c#: **Nový**  >  **projekt**  >  **Visual C#**  >  **Windows Classic Desktop**  >  **Konzolová aplikace (.NET Framework)**.
6. Na stránce s nápovědě k rozhraní API vyhledejte část **vzorový kód** v dolní části stránky.
7. Zkopírujte a vložte ukázkový kód C# do souboru Program.cs a odeberte všechny odkazy na úložiště objektů BLOB.
8. Aktualizujte hodnotu proměnné *apiKey* pomocí klíče rozhraní API, který jste uložili dříve.
9. Vyhledejte deklaraci žádosti a aktualizujte hodnoty parametrů webové služby, které jsou předány do datových modulů *Import dat* a *Export* . V takovém případě použijete původní dotaz, ale nadefinujete nový název tabulky.

    ```csharp
    var request = new BatchExecutionRequest()
    {
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
        }
    };
    ```
10. Aplikaci spusťte.

Po dokončení spuštění je do databáze obsahující výsledky bodování přidána nová tabulka.

### <a name="deploy-a-new-web-service"></a>Nasazení nové webové služby

> [!NOTE]
> Nasazení nové webové služby vyžaduje dostatečná oprávnění v předplatném, na které nasazujete webovou službu. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).

Nasazení jako nové webové služby a vytvoření aplikace pro její využívání:

1. V dolní části plátna experimentu klikněte na **Spustit**.
2. Po dokončení běhu klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [New]**.
3. Na stránce nasazení experimentu zadejte název vaší webové služby a vyberte cenový tarif a klikněte na **nasadit**.
4. Na stránce **rychlý Start** klikněte na možnost **spotřebovat**.
5. V části **vzorový kód** klikněte na **Batch**.
6. V aplikaci Visual Studio vytvořte konzolovou aplikaci v jazyce c#: **Nový**  >  **projekt**  >  **Visual C#**  >  **Windows Classic Desktop**  >  **Konzolová aplikace (.NET Framework)**.
7. Zkopírujte ukázkový kód C# a vložte ho do souboru Program.cs.
8. Aktualizujte hodnotu proměnné *apiKey* pomocí **primárního klíče** , který je umístěný v části **informace o základní spotřebě** .
9. Vyhledejte deklaraci *scoreRequest* a aktualizujte hodnoty parametrů webové služby, které jsou předány do datových modulů *Import dat* a *Export* . V takovém případě použijete původní dotaz, ale nadefinujete nový název tabulky.

    ```csharp
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
    ```
10. Aplikaci spusťte.

