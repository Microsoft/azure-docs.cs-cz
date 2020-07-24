---
title: Porovnání na základě funkcí na úrovních API Management Azure | Microsoft Docs
description: Tento článek porovnává API Management vrstev na základě funkcí, které nabízí.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: 7ec89d985c025814d903ae9a16f9b0f5123b3571
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056428"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porovnání na základě funkcí API Management vrstev Azure

Každá API Management [cenová úroveň](https://aka.ms/apimpricing) nabízí samostatnou sadu funkcí a [kapacitu](api-management-capacity.md)jednotek. Následující tabulka shrnuje klíčové funkce, které jsou k dispozici v každé z vrstev. Některé funkce můžou pracovat různě nebo mít v závislosti na vrstvě různé možnosti. V takových případech jsou rozdíly vyvolány v článcích dokumentace popisujících tyto jednotlivé funkce.

> [!IMPORTANT]
> Všimněte si prosím, že úroveň pro vývojáře je určena pro případy neprodukčního použití a hodnocení. Nenabízí smlouvu SLA.

| Funkce                                                                                      | Využití | Vývojář | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integrace Azure AD<sup>1</sup>                                                             | No          | Yes       | No    | Ano      | Ano     |
| Podpora Virtual Network (VNet)                                                               | No          | Yes       | No    | No       | Yes     |
| Nasazení ve více oblastech                                                                      | No          | No        | No    | No       | Yes     |
| Několik vlastních názvů domén                                                                 | No          | No        | No    | No       | Yes     |
| Portál pro vývojáře<sup>2</sup>                                                                 | No          | Ano       | Ano   | Ano      | Ano     |
| Integrovaná mezipaměť                                                                               | No          | Ano       | Ano   | Ano      | Ano     |
| Integrované analýzy                                                                           | No          | Ano       | Ano   | Ano      | Ano     |
| [Samoobslužná brána](self-hosted-gateway-overview.md)<sup>3</sup>                           | No          | Yes       | No    | No       | Yes     |
| [Nastavení TLS](api-management-howto-manage-protocols-ciphers.md)                             | Ano         | Ano       | Ano   | Ano      | Ano     |
| [Externí mezipaměť](https://aka.ms/apimbyoc)                                                    | Ano         | Ano       | Ano   | Ano      | Ano     |
| [Ověřování certifikátu klienta](api-management-howto-mutual-certificates-for-clients.md) | Ano         | Ano       | Ano   | Ano      | Ano     |
| [Zálohování a obnovení](api-management-howto-disaster-recovery-backup-restore.md)               | No          | Ano       | Ano   | Ano      | Ano     |
| [Správa přes Git](api-management-configuration-repository-git.md)                        | No          | Ano       | Ano   | Ano      | Ano     |
| Rozhraní API pro přímé řízení                                                                        | No          | Ano       | Ano   | Ano      | Ano     |
| Protokoly Azure Monitor a metriky                                                               | Ano         | Ano       | Ano   | Ano      | Ano     |
| Statická IP adresa                                                                                    | No          | Ano       | Ano   | Ano      | Ano     |

<sup>1</sup> povoluje použití Azure AD (a Azure AD B2C) jako zprostředkovatele identity pro přihlášení uživatele na portálu pro vývojáře.<br/>
<sup>2</sup> včetně souvisejících funkcí, např. uživatelé, skupiny, problémy, aplikace a e-mailové šablony a oznámení.<br/>
<sup>3</sup> v bráně pro samoobslužné hostování v úrovni Developer jsou omezené na uzel jedna brána.<br/>
