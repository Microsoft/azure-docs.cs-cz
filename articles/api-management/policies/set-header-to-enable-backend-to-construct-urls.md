---
title: Zásady správy Azure API ukázkový - přidat hlavičku předané | Microsoft Docs
description: Azure API management zásad ukázka - ukazuje, jak přidat hlavičku předané v příchozí žádosti o umožňuje rozhraní API pro vytvoření správné adresy URL back-end.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: a9dfcbf4be4b659d761d66d67d2ae4c7b70a245e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284160"
---
# <a name="add-a-forwarded-header"></a>Přidat hlavičku předané

Tento článek ukazuje rozhraní API služby Azure správy zásad vzorku, který ukazuje, jak přidat hlavičku předané v příchozí žádosti o umožňuje rozhraní API pro vytvoření správné adresy URL back-end. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="code"></a>Kód

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)
