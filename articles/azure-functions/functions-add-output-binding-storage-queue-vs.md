---
title: Připojení funkcí k úložišti Azure pomocí Visual Studia
description: Zjistěte, jak přidat výstupní vazbu pro připojení funkcí knihovny tříd jazyka C# do fronty azure storage pomocí Sady Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 171479a0f60741b545a171315e99cc5e4e8bc843
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74849203"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Připojení funkcí k úložišti Azure pomocí Visual Studia

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Tento článek ukazuje, jak pomocí Sady Visual Studio připojit funkci, kterou jste vytvořili v [předchozím článku rychlého startu,] k Úložišti Azure. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě úložiště fronty Azure. 

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Chcete-li to usnadnit, použijte účet úložiště, který jste vytvořili pomocí aplikace funkce. Připojení k tomuto účtu je již `AzureWebJobsStorage`uloženo v nastavení aplikace s názvem .  

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte: 

 - Dokončení [části 1 rychlého startu sady Visual Studio](./functions-create-first-function-vs-code.md). 

- Přihlaste se k předplatnému Azure z Visual Studia.

## <a name="download-the-function-app-settings"></a>Stažení nastavení aplikace pro funkce

V [předchozím článku rychlého startu](functions-create-first-function-vs-code.md)jste v Azure vytvořili aplikaci funkcí spolu s požadovaným účtem úložiště. Připojovací řetězec pro tento účet se bezpečně uchovává v nastavení aplikace v Azure. V tomto článku zapisujete zprávy do fronty úložiště ve stejném účtu. Chcete-li se připojit k účtu úložiště při místním spuštění funkce, je nutné stáhnout nastavení aplikace do souboru *local.settings.json.* 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**. 

1. V části **Akce**vyberte **Upravit nastavení služby Azure App Service**. 

    ![Úprava nastavení aplikace](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. V **části AzureWebJobsStorage**zkopírujte hodnotu **vzdáleného** řetězce do **local**a pak vyberte **OK**. 

Vazba úložiště, která `AzureWebJobsStorage` používá nastavení pro připojení, se nyní může připojit k úložišti fronty při místním spuštění.

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupní vazbu úložiště fronty, budete potřebovat rozšíření vazeb úložiště nainstalované před spuštěním projektu. S výjimkou aktivačních událostí protokolu HTTP a časovače jsou vazby implementovány jako balíčky rozšíření. 

1. V nabídce **Nástroje** vyberte konzolu**Správce balíčků** **Aplikace NuGet .** >  

1. V konzole spusťte následující příkaz [Instalovat balíček](/nuget/tools/ps-ref-install-package) a nainstalujte rozšíření úložiště:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Nyní můžete přidat vazby výstupu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Po definování vazby můžete použít `name` vazbu pro přístup k ní jako atribut v podpisu funkce. Pomocí výstupní vazby není potřeba použít kód Sady Azure Storage SDK pro ověřování, získávání odkazů na frontu nebo zápis dat. Funkce runtime a fronty výstupní vazba provést tyto úkoly za vás.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Místní spuštění funkce

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nová fronta `outqueue` s názvem je vytvořena ve vašem účtu úložiště při funkci runtime funkce při prvním použití výstupní vazby. Pomocí Průzkumníka Cloudu ověříte, že fronta byla vytvořena spolu s novou zprávou.

## <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. V sadě Visual Studio z nabídky **Zobrazení** vyberte **Průzkumníka cloudu**.

1. V **Cloud Exploreru**rozbalte **své účty**předplatného Azure a úložiště a pak rozbalte účet úložiště používaný vaší funkcí. Pokud si nemůžete vzpomenout na název `AzureWebJobsStorage` účtu úložiště, zkontrolujte nastavení připojovacího řetězce v souboru *local.settings.json.*  

1. Rozbalte uzel **Fronty** a poklepáním na frontu s názvem **outqueue** zobrazte obsah fronty v sadě Visual Studio. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí hodnotou `name` (*Azure*), zpráva fronty je *Name passed to the function: Azure*.

    ![Zpráva fronty zobrazená v Průzkumníku úložiště Azure](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Spusťte funkci znovu, odešlete další požadavek a ve frontě se zobrazí nová zpráva.  

Teď je čas znovu publikovat aktualizovanou aplikaci funkcí do Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Znovu nasadit a ověřit aktualizovanou aplikaci

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na projekt a vyberte **Publikovat**a pak zvolte **Publikovat** a znovu publikujte projekt do Azure.

1. Po dokončení nasazení můžete znovu použít prohlížeč k testování znovu nasazené funkce. Stejně jako dříve přidejte řetězec `&name=<yourname>` dotazu k adrese URL.

1. Znovu [zobrazit zprávu ve frontě úložiště](#examine-the-output-queue) k ověření, že výstupní vazba znovu generuje novou zprávu ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP tak, aby zapisovali data do fronty úložiště. Další informace o vývoji funkcí najdete v [tématu Vývoj funkcí Azure pomocí sady Visual Studio](functions-develop-vs.md).

Dále byste měli povolit monitorování Application Insights pro vaši aplikaci funkcí:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[předchozí článek o rychlém startu]: functions-create-your-first-function-visual-studio.md
