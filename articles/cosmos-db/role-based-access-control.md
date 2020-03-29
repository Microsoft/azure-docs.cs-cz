---
title: Řízení přístupu na základě role ve službě Azure Cosmos DB
description: Zjistěte, jak Azure Cosmos DB poskytuje ochranu databáze s integrací active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445089"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Řízení přístupu na základě role ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje integrované řízení přístupu na základě rolí (RBAC) pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil ve službě Azure Active Directory můžete přiřadit tyto role RBAC uživatelům, skupinám, instanční objekty nebo spravované identity udělit nebo odepřít přístup k prostředkům a operacím na prostředky Azure Cosmos DB. Přiřazení rolí jsou vymezeny pouze pro přístup k rovině řízení, který zahrnuje přístup k účtům Azure Cosmos, databázím, kontejnerům a nabídkám (propustnost).

## <a name="built-in-roles"></a>Vestavěné role

Níže jsou uvedeny předdefinované role podporované službou Azure Cosmos DB:

|**Vestavěná role**  |**Popis**  |
|---------|---------|
|[Přispěvatel účtu DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Můžete spravovat účty Azure Cosmos DB.|
|[Čtečka účtů Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Umí číst data účtu Azure Cosmos DB.|
|[Operátor zálohování Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Můžete odeslat žádost o obnovení pro databázi Azure Cosmos nebo kontejneru.|
|[Operátor Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Můžete zřídit Účty Azure Cosmos, databáze a kontejnery, ale nemůže získat přístup ke klíčům, které jsou nutné pro přístup k datům.|

> [!IMPORTANT]
> Podpora RBAC v Azure Cosmos DB platí pouze pro operace roviny řízení. Operace roviny dat jsou zabezpečeny pomocí hlavních klíčů nebo tokenů prostředků. Další informace najdete v tématu [Zabezpečený přístup k datům v Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Správa identit a přístupu (IAM)

Podokno **Řízení přístupu (IAM)** na portálu Azure se používá ke konfiguraci řízení přístupu na základě rolí na prostředky Azure Cosmos. Role jsou použity pro uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Můžete použít předdefinované role nebo vlastní role pro jednotlivce a skupiny. Následující snímek obrazovky ukazuje integraci služby Active Directory (RBAC) pomocí řízení přístupu (IAM) na webu Azure Portal:

![Řízení přístupu (IAM) na webu Azure Portal – prokázání zabezpečení databáze](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Vlastní role

Kromě předdefinovaných rolí mohou uživatelé také vytvářet [vlastní role](../role-based-access-control/custom-roles.md) v Azure a použít tyto role na objekty zabezpečení služeb ve všech předplatných v rámci jejich tenanta služby Active Directory. Vlastní role poskytují uživatelům způsob, jak vytvořit definice rolí RBAC s vlastní sadou operací zprostředkovatele prostředků. Informace o tom, které operace jsou k dispozici pro vytváření vlastních rolí pro Azure Cosmos DB, viz [operace zprostředkovatele prostředků Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Zabránění změnám z sady Cosmos SDK

Zprostředkovatele prostředků Cosmos lze uzamknout, aby se zabránilo jakékoli změny prostředků, včetně účtu Cosmos, databáze, kontejnery a propustnost z libovolného klienta připojení prostřednictvím klíče účtu (tj. aplikace připojení přes Cosmos SDK). Pokud je nastavena, změny libovolného prostředku musí být od uživatele s vlastní rolí RBAC a pověření. Tato funkce je `disableKeyBasedMetadataWriteAccess` nastavena s hodnotou vlastnosti v zprostředkovateli prostředků Cosmos. Příklad šablony Azure Resource Manager s tímto nastavením vlastností je níže.

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

- [Co je řízení přístupu na základě rolí (RBAC) pro prostředky Azure](../role-based-access-control/overview.md)
- [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)
- [Operace zprostředkovatele prostředků Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
