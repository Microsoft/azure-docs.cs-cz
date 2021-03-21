---
title: Připojení funkcí k Azure Storage pomocí sady Visual Studio
description: Naučte se, jak přidat výstupní vazbu pro připojení funkcí knihovny tříd C# ke frontě Azure Storage pomocí sady Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 635392212027c73e5aa954eb671be31228796a0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185126"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Připojení funkcí k Azure Storage pomocí sady Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

V tomto článku se dozvíte, jak pomocí sady Visual Studio připojit funkci, kterou jste vytvořili v [předchozím článku rychlý Start] , a Azure Storage. Výstupní vazba, kterou do této funkce přidáte, zapisuje data z požadavku HTTP do zprávy ve frontě úložiště Azure Queue. 

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Pro snazší použití účtu úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Předpoklady

Než začnete tento článek, musíte: 

 - Vyplňte [část 1 rychlého startu sady Visual Studio](./functions-create-your-first-function-visual-studio.md). 

- Přihlaste se ke svému předplatnému Azure ze sady Visual Studio.

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

V [předchozím článku rychlý Start](./create-first-function-vs-code-csharp.md)jste vytvořili aplikaci funkcí v Azure spolu s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do *local.settings.jsv* souboru. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**. 

1. V části **Akce** vyberte **Upravit Azure App Service nastavení**. 

    ![Upravit nastavení aplikace](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. V části **AzureWebJobsStorage** Zkopírujte hodnotu **vzdáleného** řetězce na **místní** a pak vyberte **OK**. 

Vazba úložiště, která používá `AzureWebJobsStorage` nastavení pro připojení, se nyní může připojit k úložišti fronty, pokud je spuštěno místně.

## <a name="register-binding-extensions"></a>Registrace rozšíření vazeb

Vzhledem k tomu, že používáte výstupní vazbu úložiště front, budete potřebovat rozšíření vazby úložiště nainstalované před spuštěním projektu. S výjimkou triggerů HTTP a Timer jsou vazby implementovány jako balíčky rozšíření. 

1. V nabídce **nástroje** vyberte možnost **Správce balíčků NuGet**  >  **Konzola správce balíčků**. 

1. V konzole nástroje spusťte následující příkaz [Install-Package](/nuget/tools/ps-ref-install-package) pro instalaci rozšíření úložiště:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Po definování vazby můžete použít `name` vazbu na k přístupu jako atributu v signatuře funkce. Pomocí výstupní vazby nemusíte pro ověřování používat kód Azure Storage SDK, získat odkaz na frontu nebo zapisovat data. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Místní spuštění funkce

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

`outqueue`Při prvním použití výstupní vazby se ve vašem účtu úložiště vytvoří nová fronta s názvem. modul runtime Functions. Pomocí Průzkumníka cloudu ověříte, že se vytvořila fronta spolu s novou zprávou.

## <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. V aplikaci Visual Studio v nabídce **zobrazení** vyberte **Průzkumník cloudu**.

1. V **Průzkumníku cloudu** rozbalte předplatné Azure a **účty úložiště** a potom rozbalte účet úložiště, který funkce používá. Pokud si název účtu úložiště nepamatujete, ověřte `AzureWebJobsStorage` nastavení připojovacího řetězce v *local.settings.jsv* souboru.  

1. Rozbalte uzel **fronty** a pak dvakrát klikněte **na frontu s názvem front** -to pro zobrazení obsahu fronty v aplikaci Visual Studio. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí hodnotou `name` (*Azure*), zpráva fronty je *Name passed to the function: Azure*.

    ![Zpráva fronty zobrazená v Průzkumník služby Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Znovu spusťte funkci, odešlete další žádost a ve frontě se zobrazí nová zpráva.  

Teď je čas na opětovné publikování aktualizované aplikace Function App do Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Opětovné nasazení a ověření aktualizované aplikace

1. V **Průzkumník řešení** klikněte pravým tlačítkem na projekt a vyberte **publikovat** a pak klikněte na **publikovat** , aby se projekt znovu publikoval do Azure.

1. Po dokončení nasazení můžete znovu použít prohlížeč k otestování znovu nasazené funkce. Stejně jako dřív přidejte řetězec dotazu `&name=<yourname>` k adrese URL.

1. Opětovným [zobrazením zprávy ve frontě úložiště](#examine-the-output-queue) ověřte, zda výstupní vazba znovu generuje novou zprávu ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Další informace o vývoji funkcí naleznete v tématu [vývoj Azure Functions pomocí sady Visual Studio](functions-develop-vs.md).

Dále byste měli povolit Application Insights monitorování aplikace Function App:

> [!div class="nextstepaction"]
> [Povolení integrace Application Insights](configure-monitoring.md#add-to-an-existing-function-app)

[Azure Storage Explorer]: https://storageexplorer.com/
[předchozí článek rychlý Start]: functions-create-your-first-function-visual-studio.md