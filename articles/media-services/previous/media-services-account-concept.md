---
title: Spravovat účty Azure Media Services V2 | Microsoft Docs
description: Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Tento článek vysvětluje, jak spravovat účty Azure Media Services V2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a097b186a2287ec13866c8a5ee9420641b44d131
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015940"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Správa účtů Azure Media Services V2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Přesunutí účtu Media Services mezi předplatnými

Pokud potřebujete přesunout Media Services účet k novému předplatnému, musíte nejdřív přesunout celou skupinu prostředků, která obsahuje Media Services účet k novému předplatnému. Je nutné přesunout všechny připojené prostředky: účty Azure Storage, profily Azure CDN atd. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Stejně jako u všech prostředků v Azure může dokončení přesunutí skupiny prostředků nějakou dobu trvat.

Media Services V2 nepodporuje model víceklientské architektury. Pokud potřebujete přesunout účet Media Services k předplatnému v novém tenantovi, vytvořte v novém tenantovi novou aplikaci Azure Active Directory (Azure AD). Pak přesuňte účet k předplatnému v novém tenantovi. Po dokončení přesunu tenanta můžete začít používat aplikaci Azure AD z nového tenanta pro přístup k účtu Media Services pomocí rozhraní API v2.

> [!IMPORTANT]
> Pro přístup k Media Services rozhraní API v2 je potřeba resetovat informace o [ověřování Azure AD](media-services-portal-get-started-with-aad.md) .
  
### <a name="considerations"></a>Požadavky

* Před migrací na jiné předplatné vytvořte zálohy všech dat ve vašem účtu.
* Musíte zastavit všechny koncové body streamování a prostředky živého streamování. Uživatelé nebudou mít přístup k vašemu obsahu po dobu trvání přesunutí skupiny prostředků.

> [!IMPORTANT]
> Nespouštějte koncový bod streamování, dokud se přesun úspěšně nedokončí.

### <a name="troubleshoot"></a>Řešení potíží

Pokud se účet Media Services nebo přidružený Azure Storage účet stane "Odpojeno" po přesunutí skupiny prostředků, zkuste klíče účtu úložiště otočit. Pokud se při střídání klíčů účtu úložiště nevyřeší stav "Odpojeno" účtu Media Services, založte novou žádost o podporu z nabídky podpora a řešení potíží v účtu Media Services.  

## <a name="next-steps"></a>Další kroky

[Vytvoření účtu](media-services-portal-create-account.md)
