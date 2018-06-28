---
title: Pomocí databáze poskytované RP adaptér MySQL na AzureStack | Microsoft Docs
description: Postup vytvoření a správě databází MySQL zřízení pomocí poskytovatele prostředků adaptér MySQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 0a900d75315fd0015633c036877faef84c48d65b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031823"
---
# <a name="create-mysql-databases"></a>Vytvoření databáze MySQL

Můžete vytvořit a spravovat databáze samoobslužné služby v portálu user portal. Uživatel s Azure zásobníku musí předplatné se nabídka, která zahrnuje službu databáze MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Otestujte nasazení tak, že vytvoříte databázi MySQL

1. Přihlaste se k portálu user portal zásobník Azure.
2. Vyberte **+ nový** > **Data + úložiště** > **databáze MySQL** > **přidat**.
3. V části **vytvoření databáze MySQL**, zadejte název databáze a nakonfigurujte další nastavení podle potřeby pro vaše prostředí.

    ![Vytvoření databáze MySQL testu](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. V části **Create Database**, vyberte **SKU**. V části **vyberte MySQL SKU**, vyberte SKU pro vaši databázi.

    ![Vyberte MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >Jako hostitelskými servery jsou přidány do zásobníku Azure, je přiřazený SKU. Databáze se vytvářejí ve fondu hostitelské servery v SKU.

5. V části **přihlášení**, vyberte ***konfigurovat požadované nastavení***.
6. V části **vyberte přihlášení**, můžete zvolit stávající přihlašovací údaje, nebo vyberte **+ vytvořit nové přihlašovací údaje** nastavit nové přihlašovací údaje.  Zadejte **přihlášení databázi** název a **heslo**a potom vyberte **OK**.

    ![Vytvořte nové přihlašovací údaje databáze](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Délka názvu databáze přihlášení nesmí překročit 32 znaků v MySQL 5.7. Ve starších edic ho nesmí překročit 16 znaků.

7. Vyberte **vytvořit** a dokončete nastavení databáze.

Po nasazení databáze, poznamenejte si **připojovací řetězec** pod **Essentials**. Tento řetězec můžete použít v jakékoli aplikaci, která potřebuje přístup k databázi MySQL.

![Získání připojovacího řetězce pro databázi MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Aktualizace hesla pro správu

Heslo můžete upravit změnou na instanci serveru MySQL.

1. Vyberte **prostředky pro správu** > **MySQL hostitelské servery**. Vyberte hostitelský server.
2. V části **nastavení**, vyberte **heslo**.
3. V části **heslo**, zadejte nové heslo a potom vyberte **Uložit**.

![Aktualizovat heslo správce](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-maintain.md)