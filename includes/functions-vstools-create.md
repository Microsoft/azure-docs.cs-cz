---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84730983"
---
Šablona projektu Azure Functions v aplikaci Visual Studio vytvoří projekt, který můžete publikovat do aplikace Function App v Azure. Pomocí aplikace Function App můžete seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

1. V nabídce sady Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**.

1. V části **vytvořit nový projekt** zadejte do vyhledávacího pole *funkce* , vyberte šablonu **Azure Functions** a pak vyberte **Další**.

1. V **konfiguraci nového projektu** zadejte **název projektu** a pak vyberte **vytvořit**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. V poli **vytvořit nové nastavení aplikace Azure Functions** použijte hodnoty v následující tabulce:

    | Nastavení      | Hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Modul runtime Functions** | **Azure Functions V3 <br /> (.NET Core)** | Tato hodnota vytvoří projekt funkce, který používá modul runtime verze 3. x Azure Functions, který podporuje .NET Core 3. x. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v tématu [Přehled verzí modulu runtime Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Šablona funkce** | **Trigger HTTP** | Tato hodnota vytvoří funkci spuštěnou požadavkem HTTP. |
    | **Účet úložiště (AzureWebJobsStorage)**  | **Emulátor úložiště** | Vzhledem k tomu, že funkce Azure vyžaduje účet úložiště, je jeden přiřazený nebo vytvořený při publikování projektu do Azure. Aktivační událost protokolu HTTP nepoužívá připojovací řetězec Azure Storage účtu; všechny ostatní typy triggerů vyžadují platný připojovací řetězec účtu Azure Storage.  |
    | **Úroveň autorizace** | **Anonymous** | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizaci najdete v tématu [autorizační klíče](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) a [vazby HTTP a Webhooku](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Nastavení projektu Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Ujistěte se, že jste nastavili **úroveň autorizace** na **anonymní**. Pokud zvolíte výchozí úroveň **funkce**, budete muset při žádosti o přístup ke koncovému bodu funkce prezentovat [klíč funkce](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) .

1. Vyberte **vytvořit** a vytvořte tak projekt funkce a funkci triggeru http.
