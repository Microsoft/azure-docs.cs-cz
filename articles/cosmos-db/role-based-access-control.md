---
title: Řízení přístupu na základě role v Azure Cosmos DB s integrací Azure Active Directory
description: Přečtěte si, jak Azure Cosmos DB poskytuje ochranu databáze pomocí integrace služby Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: e519df8c116244b0c74be6b189d99599d89dee77
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582830"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Řízení přístupu na základě role v Azure Cosmos DB

Azure Cosmos DB poskytuje integrované řízení přístupu na základě role (RBAC) pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil v Azure Active Directory může přiřadit tyto role RBAC uživatelům, skupinám, instančním objektům nebo spravovaným identitám pro udělení nebo odepření přístupu k prostředkům a operacím na Azure Cosmos DBch prostředcích. Přiřazování rolí je vymezené jenom na úrovni řízení a přístup, což zahrnuje přístup k účtům Azure Cosmos, databázím, kontejnerům a nabídkám (propustnost).

## <a name="built-in-roles"></a>Vestavěné role

Níže jsou uvedené předdefinované role podporované nástrojem Azure Cosmos DB:

|**Předdefinovaná role**  |**Popis**  |
|---------|---------|
|[Přispěvatel účtů DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Může spravovat účty Azure Cosmos DB.|
|[Čtečka účtů Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Může číst data Azure Cosmos DB účtu.|
|[Cosmos – operátor zálohování](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Může odeslat žádost o obnovení pro databázi Azure Cosmos nebo kontejner.|
|[Operátor Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Může zřídit účty, databáze a kontejnery Azure Cosmos, ale nemůže získat přístup k klíčům, které jsou nutné pro přístup k datům.|

> [!IMPORTANT]
> Podpora RBAC v Azure Cosmos DB se vztahuje pouze na operace řízení roviny. Operace roviny dat se zabezpečují pomocí hlavních klíčů nebo tokenů prostředků. Další informace najdete v tématu [zabezpečený přístup k datům v Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Správa identit a řízení přístupu (IAM)

Podokno **řízení přístupu (IAM)** v Azure Portal slouží ke konfiguraci řízení přístupu na základě role v prostředcích Azure Cosmos. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Pro jednotlivce a skupiny můžete použít předdefinované role nebo vlastní role. Následující snímek obrazovky ukazuje integraci služby Active Directory (RBAC) pomocí řízení přístupu (IAM) v Azure Portal:

![Řízení přístupu (IAM) ve Azure Portal – demonstrace zabezpečení databáze](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Vlastní role

Kromě předdefinovaných rolí můžou uživatelé také vytvářet [vlastní role](../role-based-access-control/custom-roles.md) v Azure a tyto role použít pro instanční objekty ve všech předplatných v rámci svého tenanta služby Active Directory. Vlastní role poskytují uživatelům způsob, jak vytvořit definice rolí RBAC s vlastní sadou operací poskytovatele prostředků. Informace o tom, které operace jsou k dispozici pro vytváření vlastních rolí pro Azure Cosmos DB najdete v tématu [Azure Cosmos DB operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) .

## <a name="preventing-changes-from-cosmos-sdk"></a>Zabránění změnám ze sady Cosmos SDK

Poskytovatel prostředků Cosmos se může uzamknout, aby se zabránilo jakýmkoli změnám v prostředcích, včetně účtů Cosmos, databází, kontejnerů a propustnosti, od jakýchkoli klientů připojujících se pomocí klíčů účtu (tj. aplikace připojující se prostřednictvím sady Cosmos SDK). Při nastavení musí být změny libovolného prostředku od uživatele s patřičnou rolí RBAC a přihlašovacími údaji. Tato možnost je nastavena s hodnotou vlastnosti `disableKeyBasedMetadataWriteAccess` v poskytovateli prostředků Cosmos. Příklad šablony Azure Resource Manager s nastavením Tato vlastnost je níže.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
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

## <a name="next-steps"></a>Další kroky

- [Co je řízení přístupu na základě role (RBAC) pro prostředky Azure](../role-based-access-control/overview.md)
- [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
