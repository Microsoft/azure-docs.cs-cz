---
title: Ukázka zásad Azure API managementu – generování sdíleného přístupového podpisu | Microsoft Docs
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
ms.openlocfilehash: 45e29673e264f64c976cce664eaf749636400c7f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067735"
---
# <a name="generate-shared-access-signature"></a>Generovat sdílený přístupový podpis

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak generovat [sdílený přístupový podpis](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pomocí výrazů a předejte požadavek do služby Azure Storage pomocí zásad přepsání-URI. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

