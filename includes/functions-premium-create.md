---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443988"
---
1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Vyberte **+ vytvořit prostředek** na levé straně a pak zvolte **Function App**.

1. V případě **plánu hostování**zvolte možnost **App Service plán**a potom vyberte **App Service plán/umístění**.

    ![Vytvoření Function App](./media/functions-premium-create/create-function-app-resource.png)

1. Vyberte **vytvořit nový**, zadejte název **App Serviceho plánu** , zvolte **umístění** v [oblasti](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup, a pak vyberte **cenovou úroveň**.

    ![Vytvoření plánu služby App Service](./media/functions-premium-create/new-app-service-plan.png)

1. Zvolte plán **EP1** (elastický Premium) a pak vyberte **použít**.

    ![Vybrat plán Premium](./media/functions-premium-create/hosting-plan.png) 

1. Vyberte **OK** a vytvořte plán, pak použijte zbývající nastavení aplikace Function App, jak je uvedeno v tabulce pod obrázkem. 

    ![Plán služby App Service se dokončil.](./media/functions-premium-create/create-function-app.png)  

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. Můžete také použít navrhovanou hodnotu. |
    | **OS** | Preferovaný operační systém | Plán Premium podporuje Linux i Windows. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. Zobrazují se jenom jazyky, které jste zvolili na zvoleném **operačním systému** . |
    | **[Úložiště](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří Application Insights prostředek stejného *názvu aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **název nového prostředku** nebo zvolit jiné **umístění** v geografických oblastech [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , kam chcete data uložit. |

1. Po ověření nastavení vyberte **vytvořit**.

1. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/functions-premium-create/function-app-create-notification.png)

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **Připnout na řídicí panel**. Připnutí usnadňuje návrat k tomuto prostředku Function App z vašeho řídicího panelu.