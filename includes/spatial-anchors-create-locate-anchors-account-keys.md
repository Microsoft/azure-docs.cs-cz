---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694188"
---
## <a name="set-up-authentication"></a>Nastavení ověřování

Chcete-li získat přístup ke službě, musíte zadat klíč účtu, přístupový token nebo token auth služby Azure Active Directory. Další informace si o tom můžete přečíst na [stránce Koncept ověřování](/azure/spatial-anchors/concepts/authentication).

### <a name="account-keys"></a>Klíče účtu

Klíče účtů jsou pověření, které umožňuje vaší aplikaci k ověření pomocí služby Azure Spatial Anchors. Zamýšleným účelem klíčů účtu je pomoci vám rychle začít. Zejména během fáze vývoje integrace vaší aplikace s Azure Spatial Anchors. Jako takové můžete použít klíče účtu jejich vložením do klientských aplikací během vývoje. Jak postupujete mimo vývoj, důrazně doporučujeme přejít na mechanismus ověřování, který je na úrovni produkčního prostředí, podporovaný přístupovými tokeny nebo ověřováním uživatelů služby Azure Active Directory. Pokud chcete získat klíč klienta pro vývoj, navštivte svůj účet Azure Spatial Anchors a přejděte na kartu Klíče.
