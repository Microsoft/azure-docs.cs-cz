---
title: Porovnání na základě funkcí na úrovních API Management Azure | Microsoft Docs
description: Porovnejte API Management vrstev na základě funkcí, které nabízí. Podívejte se na tabulku, která shrnuje klíčové funkce, které jsou k dispozici v každé cenové úrovni.
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
ms.openlocfilehash: 262d12e1b0ff74570b44b7794f8b55619a9afa97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905138"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porovnání na základě funkcí API Management vrstev Azure

Každá API Management [cenová úroveň](https://aka.ms/apimpricing) nabízí samostatnou sadu funkcí a [kapacitu](api-management-capacity.md)jednotek. Následující tabulka shrnuje klíčové funkce, které jsou k dispozici v každé z vrstev. Některé funkce můžou pracovat různě nebo mít v závislosti na vrstvě různé možnosti. V takových případech jsou rozdíly vyvolány v článcích dokumentace popisujících tyto jednotlivé funkce.

> [!IMPORTANT]
> Všimněte si prosím, že úroveň pro vývojáře je určena pro případy neprodukčního použití a hodnocení. Nenabízí smlouvu SLA.

| Příznak                                                                                      | Consumption | Vývojář | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integrace Azure AD<sup>1</sup>                                                             | No          | Yes       | No    | Yes      | Yes     |
| Podpora Virtual Network (VNet)                                                               | No          | Yes       | No    | No       | Yes     |
| Nasazení ve více oblastech                                                                      | No          | No        | No    | No       | Yes     |
| Několik vlastních názvů domén                                                                 | No          | Yes        | No    | No       | Yes     |
| Portál pro vývojáře<sup>2</sup>                                                                 | No          | Yes       | Yes   | Yes      | Yes     |
| Integrovaná mezipaměť                                                                               | No          | Yes       | Yes   | Yes      | Yes     |
| Integrované analýzy                                                                           | No          | Yes       | Yes   | Yes      | Yes     |
| [Samoobslužná brána](self-hosted-gateway-overview.md)<sup>3</sup>                           | No          | Yes       | No    | No       | Yes     |
| [Nastavení TLS](api-management-howto-manage-protocols-ciphers.md)                             | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Externí mezipaměť](https://aka.ms/apimbyoc)                                                    | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Ověřování certifikátu klienta](api-management-howto-mutual-certificates-for-clients.md) | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Zálohování a obnovení](api-management-howto-disaster-recovery-backup-restore.md)               | No          | Yes       | Yes   | Yes      | Yes     |
| [Správa přes Git](api-management-configuration-repository-git.md)                        | No          | Yes       | Yes   | Yes      | Yes     |
| Rozhraní API pro přímé řízení                                                                        | No          | Yes       | Yes   | Yes      | Yes     |
| Protokoly Azure Monitor a metriky                                                               | Yes         | Yes       | Yes   | Yes      | Yes     |
| Statická IP adresa                                                                                    | No          | Yes       | Yes   | Yes      | Yes     |

<sup>1</sup> povoluje použití Azure AD (a Azure AD B2C) jako zprostředkovatele identity pro přihlášení uživatele na portálu pro vývojáře.<br/>
<sup>2</sup> včetně souvisejících funkcí, např. uživatelé, skupiny, problémy, aplikace a e-mailové šablony a oznámení.<br/>
<sup>3</sup> v bráně pro samoobslužné hostování v úrovni Developer jsou omezené na uzel jedna brána.<br/>
