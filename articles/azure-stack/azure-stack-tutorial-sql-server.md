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
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: b9ba2bb89bb0d7e16a28a165cf14530a7a10f71b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234746"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Kurz: zpřístupníte databází SQL Azure zásobníku uživatelům

Jako správce cloudu Azure zásobníku, můžete vytvořit nabídky, které uživatelům vytvoření databáze SQL, které můžete použít s jejich nativní cloudové aplikace, weby a úlohami (klienty). Tím, že tyto databáze vlastní, na vyžádání, založené na cloudu poskytuje uživatelům, můžete je uložit čas a prostředky. Pokud chcete nastavit tuto možnost, bude takto:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků SQL serveru
> * Vytvoření nabídky
> * Testování nabídky

## <a name="deploy-the-sql-server-resource-provider"></a>Nasazení poskytovatele prostředků SQL serveru

Proces nasazení je podrobně popsány v [databáze SQL pro použití na článku zásobník Azure](azure-stack-sql-resource-provider-deploy.md)a skládá se z z následujících kroků:

1. [Nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Ověření nasazení](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Zadejte kapacitu připojením k hostování systému SQL server. Další informace najdete v tématu [přidat hostitelské servery](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Vytvoření nabídky

1.  [Nastavení kvóty](azure-stack-setting-quotas.md) a pojmenujte ji *SQLServerQuota*. Vyberte **Microsoft.SQLAdapter** pro **Namespace** pole.
2.  [Vytvoření plánu](azure-stack-create-plan.md). Pojmenujte ji *TestSQLServerPlan*, vyberte **Microsoft.SQLAdapter** služby, a **SQLServerQuota** kvóty.

    > [!NOTE]
    > Umožníte uživatelům vytvářet jiné aplikace, může být vyžadován další služby v plánu. Například vyžaduje Azure Functions **Microsoft.Storage** služby v plánu, při Wordpress vyžaduje **Microsoft.MySQLAdapter**.

3.  [Vytvořit nabídku](azure-stack-create-offer.md), pojmenujte ji **TestSQLServerOffer** a vyberte **TestSQLServerPlan** plán.

## <a name="test-the-offer"></a>Testování nabídky

Teď, když máte ukázku nasazenou poskytovatele prostředků systému SQL Server a vytvořit nabídku, můžete přihlásit jako uživatel, přihlášení k odběru na nabídku a vytvořit databázi.

### <a name="subscribe-to-the-offer"></a>Přihlášení k odběru do nabídky

1. Přihlaste se k portálu Azure zásobníku (https://portal.local.azurestack.external) jako klient.
2. Vyberte **získat předplatné** a pak zadejte **TestSQLServerSubscription** pod **zobrazovaný název**.
3. Vyberte **vyberte nabídku** > **TestSQLServerOffer** > **vytvořit**.
4. Vyberte **další služby** > **odběry** > **TestSQLServerSubscription** > **prostředků Zprostředkovatelé**.
5. Vyberte **zaregistrovat** vedle **Microsoft.SQLAdapter** zprostředkovatele.

### <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Vyberte **+**  >  **Data + úložiště** > **databáze SQL**.
2. Ponechte výchozí hodnoty nebo použít tyto příklady pro následující pole:
    - **Název databáze**: SQLdb
    - **Maximální velikost v MB**: 100
    - **Předplatné**: TestSQLOffer
    - **Skupina prostředků**: SQL RG
3. Vyberte **nastavení přihlášení**, zadejte přihlašovací údaje pro databázi a potom vyberte **OK**.
4. Vyberte **SKU** > vyberte SKU SQL, kterou jste vytvořili pro hostování serveru SQL > a pak vyberte **OK**.
5. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků SQL serveru
> * Vytvoření nabídky
> * Testování nabídky

Přechod na další kurzu se dozvíte, jak:

> [!div class="nextstepaction"]
> [Zpřístupnit webové, mobilní a aplikace API pro vaše uživatele]( azure-stack-tutorial-app-service.md)