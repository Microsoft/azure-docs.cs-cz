---
title: Azure zásady správy rozhraní API-ukázka - trasy na základě požadavku na velikosti jeho obsahu | Microsoft Docs
description: Azure API management zásad ukázka - ukazuje, jak pro směrování požadavků na základě velikosti své subjekty.
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
ms.openlocfilehash: a93e1d9fecea59ebb68c512b96c8381b5b1a9346
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284756"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Směrování požadavku na základě velikosti jeho obsahu

Tento článek ukazuje rozhraní API služby Azure správy zásad vzorku, který ukazuje, jak pro směrování požadavků na základě velikosti své subjekty. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

