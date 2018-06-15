---
title: Konfigurace brány firewall databáze SQL služby Azure Blockchain Workbench
description: Naučte se konfigurovat bránu firewall databáze SQL služby Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: afeea143f73fa4f7d3e373535007846a668616ab
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074945"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurace brány firewall databáze služby Azure Blockchain Workbench

Tento článek popisuje, jak nakonfigurovat pravidlo brány firewall pomocí Azure Portalu. Pravidla brány firewall umožňují externím klientům nebo aplikacím připojení k databázi služby Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Připojení k databázi služby Blockchain Workbench

K databázi, kde chcete nakonfigurovat pravidlo, se připojíte takto:

1. Přihlaste se k Azure Portalu pod účtem, který má oprávnění **Vlastník** k prostředkům služby Azure Blockchain Workbench.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název skupiny prostředků svého nasazení služby Blockchain Workbench.
4. Výběrem možnosti **Typ** tento seznam prostředků seřaďte a pak zvolte **SQL Server**.
5. V příkladu seznamu prostředků na následujícím snímku obrazovky se zobrazují dvě databáze: *master* a *lsgn-sdk*. Pravidlo brány firewall budete konfigurovat u databáze *lsgn-sdk*.

![Seznam prostředků služby Blockchain Workbench](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Vytvoření pravidla brány firewall databáze

Pravidlo brány firewall vytvoříte takto:

1. Zvolte odkaz na databázi „lsgn-sdk“.
2. V řádku nabídek vyberte **Nastavit bránu firewall serveru**.

   ![Nastavení brány firewall serveru](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. Pravidlo pro svou organizaci vytvoříte takto:

   * Zadejte **NÁZEV PRAVIDLA**.
   * Zadejte IP adresu do pole **POČÁTEČNÍ IP ADRESA** rozsahu adres.
   * Zadejte IP adresu do pole **KONCOVÁ IP ADRESA** rozsahu adres.

   ![Vytvoření pravidla brány firewall](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Pokud chcete přidat jen IP adresu svého počítače, zvolte **+ Přidat IP adresu klienta**.
        
1. Výběrem možnosti **Uložit** konfiguraci brány firewall uložte.
2. Připojením z aplikace nebo nástroje vyzkoušejte rozsah IP adres, který jste pro databázi nakonfigurovali. Použijte například SQL Server Management Studio.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazení databáze ve službě Azure Blockchain Workbench](blockchain-workbench-database-views.md)