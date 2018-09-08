---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095587"
---
> [!IMPORTANT]
> - Náhled ověřování Azure AD pro objekty BLOB a fronty je určeno pouze pro nevýrobní prostředí. Produkční smlouvy o úrovni služeb (SLA) nejsou aktuálně k dispozici. Pokud pro váš scénář zatím nepodporuje ověřování Azure AD, nadále používat povolení sdíleného klíče nebo tokeny SAS ve svých aplikacích.
>
> - Ve verzi preview přiřazení rolí pro RBAC může trvat až pět minut na dokončení propagace.
>
> - Musí používat protokol HTTPS k ověřování ve službě Azure AD při volání operace objektů blob a fronty.
>
> - Ve verzi preview na webu Azure portal přihlašovacích údajů Azure AD pro čtení a zápis dat objektů blob a fronty nepoužívá. Místo toho nadále portálu závisí na přístupový klíč účtu. Zobrazit nebo aktualizovat data objektů blob nebo fronty na portálu, uživatel musí mít přiřazenou roli RBAC, která zahrnuje [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role), která uděluje oprávnění pro volání [účty úložiště - Vypsat klíče](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys). Role Přispěvatel a Čtenář pro objekty BLOB a fronty v současnosti nezahrnuje **klíče listkey** akce jako součást verze preview verzi a tak neposkytuje přístup k datům prostřednictvím webu Azure portal. Prozkoumat založené na identitě přístup k datům objektu blob a fronty v předběžné verzi, pomocí Powershellu nebo rozhraní příkazového řádku Azure.
