---
title: Nakonfigurujte oprávnění k obnovení účtu Azure Cosmos DB.
description: Naučte se izolovat a omezovat oprávnění obnovit pro účet průběžného zálohování ke konkrétní roli nebo objektu zabezpečení. Ukazuje, jak přiřadit předdefinovanou roli pomocí Azure Portal, CLI nebo definovat vlastní roli.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 82af70547d20509c48f1e07bbc7610fc666a6da1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393050"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Správa oprávnění k obnovení účtu Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB umožňuje izolaci a omezení oprávnění obnovit pro účet průběžného zálohování (Preview) ke konkrétní roli nebo objektu zabezpečení. Vlastník účtu může spustit obnovení a přiřadit roli ostatním objektům zabezpečení k provedení operace obnovení. Tato oprávnění se dají použít v oboru předplatného nebo v oboru zdrojového účtu, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Seznam rolí vyžadovaných k provedení operace obnovení." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Obor je sada prostředků, které mají přístup. Další informace o oborech najdete v dokumentaci k [Azure RBAC](../role-based-access-control/scope-overview.md) . V Azure Cosmos DB jsou příslušné obory zdrojové předplatné a databázový účet pro většinu případů použití. Objekt zabezpečení, který provádí akce obnovení, by měl mít oprávnění k zápisu do cílové skupiny prostředků.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Přiřazení rolí pro obnovení pomocí Azure Portal

Aby bylo možné provést obnovení, uživatel nebo objekt zabezpečení potřebuje oprávnění k obnovení (což je oprávnění *obnovit/akce* ) a oprávnění k zřízení nového účtu (s oprávněním k *zápisu* ).  K udělení těchto oprávnění může vlastník přiřadit `CosmosRestoreOperator` `Cosmos DB Operator` objektu zabezpečení a předdefinované role.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)

1. Přejděte do svého předplatného, přejděte na kartu **řízení přístupu (IAM)** a vyberte **Přidat**  >  **přiřazení role přidat** .

1. V podokně **Přidat přiřazení role** pro pole **role** vyberte role **CosmosRestoreOperator** . Vyberte možnost **uživatel, skupina nebo instanční objekt** pro pole **přiřadit přístup k** a vyhledejte jméno nebo ID e-mailu uživatele, jak je znázorněno na následujícím obrázku:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Přiřaďte role operátorů CosmosRestoreOperator a Cosmos DB." border="true":::

1. Výběrem možnosti **Uložit** udělte oprávnění *obnovit/akce* .

1. Opakujte krok 3 s rolí **operátora Cosmos DB** , abyste udělili oprávnění k zápisu. Při přiřazení této role z Azure Portal udělí oprávnění obnovit celému předplatnému.

## <a name="permission-scopes"></a>Obory oprávnění

|Obor  |Příklad  |
|---------|---------|
|Předplatné | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Skupina prostředků | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Prostředek účtu CosmosDB obnovitelné | /Subscriptions/00000000-0000-0000-0000-000000000000/Providers/Microsoft.DocumentDB/umístění/Západní USA/restorableDatabaseAccounts/23e99a35-CD36-4df4-9614-f767a03b9995|

Prostředek účtu obnovitelné se dá extrahovat z výstupu `az cosmosdb restorable-database-account list --name <accountname>` příkazu v CLI nebo `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` rutině v PowerShellu. Atribut Name ve výstupu představuje `instanceID` účet obnovitelné. Další informace najdete v článku o [PowerShellu](continuous-backup-restore-powershell.md) nebo rozhraní příkazového [řádku](continuous-backup-restore-command-line.md) .

## <a name="permissions"></a>Oprávnění

K provádění různých aktivit, které se vztahují k obnovení účtů režimu nepřetržitého zálohování, jsou potřeba následující oprávnění:

