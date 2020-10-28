---
title: Zrušit operace správy
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si, jak zrušit operace správy spravované instance Azure SQL.
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
ms.openlocfilehash: 092981f9d74a3f9f18c491ca6cee539a29e73c83
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782497"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Ruší se operace správy spravované instance Azure SQL.
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL nabízí možnost zrušit některé [operace správy](management-operations-overview.md), třeba když nasadíte novou spravovanou instanci nebo vlastnosti instance aktualizace. 

## <a name="overview"></a>Přehled

 Všechny operace správy je možné uspořádat do následujících kategorií:

- Nasazení instance (vytvoření nové instance).
- Aktualizace instance (Změna vlastností instance, jako je virtuální jádra nebo rezervované úložiště)
- Odstranění instance.

Můžete [sledovat průběh a stav operací správy](management-operations-monitor.md) a v případě potřeby zrušit některé z nich. 

Následující tabulka shrnuje operace správy bez ohledu na to, jestli je můžete zrušit, a jejich typickou celkovou dobu trvání:

Kategorie  |Operace  |Zrušitelný  |Odhadovaná doba trvání zrušení  |
|---------|---------|---------|---------|
|Nasazení |Vytvoření instance |Ano |90% dokončených operací za 5 minut. |
|Aktualizace |Horizontální navýšení kapacity úložiště instance (Pro obecné účely) |Ne |  |
|Aktualizace |Horizontální navýšení kapacity úložiště instance (Pro důležité obchodní informace) |Ano |90% dokončených operací za 5 minut. |
|Aktualizace |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro obecné účely) |Ano |90% dokončených operací za 5 minut. |
|Aktualizace |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro důležité obchodní informace) |Ano |90% dokončených operací za 5 minut. |
|Aktualizace |Instance instance služby instance (Pro obecné účely až Pro důležité obchodní informace a naopak) |Ano |90% dokončených operací za 5 minut. |
|Odstranit |Odstranění instance |Ne |  |
|Odstranit |Odstranění virtuálního clusteru (jako operace iniciované uživatelem) |Ne |  |

## <a name="cancel-management-operation"></a>Zrušit operaci správy

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li zrušit operace správy pomocí Azure Portal, postupujte podle následujících kroků:

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Přejít na okno **Přehled** vaší spravované instance SQL. 
1. Zaškrtněte políčko **oznámení** vedle probíhající operace a otevřete stránku **probíhající operace** . 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Zaškrtnutím políčka nepřetržitá operace otevřete stránku probíhající operace.":::

1. Vyberte **zrušit operaci** v dolní části stránky. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Zaškrtnutím políčka nepřetržitá operace otevřete stránku probíhající operace.":::

1. Potvrďte, že chcete operaci zrušit. 


Pokud je žádost o zrušení úspěšná, operace správy se zruší a výsledkem bude selhání. Zobrazí se oznámení o úspěšném nebo neúspěšném zrušení.

![Výsledek operace zrušení](./media/management-operations-cancel/canceling-operation-result.png)


Pokud žádost o zrušení selže nebo pokud není aktivní tlačítko Storno, znamená to, že operace správy přešla do stavu, který není možné zrušit a brzy se dokončí.  Operace správy bude pokračovat v provádění, dokud nebude dokončena.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud ještě nemáte nainstalované Azure PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Chcete-li zrušit operaci správy, je nutné zadat název operace správy. Proto nejprve pomocí příkazu Get načtěte seznam operací a pak zrušte konkrétní operaci.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Podrobné vysvětlení příkazů naleznete v tématu [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) a [stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud ještě nemáte nainstalované rozhraní příkazového řádku Azure, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

Chcete-li zrušit operaci správy, je nutné zadat název operace správy. Proto nejprve pomocí příkazu Get načtěte seznam operací a pak zrušte konkrétní operaci.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Podrobné vysvětlení příkazů najdete v tématu [AZ SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Žádost o zrušený nasazení

S rozhraním API verze 2020-02-02, jakmile je požadavek na vytvoření instance přijatý, instance začne existovat jako prostředek bez ohledu na průběh procesu nasazení (stav spravované instance je **zřizování** ). Pokud zrušíte požadavek na nasazení instance (vytvoření nové instance), bude spravovaná instance přecházet ze stavu **zřizování** na **FailedToCreate** .

Instance, které se nepodařilo vytvořit, jsou stále přítomné jako prostředek a: 

- Nejsou účtovány
- Nepočítat s omezeními prostředků (kvóta podsítě nebo vCore)
- Zachovat název instance rezervovaný – Chcete-li nasadit instanci se stejným názvem, odstraňte název neúspěšné instance, aby se uvolnil název.


> [!NOTE]
> Chcete-li minimalizovat šum v seznamu prostředků nebo spravovaných instancí, odstraňte instance, jejichž nasazení se nezdařilo, nebo instance s zrušenými nasazeními. 


## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](instance-create-quickstart.md).
- Seznam funkcí a porovnání najdete v tématu [běžné funkce SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz týkající se použití Azure Database Migration Service k migraci najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).