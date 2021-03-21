---
title: Přesunutí prostředků Azure SQL mezi oblastmi pomocí Azure Resource stěhovací
description: Přečtěte si, jak přesunout prostředky SQL Azure do jiné oblasti pomocí Azure Resource stěhovacího.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4678a6128be13ac61dc4ac67bbd1a17e99c6d24d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99820225"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Kurz: Přesunutí prostředků Azure SQL Database do jiné oblasti

V tomto kurzu se naučíte přesunout databáze a elastické fondy Azure SQL do jiné oblasti Azure pomocí nástroje [Azure Resource stěhovací](overview.md).

> [!NOTE]
> Prostředek Azure Resource stěhovací je momentálně ve verzi Preview.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte požadavky a požadavky.
> * Vyberte prostředky, které chcete přesunout.
> * Vyřešte závislosti prostředků.
> * Připraví a přesune SQL Server do cílové oblasti.
> * Příprava a přesun databází a elastických fondů.
> * Rozhodněte, zda chcete přesun zrušit nebo potvrdit. 
> * Volitelně můžete po přesunutí odebrat prostředky ve zdrojové oblasti. 

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/). Pak se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Předpoklady

-  Ověřte, že máte přístup *vlastníka* k předplatnému obsahujícímu prostředky, které chcete přesunout.
    - Při prvním přidání prostředku pro konkrétní dvojici zdroje a cíle v rámci předplatného Azure vytvoří [Správce prostředků spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve známou jako služba zjištění spravované služby (MSI)), která je pro předplatné důvěryhodná.
    - Pokud chcete vytvořit identitu a přiřadit jí požadovanou roli (přispěvatel nebo správce přístupu uživatele ve zdrojovém předplatném), účet, který použijete k přidání prostředků, potřebuje oprávnění *vlastníka* k tomuto předplatnému. [Přečtěte si další informace](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) o rolích Azure.
- Předplatné potřebuje dostatek kvót k vytvoření prostředků, které přesouváte v cílové oblasti. Pokud nemá kvótu, [požádejte o další omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Ověřte ceny a poplatky spojené s cílovou oblastí, do které přesouváte prostředky. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) vám pomůžeme.
    

## <a name="check-sql-requirements"></a>Ověřit požadavky SQL

1. [Ověřte, jestli](support-matrix-move-region-sql.md) jsou funkce databáze nebo elastického fondu podporované pro přesun do jiné oblasti.
2. V cílové oblasti vytvořte cílový server pro každý zdrojový server. [Další informace](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users).
4. Pokud jsou databáze šifrované pomocí transparentního šifrování dat (TDE) a používáte vlastní šifrovací klíč v Azure Key Vault, [Přečtěte si, jak](../key-vault/general/move-region.md) přesunout trezory klíčů do jiné oblasti.
5. Je-li povolena synchronizace dat SQL, je přesouvání členských databází podporováno. Po přesunutí musíte nastavit synchronizaci dat SQL s novou cílovou databází.
6. Před přesunutím odebrat Pokročilá nastavení zabezpečení dat. Po přesunutí [nakonfigurujte nastavení](../azure-sql/database/azure-defender-for-sql.md) na úrovni SQL Server v cílové oblasti.
7. Pokud je auditování povolené, po přesunutí se zásady obnoví na výchozí hodnoty. [Nastavte auditování](../azure-sql/database/auditing-overview.md) znovu po přesunutí.
7. Zásady uchovávání záloh zdrojové databáze se přenesou do cílové databáze. [Přečtěte si další informace](../azure-sql/database/long-term-backup-retention-configure.md) o úpravách nastavení po přesunutí.
8. Před přesunutím odeberte pravidla brány firewall na úrovni serveru. Pravidla brány firewall na úrovni databáze se při přesunutí zkopírují ze zdrojového serveru na cílový server. Po přesunutí [nastavte pravidla firewallu](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) pro SQL Server v cílové oblasti.
9. Před přesunutím odeberte nastavení automatického ladění. Po přesunutí [nastavte autoladění](../azure-sql/database/automatic-tuning-enable.md) znovu.
10. Před přesunutím odebrat nastavení upozornění databáze. Po přesunutí [obnovit](../azure-sql/database/alerts-insights-configure-portal.md) .
    
