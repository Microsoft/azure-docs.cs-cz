---
title: Správa účtů Azure Media Services v2 | Dokumenty společnosti Microsoft
description: Chcete-li začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Mediálních služeb. Tento článek vysvětluje, jak spravovat účty Azure Media Services v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981943"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Správa účtů Azure Media Services v2

Chcete-li začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Mediálních služeb. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Přesunutí účtu mediálních služeb mezi předplatnými 

Pokud potřebujete přesunout účet Mediální služby do nového předplatného, musíte nejprve přesunout celou skupinu prostředků, která obsahuje účet Mediální služby, do nového předplatného. Musíte přesunout všechny připojené prostředky: účty Azure Storage, profily Azure CDN atd. Další informace naleznete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Stejně jako u všech prostředků v Azure, přesuny skupiny prostředků může trvat nějakou dobu k dokončení.

Media Services v2 nepodporuje víceklientské model. Pokud potřebujete přesunout účet Mediální služby do předplatného v novém tenantovi, vytvořte novou aplikaci Azure Active Directory (Azure AD) v novém tenantovi. Pak přesuňte svůj účet do předplatného v novém tenantovi. Po dokončení přesunu klienta můžete začít používat aplikaci Azure AD z nového klienta pro přístup k účtu Media Services pomocí v2 API. 

> [!IMPORTANT]
> Chcete-li získat přístup k rozhraní API služby Media Services v2, je třeba obnovit ověřovací informace [služby Azure AD.](media-services-portal-get-started-with-aad.md)  
### <a name="considerations"></a>Požadavky

* Před migrací na jiné předplatné vytvořte zálohy všech dat v účtu.
* Musíte zastavit všechny koncové body streamování a živé streamování prostředků. Uživatelé nebudou mít přístup k obsahu po dobu trvání přesunu skupiny prostředků. 

> [!IMPORTANT]
> Nespouštějte koncový bod streamování, dokud přesun úspěšně nedokončí.

### <a name="troubleshoot"></a>Řešení potíží 

Pokud se účet Mediální služby nebo přidružený účet úložiště Azure stanou "odpojené" po přesunutí skupiny prostředků, zkuste otočit klíče účtu úložiště. Pokud otočení klíčů účtu úložiště nevyřeší stav odpojeného účtu Služby Media Services, zadejte nový požadavek na podporu + řešení potíží v účtu Služby Media.  
 
## <a name="next-steps"></a>Další kroky

[Vytvoření účtu](media-services-portal-create-account.md)
