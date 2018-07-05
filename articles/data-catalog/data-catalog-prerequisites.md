---
title: Požadavky služby Azure Data Catalog | Dokumentace Microsoftu
description: Informace o požadavcích, že které potřebujete, abyste mohli začít s Azure Data Catalog.
services: data-catalog
documentationcenter: ''
author: steelanddata
manager: NA
editor: ''
tags: ''
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: d34d9e49c3ad405a86e42ada9c86615a12adaa62
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449881"
---
# <a name="azure-data-catalog-prerequisites"></a>Požadavky na Azure Data Catalog

Musíte se postarat o pár věcí, před nastavením služby Azure Data Catalog. Nedělejte si starosti, tento proces není trvat dlouho.

## <a name="azure-subscription"></a>Předplatné Azure
Nastavení katalogu dat, musí být vlastník nebo spoluvlastník předplatného Azure.

Předplatná Azure vám pomohou organizovat přístup k prostředkům cloudové služby, jako jsou Data Catalog. Předplatná také pomoci řídit, jak se využití prostředků hlásí, fakturováno a placeno pro. Každé předplatné může mít samostatné nastavení fakturace a plateb, takže budete mít odběry a plány, které se liší podle oddělení, projektů, regionální pobočky a tak dále. Každá Cloudová služba patří do předplatného, a musíte mít předplatnému předtím, než nastavíte Data Catalog. Více informací naleznete v tématu [Správa účtů, předplatných a správních rolí](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Nastavení katalogu Data Catalog, musíte být přihlášeni pomocí účtu uživatele Azure Active Directory (Azure AD).

Azure AD umožní vaší firmě snadnou správu identity a přístupu, a to jak v cloudu, tak i místně. Uživatelé můžou používat jednoho pracovního nebo školního účtu pro jednotné přihlašování do jakéhokoli cloudu a místní webové aplikace. Data Catalog ověřuje přihlášení pomocí Azure AD. Další informace najdete v tématu [co je Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

> [!NOTE]
> S použitím [webu Azure portal](http://portal.azure.com/), se můžete přihlásit se pomocí osobního účtu Microsoft nebo Azure Active Directory pracovního nebo školního účtu. K nastavení katalogu dat buď pomocí webu Azure portal nebo [portál služby Data Catalog](http://www.azuredatacatalog.com), musíte se přihlásit pomocí účtu služby Azure Active Directory, nikoli pomocí osobního účtu.
>
>

## <a name="active-directory-policy-configuration"></a>Konfigurace zásad služby Active Directory
Může nastat situace, kde se můžete přihlásit na portál služby Data Catalog, ale při pokusu o přihlášení k nástroji pro registraci zdroje dat, narazíte na chybovou zprávu, která vám brání přihlášení. Toto chování problému může dojít pouze v případě, že jste v podnikové síti, nebo může dojít pouze v případě, že se připojujete z místa mimo podnikovou síť.

Nástroj registrace zdroje dat používá k ověření vašich přihlašovacích údajů služby Active Directory, kteří ověřování pomocí formulářů. Správce služby Active Directory můžete úspěšně přihlásit, musí povolit ověřování pomocí formulářů v globální zásady ověřování.

V globální zásady ověřování je možné metody ověřování samostatně povolit pro intranet a extranet připojení, jak je znázorněno na následujícím snímku obrazovky. Pokud není povolené ověřování pomocí formulářů pro síť, ze kterého se připojujete, může dojít k chybám přihlášení.

 ![Zásady globálního ověřování služby Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [konfigurace zásad ověřování](https://technet.microsoft.com/library/dn486781.aspx).
