---
title: 'Žádost o digitální vlákna Azure se nezdařila se stavem: 404 Sub-Domain Nenalezeno.'
description: "U žádosti o službu se nezdařily příčiny a řešení. Stav: 404 Sub-Domain Nenalezeno ' u digitálních vláken Azure."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590229"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>Žádost o službu se nezdařila. Stav: 404 Sub-Domain Nenalezeno

Tento článek popisuje příčiny a postup řešení pro příjem chyby 404 z žádostí o služby do digitálních vláken Azure. 

## <a name="symptoms"></a>Příznaky

K této chybě může dojít při přístupu k instanci digitálního vlákna Azure pomocí instančního objektu nebo uživatelského účtu, který patří do jiného [klienta služby Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) z instance. Zdá se, že správné [role](concepts-security.md) jsou přiřazené k identitě, ale požadavky rozhraní API selžou se stavem chyby `404 Sub-Domain not found` .

## <a name="causes"></a>Příčiny

### <a name="cause-1"></a>Příčina #1

Digitální vlákna Azure vyžaduje, aby všichni ověření uživatelé patřili do stejného tenanta Azure AD jako instance digitálního vlákna Azure.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Řešení

### <a name="solution-1"></a>#1 řešení

Tento problém můžete vyřešit tak, že všechny federované identity z jiného tenanta požádají o **token** z tenanta "domů" z instance Azure Digital. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>#2 řešení

Pokud používáte `DefaultAzureCredential` třídu ve vašem kódu a po získání tokenu budete chtít tento problém vyřešit, můžete zadat domovského tenanta v `DefaultAzureCredential` možnostech pro objasnění tenanta i v případě, že výchozí nastavení ověřování neplatí pro jiný typ.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)
