---
title: Řízení přístupu na základě role ve službě Azure Cosmos DB
description: Přečtěte si, jak Azure Cosmos DB poskytuje ochranu databáze pomocí integrace služby Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: 10713b264429b5588826421231e45194ebed33f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569182"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Řízení přístupu na základě role ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje integrované řízení přístupu na základě role (RBAC) pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil v Azure Active Directory může přiřadit tyto role Azure uživatelům, skupinám, instančním objektům nebo spravovaným identitám pro udělení nebo odepření přístupu k prostředkům a operacím na Azure Cosmos DBch prostředcích. Přiřazování rolí je vymezené jenom na úrovni řízení a přístup, což zahrnuje přístup k účtům Azure Cosmos, databázím, kontejnerům a nabídkám (propustnost).

## <a name="built-in-roles"></a>Vestavěné role

Níže jsou uvedené předdefinované role podporované nástrojem Azure Cosmos DB:

|**Předdefinovaná role**  |**Popis**  |
|---------|---------|
|[Přispěvatel účtu DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Může spravovat účty Azure Cosmos DB.|
|[Čtečka účtů Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Může číst data Azure Cosmos DB účtu.|
|[Cosmos – operátor zálohování](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Může odeslat žádost o obnovení pro databázi Azure Cosmos nebo kontejner. Nelze získat přístup k žádným datům ani použít Průzkumník dat.|
|[Operátor Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Může zřídit účty, databáze a kontejnery služby Azure Cosmos. Nelze získat přístup k žádným datům ani použít Průzkumník dat.|

> [!IMPORTANT]
> Podpora RBAC v Azure Cosmos DB se vztahuje pouze na operace řízení roviny. Operace roviny dat se zabezpečují pomocí primárních klíčů nebo tokenů prostředků. Další informace najdete v tématu [zabezpečený přístup k datům v Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Správa identit a přístupu (IAM)

Podokno **řízení přístupu (IAM)** v Azure Portal slouží ke konfiguraci řízení přístupu na základě role v prostředcích Azure Cosmos. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Pro jednotlivce a skupiny můžete použít předdefinované role nebo vlastní role. Následující snímek obrazovky ukazuje integraci služby Active Directory (RBAC) pomocí řízení přístupu (IAM) v Azure Portal:

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Řízení přístupu (IAM) ve Azure Portal – demonstrace zabezpečení databáze":::

## <a name="custom-roles"></a>Vlastní role

Kromě předdefinovaných rolí můžou uživatelé také vytvářet [vlastní role](../role-based-access-control/custom-roles.md) v Azure a tyto role použít pro instanční objekty ve všech předplatných v rámci svého tenanta služby Active Directory. Vlastní role poskytují uživatelům způsob, jak vytvořit definice rolí Azure s vlastní sadou operací poskytovatele prostředků. Informace o tom, které operace jsou k dispozici pro vytváření vlastních rolí pro Azure Cosmos DB najdete v tématu [Azure Cosmos DB operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) .

> [!TIP]
> Vlastní role, které potřebují přístup k datům uloženým v Cosmos DB nebo používají Průzkumník dat v Azure Portal musí mít `Microsoft.DocumentDB/databaseAccounts/listKeys/*` akci.

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Zabránění změnám z Azure Cosmos DB SDK

Poskytovatel prostředků Azure Cosmos DB může být uzamčený, aby se zabránilo jakýmkoli změnám prostředků z klienta připojujícího se pomocí klíčů účtu (což jsou aplikace připojující se přes sadu Azure Cosmos SDK). To zahrnuje také změny provedené z Azure Portal. Tato funkce může být žádoucí pro uživatele, kteří mají vyšší úroveň řízení a zásad správného řízení pro produkční prostředí. Zabránění změnám v sadě SDK také umožňuje používat funkce, jako jsou zámky prostředků a diagnostické protokoly pro operace roviny řízení. Klientům, kteří se připojují z Azure Cosmos DB SDK, se nebude moct měnit žádná vlastnost pro účty, databáze, kontejnery a propustnost Azure Cosmos. Operace týkající se čtení a zápisu dat do kontejnerů Cosmos nejsou ovlivněny.

Když je tato funkce povolená, můžou se změny libovolného prostředku udělat jenom od uživatele s napravou rolí Azure a Azure Active Directory přihlašovacích údajů, včetně identit spravovaných služeb.

> [!WARNING]
> Povolení této funkce může mít vliv na vaši aplikaci. Ujistěte se, že před povolením rozumíte dopadu.

### <a name="check-list-before-enabling"></a>Před povolením ověřte seznam.

Toto nastavení zabrání jakýmkoli změnám prostředků Cosmos z libovolného klienta, který se připojuje pomocí klíčů účtu, včetně jakékoli Cosmos DB SDK, všech nástrojů, které se připojují prostřednictvím klíčů účtu nebo z Azure Portal. Chcete-li zabránit problémům nebo chybám v aplikacích po povolení této funkce, ověřte, zda aplikace nebo Azure Portal uživatelé před povolením této funkce provedou některou z následujících akcí, včetně:

- Jakékoli změny účtu Cosmos, včetně jakýchkoli vlastností nebo přidávání nebo odebírání oblastí.

- Vytváření a odstraňování podřízených prostředků, jako jsou databáze a kontejnery. To zahrnuje prostředky pro jiná rozhraní API, jako jsou Cassandra, MongoDB, Gremlin a tabulkové prostředky.

- Aktualizuje se propustnost u prostředků na úrovni databáze nebo kontejneru.

- Úprava vlastností kontejneru včetně zásad indexů, TTL a jedinečných klíčů.

- Úprava uložených procedur, triggerů nebo uživatelsky definovaných funkcí.

Pokud vaše aplikace (nebo uživatelé prostřednictvím Azure Portal) provádějí některé z těchto akcí, bude nutné provést migraci, aby je bylo možné provádět prostřednictvím [šablon ARM](manage-sql-with-resource-manager.md), [PowerShellu](manage-with-powershell.md), rozhraní příkazového [řádku Azure CLI](manage-with-cli.md), REST nebo [knihovny pro správu Azure](https://github.com/Azure-Samples/cosmos-management-net). Všimněte si, že Správa Azure je dostupná v [několika jazycích](https://docs.microsoft.com/azure/?product=featured#languages-and-tools).

### <a name="set-via-arm-template"></a>Nastavit přes šablonu ARM

Chcete-li tuto vlastnost nastavit pomocí šablony ARM, aktualizujte stávající šablonu nebo exportujte novou šablonu pro aktuální nasazení a pak zahrňte `"disableKeyBasedMetadataWriteAccess": true` do vlastností `databaseAccounts` prostředků. Níže je uveden základní příklad šablony Azure Resource Manager s tímto nastavením vlastnosti.

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

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md)
- [Vlastní role Azure](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
