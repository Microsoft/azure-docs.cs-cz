---
title: Ukázkové zásady API managementu – generování sdíleného přístupového podpisu
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak vygenerovat sdílený přístupový podpis pomocí výrazů a předejte požadavek do služby Azure Storage pomocí zásad přepsání-URI.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442465"
---
# <a name="generate-shared-access-signature"></a>Generovat sdílený přístupový podpis

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak generovat [sdílený přístupový podpis](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pomocí výrazů a předejte požadavek do služby Azure Storage pomocí zásad přepsání-URI. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

