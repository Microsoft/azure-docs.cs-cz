---
title: Řízení přístupu na základě role ve službě Azure Cosmos DB
description: Přečtěte si, jak Azure Cosmos DB poskytuje ochranu databáze pomocí integrace služby Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 0782d5f091671a235df1ab85a8b9706c7efe9170
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509028"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Řízení přístupu na základě role ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje integrované řízení přístupu na základě role (RBAC) pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil v Azure Active Directory může přiřadit tyto role RBAC uživatelům, skupinám, instančním objektům nebo spravovaným identitám pro udělení nebo odepření přístupu k prostředkům a operacím na Azure Cosmos DBch prostředcích. Přiřazování rolí je vymezené jenom na úrovni řízení a přístup, což zahrnuje přístup k účtům Azure Cosmos, databázím, kontejnerům a nabídkám (propustnost).

## <a name="built-in-roles"></a>Vestavěné role

Níže jsou uvedené předdefinované role podporované nástrojem Azure Cosmos DB:

|**Předdefinovaná role**  |**Popis**  |
|---------|---------|
|[Přispěvatel účtu DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Může spravovat účty Azure Cosmos DB.|
|[Čtečka účtů Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Může číst data Azure Cosmos DB účtu.|
|[Cosmos – operátor zálohování](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Může odeslat žádost o obnovení pro databázi Azure Cosmos nebo kontejner.|
|[Operátor Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Může zřídit účty, databáze a kontejnery Azure Cosmos, ale nemůže získat přístup k klíčům, které jsou nutné pro přístup k datům.|

> [!IMPORTANT]
> Podpora RBAC v Azure Cosmos DB se vztahuje pouze na operace řízení roviny. Operace roviny dat se zabezpečují pomocí hlavních klíčů nebo tokenů prostředků. Další informace najdete v tématu [zabezpečený přístup k datům v Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Správa identit a přístupu (IAM)

Podokno **řízení přístupu (IAM)** v Azure Portal slouží ke konfiguraci řízení přístupu na základě role v prostředcích Azure Cosmos. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Pro jednotlivce a skupiny můžete použít předdefinované role nebo vlastní role. Následující snímek obrazovky ukazuje integraci služby Active Directory (RBAC) pomocí řízení přístupu (IAM) v Azure Portal:

![Řízení přístupu (IAM) ve Azure Portal – demonstrace zabezpečení databáze](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Vlastní role

Kromě předdefinovaných rolí můžou uživatelé také vytvářet [vlastní role](../role-based-access-control/custom-roles.md) v Azure a tyto role použít pro instanční objekty ve všech předplatných v rámci svého tenanta služby Active Directory. Vlastní role poskytují uživatelům způsob, jak vytvořit definice rolí RBAC s vlastní sadou operací poskytovatele prostředků. Informace o tom, které operace jsou k dispozici pro vytváření vlastních rolí pro Azure Cosmos DB najdete v tématu [Azure Cosmos DB operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) .

## <a name="preventing-changes-from-cosmos-sdk"></a>Zabránění změnám ze sady Cosmos SDK

> [!WARNING]
> Povolení této funkce může mít nebezpečné vliv na vaši aplikaci. Před povolením této funkce si důkladně přečtěte.

Poskytovatel prostředků Azure Cosmos DB může být uzamčený, aby se zabránilo jakýmkoli změnám prostředků provedeným z libovolného klienta připojujícího se pomocí klíčů účtu (tj. aplikace připojující prostřednictvím Cosmos SDK). To zahrnuje také změny provedené z Azure Portal. To může být žádoucí pro uživatele, kteří mají vyšší úrovně řízení a řízení pro produkční prostředí, a umožňují funkce, jako jsou zámky prostředků, a také povolení diagnostických protokolů pro operace roviny řízení. Klientům, kteří se připojují prostřednictvím Cosmos DB SDK, bude znemožněna změna jakékoli vlastnosti pro účty Cosmos, databáze, kontejnery a propustnost. Operace zahrnující čtení a zápis dat do kontejnerů Cosmos nejsou ovlivněné.

Když je tato možnost nastavená, dají se změny libovolného prostředku udělat jenom od uživatele s patřičnou rolí RBAC a Azure Active Directory přihlašovacích údajů včetně identit spravovaných služeb.

### <a name="check-list-before-enabling"></a>Před povolením ověřte seznam.

Toto nastavení zabrání jakýmkoli změnám prostředků Cosmos z libovolného klienta, který se připojuje pomocí klíčů účtu, včetně jakékoli Cosmos DB SDK, všech nástrojů, které se připojují prostřednictvím klíčů účtu nebo z Azure Portal. Chcete-li zabránit problémům nebo chybám v aplikacích po povolení této funkce, ověřte, zda aplikace nebo Azure Portal uživatelé před povolením této funkce provedou některou z následujících akcí, včetně:

- Jakékoli změny účtu Cosmos, včetně jakýchkoli vlastností nebo přidávání nebo odebírání oblastí.

- Vytváření a odstraňování podřízených prostředků, jako jsou databáze a kontejnery. To zahrnuje prostředky pro jiné rozhraní API, jako jsou Cassandra, MongoDB, Gremlin a zdroje tabulek.

- Aktualizuje se propustnost u prostředků na úrovni databáze nebo kontejneru.

- Úprava vlastností kontejneru včetně zásad indexů, TTL a jedinečných klíčů.

- Úprava uložených procedur, triggerů nebo uživatelsky definovaných funkcí.

Pokud vaše aplikace (nebo uživatelé prostřednictvím Azure Portal) provádějí některé z těchto akcí, bude nutné provést migraci, aby je bylo možné provádět prostřednictvím [šablon ARM](manage-sql-with-resource-manager.md), [PowerShellu](manage-with-powershell.md), rozhraní příkazového [řádku Azure CLI](manage-with-cli.md), [REST](/rest/api/cosmos-db-resource-provider/) nebo [knihovny pro správu Azure](https://github.com/Azure-Samples/cosmos-management-net). Všimněte si, že Správa Azure je dostupná v [několika jazycích](https://docs.microsoft.com/azure/?product=featured#languages-and-tools).

### <a name="set-via-arm-template"></a>Nastavit přes šablonu ARM

Pokud chcete tuto vlastnost nastavit pomocí šablony ARM, aktualizujte stávající šablonu nebo exportujte novou šablonu pro aktuální nasazení a pak `"disableKeyBasedMetadataWriteAccess": true` do vlastností prostředků databaseAccounts přidejte. Níže je uveden základní příklad šablony Azure Resource Manager s tímto nastavením vlastnosti.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Ujistěte se, že jste při redploying s touto vlastností zahrnuli Další vlastnosti svého účtu a podřízených prostředků. Tuto šablonu nesaďte tak, jak je, nebo se resetují všechny vlastnosti vašeho účtu.

### <a name="set-via-azure-cli"></a>Nastavení prostřednictvím rozhraní příkazového řádku Azure

Pokud chcete povolit používání rozhraní příkazového řádku Azure, použijte následující příkaz:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Nastavení prostřednictvím PowerShellu

Pokud chcete povolit použití Azure PowerShell, použijte následující příkaz:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Další kroky

- [Co je řízení přístupu na základě role (RBAC) pro prostředky Azure](../role-based-access-control/overview.md)
- [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
