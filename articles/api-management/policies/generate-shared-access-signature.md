---
title: Azure zásady správy rozhraní API-ukázka - vygenerovat sdílený přístupový podpis | Microsoft Docs
description: Azure API management zásad ukázka - ukazuje, jak vygenerovat sdílený přístupový podpis pomocí výrazů a předání požadavku do úložiště Azure zásadám přepisování uri...
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
ms.openlocfilehash: fadad6f83f1a855f3ca1509a6a5e02c822db512d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="generate-shared-access-signature"></a>Vygenerovat sdílený přístupový podpis

Tento článek ukazuje Azure API management zásad ukázka, které ukazuje, jak vygenerovat [sdíleného přístupového podpisu](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pomocí výrazů a předání požadavku do úložiště Azure zásadám přepisování uri. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

