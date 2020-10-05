---
title: Vytvoření instančního objektu Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 5cff103d17138608f4932e36444847437f6da236
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321555"
---
## <a name="create-an-azure-service-principal"></a>Vytvoření instančního objektu Azure

Pokud chcete, aby vaše aplikace pracovala s vaším účtem Azure, potřebujete instanční objekt Azure pro správu oprávnění. Postupujte podle pokynů v tématu [Vytvoření instančního objektu Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Když vytvoříte instanční objekt, uvidíte, že má tajnou hodnotu, ID a ID aplikace. Uložte ID aplikace a tajný kód do dočasného umístění pro pozdější kroky.