---
title: Správa účtů služby Azure Media Services v3 | Dokumentace Microsoftu
description: Pokud chcete začít, správa, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. Tento článek vysvětluje, jak chcete spravovat účty pro Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670688"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Správa účtů služby Azure Media Services v3

Pokud chcete začít, správa, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure. Další informace najdete v tématu [účty úložiště](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Přesunutí účtu Azure Media Services mezi předplatnými 

Pokud potřebujete přesunout do nového předplatného účtu Azure Media Services, musíte nejprve přesunout celou skupinu prostředků obsahující účet Media Services do nového předplatného. Je nutné přesunout všechny připojené zdroje: Účty Azure Storage, profilů CDN, atd. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/resource-group-move-resources.md). Stejně jako všechny prostředky v Azure, přesune se skupina prostředků může trvat nějakou dobu.

> [!NOTE]
> Media Services v3 podporuje víceklientského modelu.

### <a name="considerations"></a>Požadavky

* Vytvoření zálohy všech dat ve vašem účtu před migrací do jiného předplatného.
* Budete muset zastavit všechny koncové body streamování a živé streamování prostředků. Vaši uživatelé nebudou mít přístup k vašemu obsahu po dobu trvání přechodu skupiny prostředků. 

> [!IMPORTANT]
> Koncový bod streamování nespustí, dokud se přesun dokončí úspěšně.

### <a name="troubleshoot"></a>Řešení potíží 

Pokud přidružené účtu Azure Storage nebo účtu Azure Media Services "odpojují" po přesunutí skupin prostředků, zkuste otočení klíčů účtu úložiště. Pokud otočení klíčů účtu úložiště neodstraní "odpojené" stav účtu Media Services, založte novou žádost o podporu z "Podpora a řešení potíží" nabídky v účtu Media Services.  

## <a name="next-steps"></a>Další postup

[Vytvoření účtu](create-account-cli-quickstart.md)
