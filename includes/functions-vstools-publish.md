---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 07/17/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e8cb5dadb7eed5eb33c15d7a4d1d4640a10099c9
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "40245781"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

2. Vyberte **Aplikace Azure Function**, zvolte **Vytvořit novou** a potom vyberte **Publikovat**.

    ![Výběr cíle publikování](./media/functions-vstools-publish/functions-vstools-create-new-function-app.png)

3. Pokud jste ještě nepřipojili Visual Studio k účtu Azure, vyberte **Přidat účet...** .

4. V dialogovém okně **Vytvořit App Service** použijte nastavení **Hostování** podle tabulky pod obrázkem:

    ![Dialogové okno Vytvořit službu App Service](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Plán služby App Service](../articles/azure-functions/functions-scale.md)** | Plán Consumption | Po vytvoření nového bezserverového plánu kliknutím na **Nový** nezapomeňte vybrat **Spotřeba** v části **Velikost**. Zvolte také [umístění](https://azure.microsoft.com/regions/) v **oblasti** ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. Pokud používáte jiný plán než **Spotřeba**, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |
    | **[Účet úložiště](../articles/storage/common/storage-quickstart-create-account.md)** | Účet úložiště pro obecné účely | Modul runtime Functions vyžaduje účet úložiště Azure. Kliknutím na **Nový** vytvořte účet úložiště pro obecné účely. Můžete také použít existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Kliknutím na **Vytvořit** vytvořte aplikaci funkce a související prostředky v Azure a nasaďte kód projektu funkce. 

6. Jakmile se nasazení dokončí, poznamenejte si hodnotu **Adresa URL webu**. Je to adresa vaší aplikace funkcí v Azure.

    ![Zpráva o úspěšném publikování](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
