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
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131563"
---
1. Přejděte na [Azure Portal](https://portal.azure.com).

1. Vyberte **+ vytvořit prostředek** na levé straně klikněte na tlačítko **aplikace Function app**.

1. Pro **plán Hosting**, zvolte **plán služby App Service**a pak vyberte **plán služby App Service/umístění**.

    ![Vytvoření Function App](./media/functions-premium-create/create-function-app-resource.png)

1. Vyberte **vytvořit nový**, typ **plán služby App Service** název, vyberte **umístění** v [oblasti](https://azure.microsoft.com/regions/) vaší blízkosti nebo v blízkosti jiných služeb vašich funkcí přístup a pak vyberte **cenová úroveň**.

    ![Vytvoření plánu služby App Service](./media/functions-premium-create/new-app-service-plan.png)

1. Zvolte **EP1** (Elastická Prémiová) plán a pak vyberte **použít**.

    ![Vyberte plán premium](./media/functions-premium-create/hosting-plan.png) 

1. Vyberte **OK** vytvořte plán a pak použijte zbývající nastavení aplikace function app jako uvedená v tabulce pod obrázkem. 

    ![Plán služby kompletní aplikace](./media/functions-premium-create/create-function-app.png)  

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. Můžete také použít navrhovanou hodnotu. |
    | **OS** | Windows | Plán Premium se aktuálně nepodporuje Linux. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Zvolte **.NET** pro funkce C# a F#. Pouze jazyky podporované v vaši vybranou **OS** jsou zobrazeny. |
    | **[Úložiště](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálně jedinečný název |  Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít také existující účet, který splňuje [požadavky na účet úložiště](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Výchozí | Vytvoří prostředek Application Insights stejného *název aplikace* v nejbližší podporované oblasti. Rozbalením tohoto nastavení můžete změnit **nový název prostředku** nebo zvolte jinou **umístění** v [zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/) kam chcete data ukládat. |

1. Po dokončení nastavení se ověří, vyberte **vytvořit**.

1. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](./media/functions-premium-create/function-app-create-notification.png)

1. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte. Můžete také vybrat **připnout na řídicí panel**. Připnutí usnadňuje se vraťte do tohoto prostředku aplikace funkce z řídicího panelu.