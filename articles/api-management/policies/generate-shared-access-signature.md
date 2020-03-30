---
title: Vzorové zásady správy rozhraní API – generování sdíleného přístupového podpisu
titleSuffix: Azure API Management
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak generovat sdílený přístupový podpis pomocí výrazů a předat požadavek do úložiště Azure se zásadami přepisování uri..
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 0f003bc268af6b7f8bd6b046ae84734dbefeac28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442465"
---
# <a name="generate-shared-access-signature"></a>Generovat sdílený přístupový podpis

Tento článek ukazuje ukázku zásad správy rozhraní Azure, která ukazuje, jak generovat [sdílený přístupový podpis](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pomocí výrazů a předat požadavek do úložiště Azure se zásadami přepisování uri. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

