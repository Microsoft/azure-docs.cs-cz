---
title: Použití databází od poskytovatele prostředků SQL adaptér ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvoření a Správa databází SQL Database zřízení pomocí poskytovatele prostředků SQL adaptéru
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
ms.openlocfilehash: bbb4f72e029a442362b792f3cdb4731e9563687b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244823"
---
# <a name="create-sql-databases"></a>Vytvoření databáze SQL

Můžete vytvořit a spravovat databáze samoobslužného portálu user portal. Uživatele služby Azure Stack potřebuje předplatné se nabídka, která obsahuje službu SQL database.

1. Přihlaste se k [Azure Stack](azure-stack-poc.md) portálu user portal.

2. Vyberte **+ nový** &gt; **Data + úložiště** &gt; **databázi systému SQL Server** &gt; **přidat**.

3. V části **Create Database**, zadejte požadované informace, jako například **název_databáze** a **maximální velikost v MB**.

   >[!NOTE]
   >Velikost databáze musí být minimálně 64 MB, což může zvýšit až nasazení databáze.

   Další nastavení nakonfigurujte podle potřeby pro vaše prostředí.

4. V části **Create Database**vyberte **SKU**. V části **vyberte skladovou Položku**, vyberte SKU pro vaši databázi.

   ![Vytvořit databázi](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Jako hostitelské servery jsou přidány do služby Azure Stack, je přiřazený SKU. Databáze se vytvářejí ve fondu hostitelské servery v SKU.

5. Vyberte **přihlášení**.
6. V části **vyberte přihlášení**, vyberte stávající přihlašovací údaje, nebo vyberte **+ vytvořit nové přihlašovací údaje**.
7. V části **nového přihlašovacího jména**, zadejte název pro **přihlášení k databázi** a **heslo**.

   >[!NOTE]
   >Tato nastavení jsou přihlašovací údaje ověřování SQL, který je vytvořen pro přístup k této databázi pouze. Uživatelské jméno musí být globálně jedinečný. Nastavení přihlášení pro další databáze, které používají stejným SKU můžete znovu použít.

   ![Vytvořte nové přihlašovací údaje databáze](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Vyberte **OK** k dokončení nasazení databáze.

V části **Essentials**, který se zobrazí po nasazení databáze, poznamenejte si **připojovací řetězec**. Tento řetězec můžete použít v jakékoli aplikaci, která vyžaduje přístup k databázi SQL serveru.

![Načtení připojovacího řetězce](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Databáze SQL Always On

Návrh databáze AlwaysOn jsou zpracovány jinak než v samostatném prostředí serveru. Další informace najdete v tématu [Úvod do SQL serveru skupiny dostupnosti AlwaysOn na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Ověření databáze SQL Always On

Následující snímek obrazovky ukazuje, jak můžete použít SQL Server Management Studio se podívat na stav databáze v SQL Always On.

![Stav databáze AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On databáze by měla zobrazit jako Synchronized a k dispozici na všech instancích SQL a zobrazeny podle skupin dostupnosti. Na předchozím snímku obrazovky je příklad databáze newdb1 a její stav je **newdb1 (synchronizované)**.

### <a name="delete-an-alwayson-database"></a>Odstranit databázi AlwaysOn

Při odstranění databáze SQL AlwaysOn od tohoto poskytovatele prostředků SQL odstraní databázi z primární repliky a ze skupiny dostupnosti.

SQL pak umístí na tyto repliky databáze do stavu obnovení a nebude vymazání databáze, pokud aktivuje. Pokud databáze není Vyřazená, sekundární repliky přejít do stavu Not Synchronizing.

## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-maintain.md)
