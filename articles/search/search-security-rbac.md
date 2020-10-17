---
title: Nastavení rolí Azure pro přístup pro správu Azure
titleSuffix: Azure Cognitive Search
description: Řízení přístupu na základě role (RBAC) v Azure Portal pro řízení a delegování úloh správy služby Azure Kognitivní hledání Management.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 2f9f979e5871a4888978ff14362a7fb0082917d5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151199"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Nastavení rolí Azure pro přístup pro správu k Azure Kognitivní hledání

Azure poskytuje [globální autorizační model založený na rolích](../role-based-access-control/role-assignments-portal.md) pro všechny služby spravované prostřednictvím portálu nebo rozhraní Správce prostředků API. Role vlastník, přispěvatel a čtenář určují úroveň *správy služeb* pro uživatele, skupiny a objekty zabezpečení služby Active Directory přiřazené jednotlivým rolím. 

> [!Note]
> Pro zabezpečení obsahu ve službě není k dispozici žádné řízení přístupu na základě role (RBAC). Pro ověřené požadavky do samotné služby použijete buď klíč rozhraní API pro správu, nebo klíč rozhraní API pro dotazy. Pro přístup na základě identity přes výsledky hledání můžete vytvořit filtry zabezpečení pro oříznutí výsledků podle identity a odebrání dokumentů, ke kterým by žadatel neměl mít přístup. Další informace najdete v tématu [filtry zabezpečení](search-security-trimming-for-azure-search.md).

## <a name="management-tasks-by-role"></a>Úlohy správy podle rolí

Pro Azure Kognitivní hledání role jsou přidružené k úrovním oprávnění, které podporují následující úlohy správy:

| Role | Úloha |
| --- | --- |
| Vlastník |Vytvořte nebo odstraňte službu nebo libovolný objekt ve službě, včetně klíčů rozhraní API, indexů, indexerů, zdrojů dat indexeru a plánů indexerů.<p>Zobrazení stavu služby, včetně počtu a velikosti úložiště<p>Přidat nebo odstranit členství v rolích (pouze vlastník může spravovat členství v rolích).<p>Správci předplatného a vlastníci služeb mají automatické členství v roli vlastníci. |
| Přispěvatel | Stejná úroveň přístupu jako vlastník a minus Správa rolí Azure. Přispěvatel může například vytvořit nebo odstranit objekty nebo zobrazit a znovu vygenerovat [klíče rozhraní API](search-security-api-keys.md), ale nemůže změnit členství v rolích.<br><br>[Přispěvatel Search Service](../role-based-access-control/built-in-roles.md#search-service-contributor) je ekvivalentní k předdefinované roli obecného přispěvatele. |
| Čtenář |Zobrazit základy služby, například koncový bod služby, předplatné, skupinu prostředků, oblast, úroveň a kapacitu. Metriky služby, například průměrných dotazů za sekundu, můžete také zobrazit na kartě monitorování. Členové této role nemůžou zobrazovat index, indexer, zdroj dat ani dovednosti informace. To zahrnuje data o využití těchto objektů, například kolik indexů ve službě existuje. |

Role neudělují přístupová práva ke koncovému bodu služby. Operace hledání služby, jako je například Správa indexů, naplnění indexu a dotazy na data hledání, se řídí prostřednictvím klíčů rozhraní API, nikoli rolí. Další informace najdete v tématu [Správa klíčů rozhraní API](search-security-api-keys.md).

## <a name="permissions-table"></a>Tabulka oprávnění

V následující tabulce najdete souhrn operací povolených v Azure Kognitivní hledání a který klíč odemkne přístup k určité operaci.

Oprávnění RBAC se vztahují na operace portálu a správu služeb (vytvoření, odstranění nebo změna služby nebo jeho klíčů rozhraní API). Klíče rozhraní API se vytvoří po existenci služby a vztahují se na operace obsahu ve službě. V případě operací souvisejících s obsahem na portálu, jako je například vytváření nebo odstraňování objektů, spolupracuje se službou nebo vlastníkem nebo přispěvatelem služby s předpokládaným klíčem rozhraní API pro správu.

| Operace | Řízeno |
|-----------|-------------------------|
| Vytvoření služby | Oprávnění RBAC: vlastník nebo přispěvatel |
| Škálování služby | Oprávnění RBAC: vlastník nebo přispěvatel|
| Odstranění služby | Oprávnění RBAC: vlastník nebo přispěvatel |
| Správa klíčů pro správce nebo dotazy | Oprávnění RBAC: vlastník nebo přispěvatel|
| Zobrazení informací o službě na portálu nebo v rozhraní API pro správu | Oprávnění RBAC: vlastník, přispěvatel nebo čtenář  |
| Zobrazení informací o objektu a metrik na portálu nebo rozhraní API pro správu | Oprávnění RBAC: vlastník nebo přispěvatel |
| Vytváření, úpravy a odstraňování objektů ve službě: <br>Indexy a části komponent (včetně definic analyzátoru, profilů vyhodnocování, možností CORS), indexerů, zdrojů dat, synonym a návrhů | Klíč správce při použití rozhraní API, vlastníka RBAC nebo přispěvatele při použití portálu |
| Dotazování indexu | Správce nebo klíč dotazu při použití rozhraní API, vlastníka RBAC nebo přispěvatele při použití portálu |
| Dotaz na systémové informace o objektech, jako je například vrácení statistik, počtů a seznamů objektů | Klíč správce při použití rozhraní API, vlastníka RBAC nebo přispěvatele při použití portálu |

## <a name="next-steps"></a>Další kroky

+ [Správa pomocí prostředí PowerShell](search-manage-powershell.md) 
+ [Výkon a optimalizace v Azure Kognitivní hledání](search-performance-optimization.md)
+ Začněte [s Role-Based Access Control v Azure Portal](../role-based-access-control/overview.md).
