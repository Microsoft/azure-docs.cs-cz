---
title: Používání databáze poskytnutých poskytovatele prostředků MySQL adaptéru na AzureStack | Dokumentace Microsoftu
description: Vytvoření a Správa databází MySQL zřízené s využitím poskytovatele prostředků MySQL adaptéru
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 3063a3f3f199a591fa94c3a665ea9ecc5457e502
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247591"
---
# <a name="create-mysql-databases"></a>Vytvoření databáze MySQL

Můžete vytvořit a spravovat databáze samoobslužného portálu user portal. Uživatele služby Azure Stack potřebuje předplatné se nabídka, která zahrnuje databázová služba MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Otestování nasazení tak, že vytvoříte databázi MySQL

1. Přihlaste se k portálu user portal pro Azure Stack.
2. Vyberte **+ vytvořit prostředek** > **Data + úložiště** > **databázi MySQL** > **přidat**.
3. V části **vytvořit databázi MySQL**, zadejte název databáze a podle potřeby pro vaše prostředí nakonfigurujte další nastavení.

    ![Vytvoření testu databáze MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. V části **Create Database**vyberte **SKU**. V části **vyberte MySQL SKU**, vyberte SKU pro vaši databázi.

    ![Vyberte MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Jako hostitelské servery jsou přidány do služby Azure Stack, je přiřazený SKU. Databáze se vytvářejí ve fondu hostitelské servery v SKU.

5. V části **přihlášení**vyberte ***konfigurovat požadované nastavení***.
6. V části **vyberte přihlášení**, můžete zvolit stávající přihlašovací údaje nebo vyberte **+ vytvořit nové přihlašovací údaje** nastavit nové přihlašovací údaje.  Zadejte **přihlášení k databázi** název a **heslo**a pak vyberte **OK**.

    ![Vytvořte nové přihlašovací údaje databáze](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Délka názvu databáze přihlášení nesmí překročit 32 znaků v MySQL 5.7. Ve starších verzích se nesmí překročit 16 znaků.

7. Vyberte **vytvořit** dokončete nastavení databáze.

Po nasazení databáze, poznamenejte si **připojovací řetězec** pod **Essentials**. Tento řetězec můžete použít v jakékoliv aplikace, která potřebuje přístup k databázi MySQL.

![Získání připojovacího řetězce pro databázi MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Aktualizace hesla pro správu

Heslo můžete změnit pomocí změny na instanci serveru MySQL.

1. Select **ADMINISTRATIVE RESOURCES** > **MySQL Hosting Servers**. Vyberte hostitelském serveru.
2. V části **nastavení**vyberte **heslo**.
3. V části **heslo**, zadejte nové heslo a pak vyberte **Uložit**.

![Aktualizovat heslo správce](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Další postup

[Aktualizace poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-update.md)
