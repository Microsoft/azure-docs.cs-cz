---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1cecafb013b5d22557961e98935fc9a2fee2a79b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198956"
---
1. Vyberte **vytvořit prostředek** nalezeno tlačítko v levém horním rohu webu Azure portal, pak vyberte **Compute** > **aplikace Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **OS** | Windows | Hostování bez serveru v Linuxu je aktuálně ve verzi preview. Další informace najdete v tématu [v tomto článku aspekty](https://aka.ms/funclinux).|
    | **[Plán hostování](../articles/azure-functions/functions-scale.md)** | Plán Consumption | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím **plánu Consumption** se prostředky přidávají dynamicky podle požadavků příslušných funkcí. U tohoto hostování [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) platíte jenom za dobu, kdy jsou funkce spuštěné. Pokud používáte plán služby App Service, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |
    | **Umístění** | Západní Evropa | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    | **[Úložiště](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří prostředek Application Insights stejného *název aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **nový název prostředku** nebo zvolte jinou **umístění** v [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) kam chcete data ukládat. |

3. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

4. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.
