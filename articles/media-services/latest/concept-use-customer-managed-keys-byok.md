---
title: Přineste si vlastní klíč (spravované klíče zákazníka) s Media Services
description: Pomocí Media Services můžete použít spravovaný klíč zákazníka (to znamená, že Přineste si vlastní klíč).
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a89ff56eb9e0f0a29b5b1fed7543c5f718425e51
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325997"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Přineste si vlastní klíč (klíče spravované zákazníky) pomocí Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) je iniciativa pro Azure, která zákazníkům umožňuje přesunout své úlohy do cloudu. Klíče spravované zákazníkem umožňují zákazníkům dodržovat předpisy v oblasti dodržování předpisů a zdokonalují izolaci klientů služby. Poskytování šifrovacích klíčů zákazníkům je způsob, jak minimalizovat zbytečný přístup a řízení a spolehlivé sestavování v rámci služeb Microsoftu.

## <a name="keys-and-key-management"></a>Klíče a Správa klíčů

Pokud používáte rozhraní API Media Services 2020-05-01, můžete použít vlastní klíč s Media Services. Vytvoří se výchozí klíč účtu pro všechny účty, které jsou zašifrované pomocí systémového klíče, který patří Media Services. Když použijete vlastní klíč, klíč účtu se zašifruje s vaším klíčem. Klíče účtu šifrují klíče obsahu. Jsou také šifrované JobInputHttp adresy URL a ověřovací klíče symetrického tokenu.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Klíč spravovaný zákazníkem nahrazuje klíč spravovaný systémem.&quot;:::

Media Services používá spravovanou identitu účtu Media Services ke čtení klíče z Key Vault, které vlastníte. Media Services vyžaduje, aby se Key Vault ve stejné oblasti jako účet a aby byla povolená ochrana proti odstranění a vyprázdnění.

Váš klíč může být 2048, 3072 nebo 4096 klíč RSA, přičemž klíče HSM a software jsou podporovány.

> [!NOTE]
> Klíče ES nejsou podporovány.

Můžete zadat název klíče a verzi klíče nebo jenom název klíče. Když použijete jenom název klíče, Media Services použije nejnovější verzi klíče. Automaticky se zjišťují nové verze zákaznických klíčů a klíč účtu se znovu zašifruje.

> [!WARNING]
> Media Services monitoruje přístup k tomuto klíči zákazníka. Pokud je klíč zákazníka nepřístupný (například klíč byl odstraněn nebo byl odstraněn Key Vault nebo byl odebrán přístup), Media Services účet převede do stavu &quot;nepřístupný klíč zákazníka" (efektivně zakáže účet). Účet však lze v tomto stavu odstranit. Jedinými podporovanými operacemi je účet GET, LIST a DELETE; všechny ostatní požadavky (kódování, streamování atd.) selžou, dokud nebude obnoven přístup k klíči účtu.

## <a name="tutorial"></a>Kurz
Vyzkoušejte si kurz [použití spravovaných klíčů zákazníků nebo Přineste si vlastní klíč (BYOK) s Media Services](tutorial-byok.md) , abyste se dozvěděli o nastavení a používání zákaznických klíčů pomocí služby post a Azure REST API.

## <a name="next-steps"></a>Další kroky

* [Kurz: použití zákaznických klíčů nebo Přineste si vlastní klíč (BYOK) s Media Services](tutorial-byok.md)
