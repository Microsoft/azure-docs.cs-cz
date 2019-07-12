---
title: Správa účtů služby Azure Media Services v2 | Dokumentace Microsoftu
description: Pokud chcete začít, správa, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. Tento článek vysvětluje, jak chcete spravovat účty pro Azure Media Services v2.
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
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622040"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Správa účtů služby Azure Media Services v2

Pokud chcete začít, správa, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Účet Media Services a všechny přidružené účty úložiště musí být ve stejném předplatném Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Přesunutí účtu Azure Media Services mezi předplatnými 

Pokud potřebujete přesunout do nového předplatného účtu Azure Media Services, musíte nejprve přesunout celou skupinu prostředků obsahující účet Media Services do nového předplatného. Je nutné přesunout všechny připojené zdroje: Účty Azure Storage, profilů CDN, atd. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/resource-group-move-resources.md). Stejně jako všechny prostředky v Azure, přesune se skupina prostředků může trvat nějakou dobu.

Služba Media Services v2 nepodporuje víceklientského modelu. Pokud potřebujete přesunout účtu Azure Media Services do předplatného do nového tenanta, vytvořte novou aplikaci Azure Active Directory (Azure AD) do nového tenanta. Přesuňte váš účet pro toto předplatné nového tenanta. Po dokončení přesunu tenanta, můžete začít používat aplikaci Azure AD z nového tenanta pro přístup k účtu Media Services pomocí rozhraní API v2. 

> [!IMPORTANT]
> Budete potřebovat resetovat [ověřování Azure AD](media-services-portal-get-started-with-aad.md) údaje pro přístup k rozhraní API služby Media Services v2.  
### <a name="considerations"></a>Požadavky

* Vytvoření zálohy všech dat ve vašem účtu před migrací do jiného předplatného.
* Budete muset zastavit všechny koncové body streamování a živé streamování prostředků. Vaši uživatelé nebudou mít přístup k vašemu obsahu po dobu trvání přechodu skupiny prostředků. 

> [!IMPORTANT]
> Koncový bod streamování nespustí, dokud se přesun dokončí úspěšně.

### <a name="troubleshoot"></a>Řešení potíží 

Pokud přidružené účtu Azure Storage nebo účtu Azure Media Services "odpojují" po přesunutí skupin prostředků, zkuste otočení klíčů účtu úložiště. Pokud otočení klíčů účtu úložiště neodstraní "odpojené" stav účtu Media Services, založte novou žádost o podporu z "Podpora a řešení potíží" nabídky v účtu Media Services.  
 
## <a name="next-steps"></a>Další kroky

[Vytvoření účtu](media-services-portal-create-account.md)
