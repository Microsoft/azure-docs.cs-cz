---
title: Monitorování operací správy
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si o různých způsobech monitorování operací správy spravované instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: bdb021bc0247972fa29975c62bc9214e3b474e2c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995356"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Monitorování operací správy spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL poskytuje monitorování [operací správy](management-operations-overview.md) , které slouží k nasazení nových spravovaných instancí, aktualizaci vlastností instance nebo odstranění instancí, pokud už nepotřebujete. 

## <a name="overview"></a>Přehled

Všechny operace správy je možné uspořádat do následujících kategorií:

- Nasazení instance (vytvoření nové instance).
- Aktualizace instance (Změna vlastností instance, jako je virtuální jádra nebo rezervované úložiště)
- Odstranění instance.

Většina operací správy je [dlouhodobě spuštěných operací](management-operations-overview.md#duration). Proto je nutné monitorovat stav nebo postupovat podle kroků operace. 

K dispozici je několik způsobů, jak monitorovat operace správy spravované instance:

- [Nasazení skupiny prostředků](../../azure-resource-manager/templates/deployment-history.md)
- [Protokol aktivit](../../azure-monitor/platform/activity-log.md)
- [Rozhraní API pro Operations Managed instance](#managed-instance-operations-api)


Následující tabulka porovnává možnosti monitorování operací správy: 

| Možnost | Uchovávání | Podporuje zrušení | Vytvořit | Aktualizace | Odstranit | Zrušit | Postup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Nasazení skupiny prostředků | Neomezený<sup>1</sup> | Ne<sup>2</sup> | Viditelné | Viditelné | Neviditelné | Viditelné | Neviditelné |
| Protokol aktivit | 90 dnů | No | Viditelné | Viditelné | Viditelné | Viditelné |  Neviditelné |
| Rozhraní API pro Operations Managed instance | 24 hodin | [Ano](management-operations-cancel.md) | Viditelné | Viditelné | Viditelné | Viditelné | Viditelné |
|  |  |  |  |  |  |  | |

<sup>1</sup> historie nasazení pro skupinu prostředků je omezená na 800 nasazení.

<sup>2</sup> nasazení skupin prostředků podporují operaci zrušit. Kvůli zrušení logiky se ale zruší jenom operace naplánovaná pro nasazení po akci zrušení. Průběžné nasazování se nezrušilo, když se zruší nasazení skupiny prostředků. Vzhledem k tomu, že nasazení spravované instance se skládá z jednoho dlouhého běhu (z perspektivy Azure Resource Manageru), zrušení nasazení skupiny prostředků nebude rušit nasazení spravované instance a operace se dokončí. 

## <a name="managed-instance-operations-api"></a>Rozhraní API pro Operations Managed instance

Rozhraní API pro operace správy jsou speciálně navržena pro monitorování operací. Monitorování operací spravované instance může poskytovat přehled o parametrech operací a krocích operací a také [rušit konkrétní operace](management-operations-cancel.md). Kromě podrobností o operacích a příkazu cancelů se toto rozhraní API dá použít ve skriptech Automation s nasazeními s více prostředky – na základě kroku průběhu můžete aktivovat některé závislé nasazení prostředků.

Rozhraní API: 

| Příkaz | Popis |
| --- | --- |
|[Operace spravované instance – získání](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|Načte operaci správy na spravované instanci.|
|[Operace spravované instance – zrušit](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|Zruší asynchronní operaci na spravované instanci.|
|[Operace spravované instance – seznam podle spravované instance](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Načte seznam operací provedených na spravované instanci.|

> [!NOTE]
> K zobrazení operace vytvoření spravované instance v seznamu operací použijte rozhraní API verze 2020-02-02. Toto je výchozí verze používaná v Azure Portal a nejnovější balíčky PowerShellu a rozhraní příkazového řádku Azure CLI.

## <a name="monitor-operations"></a>Monitorování operací

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V Azure Portal použijte stránku **Přehled** spravované instance a sledujte operace spravované instance. 

Například **operace vytvořit** je zobrazena na začátku procesu vytváření na stránce **Přehled** : 

![Průběh vytváření spravované instance](./media/management-operations-monitor/monitoring-create-operation.png)

Výběrem **trvalé operace** otevřete stránku **probíhající operace** a zobrazte operace **vytvořit** nebo **aktualizovat** . Také je možné [Zrušit](management-operations-cancel.md) i operace z této stránky.  

![Podrobnosti o operaci spravované instance](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Operace vytváření odeslané prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure a dalších nástrojů pomocí REST API verze 2020-02-02 [se dají zrušit](management-operations-cancel.md). REST API verzí starších než 2020-02-02 použitých k odeslání operace vytvoření spustí nasazení instance, ale nasazení nebude uvedené v rozhraní API operací a nedá se zrušit.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rutina Get-AzSqlInstanceOperation získává informace o operacích ve spravované instanci. Můžete zobrazit všechny operace na spravované instanci nebo zobrazit konkrétní operaci zadáním názvu operace.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Podrobné vysvětlení příkazů naleznete v tématu [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Seznam AZ SQL mi op získá seznam operací provedených na spravované instanci. Pokud ještě nemáte nainstalované rozhraní příkazového řádku Azure, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Podrobné vysvětlení příkazů najdete v tématu [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](instance-create-quickstart.md).
- Seznam funkcí a porovnání najdete v tématu [běžné funkce SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz týkající se použití Azure Database Migration Service k migraci najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
