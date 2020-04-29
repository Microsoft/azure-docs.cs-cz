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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80657558"
---
Šablona projektu Azure Functions v aplikaci Visual Studio vytvoří projekt, který můžete publikovat do aplikace Function App v Azure. Pomocí aplikace Function App můžete seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

1. V aplikaci Visual Studio v nabídce **soubor** vyberte **Nový** > **projekt**.

1. V části **vytvořit nový projekt**zadejte do vyhledávacího pole *funkce* a pak zvolte šablonu **Azure Functions** .

1. V **konfiguraci nového projektu**zadejte **název projektu** a pak vyberte **vytvořit**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. Pro **Nový projekt – &lt;nastavení názvu&gt; projektu** použijte hodnoty v následující tabulce:

    | Nastavení      | Hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Modul runtime Functions** | **Azure Functions v2 <br />(.NET Core)** | Tato hodnota vytvoří projekt funkce, který používá modul runtime verze 2. x Azure Functions, který podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v tématu [Přehled verzí modulu runtime Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Šablona funkce** | **Trigger HTTP** | Tato hodnota vytvoří funkci spuštěnou požadavkem HTTP. |
    | **Účet úložiště**  | **Emulátor úložiště** | Vzhledem k tomu, že funkce Azure vyžaduje účet úložiště, je jeden přiřazený nebo vytvořený při publikování projektu do Azure. Aktivační událost protokolu HTTP nepoužívá připojovací řetězec Azure Storage účtu; všechny ostatní typy triggerů vyžadují platný připojovací řetězec účtu Azure Storage.  |
    | **Přístupová práva** | **Anonymní** | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizaci najdete v tématu [autorizační klíče](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) a [vazby HTTP a Webhooku](../articles/azure-functions/functions-bindings-http-webhook.md). |
    

    
    ![Nastavení projektu Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Ujistěte se, že jste nastavili **přístupová práva** **anonymní**. Pokud zvolíte výchozí úroveň **funkce**, budete muset při žádosti o přístup ke koncovému bodu funkce prezentovat [klíč funkce](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) .

1. Výběrem **OK** vytvořte projekt funkce a funkci SPUŠTĚNou protokolem HTTP.