## <a name="select-resources"></a>Vybrat prostředky

Vyberte prostředky, které chcete přesunout.

- Libovolný typ podporovaného prostředku můžete vybrat ve všech skupinách prostředků ve vybrané zdrojové oblasti.
- Prostředky přesunete do cílové oblasti ve stejném předplatném jako zdrojová oblast. Pokud chcete změnit předplatné, můžete to udělat po přesunu prostředků.

1. V Azure Portal vyhledejte *prostředek Resource stěhovací*. Pak v části **služby** vyberte **Azure Resource stěhovací**.

     ![Výsledky hledání pro prostředek Resource stěhovací v Azure Portal](./media/tutorial-move-region-sql/search.png)

2. V **přehledu** klikněte na **Začínáme.**

    ![Tlačítko pro přidání prostředků pro přesun do jiné oblasti](./media/tutorial-move-region-sql/get-started.png)

3. V části **přesunout prostředky**  >  **zdroj + cíl** vyberte zdrojové předplatné a oblast.
4. V části **cíl** vyberte oblast, do které chcete prostředky přesunout. Potom klikněte na **Další**.

    ![Stránka pro výběr zdrojové a cílové oblasti](./media/tutorial-move-region-sql/source-target.png)

6. V nabídce **prostředky k přesunutí** klikněte na **vybrat prostředky**.
7. V části **vybrat prostředky** vyberte prostředky. Je možné přidat pouze prostředky podporované pro Move. Pak klikněte na **Hotovo**.

    ![Stránka pro výběr prostředků SQL k přesunutí](./media/tutorial-move-region-sql/select-resources.png)

8. V nabídce **prostředky k přesunutí** klikněte na **Další**.

9. V části **Revize a přidat** zkontrolujte nastavení zdroje a cíle. Ověřte, že jste pochopili, že metadata o přesunu se uloží do skupiny prostředků vytvořené pro tento účel v oblasti metadat.


    ![Stránku ke kontrole nastavení a pokračování v přesunutí](./media/tutorial-move-region-sql/review.png)

10. Klikněte na **pokračovat** a začněte přidávat prostředky.
11. Po úspěšném dokončení procesu přidávání klikněte na ikonu oznámení na **Přidat prostředky pro přesun** .
12. Po kliknutí na oznámení zkontrolujte prostředky na stránce **napříč oblastmi** .


