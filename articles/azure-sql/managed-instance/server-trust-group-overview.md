---
title: Skupina důvěryhodnosti serverů
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
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790725"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>Nastavení a Správa důvěryhodnosti mezi spravovanými instancemi SQL pomocí skupin důvěryhodných serverů
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Skupina důvěryhodných serverů je koncept, který se používá ke správě vztahů důvěryhodnosti mezi spravovanými instancemi Azure SQL. Vytvořením skupiny se mezi jejími členy vytvoří vztah důvěryhodnosti založený na certifikátu. Tento vztah důvěryhodnosti lze použít pro různé scénáře různých instancí. Odebráním serverů ze skupiny nebo odstraněním skupiny odeberete vztah důvěryhodnosti mezi servery. Aby uživatel mohl vytvořit nebo odstranit skupinu důvěryhodných serverů, musí mít oprávnění k zápisu do spravované instance.
[Skupina důvěryhodných serverů](/azure/templates/microsoft.sql/allversions) je objekt Azure Resource Manager, který byl označen jako **Skupina důvěryhodnosti SQL** v Azure Portal.

> [!NOTE]
> Skupina důvěryhodných serverů je zavedená ve verzi Public Preview distribuovaných transakcí mezi spravovanými instancemi Azure SQL a v současné době má určitá omezení, která budou popsána dále v tomto článku.

## <a name="server-trust-group-setup"></a>Nastavení skupiny důvěryhodnosti serveru

Následující část popisuje nastavení skupiny důvěryhodných serverů.

1. Přejděte na [Azure Portal](https://portal.azure.com/).

2. Přejděte do spravované instance Azure SQL, kterou plánujete přidat do nově vytvořené skupiny důvěryhodných serverů.

3. V nastavení **zabezpečení** vyberte kartu **skupiny důvěryhodných certifikátů SQL** .

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Skupiny vztahů důvěryhodnosti serveru":::

4. Na stránce Konfigurace skupiny důvěryhodných serverů vyberte ikonu **Nová skupina** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Nová skupina":::

5. V okně vytvořit **skupinu důvěryhodných certifikátů SQL** nastavte **název skupiny**. Musí být jedinečný ve všech oblastech, kde se nacházejí členové skupiny. **Obor důvěryhodnosti** definuje typ scénáře mezi instancemi, který je povolený pro skupinu důvěryhodných serverů. Ve verzi Preview je jediným platným oborem důvěryhodnosti **distribuované transakce**, takže je předvybráno a nelze je změnit. Všichni **Členové skupiny** musí patřit do stejného **předplatného** , ale můžou být v různých skupinách prostředků. Vyberte **skupinu prostředků** a **SQL Server/instanci** pro výběr spravované instance Azure SQL, která bude členem skupiny.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Okno vytvořit skupinu důvěryhodných serverů":::

6. Po vyplnění všech povinných polí klikněte na **Uložit**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Údržba a odstraňování skupiny důvěryhodnosti serveru

Skupinu důvěryhodných serverů nelze upravovat. Chcete-li odebrat spravovanou instanci ze skupiny, je nutné skupinu odstranit a vytvořit novou.

Následující část popisuje proces odstranění skupiny důvěryhodnosti serveru. 
1. Přejděte na Azure Portal.
2. Přejděte do spravované instance, která patří do skupiny důvěryhodnosti.
3. Na stránce nastavení **zabezpečení** vyberte kartu **skupiny důvěryhodných certifikátů SQL** .
4. Vyberte skupinu důvěryhodnosti, kterou chcete odstranit.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Vybrat skupinu důvěryhodných serverů":::
5. Klikněte na **Odstranit skupinu**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Odstranit skupinu důvěryhodných serverů":::
6. Zadáním názvu skupiny důvěryhodných serverů potvrďte odstranění a klikněte na **Odstranit**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Potvrďte odstranění skupiny důvěryhodných serverů":::

> [!NOTE]
> Odstraněním skupiny důvěryhodných serverů se nemusí okamžitě odstranit vztah důvěryhodnosti mezi těmito dvěma spravovanými instancemi. Odstranění vztahu důvěryhodnosti se dá vyhovět vyvoláním [převzetí služeb](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) spravovaných instancí při selhání. Další informace najdete v části [známé problémy](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues) s nejnovějšími aktualizacemi.

## <a name="limitations"></a>Omezení

Během veřejné verze Preview platí následující omezení pro skupiny důvěryhodných serverů.
 * Název skupiny důvěryhodných serverů musí být jedinečný ve všech oblastech, kde jsou její členové.
 * Skupina může obsahovat jenom spravované instance Azure SQL a musí být ve stejném předplatném Azure.
 * Ve verzi Preview může skupina obsahovat přesně dvě spravované instance. Chcete-li spustit distribuované transakce napříč více než dvěma spravovanými instancemi, budete muset vytvořit skupinu důvěryhodných serverů pro každou dvojici spravovaných instancí.
 * Pro skupiny důvěryhodných serverů jsou distribuované transakce jediným platným oborem.
 * Skupinu důvěryhodných serverů lze spravovat pouze z Azure Portal. Podpora PowerShellu a rozhraní příkazového řádku se bude nacházet později.
 * Na Azure Portal nelze upravovat skupinu důvěryhodných serverů. Dá se vytvořit nebo vyřadit jenom.
 * Další omezení distribuovaných transakcí můžou souviset s vaším scénářem. Nejdůležitější je, že mezi spravovanými instancemi přes soukromé koncové body, prostřednictvím sítě VNET nebo VNET peering, musí existovat spojení. Ujistěte se, že jste si vědomi aktuálních [omezení distribuovaných transakcí pro spravovanou instanci](../database/elastic-transactions-overview.md#limitations).

## <a name="next-steps"></a>Další kroky

* Další informace o distribuovaných transakcích ve spravované instanci SQL Azure najdete v tématu [distribuované transakce](../database/elastic-transactions-overview.md).
* Informace o aktualizacích vydaných verzí a známých problémech najdete v tématu [poznámky k verzi spravované instance](../database/doc-changes-updates-release-notes.md).
* Pokud máte žádosti o funkce, přidejte je do [fóra spravované instance](https://feedback.azure.com/forums/915676-sql-managed-instance).