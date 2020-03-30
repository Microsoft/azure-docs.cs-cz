---
title: Nastavení rolí RBAC pro přístup pro správu Azure
titleSuffix: Azure Cognitive Search
description: Řízení správy na základě rolí (RBAC) na portálu Azure pro řízení a delegování úloh správy pro správu Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9262d01e35bd03a9116a30b070b023f578f0b15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112554"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Nastavení rolí RBAC pro přístup správce k Azure Cognitive Search

Azure poskytuje [globální model autorizace založený na rolích](../role-based-access-control/role-assignments-portal.md) pro všechny služby spravované prostřednictvím portálu nebo api Správce prostředků. Role vlastníka, přispěvatele a čtenáře určují úroveň *správy služeb* pro uživatele, skupiny a objekty zabezpečení přiřazené ke každé roli. 

> [!Note]
> Neexistují žádné ovládací prvky přístupu založené na rolích pro zabezpečení částí indexu nebo podmnožiny dokumentů. Pro přístup založený na identitě přes výsledky hledání můžete vytvořit filtry zabezpečení pro oříznutí výsledků podle identity a odebrání dokumentů, pro které by žadatel neměl mít přístup. Další informace naleznete v [tématu Security filters](search-security-trimming-for-azure-search.md) and [Secure with Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Úlohy správy podle rolí

Pro Azure Cognitive Search jsou role přidruženy k úrovním oprávnění, které podporují následující úlohy správy:

| Role | Úkol |
| --- | --- |
| Vlastník |Vytvořte nebo odstraňte službu nebo libovolný objekt ve službě, včetně klíčů rozhraní api, indexů, indexerů, zdrojů dat indexeru a plánů indexeru.<p>Zobrazení stavu služby, včetně počtu a velikosti úložiště.<p>Přidat nebo odstranit členství v roli (členství v rolích může spravovat pouze vlastník).<p>Správci předplatného a vlastníci služeb mají automatické členství v roli Vlastníci. |
| Přispěvatel |Stejná úroveň přístupu jako vlastník, minus správa rolí RBAC. Přispěvatel může například vytvářet nebo odstraňovat objekty nebo zobrazit a znovu generovat [klíče rozhraní api](search-security-api-keys.md), ale nemůže měnit členství v rolích. |
| [Integrovaná role přispěvatele vyhledávací služby](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Ekvivalent role přispěvatele. |
| Čtenář |Zobrazit základní informace o službách a metriky. Členové této role nemohou zobrazit index, indexer, zdroj dat nebo klíčové informace.  |

Role neudělují přístupová práva ke koncovému bodu služby. Operace vyhledávací služby, jako je například správa indexu, plnění indexu a dotazy na vyhledávací data, jsou řízeny pomocí klíčů rozhraní api, nikoli rolí. Další informace naleznete v [tématu Správa klíčů rozhraní API](search-security-api-keys.md).

## <a name="see-also"></a>Viz také

+ [Správa pomocí prostředí PowerShell](search-manage-powershell.md) 
+ [Výkon a optimalizace v Azure Cognitive Search](search-performance-optimization.md)
+ [Začínáme s řízením přístupu na základě rolí na webu Azure Portal](../role-based-access-control/overview.md).