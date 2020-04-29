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
ms.openlocfilehash: 8a4fbb3803f2eed4f961792a29a6bf8a327ea208
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80657588"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

2. V části **vybrat cíl publikování**použijte možnosti publikování uvedené v následující tabulce: 

    | Možnost      | Popis                                |
    | ------------ |  -------------------------------------------------- |
    | **Function App Azure** | Vytvořte aplikaci Function App v cloudovém prostředí Azure. | 
    | **Vytvořit nový** | V Azure se vytvoří nová aplikace Function App se souvisejícími prostředky. <br/>Pokud zvolíte **Vybrat existující**, všechny soubory v existující aplikaci Function App v Azure se přepíší soubory z místního projektu. Tuto možnost použijte pouze v případě, že znovu publikujete aktualizace existující aplikace Function App. |
    | **Spustit ze souboru balíčku** | Aplikace Function App se nasazuje pomocí funkce [nasazení zip](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) s povoleným režimem [spuštění z balíčku](../articles/azure-functions/run-functions-from-deployment-package.md) . Toto nasazení, které má za následek lepší výkon, je doporučeným způsobem, který spouští vaše funkce. <br/>Pokud tuto možnost nepoužijete, ujistěte se, že jste před publikováním do Azure zastavili spuštění projektu Function App v místním prostředí. |

    ![Výběr cíle publikování](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Vyberte **Publikovat**. Pokud jste se ještě přihlásili ke svému účtu Azure ze sady Visual Studio, vyberte **Přihlásit**se. Můžete si také vytvořit bezplatný účet Azure.

4. V **Azure App Service: vytvořit nové**použijte hodnoty uvedené v následující tabulce:

    | Nastavení      | Hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. Přijměte tento název nebo zadejte nový název. Platné znaky jsou: `a-z`, `0-9`a `-`. |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. Přijměte toto předplatné nebo v rozevíracím seznamu vyberte nový. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** | Název vaší skupiny prostředků |  Skupina prostředků, ve které se má vytvořit aplikace Function App V rozevíracím seznamu vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků výběrem možnosti **nové** .|
    | **[Plán hostování](../articles/azure-functions/functions-scale.md)** | Název vašeho plánu hostování | Vyberte **Nový** a nakonfigurujte plán bez serveru. Ujistěte se, že vybíráte **spotřebu** v rámci **velikosti**. Když publikujete projekt do aplikace Function App, která běží v [plánu spotřeby](../articles/azure-functions/functions-scale.md#consumption-plan), platíte jenom za spuštění vaší aplikace Functions. Jiné plány hostování účtují vyšší náklady. Pokud spustíte jiný plán než **spotřebu**, musíte spravovat [škálování aplikace Function App](../articles/azure-functions/functions-scale.md). Vyberte **umístění** v [oblasti](https://azure.microsoft.com/regions/) poblíž vaší nebo jiné služby, ke které vaše funkce přistupuje.  |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Účet úložiště pro obecné účely | Modul runtime Functions vyžaduje účet Azure Storage. Vyberte **Nový** a nakonfigurujte účet úložiště pro obecné účely. Můžete také zvolit existující účet, který splňuje požadavky na [účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Dialogové okno Vytvoření služby App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. Vyberte **vytvořit** , pokud chcete vytvořit aplikaci Function App a její související prostředky v Azure s těmito nastaveními a nasadit svůj kód projektu funkce. 

6. Vyberte publikovat a po dokončení nasazení si poznamenejte hodnotu **Adresa URL webu** , což je adresa vaší aplikace Function App v Azure.

    ![Zpráva o úspěšném publikování](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
