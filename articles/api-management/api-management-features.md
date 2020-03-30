---
title: Porovnání úrovní Azure API Management založené na funkcích | Dokumenty společnosti Microsoft
description: Tento článek porovnává úrovně správy rozhraní API na základě funkcí, které nabízejí.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2019
ms.author: apimpm
ms.openlocfilehash: 2e84138419986ef1033ab076b3745187812e91b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335889"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porovnání úrovní Azure API Management založené na funkcích

Každá [cenová úroveň](https://aka.ms/apimpricing) api management nabízí odlišnou sadu funkcí a kapacitu na [jednotku](api-management-capacity.md). Následující tabulka shrnuje klíčové funkce, které jsou k dispozici v každé z úrovní. Některé funkce mohou fungovat jinak nebo mají různé možnosti v závislosti na úrovni. V takových případech jsou rozdíly volány v dokumentech popisujících tyto jednotlivé funkce.

> [!IMPORTANT]
> Upozorňujeme, že úroveň Vývojář je pro neprodukční případy použití a hodnocení. Nenabízí SLA.

| Funkce                                                                                      | Využití | Developer | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integrace Azure AD<sup>1</sup>                                                             | Ne          | Ano       | Ne    | Ano      | Ano     |
| Podpora virtuální sítě (VNet)                                                               | Ne          | Ano       | Ne    | Ne       | Ano     |
| Nasazení ve více oblastech                                                                      | Ne          | Ne        | Ne    | Ne       | Ano     |
| Více vlastních názvů domén                                                                 | Ne          | Ne        | Ne    | Ne       | Ano     |
| Vývojářský portál<sup>2</sup>                                                                 | Ne          | Ano       | Ano   | Ano      | Ano     |
| Vestavěná mezipaměť                                                                               | Ne          | Ano       | Ano   | Ano      | Ano     |
| Integrovaná analýza                                                                           | Ne          | Ano       | Ano   | Ano      | Ano     |
| [Brána s vlastním hostitelem](self-hosted-gateway-overview.md)<sup>3</sup>                           | Ne          | Ano       | Ne    | Ne       | Ano     |
| [Nastavení TLS](api-management-howto-manage-protocols-ciphers.md)                             | Ano         | Ano       | Ano   | Ano      | Ano     |
| [Externí mezipaměť](https://aka.ms/apimbyoc)                                                    | Ano         | Ano       | Ano   | Ano      | Ano     |
| [Ověření klientského certifikátu](api-management-howto-mutual-certificates-for-clients.md) | Ano         | Ano       | Ano   | Ano      | Ano     |
| [Zálohování a obnovení](api-management-howto-disaster-recovery-backup-restore.md)               | Ne          | Ano       | Ano   | Ano      | Ano     |
| [Správa přes Git](api-management-configuration-repository-git.md)                        | Ne          | Ano       | Ano   | Ano      | Ano     |
| Rozhraní API pro přímou správu                                                                        | Ne          | Ano       | Ano   | Ano      | Ano     |
| Protokoly a metriky Azure Monitoru                                                               | Ne          | Ano       | Ano   | Ano      | Ano     |
| Statická IP adresa                                                                                    | Ne          | Ano       | Ano   | Ano      | Ano     |

<sup>1</sup> Umožňuje použití Azure AD (a Azure AD B2C) jako zprostředkovatele identity pro přihlášení uživatele na portálu pro vývojáře.<br/>
<sup>2</sup> Včetně souvisejících funkcí, například uživatelů, skupin, problémů, aplikací a šablon e-mailů a oznámení.<br/>
<sup>3</sup> Omezeno na jedno nasazení brány s vlastním hostitelem s jedním uzly brány.<br/>
