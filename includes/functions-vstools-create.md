---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 034e966d259f1ca5f22eec5935013de32c883b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056558"
---
Šablona projektu Azure Functions v Sadě Visual Studio vytvoří projekt, který můžete publikovat do aplikace funkce v Azure. Pomocí aplikace funkce můžete seskupit funkce jako logickou jednotku pro snadnější správu, nasazení, škálování a sdílení prostředků.

1. V sadě Visual Studio vyberte v nabídce **Soubor** **položku Nový** > **projekt**.

1. V **části Vytvoření nového projektu**zadejte *funkce* do vyhledávacího pole a pak zvolte šablonu **Azure Functions.**

1. V **okně Konfigurace nového projektu**zadejte název **projektu** a vyberte **vytvořit**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. Pro **nový projekt &lt;-&gt; ** nastavení názvu projektu použijte hodnoty v následující tabulce:

    | Nastavení      | Hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Za běhu funkcí** | **Funkce Azure <br />v2 (.NET Core)** | Tato hodnota vytvoří projekt funkce, který používá runtime verze 2.x Azure Functions, který podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v [tématu Azure Functions runtime verze přehled](../articles/azure-functions/functions-versions.md).   |
    | **Šablona funkce** | **Aktivační událost PROTOKOLU HTTP** | Tato hodnota vytvoří funkci aktivovanou požadavkem HTTP. |
    | **Účet úložiště**  | **Emulátor úložiště** | Vzhledem k tomu, že funkce Azure vyžaduje účet úložiště, jeden se přidá nebo vytvoří při publikování projektu do Azure. Aktivační událost HTTP nepoužívá připojovací řetězec účtu Azure Storage. všechny ostatní typy aktivačních událostí vyžadují platný připojovací řetězec účtu Azure Storage.  |
    | **Přístupová práva** | **Anonymní** | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizaci naleznete v [tématu Autorizace klíčů](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) a [vazby HTTP a webhooku](../articles/azure-functions/functions-bindings-http-webhook.md). |
    

    
    ![Nastavení projektu Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Ujistěte se, že jste nastavili **přístupová práva** na **anonymní**. Pokud zvolíte výchozí úroveň **funkce**, budete muset prezentovat [funkční klíč](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) v požadavcích na přístup ke koncovému bodu funkce.

1. Výběrem **možnosti OK** vytvořte projekt funkce a funkci spouštěnou protokolem HTTP.
