---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213671"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Nakonfigurovat poskytovatele otisku prstu snímače

Začneme vytvořením a konfigurací poskytovatele otisku prstu snímače. Poskytovatel otisku prstu snímače se postará o čtení senzorů specifických pro platformu na vašem zařízení a převádění jejich čtení do běžné reprezentace spotřebované v Cloud prostorové kotvy.

> [!IMPORTANT]
> Pokud máte senzory, které chcete povolit, k dispozici na vaší platformě, [Nezapomeňte toto zaškrtnout](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) .