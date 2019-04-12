---
title: Vývoj s využitím rozhraní API v3 – Azure | Dokumentace Microsoftu
description: Tento článek popisuje pravidla, které se vztahují k entitám a rozhraním API při vývoji pomocí Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492137"
---
# <a name="developing-with-media-services-v3-apis"></a>Vývoj s využitím Media Services v3 rozhraní API

Tento článek popisuje pravidla, které se vztahují k entitám a rozhraním API při vývoji pomocí Media Services v3.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID). 

Názvy prostředků služby Media Services nemůže obsahovat znaky <, >, %, &, :, &#92;, ?, /, *, +, ., jednoduché uvozovky ani žádné řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků. 

Další informace o zadávání názvů Azure Resource Manageru najdete v tématu: [Požadavky na pojmenování](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [zásady vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>Principy návrhu rozhraní API v3 a RBAC

Jedním z klíčových principů návrhu rozhraní API v3 je vyšší zabezpečení rozhraní API. rozhraní API v3 nevrátí tajné kódy nebo přihlašovací údaje na **získat** nebo **seznamu** operace. Klíče v odpovědi mají vždy hodnotu null, jsou prázdné nebo upravené. Uživatel musí volat metodu samostatnou akci tajné kódy nebo přihlašovací údaje. **Čtečky** role nelze volat operace, takže ji nelze volat operace, jako je ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas StreamingLocator.ListContentKeys,. S samostatné akce umožňuje nastavit oprávnění zabezpečení podrobnější RBAC v vlastní roli, v případě potřeby.

Další informace naleznete v tématu:

- [Definice předdefinovaná role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Ke správě přístupu pomocí RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Řízení přístupu na základě rolí pro účty Media Services](rbac-overview.md)
- [Získání obsahu klíče zásad – .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Dlouho běžící operace

Operace označené `x-ms-long-running-operation` ve službě Azure Media Services [swagger soubory](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) se dlouho běžící operace. 

Podrobnosti o tom, jak sledovat asynchronních operací v Azure najdete v tématu [asynchronních operací v](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Služba Media Services má následující dlouho běžící operace:

* Vytvoření Livestream
* Aktualizace Livestream
* Odstranit Livestream
* Spustit Livestream
* Zastavit Livestream
* Resetovat Livestream
* Vytvoření LiveOutput
* Odstranit LiveOutput
* Vytvoření StreamingEndpoint
* Aktualizace StreamingEndpoint
* Odstranit StreamingEndpoint
* Spustit StreamingEndpoint
* Zastavit StreamingEndpoint
* Škálování StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrování, řazení, stránkování entit Media Services

Zobrazit [filtrování, řazení, stránkování entit Azure Media Services](entities-overview.md)

## <a name="next-steps"></a>Další postup

[Začněte vyvíjet s využitím rozhraní API služby Media Services v3 pomocí sady SDK a nástroje](developers-guide.md)
