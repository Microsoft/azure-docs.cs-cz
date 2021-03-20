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
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078421"
---
# <a name="generate-shared-access-signature"></a>Generovat sdílený přístupový podpis

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak generovat [sdílený přístupový podpis](../../storage/common/storage-sas-overview.md) pomocí výrazů a předejte požadavek do služby Azure Storage pomocí zásad přepsání-URI. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)