---
title: Správa účtů Azure Media Services V3
description: Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Tento článek vysvětluje, jak spravovat účty Azure Media Services V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: 5f48a60ee139cf4a89c0873633feea93cdde0940
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964015"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Správa účtů Azure Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pokud chcete začít spravovat, šifrovat, kódovat, analyzovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Další informace najdete v tématu [účty úložiště](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="moving-a-media-services-account-between-subscriptions"></a>Přesunutí účtu Media Services mezi předplatnými 

Pokud potřebujete přesunout Media Services účet k novému předplatnému, musíte nejdřív přesunout celou skupinu prostředků, která obsahuje Media Services účet k novému předplatnému. Je nutné přesunout všechny připojené prostředky: účty Azure Storage, profily Azure CDN atd. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Stejně jako u všech prostředků v Azure může dokončení přesunutí skupiny prostředků nějakou dobu trvat.

> [!NOTE]
> Media Services V3 podporuje model víceklientské architektury.

### <a name="considerations"></a>Požadavky

* Před migrací na jiné předplatné vytvořte zálohy všech dat ve vašem účtu.
* Musíte zastavit všechny koncové body streamování a prostředky živého streamování. Uživatelé nebudou mít přístup k vašemu obsahu po dobu trvání přesunutí skupiny prostředků. 

> [!IMPORTANT]
> Nespouštějte koncový bod streamování, dokud se přesun úspěšně nedokončí.

### <a name="troubleshoot"></a>Řešení potíží

Pokud se účet Media Services nebo přidružený Azure Storage účet stane "Odpojeno" po přesunutí skupiny prostředků, zkuste klíče účtu úložiště otočit. Pokud se při střídání klíčů účtu úložiště nevyřeší stav "Odpojeno" účtu Media Services, založte novou žádost o podporu z nabídky podpora a řešení potíží v účtu Media Services.  

## <a name="next-steps"></a>Další kroky

[Vytvoření účtu](./account-create-how-to.md)
