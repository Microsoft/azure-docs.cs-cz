---
title: Vytvoření první funkce v Azure pomocí sady Visual Studio | Dokumentace Microsoftu
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí Azure Functions Tools for Visual Studio.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/13/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 6fa56a9978950d28f6e25192f2044b823050f718
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="create-your-first-function-using-visual-studio"></a>Vytvoření první funkce pomocí sady Visual Studio

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/overview/serverless-computing/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci.

V tomto článku se dozvíte, jak pomocí nástrojů sady Visual Studio 2017 pro službu Azure Functions místně vytvořit a otestovat funkci Hello World. Kód funkce potom publikujete do Azure. Tyto nástroje jsou dostupné jako součást sady funkcí Azure – vývoj v sadě Visual Studio 2017.

![Kód služby Azure Functions v projektu sady Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

Toto téma obsahuje [video](#watch-the-video), které ilustruje stejný základní postup.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Instalaci sady [Visual Studio 2017 verze 15.5](https://www.visualstudio.com/vs/) nebo novější včetně sady funkcí **Vývoj pro Azure**.

    ![Instalace sady Visual Studio 2017 se sadou funkcí Vývoj pro Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

    Pokud už máte sadu Visual Studio nainstalovanou, ujistěte se, že jste nainstalovali případné čekající aktualizace. 

* Pokud jste nainstalovali sadu funkcí Vývoj pro Azure společně se sadou Visual Studio 2017 verze 15.4 nebo starší, možná bude také potřeba [aktualizovat nástroje Azure Functions](functions-develop-vs.md#check-your-tools-version). 

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio vytvoří projekt a v něm třídu, která obsahuje často používaný kód pro zvolený typ funkce. Atribut **FunctionName** metody nastavuje název funkce. Atribut **HttpTrigger** určuje, že je funkce aktivována požadavkem HTTP. Často používaný kód odešle odpověď HTTP obsahující hodnotu z textu žádosti nebo řetězce dotazu. Použitím příslušných atributů pro metodu můžete přidat vstupní a výstupní vazbu funkce. Další informace najdete v části [Triggery a vazby](functions-dotnet-class-library.md#triggers-and-bindings) v [referenčních informacích pro vývojáře v jazyce C# v Azure Functions](functions-dotnet-class-library.md).

![Soubor s kódem funkce](./media/functions-create-your-first-function-visual-studio/functions-code-page.png)

Teď máte vytvořený projekt funkcí a funkci aktivovanou protokolem HTTP, takže ji můžete otestovat na místním počítači.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.  

1. Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.  

    ![Místní modul runtime Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Následuje ukázka odezvy na místní požadavek GET vrácené funkcí v prohlížeči: 

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. Pokud chcete zastavit ladění, stiskněte Shift + F5.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než budete pokračovat.

Před publikováním projektu musíte mít v předplatném Azure aplikaci funkcí. Aplikaci funkcí ve svém předplatném můžete vytvořit přímo v sadě Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace funkcí ze stránky Publikovat profil. Nahraďte část adresy URL použité při místním testování funkce, která obsahuje `localhost:port`, novou základní adresou URL. Stejně jako dříve nezapomeňte k této adrese URL připojit řetězec dotazu `?name=<yourname>` a provést požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následuje ukázka odezvy na vzdálený požadavek GET vrácené funkcí v prohlížeči: 

    ![Odezva funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)

## <a name="watch-the-video"></a>Přehrát video

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>Další kroky

Pomocí sady Visual Studio jste vytvořili a publikovali aplikaci funkcí jazyka C# s jednoduchou funkcí aktivovanou protokolem HTTP. 

+ Další informace o vývoji funkcí jako knihoven tříd .NET najdete v tématu [Použití knihoven tříd .NET se službou Azure Functions](functions-dotnet-class-library.md). 
+ Pokud chcete zjistit, jak nakonfigurovat projekt tak, aby podporoval další typy triggerů a vazeb, přečtěte si část [Konfigurace projektu pro místní vývoj](functions-develop-vs.md#configure-the-project-for-local-development) v tématu [Azure Functions Tools for Visual Studio](functions-develop-vs.md).
+ Další informace o místním testování a ladění pomocí základních funkcí služby Azure Functions najdete v tématu [Místní kódování a testování služby Azure Functions](functions-run-local.md). 
