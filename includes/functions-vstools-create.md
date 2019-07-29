---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 17b7626f79d7d356e3e8f3440e4a6526f2df776d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68593258"
---
Šablona projektu Azure Functions v sadě Visual Studio vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Funkci aplikace Function App můžete použít k seskupení funkcí jako logické jednotky pro správu, nasazení a sdílení prostředků.

1. V aplikaci Visual Studio v nabídce **soubor** vyberte **Nový** > **projekt**.

1. V dialogovém okně **vytvořit nový projekt** vyhledejte `functions`, vyberte šablonu **Azure Functions** a vyberte **Další**.

1. Zadejte název projektu a vyberte **vytvořit**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. V části **Vytvoření nové aplikace Azure Functions**použijte následující možnosti:

    + **Azure Functions v2 (.NET Core)** 1
    + **Aktivační událost HTTP**
    + **Účet úložiště**: **Emulátor úložiště**
    + **Úroveň autorizace**: **Anonymous** 

    | Možnost      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | Modul runtime Functions | **Azure Functions 2. x <br />(.NET Core)** | Toto nastavení vytvoří projekt funkce, který používá modul runtime verze 2. x Azure Functions, který podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v tématu [cílová verze modulu runtime Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | Šablona funkce | **Aktivační událost HTTP** | Toto nastavení vytvoří funkci spuštěnou požadavkem HTTP. |
    | **Účet úložiště**  | **Emulátor úložiště** | Aktivační událost protokolu HTTP nepoužívá připojení účtu Azure Storage. Všechny ostatní typy triggerů vyžadují platný připojovací řetězec účtu úložiště. Vzhledem k tomu, že funkce vyžadují účet úložiště, je jeden přiřazen nebo vytvořen při publikování projektu do Azure. |
    | **Úroveň autorizace** | **Anonymous** | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizace najdete v části věnovaném [klíčům autorizace](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) v tématu [HTTP a webhookové vazby](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Ujistěte se, že jste nastavili `Anonymous` **úroveň autorizace** na. Pokud zvolíte výchozí úroveň `Function`, budete muset při žádosti o přístup ke koncovému bodu funkce prezentovat [klíč funkce](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) .
    
4. Vyberte **vytvořit** k vytvoření projektu funkce a funkce aktivované protokolem HTTP.
