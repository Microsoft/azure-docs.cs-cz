---
title: Vytvoření instančního objektu Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95098468"
---
## <a name="create-an-azure-service-principal"></a>Vytvoření instančního objektu Azure

Pokud chcete, aby vaše aplikace pracovala s vaším účtem Azure, potřebujete instanční objekt Azure pro správu oprávnění. Postupujte podle pokynů v tématu [Vytvoření instančního objektu Azure](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Když vytvoříte instanční objekt, uvidíte, že má tajnou hodnotu, ID a ID aplikace. Uložte ID aplikace a tajný kód do dočasného umístění pro pozdější kroky.