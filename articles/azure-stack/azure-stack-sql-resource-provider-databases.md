---
title: Pomocí databáze poskytované RP adaptér SQL v Azure zásobníku | Microsoft Docs
description: Postup vytvoření a správě databází SQL, které jsou zřízené prostřednictvím poskytovatele prostředků adaptér SQL
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2808847642639069e60102b195ac97957c8593f0
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-sql-databases"></a>Vytvoření databáze SQL
Databáze samoobslužné služby je zajišťována prostřednictvím portálu činnost koncového uživatele. Uživatel potřebuje odběr, který má nabídku, který obsahuje službu databáze.

1. Přihlaste se k [zásobník Azure](azure-stack-poc.md) portálu user portal (Správci služby můžete také použít portál pro správu).

2. Klikněte na tlačítko **+ nový** &gt; **Data + úložiště "** &gt; **databáze systému SQL Server** &gt; **přidat**.

3. Vyplňte formulář s podrobnostmi o databázi, včetně **název databáze**, **maximální velikost**a podle potřeby změnit ostatní parametry. Jste vyzváni k vyberte SKU pro vaši databázi. Při přidávání hostitelskými servery, je přiřazený SKU. Databáze se vytvoří v tomto fondu hostování servery, které tvoří verze SKU.

  ![Nová databáze](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > Velikost databáze musí být minimálně 64 MB. Může být zvýšena pomocí nastavení.

4. Zadejte nastavení přihlášení: **přihlášení databázi**, a **heslo**. Tato nastavení jsou vytvořené pro přístup k této databázi pouze pověření ověřování SQL. Přihlašovací uživatelské jméno musí být globálně jedinečný. Vytvořit nové nastavení přihlášení nebo vyberte nějaký existující. Můžete znovu použít nastavení přihlášení pro jiné databáze pomocí stejné SKU.

    ![Vytvořte nové přihlašovací údaje databáze](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Odeslání formuláře a počkejte na dokončení nasazení.

    V okně výsledné Všimněte si pole "Připojovací řetězec". V zásobníku vaší Azure můžete použít tento řetězec v jakékoli aplikaci, která vyžaduje přístup k serveru SQL (například webové aplikace).

    ![Načtení připojovacího řetězce](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>Odstranit databáze služby SQL AlwaysOn
Při odstranění databáze SQL AlwaysOn od zprostředkovatele prostředků je úspěšně odstraněna z primární a dostupnosti AlwaysOn skupiny, ale podle návrhu, SQL AG umístí databáze ve stavu v každé repliky obnovení a není odpojení databáze, pokud aktivuje. Pokud není vyřazen databáze sekundární repliky přejde do Not synchronizing stavu. Znovu přidávání nové databáze pro skupinu AG se stejným prostřednictvím RP stále funguje.

## <a name="verify-sql-alwayson-databases"></a>Ověřte databází SQL AlwaysOn
AlwaysOn databáze by měl zobrazit, protože synchronizován a k dispozici na všech instancích a ve skupině dostupnosti. Po převzetí služeb při selhání by měl bezproblémově připojit databázi. Chcete-li ověřit, že databáze je synchronizace můžete použít SQL Server Management Studio:

![Ověřte AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>Další postup

[Spravovat poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-maintain.md)
