---
title: Nastavení rolí RBAC pro přístup pro správu Azure
titleSuffix: Azure Cognitive Search
description: Administrativní řízení na základě rolí (RBAC) v Azure Portal pro řízení a delegování úloh správy služby Azure Kognitivní hledání Management.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 402fae5622219b14cfdab921ebe1a78ad5dd111e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84462834"
---
# <a name="set-rbac-roles-for-administrative-access-to-azure-cognitive-search"></a>Nastavení rolí RBAC pro přístup pro správu k Azure Kognitivní hledání

Azure poskytuje [globální autorizační model založený na rolích](../role-based-access-control/role-assignments-portal.md) pro všechny služby spravované prostřednictvím portálu nebo rozhraní Správce prostředků API. Role vlastník, přispěvatel a čtenář určují úroveň *správy služeb* pro uživatele, skupiny a objekty zabezpečení služby Active Directory přiřazené jednotlivým rolím. 

> [!Note]
> Pro zabezpečení částí indexu nebo podmnožiny dokumentů nejsou k dispozici žádná řízení přístupu na základě rolí. Pro přístup na základě identity přes výsledky hledání můžete vytvořit filtry zabezpečení pro oříznutí výsledků podle identity a odebrání dokumentů, ke kterým by žadatel neměl mít přístup. Další informace najdete v tématech [filtry zabezpečení](search-security-trimming-for-azure-search.md) a zabezpečení [pomocí služby Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Úlohy správy podle rolí

Pro Azure Kognitivní hledání role jsou přidružené k úrovním oprávnění, které podporují následující úlohy správy:

| Role | Úkol |
| --- | --- |
| Vlastník |Vytvořte nebo odstraňte službu nebo libovolný objekt ve službě, včetně klíčů rozhraní API, indexů, indexerů, zdrojů dat indexeru a plánů indexerů.<p>Zobrazení stavu služby, včetně počtu a velikosti úložiště<p>Přidat nebo odstranit členství v rolích (pouze vlastník může spravovat členství v rolích).<p>Správci předplatného a vlastníci služeb mají automatické členství v roli vlastníci. |
| Přispěvatel |Stejná úroveň přístupu jako vlastník a minus Správa rolí RBAC. Přispěvatel může například vytvořit nebo odstranit objekty nebo zobrazit a znovu vygenerovat [klíče rozhraní API](search-security-api-keys.md), ale nemůže změnit členství v rolích. |
| [Předdefinovaná role Search Service Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Odpovídá roli Přispěvatel. |
| Čtenář |Zobrazit základy služby a metriky. Členové této role nemůžou zobrazovat index, indexer, zdroj dat ani informace o klíčích.  |

Role neudělují přístupová práva ke koncovému bodu služby. Operace hledání služby, jako je například Správa indexů, naplnění indexu a dotazy na data hledání, se řídí prostřednictvím klíčů rozhraní API, nikoli rolí. Další informace najdete v tématu [Správa klíčů rozhraní API](search-security-api-keys.md).

## <a name="permissions-table"></a>Tabulka oprávnění

V následující tabulce najdete souhrn operací povolených v Azure Kognitivní hledání a který klíč odemkne přístup k určité operaci.

| Operace | Oprávnění |
|-----------|-------------------------|
| Vytvoření služby | Vlastník předplatného Azure |
| Škálování služby | Klíč správce, vlastník RBAC nebo přispěvatel na prostředku  |
| Odstranění služby | Klíč správce, vlastník RBAC nebo přispěvatel na prostředku |
| Vytváření, úpravy a odstraňování objektů ve službě: <br>Indexy a části komponent (včetně definic analyzátoru, profilů vyhodnocování, možností CORS), indexerů, zdrojů dat, synonym a návrhů | Klíč správce, vlastník RBAC nebo přispěvatel na prostředku |
| Dotazování indexu | Správce nebo klíč dotazu (RBAC není k dispozici) |
| Dotazování systémových informací, jako je vrácení statistik, počtů a seznamů objektů | Klíč správce, RBAC na prostředku (vlastník, přispěvatel, čtenář) |
| Správa klíčů pro správu | Klíč správce, vlastník RBAC nebo přispěvatel v prostředku |
| Správa klíčů dotazů |  Klíč správce, vlastník RBAC nebo přispěvatel v prostředku  |

## <a name="see-also"></a>Viz také

+ [Správa pomocí prostředí PowerShell](search-manage-powershell.md) 
+ [Výkon a optimalizace v Azure Kognitivní hledání](search-performance-optimization.md)
+ Začněte [s Access Control na základě rolí v Azure Portal](../role-based-access-control/overview.md).