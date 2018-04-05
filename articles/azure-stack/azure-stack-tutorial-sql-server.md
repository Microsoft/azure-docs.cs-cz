---
title: Databáze SQL zpřístupnit uživatelům zásobník Azure | Microsoft Docs
description: Kurz k instalaci zprostředkovatele prostředků SQL serveru a vytvořte nabízí, která umožní uživatelům zásobník Azure vytvářet databáze SQL.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2017
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: f8d2dd65d9d427872fe78508ed0bcc61e644fdb0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Databáze SQL zpřístupnit uživatelům Azure zásobníku
Jako správce cloudu Azure zásobníku, můžete vytvořit nabídky, které uživatelům vytvoření databáze SQL, které můžete použít s jejich nativní cloudové aplikace, weby a úlohami (klienty). Tím, že tyto databáze vlastní, na vyžádání, založené na cloudu poskytuje uživatelům, můžete je uložit čas a prostředky. Pokud chcete nastavit tuto možnost, bude takto:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků SQL serveru
> * Vytvoření nabídky
> * Testování nabídky

## <a name="deploy-the-sql-server-resource-provider"></a>Nasazení poskytovatele prostředků SQL serveru

Proces nasazení je podrobně popsány v [databáze SQL pomocí na Azure zásobníku článku](azure-stack-sql-resource-provider-deploy.md)a se skládá z následujících kroků:

1. [Nasazení poskytovatele prostředků SQL]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider).
2. [Ověření nasazení]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Zadejte kapacitu připojením k hostování systému SQL server.

## <a name="create-an-offer"></a>Vytvoření nabídky

1.  [Nastavení kvóty](azure-stack-setting-quotas.md) a pojmenujte ji *SQLServerQuota*. Vyberte **Microsoft.SQLAdapter** pro **Namespace** pole.
2.  [Vytvoření plánu](azure-stack-create-plan.md). Pojmenujte ji *TestSQLServerPlan*, vyberte **Microsoft.SQLAdapter** služby, a **SQLServerQuota** kvóty.

    > [!NOTE]
    > Umožníte uživatelům vytvářet jiné aplikace, může být vyžadován další služby v plánu. Například Azure Functions vyžaduje, aby plánu zahrnout **Microsoft.Storage** služby, zatímco Wordpress vyžaduje **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Vytvořit nabídku](azure-stack-create-offer.md), pojmenujte ji **TestSQLServerOffer** a vyberte **TestSQLServerPlan** plán.

## <a name="test-the-offer"></a>Testování nabídky

Teď, když máte ukázku nasazenou poskytovatele prostředků systému SQL Server a vytvořit nabídku, můžete přihlásit jako uživatel, přihlášení k odběru na nabídku a vytvořit databázi.

### <a name="subscribe-to-the-offer"></a>Přihlášení k odběru do nabídky
1. Přihlaste se k portálu Azure zásobníku (https://portal.local.azurestack.external) jako klient.
2. Klikněte na tlačítko **získat předplatné** a pak zadejte **TestSQLServerSubscription** pod **zobrazovaný název**.
3. Klikněte na tlačítko **vyberte nabídku** > **TestSQLServerOffer** > **vytvořit**.
4. Klikněte na tlačítko **další služby** > **odběry** > **TestSQLServerSubscription** > **prostředků Zprostředkovatelé**.
5. Klikněte na tlačítko **zaregistrovat** vedle **Microsoft.SQLAdapter** zprostředkovatele.

### <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Klikněte na tlačítko **+**  >  **Data + úložiště** > **databáze SQL**.
2. Ponechte výchozí hodnoty pro pole, nebo můžete použít tyto příklady:
    - **Název databáze**: SQLdb
    - **Maximální velikost v MB**: 100
    - **Předplatné**: TestSQLOffer
    - **Skupina prostředků**: SQL RG
3. Klikněte na tlačítko **nastavení přihlášení**, zadejte přihlašovací údaje pro databázi a pak klikněte na tlačítko **OK**.
4. Klikněte na tlačítko **SKU** > vyberte SKU SQL, kterou jste vytvořili pro hostování serveru SQL > **OK**.
5. Klikněte na možnost **Vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků SQL serveru
> * Vytvoření nabídky
> * Testování nabídky

Přechod na další kurzu se dozvíte, jak:

> [!div class="nextstepaction"]
> [Zpřístupnit webové, mobilní a aplikace API pro vaše uživatele]( azure-stack-tutorial-app-service.md)

