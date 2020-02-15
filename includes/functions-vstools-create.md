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
ms.openlocfilehash: 3d93d3aa3e4e646f8e054f96f17bbe4a011d422d
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211415"
---
Šablona projektu Azure Functions v sadě Visual Studio vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Pomocí aplikace Function App můžete seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

1. V aplikaci Visual Studio v nabídce **soubor** vyberte **Nový** > **projekt**.

1. V dialogovém okně **vytvořit nový projekt** vyhledejte `functions`, zvolte šablonu **Azure Functions** a vyberte **Další**.

1. Zadejte název projektu a vyberte **vytvořit**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. V části **Vytvoření nové aplikace Azure Functions**použijte následující možnosti:

    + **Azure Functions v2 (.NET Core)**
    + **Aktivační událost HTTP**
    + **Účet úložiště**: **emulátor úložiště**
    + **Úroveň autorizace**: **anonymní** 

    | Možnost      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Modul runtime Functions** | **Azure Functions 2. x <br />(.NET Core)** | Toto nastavení vytvoří projekt funkce, který používá modul runtime verze 2. x Azure Functions, který podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v tématu [cílová verze modulu runtime Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Šablona funkce** | **Aktivační událost HTTP** | Toto nastavení vytvoří funkci spuštěnou požadavkem HTTP. |
    | **Účet úložiště**  | **Emulátor úložiště** | Aktivační událost protokolu HTTP nepoužívá připojení účtu Azure Storage. Všechny ostatní typy triggerů vyžadují platný připojovací řetězec účtu úložiště. Vzhledem k tomu, že funkce vyžadují účet úložiště, je jeden přiřazen nebo vytvořen při publikování projektu do Azure. |
    | **Úroveň autorizace** | **Anonymous** | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizace najdete v části věnovaném [klíčům autorizace](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) v tématu [HTTP a webhookové vazby](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Ujistěte se, že jste nastavili **úroveň autorizace** na `Anonymous`. Pokud zvolíte výchozí úroveň `Function`, je nutné prezentovat [klíč funkce](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) v žádosti o přístup ke koncovému bodu funkce.
    
4. Vyberte **vytvořit** k vytvoření projektu funkce a funkce aktivované protokolem HTTP.
