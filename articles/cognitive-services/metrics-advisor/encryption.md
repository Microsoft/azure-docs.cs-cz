---
title: Šifrování služby Advisor metriky
titleSuffix: Azure Cognitive Services
description: Služba Advisor metrik nedrží Šifrování neaktivních dat.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 9d6a2f8a69d41d29e635b03425ed738484d6f408
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936009"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Služba Advisor metriky Šifrování neaktivních dat

Služba Advisor metriky automaticky šifruje vaše data při trvalém uložení do cloudu. Služba Advisor metriky pro šifrování chrání vaše data a pomůže vám splnit vaše závazky na zabezpečení a dodržování předpisů v organizaci.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Klíče spravované zákazníkem jsou dostupné jenom v cenové úrovni E0. Chcete-li požádat o možnost použití klíčů spravovaných zákazníkem, vyplňte a odešlete [formulář žádosti o klíč pro klíčové zákazníky služby Advisory](https://aka.ms/cogsvc-cmk). Bude to trvat přibližně 3-5 pracovních dnů, než se vrátí na stav vaší žádosti. V závislosti na poptávce můžete být do fronty zařazené a schválené, protože místo bude k dispozici. Po schválení pro používání CMK se službou Advisory metrik budete muset vytvořit nový prostředek Advisoru metriky a jako cenovou úroveň vybrat E0. Jakmile se vytvoří prostředek poradce metriky s cenovou úrovní E0, můžete k nastavení spravované identity použít Azure Key Vault.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Další kroky

* [Formulář žádosti o klíč pro klíčové zákazníky služby metriky](https://aka.ms/cogsvc-cmk)
* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
