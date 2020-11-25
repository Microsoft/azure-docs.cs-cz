---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ef617b9ed6c35d33350b173efe6189b5b79b6ae6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008159"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V **cíli** vyberte **Azure** :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="vybrat cíl Azure"::: .

1. V **konkrétním cíli** vyberte **Azure Function App (Windows)** .

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Vyberte Azure Function App":::

1. V části **instance funkce** vyberte **vytvořit novou funkci Azure Functions...** a pak použijte hodnoty uvedené v následující tabulce:

    | Nastavení      | Hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. Přijměte tento název nebo zadejte nový název. Platné znaky jsou: `a-z` , `0-9` a `-` . |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. Přijměte toto předplatné nebo v rozevíracím seznamu vyberte nový. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** | Název vaší skupiny prostředků |  Skupina prostředků, ve které se má vytvořit aplikace Function App V rozevíracím seznamu vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků výběrem možnosti **nové** .|
    | **[Typ plánu](../articles/azure-functions/functions-scale.md)** | Consumption | Když publikujete projekt do aplikace Function App, která běží v [plánu spotřeby](../articles/azure-functions/functions-scale.md#consumption-plan), platíte jenom za spuštění vaší aplikace Functions. Jiné plány hostování účtují vyšší náklady. |
    | **Umístění** | Umístění služby App Service | Vyberte **umístění** v [oblasti](https://azure.microsoft.com/regions/) poblíž vaší nebo jiné služby, ke které vaše funkce přistupuje. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Účet úložiště pro obecné účely | Modul runtime Functions vyžaduje účet Azure Storage. Vyberte **Nový** a nakonfigurujte účet úložiště pro obecné účely. Můžete také zvolit existující účet, který splňuje požadavky na [účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Dialogové okno Vytvoření služby App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Výběrem možnosti **vytvořit** vytvoříte aplikaci funkcí a související prostředky v Azure. 
1. V **instanci Functions** se ujistěte, že je zaškrtnuté políčko **Spustit ze souboru balíčku** . Aplikace Function App se nasazuje pomocí funkce [nasazení zip](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) s povoleným režimem [spuštění z balíčku](../articles/azure-functions/run-functions-from-deployment-package.md) . Toto je doporučená metoda nasazení pro váš projekt Functions, protože má za následek lepší výkon. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Dokončit vytvoření profilu":::

1. Vyberte **Dokončit** a na stránce Publikovat vyberte **publikovat** a nasaďte balíček obsahující soubory projektu do nové aplikace Function App v Azure. 

    Po dokončení nasazení se na kartě **publikovat** zobrazí kořenová adresa URL aplikace Function App v Azure. 
    
1.  Na kartě publikovat vyberte **v Průzkumníku cloudu možnost spravovat**. Tím se otevře nový prostředek Azure aplikace Function App v Průzkumníkovi cloudu. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Zpráva o úspěšném publikování":::
    
    Průzkumník cloudu umožňuje použít Visual Studio k zobrazení obsahu webu, spuštění a zastavení aplikace Function App a procházení přímo na prostředky aplikace Function App v Azure a v Azure Portal. 
