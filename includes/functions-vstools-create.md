---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
origin.date: 03/05/2019
ms.date: 03/25/2019
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403647"
---
Šablona projektu Azure Functions v sadě Visual Studio vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Můžete použít aplikaci function app na skupiny funkce jako logickou jednotku pro správu, nasazování a sdílení prostředků.

1. V sadě Visual Studio na **souboru** nabídce vyberte možnost **nový** > **projektu**.

2. V **nový projekt** dialogu **nainstalováno** > **Visual C#**   >  **cloudu**  >  **Azure Functions**. Zadejte název pro váš projekt a vyberte **OK**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

    ![Dialogové okno Nový projekt pro vytvoření funkce v sadě Visual Studio](./media/functions-vstools-create/functions-vs-new-project.png)

3. Použijte nastavení uvedená v tabulce pod obrázkem.

    ![Dialogové okno nové funkce v sadě Visual Studio](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Nastavení      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verze** | Azure Functions 2.x <br />(.NET Core) | Toto nastavení se vytvoří projekt funkce, který používá modul runtime verze 2.x Azure Functions, která podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v tématu [verze modulu runtime Azure Functions cílové](../articles/azure-functions/functions-versions.md).   |
    | **Šablona** | Trigger HTTP | Toto nastavení se vytvoří funkce aktivované službou požadavek HTTP. |
    | **Účet úložiště**  | Emulátor úložiště | Aktivační událost HTTP nepoužívá připojení k účtu úložiště Azure. Všechny ostatní typy triggerů vyžadují platný připojovací řetězec účtu úložiště. |
    | **Přístupová práva** | Anonymní | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizace najdete v části věnovaném [klíčům autorizace](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) v tématu [HTTP a webhookové vazby](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Ujistěte se, že nastavíte **přístupová práva** k `Anonymous`. Pokud se rozhodnete výchozí úroveň `Function`, je nutné k dispozici [klíč funkce](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) v žádosti o přístup ke koncovému bodu funkce.
    
4. Vyberte **OK** vytvořit projekt funkcí a funkci aktivovanou protokolem HTTP.

