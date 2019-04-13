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
ms.date: 04/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9d1fa5786dcde70d42363dbb9af7221ca5383e64
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546394"
---
# <a name="developing-with-media-services-v3-apis"></a>Vývoj s využitím Media Services v3 rozhraní API

Tento článek popisuje pravidla, které se vztahují k entitám a rozhraním API při vývoji pomocí Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Přístup k Azure Media Services rozhraní API

Pro přístup k prostředkům Azure Media Services, měli byste použít ověřování instančního objektu služby Azure Active Directory (AD). Rozhraní API služby Azure Media Services vyžaduje, že uživatel nebo aplikace, která bude rozhraní REST API požádá o přístup k prostředku účtu Azure Media Services (obvykle buď **Přispěvatel** nebo **vlastníka** role). Další informace najdete v tématu [řízení přístupu na základě rolí pro účty Media Services](rbac-overview.md).

Místo vytvoření instančního objektu, zvažte použití spravované identity pro prostředky Azure pro přístup k rozhraní API služby Media Services prostřednictvím Azure Resource Manageru. Další informace o spravovaných identit pro prostředky Azure, najdete v článku [co je spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Instanční objekt Azure AD 

Pokud vytváříte aplikace Azure AD a služby instančního objektu, aplikace musí být ve vlastním tenanta. Po vytvoření aplikace, dejte aplikaci **Přispěvatel** nebo **vlastníka** role přístup k účtu Media Services. 

Pokud si nejste jisti, zda máte oprávnění k vytvoření aplikace Azure AD, najdete v článku [požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na následujícím obrázku představuje čísla tok žádostí v chronologickém pořadí:

![Aplikace střední vrstvy](../previous/media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Střední vrstvy aplikace požaduje přístupový token Azure AD, který má následující parametry:  

   * Koncový bod pro tenanta Azure AD.
   * Identifikátor URI prostředku služby Media Services.
   * Identifikátor URI pro REST Media Services.
   * Hodnoty aplikace Azure AD: ID klienta a tajný kód klienta.
   
   Pokud chcete získat všechny potřebné hodnoty, najdete v článku [rozhraní API k přístupu k Azure Media Services pomocí rozhraní příkazového řádku Azure](access-api-cli-how-to.md)

2. Přístupový token Azure AD se odešle do střední vrstvy.
4. Střední vrstva odešle požadavek REST API služby Azure Media s tokenem Azure AD.
5. Střední vrstva získá zpět data ze služby Media Services.

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID). 

Názvy prostředků služby Media Services nemůže obsahovat znaky <, >, %, &, :, &#92;, ?, /, *, +, ., jednoduché uvozovky ani žádné řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků. 

Další informace o zadávání názvů Azure Resource Manageru najdete v tématu: [Požadavky na pojmenování](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [zásady vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

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
