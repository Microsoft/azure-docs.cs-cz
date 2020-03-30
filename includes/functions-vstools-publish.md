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
ms.openlocfilehash: dee7d778c4a1c1f37ddab7b9d072f83a22acbc60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056630"
---
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

2. V **části Vyberte cíl publikování**použijte možnosti publikování uvedené v následující tabulce: 

    | Možnost      | Popis                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Function App** | Vytvořte aplikaci funkcí v cloudovém prostředí Azure. | 
    | **Vytvořit nový** | V Azure se vytvoří nová aplikace funkcí se souvisejícími prostředky. <br/>Pokud zvolíte **Vybrat existující**, všechny soubory v existující aplikaci funkce v Azure jsou přepsány soubory z místního projektu. Tuto možnost použijte pouze v případě, že znovu publikujete aktualizace existující aplikace funkcí. |
    | **Spustit ze souboru balíčku** | Vaše aplikace funkcí se nasazuje pomocí [režimu Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) s [povoleným režimem Run-From-Package.](../articles/azure-functions/run-functions-from-deployment-package.md) Toto nasazení, což má za následek lepší výkon, je doporučený způsob spuštění funkcí. <br/>Pokud tuto možnost nepoužíváte, nezapomeňte před publikováním do Azure zastavit spuštění projektu aplikace funkce místně. |

    ![Výběr cíle publikování](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Vyberte **Publikovat**. Pokud jste se ještě nepřihlásili ke svému účtu Azure z Visual Studia, **vyberte Přihlásit se**. Můžete si taky vytvořit bezplatný účet Azure.

4. Ve **službě Azure App Service: Create new**, použijte hodnoty zadané v následující tabulce:

    | Nastavení      | Hodnota  | Popis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název** | Globálně jedinečný název | Název jednoznačně identifikující novou aplikaci funkcí. Přijměte toto jméno nebo zadejte nový název. Platné znaky `a-z`jsou: `0-9` `-`, , a . |
    | **Předplatné** | Vaše předplatné | Předplatné Azure, které se má použít. Přijměte toto předplatné nebo vyberte nové z rozevíracího seznamu. |
    | **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** | Název skupiny prostředků |  Skupina prostředků, ve které chcete vytvořit aplikaci funkce. Vrozené části rozevíracího seznamu vyberte existující skupinu prostředků nebo zvolte **Nový** a vytvořte novou skupinu prostředků.|
    | **[Hostingový plán](../articles/azure-functions/functions-scale.md)** | Název vašeho hostingového plánu | Chcete-li nakonfigurovat plán bez serveru, vyberte **možnost Nový.** Ujistěte se, že jste zvolili **spotřebu** v části **Velikost**. Když publikujete projekt do aplikace funkce, která běží v [plánu spotřeba](../articles/azure-functions/functions-scale.md#consumption-plan), platíte jenom za spuštění aplikace funkce. Další hostingové plány vznikají vyšší náklady. Pokud spustíte v jiném plánu než **spotřeba**, je nutné spravovat [škálování vaší aplikace funkce](../articles/azure-functions/functions-scale.md). Zvolte **umístění** v [oblasti](https://azure.microsoft.com/regions/) ve vašem okolí nebo jiné služby, ke které vaše funkce přistupují.  |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Účet úložiště pro obecné účely | Runtime Functions vyžaduje účet Azure Storage. Chcete-li nakonfigurovat účet úložiště pro obecné účely, vyberte **nový.** Můžete také zvolit existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Dialogové okno Vytvoření služby App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. Vyberte **Vytvořit,** chcete-li vytvořit aplikaci funkcí a související prostředky v Azure s těmito nastaveními a nasadit kód projektu funkce. 

6. Po dokončení nasazení si poznamenejte hodnotu **adresy URL webu,** což je adresa vaší aplikace pro funkce v Azure.

    ![Zpráva o úspěšném publikování](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
