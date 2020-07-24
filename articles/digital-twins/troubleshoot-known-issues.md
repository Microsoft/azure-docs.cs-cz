---
title: Známé problémy – digitální vlákna Azure
description: Získejte pomoc s rozpoznáváním a zmírněním známých problémů s využitím digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044135"
---
# <a name="known-issues-in-azure-digital-twins"></a>Známé problémy v případě digitálních vláken Azure

Tento článek poskytuje informace o známých problémech souvisejících s digitálními podseznamy Azure.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 chyba klienta: Chybný požadavek" v Cloud Shell

Příkazy v Cloud Shell můžou selhat neúspěšné s chybou 400 klienta chyba: Chybný požadavek na adresu URL: http://localhost:50342/oauth2/token "následovaný úplným trasováním zásobníku.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Tuto možnost můžete vyřešit opakovaným spuštěním `az login` příkazu a dokončením následujících kroků přihlášení.

Potom byste měli být schopni příkaz znovu spustit.

### <a name="possible-causes"></a>Možné příčiny

Toto je výsledek známého problému v Cloud Shell: [*získání tokenu z Cloud Shell přerušovaně se nezdařilo s 400 chybou klienta: Chybný požadavek*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o zabezpečení a oprávněních u digitálních vláken Azure:
* [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md)