---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3ac7d8cc4705fe1b6e80f1e0c7e26d847d761cf6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54357029"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

2. Vyberte **Aplikace Azure Function**, zvolte **Vytvořit novou** a potom vyberte **Publikovat**.

    ![Výběr cíle publikování](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    Když povolíte **spustit z Zip**, vaše aplikace function app v Azure, přejde do režimu jen pro čtení a je spuštěna přímo z balíčku pro nasazení. Další informace najdete v tématu věnovaném [spouštění Azure Functions ze souboru balíčku](../articles/azure-functions/run-functions-from-deployment-package.md).
     
    >[!CAUTION]
    >Pokud zvolíte **Vybrat existující**, soubory z místního projektu přepíší všechny soubory ve stávající aplikaci funkcí v Azure. Tuto možnost použijte pouze v případě, že znovu publikujete aktualizace stávající aplikace funkcí.

3. Pokud jste ještě nepřipojili Visual Studio k účtu Azure, vyberte **Přidat účet...** .

4. V dialogovém okně **Vytvořit App Service** použijte nastavení **Hostování** podle tabulky pod obrázkem:

    ![Dialogové okno Vytvořit službu App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Plán služby App Service](../articles/azure-functions/functions-scale.md)** | Plán Consumption | Po vytvoření nového bezserverového plánu kliknutím na **Nový** nezapomeňte vybrat **Spotřeba** v části **Velikost**. Zvolte také [umístění](https://azure.microsoft.com/regions/) v **oblasti** ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. Pokud používáte jiný plán než **Spotřeba**, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |
    | **[Účet úložiště](../articles/storage/common/storage-quickstart-create-account.md)** | Účet úložiště pro obecné účely | Modul runtime Functions vyžaduje účet úložiště Azure. Kliknutím na **Nový** vytvořte účet úložiště pro obecné účely. Můžete také použít existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Kliknutím na **Vytvořit** vytvořte aplikaci funkce a související prostředky v Azure a nasaďte kód projektu funkce. 

6. Jakmile se nasazení dokončí, poznamenejte si hodnotu **Adresa URL webu**. Je to adresa vaší aplikace funkcí v Azure.

    ![Zpráva o úspěšném publikování](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
