---
title: Konfigurace brány firewall Azure Blockchain Workbench SQL DB
description: Zjistěte, jak nakonfigurovat bránu firewall, Azure Blockchain Workbench SQL DB.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: dc22f212c014ab1d6622eff3491d669b21ca6f47
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurace brány firewall databáze Azure Blockchain Workbench

Tento článek ukazuje, jak nakonfigurovat pomocí portálu Azure pravidlo brány firewall. Externí klienti moci pravidla brány firewall nebo aplikace připojit k databázi Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi Blockchain Workbench

Pro připojení k databázi, ve které chcete nakonfigurovat pravidlo:

1. Přihlaste se k portálu Azure pomocí účtu, který má **vlastníka** oprávnění pro prostředky Azure Blockchain Workbench.
2. V levém navigačním podokně zvolte **skupiny prostředků**.
3. Vyberte název skupiny prostředků pro vaše nasazení Blockchain Workbench.
4. Vyberte **typ** řazení seznamu prostředků, a pak vyberte vaše **systému SQL server**.
5. Příklad seznamu prostředků v následující snímek obrazovky ukazuje dvě databáze: *hlavní* a *lsgn-sdk*. Nakonfigurujte pravidlo brány firewall na *lsgn-sdk*.

![Seznam Blockchain Workbench prostředky](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Vytvoření pravidla firewallu databáze

Pokud chcete vytvořit pravidlo brány firewall:

1. Vyberte odkaz na databázi "lsgn-sdk".
2. Na panelu nabídek vyberte **nastavení brány firewall serveru**.

   ![Nastavit bránu firewall serveru](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. K vytvoření pravidla pro vaši organizaci:

   * Zadejte **název pravidla**
   * Zadejte IP adresu pro **počáteční IP** rozsahů adres
   * Zadejte IP adresu pro **KONCOVÁ IP adresa** rozsahů adres

   ![Vytvořte pravidlo brány firewall](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Pokud chcete přidat IP adresu počítače, zvolte **+ přidat IP adresu klienta**.
        
1. Chcete-li uložit konfiguraci brány firewall, vyberte **Uložit**.
2. Rozsah IP adres, které jste nakonfigurovali pro databázi připojením z aplikace nebo nástroj pro testování. Například SQL Server Management Studio.

## <a name="next-steps"></a>Další postup

* [Zobrazení databáze v Azure Blockchain Workbench](blockchain-workbench-database-views.md)