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
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100529710"
---
1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte **publikovat** a v části **cíl** vyberte **Azure** a **Další**.

1. V případě **konkrétního cíle** vyberte **Azure Function App (Windows)**, který vytvoří aplikaci Function App, která běží ve Windows.

1. V možnosti **instance funkce** vyberte **vytvořit novou funkci Azure Functions...** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Vytvoření nové instance aplikace Function App":::

1. Vytvořte novou instanci pomocí hodnot uvedených v následující tabulce:

    | Nastavení      | Hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. Přijměte tento název nebo zadejte nový název. Platné znaky jsou: `a-z` , `0-9` a `-` . |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. Přijměte toto předplatné nebo v rozevíracím seznamu vyberte nový. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** | Název vaší skupiny prostředků |  Skupina prostředků, ve které se má vytvořit aplikace Function App V rozevíracím seznamu vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků výběrem možnosti **nové** .|
    | **[Typ plánu](../articles/azure-functions/functions-scale.md)** | Využití | Když publikujete projekt do aplikace Function App, která běží v [plánu spotřeby](../articles/azure-functions/consumption-plan.md), platíte jenom za spuštění vaší aplikace Functions. Jiné plány hostování účtují vyšší náklady. |
    | **Umístění** | Umístění služby App Service | Vyberte **umístění** v [oblasti](https://azure.microsoft.com/regions/) poblíž vaší nebo jiné služby, ke které vaše funkce přistupuje. |
    | **[Azure Storage](../articles/azure-functions/storage-considerations.md)** | Účet úložiště pro obecné účely | Modul runtime Functions vyžaduje účet Azure Storage. Vyberte **Nový** a nakonfigurujte účet úložiště pro obecné účely. Můžete také zvolit existující účet, který splňuje požadavky na [účet úložiště](../articles/azure-functions/storage-considerations.md#storage-account-requirements).  |

    ![Dialogové okno Vytvoření služby App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Výběrem možnosti **vytvořit** vytvoříte aplikaci funkcí a související prostředky v Azure. Stav vytvoření prostředku se zobrazí v levém dolním rohu okna. 

1. Vraťte se do **instance Functions**, ujistěte se, že je zaškrtnuté políčko **Spustit ze souboru balíčku** . Aplikace Function App se nasazuje pomocí funkce [nasazení zip](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) s povoleným režimem [spuštění z balíčku](../articles/azure-functions/run-functions-from-deployment-package.md) . Toto je doporučená metoda nasazení pro váš projekt Functions, protože má za následek lepší výkon. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Dokončit vytvoření profilu":::

1. Vyberte **Dokončit** a na stránce Publikovat vyberte **publikovat** a nasaďte balíček obsahující soubory projektu do nové aplikace Function App v Azure. 

    Po dokončení nasazení se na kartě **publikovat** zobrazí kořenová adresa URL aplikace Function App v Azure. 
    
1.  Na kartě publikovat vyberte **v Průzkumníku cloudu možnost spravovat**. Tím se otevře nový prostředek Azure aplikace Function App v Průzkumníkovi cloudu. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Zpráva o úspěšném publikování":::
    
    Průzkumník cloudu umožňuje použít Visual Studio k zobrazení obsahu webu, spuštění a zastavení aplikace Function App a procházení přímo na prostředky aplikace Function App v Azure a v Azure Portal. 