|Oprávnění  |Dopad  |Minimální rozsah  |Maximální rozsah  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Tato oprávnění jsou nutná k vytvoření obnoveného účtu nasazením šablony ARM. Postup nastavení této role najdete níže v ukázce oprávnění [RestorableAction](#custom-restorable-action) . | Nelze použít | Nelze použít  |
|Microsoft.DocumentDB/databaseAccounts/Write | Toto oprávnění se vyžaduje k obnovení účtu do skupiny prostředků. | Skupina prostředků, pod kterou se vytvoří obnovený účet. | Předplatné, pod kterým je vytvořený obnovený účet |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |Toto oprávnění je vyžadováno v oboru účtu databáze zdrojového obnovitelné, aby bylo možné provádět akce obnovení.  | Prostředek *RestorableDatabaseAccount* patřící k obnovenému zdrojovému účtu. Tato hodnota je také dána `ID` vlastností prostředku databázového účtu obnovitelné. Příkladem účtu obnovitelné je */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>* | Předplatné obsahující účet databáze obnovitelné Skupinu prostředků nelze zvolit jako obor.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |Toto oprávnění je vyžadováno v oboru databázového účtu obnovitelné zdrojového kódu pro výpis účtů databáze, které je možné obnovit.  | Prostředek *RestorableDatabaseAccount* patřící k obnovenému zdrojovému účtu. Tato hodnota je také dána `ID` vlastností prostředku databázového účtu obnovitelné. Příkladem účtu obnovitelné je */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| Předplatné obsahující účet databáze obnovitelné Skupinu prostředků nelze zvolit jako obor.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | Toto oprávnění se vyžaduje v oboru zdrojového účtu obnovitelné, aby bylo možné číst prostředky obnovitelné, jako je například seznam databází a kontejnerů pro účet obnovitelné.  | Prostředek *RestorableDatabaseAccount* patřící k obnovenému zdrojovému účtu. Tato hodnota je také dána `ID` vlastností prostředku databázového účtu obnovitelné. Příkladem účtu obnovitelné je */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/Locations/RegionName/restorableDatabaseAccounts/<GUID-instanceid>*| Předplatné obsahující účet databáze obnovitelné Skupinu prostředků nelze zvolit jako obor. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Scénáře přiřazení role CLI Azure pro obnovení v různých oborech

Role s oprávněním se dají přiřadit k různým oborům, abyste dosáhli podrobného řízení toho, kdo může operaci obnovení provést v rámci předplatného nebo daného účtu.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Přiřazení možnosti obnovení z libovolného účtu obnovitelné v předplatném

Přiřazení `CosmosRestoreOperator` předdefinované role na úrovni předplatného

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Přiřazení možnosti obnovení z konkrétního účtu

* Přiřaďte akci zápisu uživatele do konkrétní skupiny prostředků. Tato akce je nutná k vytvoření nového účtu ve skupině prostředků.

* Přiřaďte předdefinovanou roli *CosmosRestoreOperator* konkrétnímu účtu databáze obnovitelné, který se musí obnovit. V následujícím příkazu se rozsah pro *RestorableDatabaseAccount* načítá z `ID` vlastnosti ve výstupu `az cosmosdb restorable-database-account` (Pokud se používá CLI) nebo  `Get-AzCosmosDBRestorableDatabaseAccount` (Pokud používáte PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Přiřaďte schopnost obnovení z libovolného zdrojového účtu ve skupině prostředků.
Tato operace v tuto chvíli není podporovaná.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>Vytvoření vlastní role pro akci obnovení pomocí rozhraní příkazového řádku

Vlastník předplatného může poskytnout oprávnění k obnovení do jakékoli jiné identity Azure AD. Oprávnění k obnovení je založené na akci: `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` a měla by být zahrnutá do oprávnění obnovit. K dispozici je předdefinovaná role s názvem *CosmosRestoreOperator* , která má tuto roli zahrnutou. Můžete buď přiřadit oprávnění pomocí této předdefinované role nebo vytvořit vlastní roli.

RestorableAction níže představuje vlastní roli. Tuto roli musíte explicitně vytvořit. Následující šablona JSON vytvoří vlastní roli *RestorableAction* s oprávněním obnovit:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Dále použijte následující příkaz pro nasazení šablony k vytvoření role s oprávněním obnovit pomocí šablony ARM:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [PowerShellu](continuous-backup-restore-powershell.md), rozhraní příkazového [řádku](continuous-backup-restore-command-line.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Model prostředků režimu průběžné zálohování](continuous-backup-restore-resource-model.md)
