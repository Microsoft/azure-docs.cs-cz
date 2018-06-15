---
title: Zásady správy Azure API ukázkový - přidat hlavičku obsahující id korelace | Microsoft Docs
description: Azure API management zásad ukázka - ukazuje, jak přidat hlavičku obsahující id korelace na příchozí požadavek.
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
ms.openlocfilehash: 7d339ba08720d482f622aa9b708d3a6e057eaaa8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935106"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Přidat hlavičku obsahující id korelace

Tento článek ukazuje rozhraní API služby Azure správy zásad vzorku, který ukazuje, jak přidat hlavičku obsahující id korelace na příchozí požadavek. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

