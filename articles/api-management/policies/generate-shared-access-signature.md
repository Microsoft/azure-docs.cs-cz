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
ms.openlocfilehash: 62a6e8c9fdec3b30bd193e9887d7e0cb7926e73e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86242997"
---
# <a name="generate-shared-access-signature"></a>Generovat sdílený přístupový podpis

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak generovat [sdílený přístupový podpis](../../storage/common/storage-sas-overview.md) pomocí výrazů a předejte požadavek do služby Azure Storage pomocí zásad přepsání-URI. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)
