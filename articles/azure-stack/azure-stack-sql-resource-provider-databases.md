---
title: Pomocí databáze poskytované poskytovatelem prostředků adaptér SQL v Azure zásobníku | Microsoft Docs
description: Jak vytvořit a spravovat přístupová práva pomocí zprostředkovatele prostředků adaptér SQL databáze SQL
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 56d21b76268f94f4254985a6924c4ca2d778a9cd
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300812"
---
# <a name="create-sql-databases"></a>Vytvoření databáze SQL

Můžete vytvořit a spravovat databáze samoobslužné služby v portálu user portal. Uživatel s Azure zásobníku musí předplatné s nabídku obsahující službu SQL database.

1. Přihlaste se k [zásobník Azure](azure-stack-poc.md) portálu user portal.

2. Vyberte **+ nový** &gt; **Data + úložiště** &gt; **databáze systému SQL Server** &gt; **přidat**.

3. V části **Create Database**, zadejte požadované informace, jako například **název databáze** a **maximální velikost v MB**.

   >[!NOTE]
   >Velikost databáze musí být minimálně 64 MB, což může zvýšit po nasazení databáze.

   Nakonfigurujte další nastavení podle potřeby pro vaše prostředí.

4. V části **Create Database**, vyberte **SKU**. V části **vyberte SKU**, vyberte SKU pro vaši databázi.

   ![Vytvořit databázi](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Jako hostitelskými servery jsou přidány do zásobníku Azure, je přiřazený SKU. Databáze se vytvářejí ve fondu hostitelské servery v SKU.

5. Vyberte **přihlášení**.
6. V části **vyberte přihlášení**, vyberte stávající přihlašovací údaje, nebo vyberte **+ vytvořit nové přihlašovací údaje**.
7. V části **nového přihlašovacího jména**, zadejte název **přihlášení databázi** a **heslo**.

   >[!NOTE]
   >Tato nastavení jsou vytvořené pro přístup k této databázi pouze pověření ověřování SQL. Přihlašovací uživatelské jméno musí být globálně jedinečný. Můžete znovu použít nastavení přihlášení pro jiné databáze, které používají stejné SKU.

   ![Vytvořte nové přihlašovací údaje databáze](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Vyberte **OK** pro dokončení nasazení databáze.

V části **Essentials**, který se zobrazí po nasazení databáze, poznamenejte si **připojovací řetězec**. Tento řetězec můžete použít v jakékoli aplikaci, která potřebuje přístup k databázi systému SQL Server.

![Načtení připojovacího řetězce](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Databáze SQL Always On

Návrh Always On databáze jsou zpracovány jinak než v samostatném prostředí serveru. Další informace najdete v tématu [představení SQL serveru Always On skupiny dostupnosti na virtuálních počítačích Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Ověřte databází SQL Always On

Následující snímek obrazovky ukazuje, jak můžete použít SQL Server Management Studio se podívat na stav databáze v SQL Always On.

![Stav databáze AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On databáze by měla zobrazit jako Synchronized a k dispozici na všech instancích SQL a zobrazí v skupiny dostupnosti. V předchozím snímku obrazovky je příklad databáze newdb1 a její stav je **newdb1 (synchronizované)**.

### <a name="delete-an-alwayson-database"></a>Odstraňte databázi AlwaysOn

Po odstranění databáze SQL AlwaysOn od zprostředkovatele prostředků se odstraní SQL databáze, z primární repliky a ze skupiny dostupnosti.

SQL pak převádí databázi do stavu obnovení na jiné repliky a nepodporuje odpojení databáze, pokud aktivuje. Pokud není vyřazen databáze sekundární repliky přejděte do stavu Not Synchronizing.

## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-maintain.md)
