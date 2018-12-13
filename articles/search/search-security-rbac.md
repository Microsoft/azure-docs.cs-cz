---
title: Nastavte role RBAC pro Azure pro správu přístupu na portálu – Azure Search
description: Na základě rolí správy (RBAC) na webu Azure Portal pro řízení a delegování úloh správy pro správu Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 38b8e8a0e413f367d34a4ccf5dbd87817891b8ea
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313016"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Nastavte role RBAC pro přístup pro správu

Azure poskytuje [modelu globální autorizace na základě rolí](../role-based-access-control/role-assignments-portal.md) pro všechny služby spravovat prostřednictvím portálu nebo rozhraní API Resource Manageru. Role vlastník, Přispěvatel a čtenář určují úroveň *Správa služby* pro uživatele, skupiny nebo objekty zabezpečení přiřazené ke každé roli služby Active Directory. 

> [!Note]
> Není k dispozici žádné ovládací prvky přístupu na základě rolí pro zabezpečení části indexu nebo podmnožinu dokumentů. Pro přístup na základě identity výsledků vyhledávání můžete vytvořit filtry zabezpečení mají být odebrány výsledky podle identity, odebírání dokumentů, pro které žadatel by přístup mít neměly. Další informace najdete v tématu [filtry zabezpečení](search-security-trimming-for-azure-search.md) a [zabezpečené službou Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Úkoly správy rolí

Pro službu Azure Search jsou spojeny s úrovní oprávnění, které podporují následující úkoly správy rolí:

| Role | Úkol |
| --- | --- |
| Vlastník |Vytvoření nebo odstranění služby nebo libovolný objekt služby, včetně klíče api Key, indexy, indexery, indexer zdroje dat a indexeru plány.<p>Zobrazit stav služby, včetně počtu a velikosti úložiště.<p>Přidání nebo odstranění členství v roli (pouze vlastník můžete spravovat členství v roli).<p>Správci předplatného a vlastníci služby mají automatickým členstvím v roli vlastníky. |
| Přispěvatel |Stejnou úroveň přístupu jako vlastník, minus správu role RBAC. Například můžete přispěvatele vytvořit nebo odstranit objekty, nebo zobrazit a znovu vygenerovat [klíče api Key](search-security-api-keys.md), ale nelze upravit členství v rolích. |
| [Předdefinovaná role Přispěvatel služby Search](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Odpovídá roli Přispěvatel. |
| Čtenář |Základy služby zobrazení a metriky. Členové této role nelze zobrazit, index, indexer, zdroj dat nebo informace o klíči.  |

Role nejsou udělena přístupová práva ke koncovému bodu služby. Hledání operace služby, jako je například Správa indexů, naplňování indexu a dotazy na data vyhledávání, jsou ovládaná klíče api Key, ne rolí. Další informace najdete v tématu [spravovat klíče api Key](search-security-api-keys.md).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Správa s využitím PowerShellu](search-manage-powershell.md) 
+ [Výkon a optimalizace do služby Azure Search](search-performance-optimization.md)
+ [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](../role-based-access-control/overview.md).