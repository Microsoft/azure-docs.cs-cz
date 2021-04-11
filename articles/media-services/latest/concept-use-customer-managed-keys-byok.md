---
title: Přineste si vlastní klíč (spravované klíče zákazníka)
description: Pomocí Media Services můžete použít spravovaný klíč zákazníka (to znamená, že Přineste si vlastní klíč).
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: bc7b1a0742e79c3c84bf533e75467b5def2706b4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277721"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Přineste si vlastní klíč (klíče spravované zákazníky) pomocí Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) je iniciativa pro Azure, která zákazníkům umožňuje přesunout své úlohy do cloudu. Klíče spravované zákazníkem umožňují zákazníkům dodržovat předpisy v oblasti dodržování předpisů a zdokonalují izolaci klientů služby. Poskytování šifrovacích klíčů zákazníkům je způsob, jak minimalizovat zbytečný přístup a řízení a spolehlivé sestavování v rámci služeb Microsoftu.

## <a name="keys-and-key-management"></a>Klíče a Správa klíčů

Pokud používáte rozhraní API Media Services 2020-05-01, můžete použít vlastní klíč s Media Services. Vytvoří se výchozí klíč účtu pro všechny účty, které jsou zašifrované pomocí systémového klíče, který patří Media Services. Když použijete vlastní klíč, klíč účtu se zašifruje s vaším klíčem. Klíče účtu šifrují klíče obsahu. Jsou také šifrované JobInputHttp adresy URL a ověřovací klíče symetrického tokenu.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Klíč spravovaný zákazníkem nahrazuje klíč spravovaný systémem.":::

Media Services používá spravovanou identitu účtu Media Services ke čtení klíče z Key Vault, které vlastníte. Media Services vyžaduje, aby se Key Vault ve stejné oblasti jako účet a aby byla povolená ochrana proti odstranění a vyprázdnění.

Váš klíč může být 2048, 3072 nebo 4096 klíč RSA, přičemž klíče HSM a software jsou podporovány.

> [!NOTE]
> Klíče ES nejsou podporovány.

Můžete zadat název klíče a verzi klíče nebo jenom název klíče. Když použijete jenom název klíče, Media Services použije nejnovější verzi klíče. Automaticky se zjišťují nové verze zákaznických klíčů a klíč účtu se znovu zašifruje.

> [!WARNING]
> Media Services monitoruje přístup k tomuto klíči zákazníka. Pokud je klíč zákazníka nepřístupný (například klíč byl odstraněn nebo byl odstraněn Key Vault nebo byl odebrán přístup), Media Services účet převede do stavu "nepřístupný klíč zákazníka" (efektivně zakáže účet). Účet však lze v tomto stavu odstranit. Jedinými podporovanými operacemi je účet GET, LIST a DELETE; všechny ostatní požadavky (kódování, streamování atd.) selžou, dokud nebude obnoven přístup k klíči účtu.

## <a name="double-encryption"></a>Dvojité šifrování

Media Services automaticky podporuje šifrování typu Double. V případě neaktivních dat používá první vrstva šifrování spravovaný klíč zákazníka nebo spravovaný klíč společnosti Microsoft v závislosti na `AccountEncryption` Nastavení účtu.  Druhá vrstva šifrování pro neaktivní data je poskytována automaticky pomocí samostatného spravovaného klíče společnosti Microsoft. Další informace o dvojitém šifrování najdete v tématu [dvojité šifrování v Azure](../../security/fundamentals/double-encryption.md).

> [!NOTE]
> Na účtu Media Services se automaticky povolí dvojité šifrování. Je ale potřeba nakonfigurovat na svém účtu úložiště samostatně spravovaný klíč zákazníka a dvakrát ho zašifrovat. Viz [šifrování storege](../../storage/common/storage-service-encryption.md).

## <a name="tutorials"></a>Kurzy

- [Použití Azure Portal k použití klíčů spravovaných zákazníkem nebo BYOK s Media Services](security-customer-managed-keys-portal-tutorial.md)
- [Použití klíčů spravovaných zákazníkem nebo BYOK s využitím Media Services REST API](security-customer-managed-keys-rest-postman-tutorial.md).

## <a name="next-steps"></a>Další kroky

[Ochrana obsahu pomocí Media Services dynamického šifrování](drm-content-protection-concept.md)