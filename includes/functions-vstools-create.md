---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 8c746fc86ea4d260575eb97b16d4a190b175f754
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52585664"
---
Šablona projektu Azure Functions v sadě Visual Studio vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. V sadě Visual Studio zvolte v nabídce **Soubor** možnost **Nový** > **Projekt**.

2. V dialogovém okně **Nový projekt** vyberte **Nainstalované**, rozbalte uzel **Visual C#** > **Cloud**, vyberte **Azure Functions**, zadejte **název** vašeho projektu a klikněte na **OK**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

    ![Dialogové okno Nový projekt pro vytvoření funkce v sadě Visual Studio](./media/functions-vstools-create/functions-vs-new-project.png)

3. Použijte nastavení uvedená v tabulce pod obrázkem.

    ![Dialogové okno Nová funkce v sadě Visual Studio](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Nastavení      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verze** | Azure Functions 2.x <br />(.NET Core) | Vytvoří se projekt funkce, který používá modul runtime Azure Functions verze 2.x, která podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v tématu s [přehledem verzí modulu runtime Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Šablona** | Trigger HTTP | Vytvoří se funkce, kterou aktivuje požadavek HTTP. |
    | **Účet úložiště**  | Emulátor úložiště | Trigger HTTP nepoužívá připojení účtu úložiště. Všechny ostatní typy triggerů vyžadují platný připojovací řetězec účtu úložiště. |
    | **Přístupová práva** | Anonymní | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizace najdete v části věnovaném [klíčům autorizace](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) v tématu [HTTP a webhookové vazby](../articles/azure-functions/functions-bindings-http-webhook.md). |
4. Kliknutím na **OK** vytvoříte projekt funkce a funkci aktivovanou HTTP.
