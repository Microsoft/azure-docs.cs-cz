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
ms.openlocfilehash: 460199c1958e3bb22148624b9f013eadbb707bd2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564351"
---
1. Vyberte tlačítko **vytvořit prostředek** v levém horním rohu Azure Portal a pak vyberte > COMPUTE**Function App**.

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |
    | **OS** | Windows | Dostupné možnosti jazyka závisí na operačním systému aplikace Function App. Například Python vyžaduje Linux. |
    | **[Plán hostování](../articles/azure-functions/functions-scale.md)** | Plán Consumption | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím **plánu Consumption** se prostředky přidávají dynamicky podle požadavků příslušných funkcí. U tohoto hostování [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) platíte jenom za dobu, kdy jsou funkce spuštěné. Pokud používáte plán služby App Service, musíte zajistit správu [škálování vaší aplikace funkcí](../articles/azure-functions/functions-scale.md).  |
    | **Location** | Západní Evropa | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. |
    | **[Úložiště](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v geografických oblastech [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete data uložit. |

3. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

4. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.
