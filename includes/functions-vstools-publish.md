---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6cbf24c56458ab8b3b6c7b44bedbd8b48d4677b3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739210"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

2. Vyberte **Aplikace Azure Function**, zvolte **Vytvořit novou** a potom vyberte **Publikovat**.

    ![Výběr cíle publikování](./media/functions-vstools-publish/functions-vstools-create-new-function-app.png)

2. Pokud jste ještě nepřipojili Visual Studio k účtu Azure, vyberte **Přidat účet...** .

3. V dialogovém okně **Vytvořit App Service** použijte nastavení **Hostování** podle tabulky pod obrázkem:

    ![Dialogové okno Vytvořit službu App Service](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Plán služby App Service](../articles/azure-functions/functions-scale.md)** | Plán Consumption | Po vytvoření nového plánu kliknutím na **Nový** nezapomeňte vybrat **Spotřeba** v části **Velikost**. Zvolte také [umístění](https://azure.microsoft.com/regions/) v **oblasti** ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají.  |
    | **[Účet úložiště](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** | Účet úložiště pro obecné účely | Modul runtime Functions vyžaduje účet úložiště Azure. Kliknutím na **Nový** vytvořte účet úložiště pro obecné účely, nebo použijte už existující účet.  |

4. Kliknutím na **Vytvořit** vytvořte aplikaci funkce a související prostředky v Azure a nasaďte kód projektu funkce. 

5. Jakmile se nasazení dokončí, poznamenejte si hodnotu **Adresa URL webu**. Je to adresa vaší aplikace funkcí v Azure.

    ![Zpráva o úspěšném publikování](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
