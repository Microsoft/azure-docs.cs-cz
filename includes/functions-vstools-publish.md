---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 327a7689e0297002f1ce3f48d2bd367104277680
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769692"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

2. V dialogovém okně **vybrat cíl publikování** použijte možnosti publikování, jak je uvedeno v tabulce pod obrázkem: 

    ![Výběr cíle publikování](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    | Možnost      | Popis                                |
    | ------------ |  -------------------------------------------------- |
    | **Plán spotřeby Azure Functions** |   Když publikujete projekt do aplikace Function App, která běží v [plánu spotřeby](../articles/azure-functions/functions-scale.md#consumption-plan), platíte jenom za provádění vaší aplikace Functions. Jiné plány hostování účtují vyšší náklady. Další informace najdete v tématu [škálování Azure functions a hostování](../articles/azure-functions/functions-scale.md). | 
    | **Vytvořit nový** | V Azure se vytvoří nová aplikace Function App se souvisejícími prostředky. Pokud zvolíte **Vybrat existující**, soubory z místního projektu přepíší všechny soubory ve stávající aplikaci funkcí v Azure. Tuto možnost použijte pouze v případě, že znovu publikujete aktualizace stávající aplikace funkcí. |
    | **Spustit ze souboru balíčku** | Aplikace Function App se nasazuje pomocí funkce [nasazení zip](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) s povoleným režimem [spuštění z balíčku](../articles/azure-functions/run-functions-from-deployment-package.md) . Toto je doporučený způsob spouštění vašich funkcí, což vede k lepšímu výkonu. Pokud tuto možnost nepoužíváte, ujistěte se, že jste před publikováním do Azure zastavili spuštění projektu Function App v místním prostředí. |


3. Vyberte **Publikovat**. Pokud jste se ještě přihlásili ke svému účtu Azure ze sady Visual Studio, vyberte **Přihlásit**se. Můžete si také vytvořit bezplatný účet Azure.

4. V dialogovém okně **Azure App Service: vytvořit nové** použijte nastavení **hostování** , jak je uvedeno v tabulce pod obrázkem:

    ![Dialogové okno Vytvoření služby App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. Platné znaky jsou `a-z`, `0-9` a `-`. |
    | **Předplatné** | Zvolte vaše předplatné. | Předplatné Azure, které se má použít. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Název skupiny prostředků, ve které chcete vytvořit aplikaci funkcí. Pokud chcete vytvořit novou skupinu prostředků, zvolte **Nová**.|
    | **[Plán hostování](../articles/azure-functions/functions-scale.md)** | Plán Consumption | Až vyberete **Nový** a vytvoříte plán bez serveru, nezapomeňte zvolit **spotřebu** v nabídce **Velikost** . Zvolte také [umístění](https://azure.microsoft.com/regions/) v **oblasti** ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. Pokud používáte jiný plán než **Spotřeba**, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |
    | **[Azure Storage](../articles/storage/common/storage-quickstart-create-account.md)** | Účet úložiště pro obecné účely | Modul runtime Functions vyžaduje účet úložiště Azure. Pokud chcete vytvořit účet úložiště pro obecné účely, vyberte **Nový** . Můžete také použít existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Výběrem možnosti **vytvořit** vytvoříte aplikaci funkcí a související prostředky v Azure s těmito nastaveními a nasadíte svůj kód projektu funkce. 

6. Jakmile se nasazení dokončí, poznamenejte si hodnotu **Adresa URL webu**. Je to adresa vaší aplikace funkcí v Azure.

    ![Zpráva o úspěšném publikování](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
