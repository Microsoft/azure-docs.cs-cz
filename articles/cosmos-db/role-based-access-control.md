---
title: Řízení přístupu na základě role ve službě Azure Cosmos DB pomocí integrace služby Azure Active Directory
description: Zjistěte, jak službu Azure Cosmos DB poskytuje ochranu databáze s integrací služby Active directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 971d2ec96906a3309963495dd1af5d293a71f265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243506"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Řízení přístupu na základě rolí ve službě Azure Cosmos DB

Azure Cosmos DB poskytuje řízení přístupu na integrované na základě rolí (RBAC) pro běžné scénáře správy ve službě Azure Cosmos DB. Uživatel, který má vlastní profil ve službě Azure Active Directory můžete přiřadit tyto role RBAC uživatelům, skupinám, objektů služby nebo spravovat identity udělit nebo odepřít přístup k prostředkům a operací s prostředky Azure Cosmos DB. Rovina řízení přístup pouze, což zahrnuje přístup k účtům Azure Cosmos, databáze, kontejnery a nabízí (propustnost) mají rozsah přiřazení rolí.

## <a name="built-in-roles"></a>Vestavěné role

Tady jsou předdefinované role, které jsou podporovány službou Azure Cosmos DB:

|**Předdefinovaná role**  |**Popis**  |
|---------|---------|
|[Přispěvatel účtů DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Můžete spravovat účty služby Azure Cosmos DB.  |
|[Čtenáře účtu cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Může číst data účtu služby Azure Cosmos DB.        |
|[Operátor zálohování cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Můžete odeslat žádost o obnovení pro databázi Azure Cosmos nebo kontejneru.       |
|[Cosmos DB – operátor](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Můžete zřídit kontejnery, databáze a účty Azure Cosmos, ale nemáte přístup ke klíči, které jsou nutné pro přístup k datům.         |

> [!IMPORTANT]
> Podpora RBAC ve službě Azure Cosmos DB se týká pouze operace roviny řízení. Operace roviny dat jsou zabezpečené pomocí hlavních klíčů nebo tokenů prostředku. Další informace najdete v tématu [zabezpečený přístup k datům ve službě Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Správa identit a řízení přístupu (IAM)

**Řízení přístupu (IAM)** podokně webu Azure Portal slouží ke konfiguraci řízení přístupu na základě rolí pro prostředky Azure Cosmos. Role se použijí na uživatele, skupiny, instančních objektů a spravovaných identit ve službě Active Directory. Pro jednotlivce a skupiny můžete použít předdefinované role nebo vlastních rolí. Následující snímek obrazovky ukazuje integrace služby Active Directory (RBAC) pomocí řízení přístupu (IAM) na webu Azure Portal:

![Řízení přístupu (IAM) na webu Azure Portal – ukázka zabezpečení databáze](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Vlastní role

Kromě předdefinovaných rolí, uživatelé mohou také vytvořit [vlastní role](../role-based-access-control/custom-roles.md) v Azure a použít tyto role pro instanční objekty napříč všemi předplatnými v rámci svého tenanta služby Active Directory. Vlastní role poskytují uživatelům způsob, jak vytvořit definice role RBAC s vlastní sadou prostředků operace poskytovatele. Další operace, které jsou k dispozici pro vytváření vlastních rolí pro službu Azure Cosmos DB najdete v tématu, [operace poskytovatele prostředků služby Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Další postup

- [Co je řízení přístupu na základě rolí (RBAC) pro prostředky Azure](../role-based-access-control/overview.md)
- [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)
- [Operace poskytovatele prostředků služby Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
