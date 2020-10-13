---
title: Skupina důvěryhodných serverů
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si o skupině důvěryhodných serverů a o tom, jak spravovat vztahy důvěryhodnosti mezi spravovanými instancemi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 911d7ffa2b1d313147ca73d0ceb285ea2e84b1f7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979490"
---
# <a name="use-server-trust-groups-to-setup-and-manage-trust-between-sql-managed-instances"></a>Nastavení a Správa důvěryhodnosti mezi spravovanými instancemi SQL pomocí skupin důvěryhodných serverů
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Skupina důvěryhodných serverů je koncept, který se používá ke správě vztahů důvěryhodnosti mezi spravovanými instancemi Azure SQL. Vytvořením skupiny a přidáním instancí k tomuto vztahu důvěryhodnosti založeného na certifikátech se vytvoří mezi všemi členy a dá se použít pro různé scénáře různých instancí. Odebráním serverů ze skupiny nebo odstraněním skupiny dojde k odebrání vztahu důvěryhodnosti mezi servery. Aby uživatel mohl vytvořit nebo odstranit skupinu důvěryhodných serverů, musí mít oprávnění k zápisu do spravované instance.
[Skupina důvěryhodných serverů](https://aka.ms/mi-server-trust-group-arm) je Azure Resource Manager objekt, který odpovídá entitě Azure Portal s názvem **Skupina důvěryhodnosti SQL**.

> [!NOTE]
> Skupina důvěryhodných serverů je zavedená ve verzi Public Preview distribuovaných transakcí mezi spravovanými instancemi Azure SQL a v současné době má určitá omezení, která budou popsána dále v tomto článku.

## <a name="server-trust-group-setup"></a>Nastavení skupiny důvěryhodnosti serveru

Následující část popisuje nastavení skupiny důvěryhodných serverů.

1. Přejděte na web [Azure Portal](https://portal.azure.com/).

2. Přejděte do spravované instance Azure SQL, kterou plánujete přidat do nově vytvořené skupiny důvěryhodných serverů.

3. V nastavení **zabezpečení** vyberte kartu **skupiny důvěryhodných certifikátů SQL** .

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Skupiny důvěryhodných serverů":::

4. Na stránce Konfigurace skupiny důvěryhodných serverů vyberte ikonu **Nová skupina** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Skupiny důvěryhodných serverů":::

5. V okně vytvořit **skupinu důvěryhodných certifikátů SQL** nastavte **název skupiny**. Musí být jedinečný ve všech oblastech, kde se nacházejí členové skupiny. **Obor důvěryhodnosti** definuje typ scénáře mezi instancemi, který je povolený pro skupinu důvěryhodných serverů a ve verzi Preview jediný příslušný obor důvěryhodnosti je **distribuovaných transakcí**, takže je předem vybraný a nedá se změnit. Všichni **Členové skupiny** musí patřit do stejného **předplatného** , ale můžou být v různých skupinách prostředků. Vyberte **skupinu prostředků** a **SQL Server/instanci** pro výběr spravované instance Azure SQL, která bude členem skupiny.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Skupiny důvěryhodných serverů":::

6. Po vyplnění všech povinných polí klikněte na **Uložit**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Údržba a odstraňování skupiny důvěryhodnosti serveru

Neexistuje žádný způsob, jak upravit skupinu důvěryhodných serverů. Chcete-li odebrat spravovanou instanci ze skupiny, je třeba skupinu odstranit a vytvořit novou.

Následující část popisuje proces odstranění skupiny důvěryhodnosti serveru. 
1. Přejděte na Azure Portal.
2. Přejděte do spravované instance, která patří do skupiny důvěryhodnosti.
3. Na stránce nastavení **zabezpečení** vyberte kartu **skupiny důvěryhodných certifikátů SQL** .
4. Vyberte skupinu důvěryhodnosti, kterou chcete odstranit.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Skupiny důvěryhodných serverů":::
5. Klikněte na **Odstranit skupinu**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Skupiny důvěryhodných serverů":::
6. Zadáním názvu skupiny důvěryhodných serverů potvrďte odstranění a klikněte na **Odstranit**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Skupiny důvěryhodných serverů":::

> [!NOTE]
> Odstraněním skupiny důvěryhodných serverů se nemusí okamžitě odstranit vztah důvěryhodnosti mezi těmito dvěma spravovanými instancemi. Odstranění vztahu důvěryhodnosti se dá vyhovět vyvoláním [převzetí služeb](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) spravovaných instancí při selhání. Další informace najdete v části [známé problémy](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) s nejnovějšími aktualizacemi.

## <a name="limitations"></a>Omezení

Během veřejné verze Preview platí následující omezení pro skupiny důvěryhodných serverů.
 * Název skupiny důvěryhodných serverů musí být jedinečný ve všech oblastech, kde jsou její členové.
 * Skupina může obsahovat jenom spravované instance Azure SQL a musí být ve stejném předplatném Azure.
 * Skupina může mít přesně dvě spravované instance. Pokud potřebujete provést distribuované transakce napříč více než dvěma spravovanými instancemi, které lze provést vytvořením skupiny důvěryhodných serverů pro každý pár spravovaných instancí.
 * Pro skupiny důvěryhodných serverů jsou distribuované transakce jediným platným oborem.
 * Skupinu důvěryhodných serverů lze spravovat pouze z Azure Portal. Podpora PowerShellu a rozhraní příkazového řádku se bude nacházet později.
 * Na Azure Portal nelze upravovat skupinu důvěryhodných serverů. Dá se vytvořit nebo vyřadit jenom.
 * Další omezení distribuovaných transakcí můžou souviset s vaším scénářem. Nejvýraznějším řešením je, že spravované instance SQL, které se účastní skupiny důvěryhodných serverů, musí být dosažitelné prostřednictvím privátních koncových bodů, nebo v jiném připojení musí fungovat na úrovni virtuální sítě. Ujistěte se prosím, že jste si vědomi aktuálních [omezení distribuovaných transakcí pro Azure SQL Managed instance](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations).

## <a name="next-steps"></a>Další kroky

* Další informace o distribuovaných transakcích ve spravované instanci SQL Azure najdete v tématu [distribuované transakce](../database/elastic-transactions-overview.md).
* Informace o aktualizacích vydaných verzí a známých problémech najdete v tématu [poznámky k verzi spravované instance](../database/doc-changes-updates-release-notes.md).
* V případě žádostí o funkce je prosím přidejte do [fóra spravované instance](https://feedback.azure.com/forums/915676-sql-managed-instance).