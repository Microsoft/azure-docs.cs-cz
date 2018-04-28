---
title: Nastavte role RBAC pro přístup pro správu Azure Search na portálu | Microsoft Docs
description: Na základě rolí administrativní řízení na portálu Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 1a463c9eb52ffe2a667cdeace7478e67233a0806
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="set-rbac-roles-for-administrative-access"></a>Nastavte role RBAC pro přístup pro správu

Azure poskytuje [modelu globální autorizace na základě rolí](../role-based-access-control/role-assignments-portal.md) pro všechny služby spravovat prostřednictvím portálu nebo Resource Manager rozhraní API. Role vlastník, Přispěvatel a čtečky určují úroveň *služby správy* pro uživatele, skupiny a objekty zabezpečení přiřazené ke každé roli služby Active Directory. 

> [!Note]
> Neexistuje žádné přístupu podle rolí ovládací prvky pro zabezpečení části indexu nebo podmnožinu dokumentů. Pro přístup na základě identity prostřednictvím výsledky hledání můžete vytvořit filtry zabezpečení oříznout výsledky identita, odebírání dokumentů, pro které žadatel by neměly mít přístup. Další informace najdete v tématu [filtrů zabezpečení](search-security-trimming-for-azure-search.md) a [zabezpečeného se službou Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Úlohy správy rolí

Pro službu Azure Search jsou přidruženy úrovně oprávnění, které podporují následující úlohy správy rolí:

| Role | Úkol |
| --- | --- |
| Vlastník |Vytvořit nebo odstranit službu nebo libovolného objektu na služby, včetně klíče api Key, indexy, indexery, indexer zdroje dat a indexeru plány.<p>Zobrazit stav služby, včetně počtu a velikosti úložiště.<p>Přidání nebo odstranění členství role (pouze vlastníka můžete spravovat členství role).<p>Správci předplatného a vlastníky služby mají automatické členství v roli vlastníky. |
| Přispěvatel |Stejnou úroveň přístupu jako vlastník, minus RBAC správu rolí. Například můžete Přispěvatel vytvořit nebo odstranit objekty, nebo zobrazit a znovu je obnovovat [klíče api Key](search-security-api-keys.md), ale nelze upravit členství v rolích. |
| [Předdefinovaná role Přispěvatel služby vyhledávání](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Ekvivalent k roli Přispěvatel. |
| Čtenář |Zobrazení služby essentials a metriky. Členové této role nelze zobrazit index, indexer, zdroj dat nebo informace o klíči.  |

Role neudělujte přístupová práva ke koncovému bodu služby. Hledání operací služby, jako je například Správa indexu, naplňování indexu a dotazy na data vyhledávání, jsou řízena pomocí klíče api Key, není role. Další informace najdete v tématu [spravovat klíče api Key](search-security-api-keys.md).

## <a name="see-also"></a>Další informace najdete v tématech

+ [Správa s využitím PowerShellu](search-manage-powershell.md) 
+ [Výkon a optimalizace ve službě Azure Search](search-performance-optimization.md)
+ [Začínáme s řízením přístupu na základě rolí na portálu Azure](../role-based-access-control/overview.md).