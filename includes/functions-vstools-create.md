---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 477f3cf270377bc1341e65ab500093e05277afa8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657558"
---
Šablona projektu Azure Functions v Sadě Visual Studio vytvoří projekt, který můžete publikovat do aplikace funkce v Azure. Pomocí aplikace funkce můžete seskupit funkce jako logickou jednotku pro snadnější správu, nasazení, škálování a sdílení prostředků.

1. V nabídce Visual Studio vyberte **Soubor** > **nového** > **projektu**.

1. V **části Vytvoření nového projektu**zadejte do vyhledávacího pole *funkce,* zvolte šablonu **Azure Functions** a pak vyberte **Další**.

1. V **okně Konfigurace nového projektu**zadejte název **projektu** a vyberte **vytvořit**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

1. Pro **vytvoření nového** nastavení aplikace funkce Azure použijte hodnoty v následující tabulce:

    | Nastavení      | Hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Za běhu funkcí** | **Funkce Azure <br />v2 (.NET Core)** | Tato hodnota vytvoří projekt funkce, který používá runtime verze 2.x Azure Functions, který podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v [tématu Azure Functions runtime verze přehled](../articles/azure-functions/functions-versions.md).   |
    | **Šablona funkce** | **Aktivační událost PROTOKOLU HTTP** | Tato hodnota vytvoří funkci aktivovanou požadavkem HTTP. |
    | **Účet úložiště**  | **Emulátor úložiště** | Vzhledem k tomu, že funkce Azure vyžaduje účet úložiště, jeden se přidá nebo vytvoří při publikování projektu do Azure. Aktivační událost HTTP nepoužívá připojovací řetězec účtu Azure Storage. všechny ostatní typy aktivačních událostí vyžadují platný připojovací řetězec účtu Azure Storage.  |
    | **Přístupová práva** | **Anonymní** | Vytvořenou funkci může aktivovat libovolný klient bez zadání klíče. Toto nastavení autorizace usnadňuje testování nových funkcí. Další informace o klíčích a autorizaci naleznete v [tématu Autorizace klíčů](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) a [vazby HTTP a webhooku](../articles/azure-functions/functions-bindings-http-webhook.md). |
    

    
    ![Nastavení projektu Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Ujistěte se, že jste nastavili **přístupová práva** na **anonymní**. Pokud zvolíte výchozí úroveň **funkce**, budete muset prezentovat [funkční klíč](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) v požadavcích na přístup ke koncovému bodu funkce.

1. Vyberte **Vytvořit,** chcete-li vytvořit projekt funkce a aktivační funkci HTTP.
