---
title: Role čtenáři adresáře v Azure Active Directory pro Azure SQL
description: Přečtěte si o roli čtečky adresářů ve službě Azure AD pro Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 5764a8df862610fc076ce2810fcc0d4bf8dbda3c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094552"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Role čtenáři adresáře v Azure Active Directory pro Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Tato funkce je v tomto článku ve **verzi Public Preview**.

Služba Azure Active Directory (Azure AD) zavedla [použití cloudových skupin pro správu přiřazení rolí v Azure Active Directory (Preview)](../../active-directory/roles/groups-concept.md). Díky tomu můžou být role Azure AD přiřazené skupinám.

Při povolování [spravované identity](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) pro Azure SQL Database, spravované instance Azure SQL nebo Azure synapse Analytics musí být role [**čtenáři adresáře**](../../active-directory/roles/permissions-reference.md#directory-readers) Azure AD přiřazená k identitě, aby bylo možné povolit přístup pro čtení [Graph API Azure AD](../../active-directory/develop/active-directory-graph-api.md). Spravovaná identita SQL Database a Azure synapse se označuje jako identita serveru. Spravovaná identita spravované instance SQL je označována jako identita spravované instance a je automaticky přiřazena při vytvoření instance. Další informace o přiřazování identity serveru SQL Database nebo synapse Azure najdete v tématu [Povolení instančních objektů pro vytváření uživatelů Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Role **čtenáři adresáře** je nutná pro:

- Vytvoření přihlášení Azure AD pro spravovanou instanci SQL
- Zosobnění uživatelů Azure AD v Azure SQL
- Migrace SQL Server uživatelů, kteří používají ověřování systému Windows, do spravované instance SQL pomocí ověřování Azure AD (pomocí příkazu [ALTER User (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) )
- Změna správce Azure AD pro spravovanou instanci SQL
- Umožňuje [instančním objektům (aplikacím)](authentication-aad-service-principal.md) vytvářet uživatele Azure AD v Azure SQL.

## <a name="assigning-the-directory-readers-role"></a>Přiřazení role čtenáři adresáře

Aby bylo možné přiřadit roli [**čtenáři adresáře**](../../active-directory/roles/permissions-reference.md#directory-readers) k identitě, je nutné mít oprávnění uživatele s oprávněním správce [globálního správce](../../active-directory/roles/permissions-reference.md#global-administrator) nebo [privilegované role](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) . Uživatelé, kteří často spravují nebo nasazují SQL Database, spravované instance SQL nebo Azure synapse, nemusí mít přístup k těmto vysoce privilegovaným rolím. To může často způsobovat komplikace pro uživatele, kteří vytvářejí neplánované prostředky SQL Azure, nebo potřebují pomáhat při nepřístupných členech role s vysokou úrovní oprávnění, které jsou často nedostupné ve velkých organizacích.

V případě spravované instance SQL musí být role **čtenáři adresáře** přiřazena k identitě spravované instance předtím, než můžete [nastavit správce Azure AD pro spravovanou instanci](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

Přiřazení role **čtenáři adresáře** k identitě serveru není vyžadováno pro SQL Database nebo Azure synapse při nastavování správce Azure AD pro logický Server. Pokud ale chcete povolit vytvoření objektu Azure AD v SQL Database nebo Azure synapse jménem aplikace služby Azure AD, je potřeba role **čtenáři adresáře** . Pokud není role přiřazená k identitě logického serveru SQL, vytváření uživatelů Azure AD v Azure SQL selže. Další informace najdete v tématu [Azure Active Directory instančního objektu se službou Azure SQL](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Udělení role čtenáři adresáře pro skupinu Azure AD

V současné době ve **verzi Public Preview** teď můžete mít správce [globálních správců](../../active-directory/roles/permissions-reference.md#global-administrator) nebo [privilegovaných rolí](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) vytvořit skupinu Azure AD a přiřadit oprávnění [**čtenářům adresáře**](../../active-directory/roles/permissions-reference.md#directory-readers) ke skupině. Tím se umožní přístup k Graph API Azure AD pro členy této skupiny. Kromě toho můžou uživatelé Azure AD, kteří jsou vlastníky této skupiny, přiřadit nové členy této skupiny, včetně identit logických serverů Azure SQL.

Toto řešení ještě vyžaduje uživatele s vysokou úrovní oprávnění (správce globálního správce nebo privilegované role), aby mohl vytvořit skupinu a přiřazovat uživatele jako jednorázovou aktivitu, ale vlastníci skupiny Azure AD budou moci přiřadit další členy, kteří budou přesměrováni. Tím se eliminuje nutnost zahrnovat uživatele s vysokými oprávněními v budoucnu, aby se nakonfigurovaly všechny databáze SQL, spravované instance SQL nebo servery Azure synapse ve svém tenantovi Azure AD.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: přiřazení role čtenářů adresáře ke skupině Azure AD a správa přiřazení rolí](authentication-aad-directory-readers-role-tutorial.md)