> [!NOTE]
> 
> - SQL Server je nyní v *nedokončeném stavu ručního přiřazení* .
> - Další přidané prostředky jsou ve stavu *Příprava čeká na vyřízení* .
> - Pokud chcete odebrat prostředek z kolekce přesunutí, metoda pro to závisí na tom, kde se nacházíte v procesu přesunutí. [Další informace](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Vyřešit závislosti


1. V **různých oblastech**, pokud zdroje zobrazují zprávu *ověřit závislosti* ve sloupci **problémy** , klikněte na tlačítko **ověřit závislosti** . Spustí se proces ověřování.
2. Pokud se najde závislosti, klikněte na **přidat závislosti**.

    ![Tlačítko pro přidání závislostí](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. V části **přidat závislosti** vyberte závislé prostředky > **přidat závislosti**. Sledujte průběh oznámení.

4. V případě potřeby přidejte další závislosti a znovu ověřte závislosti. 

5. Na stránce **napříč oblastmi** ověřte, že prostředky jsou nyní ve stavu *Příprava čeká* bez problémů.

    ![Stránka znázorňující prostředky v připraveném stavu Příprava](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>Přesunout SQL Server

Přiřaďte cílový SQL Server v cílové oblasti a potvrďte přesunutí.

### <a name="assign-a-target-sql-server"></a>Přiřadit cílový SQL Server

1. V **různých oblastech** pro prostředek SQL Server ve sloupci **cílová konfigurace** klikněte na **prostředek není přiřazeno**.
2. Vyberte existující prostředek SQL Server v cílové oblasti. 
    
    ![Položka ukazující SQL Server stavového nastavení na potvrzení přesun čeká](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> Stav zdrojového SQL Server se změní na *nedokončené přesunutí*. 

### <a name="commit-the-sql-server-move"></a>Potvrďte SQL Server přesun

1. V **různých oblastech** vyberte SQL Server a pak klikněte na **Potvrdit přesunutí**.
2. V nabídce **potvrzení prostředků** klikněte na **Potvrdit**.

    ![Stránka pro potvrzení přesunutí SQL Server](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Sledujte průběh přesunu na panelu oznámení.

> [!NOTE]
> Po potvrzení je SQL Server nyní ve stavu *odstranění zdroje, který čeká na vyřízení* .


## <a name="prepare-resources-to-move"></a>Příprava prostředků k přesunu

Po přesunutí zdrojového SQL Server se můžete připravit na přesun dalších prostředků.

## <a name="prepare-an-elastic-pool"></a>Příprava elastického fondu

1. V **různých oblastech** vyberte zdrojový elastický fond (demo-test1-elasticpool v našem návodu) a pak klikněte na **připravit**.

    ![Tlačítko pro přípravu prostředků](./media/tutorial-move-region-sql/prepare-elastic.png)

2. V nabídce **Příprava prostředků** klikněte na **připravit**.
3. Pokud oznámení ukazují, že proces přípravy byl úspěšný, klikněte na tlačítko **aktualizovat**.

> [!NOTE]
> Elastický fond je nyní ve stavu *zahájení přesunu čeká na vyřízení* .

## <a name="prepare-a-single-database"></a>Příprava izolované databáze

1. V **různých oblastech** vyberte izolovanou databázi (ne v elastickém fondu) a pak klikněte na **připravit**.

    ![Tlačítko pro přípravu vybraných prostředků](./media/tutorial-move-region-sql/prepare-db.png)

2. V nabídce **Příprava prostředků** klikněte na **připravit**.
3. Pokud oznámení ukazují, že proces přípravy byl úspěšný, klikněte na tlačítko **aktualizovat**.

> [!NOTE]
> Databáze je nyní ve stavu *zahájení přesunutí čeká na vyřízení* a byla vytvořena v cílové oblasti.


## <a name="move-the-pool-and-prepare-pool-databases"></a>Přesunout fond a připravit databáze fondu

Pro přípravu databází v elastickém fondu musí být elastický fond ve stavu *čekání na potvrzení* . Chcete-li přejít na tento stav, zahajte přesun pro fond.

#### <a name="initiate-move---elastic-pool"></a>Spustit přesun – elastický fond

1. V **různých oblastech** vyberte zdrojový elastický fond (demo-test1-elasticpool v našem návodu) a pak klikněte na **Spustit přesun**.
2. V nabídce **přesunout prostředky** klikněte na možnost **Spustit přesun**.

    
    ![Tlačítko pro zahájení přesunu elastického fondu](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Sledujte průběh přesunu na panelu oznámení.
1. Po zobrazení oznámení o úspěšném přesunutí klikněte na **aktualizovat**.

> [!NOTE]
> Elastický fond je teď ve stavu *čeká na přesunutí* .

#### <a name="prepare-database"></a>Příprava databáze

1. V **různých oblastech** vyberte databázi (demo-test2-SQLDB v našem návodu) a pak klikněte na **připravit**.
2. V nabídce **Příprava prostředků** klikněte na **připravit**.

    ![Tlačítko pro přípravu databáze v elastickém fondu](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Během přípravy se cílová databáze vytvoří v cílové oblasti a spustí se replikace dat. Po přípravě se databáze nachází v *nedokončeném stavu přesunutí* . 

![Tlačítko pro přípravu vybrané databáze v elastickém fondu](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Přesun databází

Začněte přesouvat databáze.
1. V **různých oblastech** vyberte prostředky se stavem **zahájit přesun čeká na vyřízení**. Pak klikněte na **Spustit přesun**.
2. V nabídce **přesunout prostředky** klikněte na možnost **Spustit přesun**.

    ![Stránka pro zahájení přesunu](./media/tutorial-move-region-sql/initiate-move.png)

3. Sledujte průběh přesunu na panelu oznámení.

> [!NOTE]
> Databáze jsou nyní ve stavu *čekání na potvrzení* .


## <a name="discard-or-commit"></a>Zahodit nebo potvrdit?

Po počátečním přesunu se můžete rozhodnout, jestli chcete přesunutí potvrdit, nebo ho zahodit. 

- **Zahodit**: Pokud testujete a nechcete skutečně přesunout zdrojový prostředek, můžete zrušit jeho přesunutí. Zrušením přesunutí se daný prostředek vrátí do stavu **zahájení přesunu čeká na vyřízení**.
- **Potvrdit**: potvrzení dokončí přesun do cílové oblasti. Po potvrzení bude zdrojový prostředek ve stavu **čeká na odstranění zdroje** a Vy se můžete rozhodnout, jestli ho chcete odstranit.


## <a name="discard-the-move"></a>Zrušit přesun 

Přesunutí můžete zrušit následujícím způsobem:

1. V **různých oblastech** vyberte prostředky s **potvrzením změny stavu čeká na přesunutí** a klikněte na **zrušit přesun**.
2. V nabídce **Zrušit přesunutí** klikněte na **Zahodit**.
3. Sledujte průběh přesunu na panelu oznámení.


> [!NOTE]
> - Po zahození prostředků se ve stavu *zahájení čeká na dokončení přesunutí* .
> - Pokud je k dispozici pouze elastický fond, zahodí se průběh a elastický fond vytvořený v cílové oblasti je odstraněn.
> - Pokud existuje elastický fond s přidruženými databázemi ve stavu *čekání na přesunutí* , nemůžete tento elastický fond zahodit.
> - Pokud zrušíte databázi SQL, prostředky cílové oblasti se neodstraní. 

Pokud chcete znovu spustit přesun po zahození, vyberte databázi SQL nebo elastický fond a znovu spusťte přesun.


## <a name="commit-the-move"></a>Potvrdit přesun

Přesun databází a elastických fondů dokončíte takto:


1. Ověřte, že SQL Server je stav *odstranění zdroje čeká na vyřízení* .
2. Než potvrdíte, aktualizujte připojovací řetězce databáze do cílové oblasti.
3. V **různých oblastech** vyberte prostředky SQL a pak klikněte na **Potvrdit přesunutí**.
4. V nabídce **potvrzení prostředků** klikněte na **Potvrdit**.

    ![Přesunout změny](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Sledovat průběh potvrzení na panelu oznámení.


> [!NOTE]
> Pro databáze SQL během procesu potvrzení dojde k nějakému výpadku.
> Potvrzené databáze a elastické fondy jsou teď ve stavu *čekání na odstranění zdroje* .
> Po potvrzení aktualizujte nastavení související s databází, včetně pravidel brány firewall, zásad a výstrah, v cílové databázi.


## <a name="delete-source-resources-after-commit"></a>Odstranit zdrojové prostředky po potvrzení změn

Po přesunutí můžete případně Odstranit prostředky ve zdrojové oblasti. 

> [!NOTE]
> SQL Server servery nejde odstranit z portálu a musí se odstranit ze stránky vlastností prostředku.

1. V **různých oblastech** klikněte na název zdrojového prostředku, který chcete odstranit.
2. Vyberte **Odstranit zdroj**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Databáze Azure SQL se přesunuly do jiné oblasti Azure.
> * Elastické fondy Azure SQL byly přesunuty do jiné oblasti.

Nyní se snažíte přesunout virtuální počítače Azure do jiné oblasti.

> [!div class="nextstepaction"]
> [Přesun virtuálních počítačů Azure](./tutorial-move-region-virtual-machines.md)