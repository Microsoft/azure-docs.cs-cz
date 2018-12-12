---
title: Rozhraní API správy zásad ukázkový Azure – generovat sdílený přístupový podpis | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak vygenerovat sdílený přístupový podpis pomocí výrazů a předání požadavku do služby Azure storage pomocí zásady revize uri...
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
ms.openlocfilehash: 2c3adaa6f4e113f09e676583c2c35b5f1fbdb622
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877849"
---
# <a name="generate-shared-access-signature"></a>Vygenerovat sdílený přístupový podpis

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak vygenerovat [sdílený přístupový podpis](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pomocí výrazů a předání požadavku do služby Azure storage pomocí zásady revize uri. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

