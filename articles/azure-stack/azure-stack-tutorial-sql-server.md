---
title: Zpřístupnění databáze SQL pro vaše uživatele Azure stacku | Dokumentace Microsoftu
description: Kurz k instalaci poskytovatele prostředků SQL serveru a vytvořte nabízí, která umožní uživatelům Azure stacku vytvářet databáze SQL.
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
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.custom: mvc
ms.openlocfilehash: ae32138dffa296a4aad9917897e180d364f189fc
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037825"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Kurz: zpřístupnění databáze SQL pro vaše uživatele Azure stacku

Jako správce cloudu Azure Stack, můžete vytvořit nabídek, které uživatelům (tenantů) vytvářet databáze SQL, které můžete použít s jejich nativně cloudové aplikace, weby a úlohy. Tím, že poskytuje tyto databáze vlastní, na vyžádání, založené na cloudu pro vaše uživatele, můžete je šetřit čas i prostředky. Chcete-li toto nastavení bude:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků SQL serveru
> * Vytvoření nabídky
> * Test nabídky

## <a name="deploy-the-sql-server-resource-provider"></a>Nasazení poskytovatele prostředků SQL serveru

Proces nasazení je podrobně popsány v [použití SQL Database v Azure stacku článku](azure-stack-sql-resource-provider-deploy.md), která se skládá z následujících kroků:

1. [Nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Ověření nasazení](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Zadejte kapacitu propojíte hostující SQL server. Další informace najdete v tématu [přidat hostitelské servery](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Vytvoření nabídky

1.  [Nastavení kvóty](azure-stack-setting-quotas.md) a pojmenujte ho *SQLServerQuota*. Vyberte **Microsoft.SQLAdapter** pro **Namespace** pole.
2.  [Vytvoření plánu](azure-stack-create-plan.md). Pojmenujte ji *TestSQLServerPlan*, vyberte **Microsoft.SQLAdapter** služby, a **SQLServerQuota** kvóty.

    > [!NOTE]
    > Umožníte uživatelům vytvářet další aplikace, může se vyžadovat další služby v plánu. Například Azure Functions vyžaduje **Microsoft.Storage** služby v plánu, zatímco Wordpress vyžaduje **Microsoft.MySQLAdapter**.

3.  [Vytvoření nabídky](azure-stack-create-offer.md), pojmenujte ho **TestSQLServerOffer** a vyberte **TestSQLServerPlan** plánu.

## <a name="test-the-offer"></a>Test nabídky

Teď, když jste nasadili poskytovatele prostředků SQL serveru a vytvoří v rámci nabídky, se můžete přihlásit jako uživatel, předplacení nabídky a vytvořte databázi.

### <a name="subscribe-to-the-offer"></a>Předplacení nabídky

1. Přihlaste se k portálu Azure Stack (https://portal.local.azurestack.external) jako tenant.
2. Vyberte **pořiďte si předplatné** a pak zadejte **TestSQLServerSubscription** pod **zobrazovaný název**.
3. Vyberte **vybrat některou nabídku** > **TestSQLServerOffer** > **vytvořit**.
4. Vyberte **všechny služby** > **předplatná** > **TestSQLServerSubscription** > **prostředků Poskytovatelé**.
5. Vyberte **zaregistrovat** vedle **Microsoft.SQLAdapter** zprostředkovatele.

### <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Vyberte **+**  >  **Data + úložiště** > **SQL Database**.
2. Ponechte výchozí hodnoty nebo použít tyto příklady pro následující pole:
    - **Název databáze**: SQLdb
    - **Maximální velikost v MB**: 100
    - **Předplatné**: TestSQLOffer
    - **Skupina prostředků**: SQL-RG
3. Vyberte **nastavení přihlášení**, zadejte přihlašovací údaje pro databázi a pak vyberte **OK**.
4. Vyberte **SKU** > vyberte jednotku SKU SQL, kterou jste vytvořili pro SQL Server, který je hostitelem > a pak vyberte **OK**.
5. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků SQL serveru
> * Vytvoření nabídky
> * Test nabídky

Pokračujte k dalším kurzu se dozvíte, jak:

> [!div class="nextstepaction"]
> [Zpřístupnění webové, mobilní a API apps pro vaše uživatele]( azure-stack-tutorial-app-service.md)