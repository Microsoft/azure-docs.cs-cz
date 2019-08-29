---
title: Porovnání na základě funkcí na úrovních API Management Azure | Microsoft Docs
description: Tento článek porovnává API Management vrstev na základě funkcí, které nabízí.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: bc5eab6bb5044e474c386d3e5771d8517e6ba9cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072532"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porovnání na základě funkcí API Management vrstev Azure

>[!IMPORTANT]
> Všimněte si prosím, že úroveň pro vývojáře je určena pro případy neprodukčního použití a hodnocení. Nenabízí smlouvu SLA. 

Každá API Management [cenová úroveň](https://aka.ms/apimpricing) nabízí samostatnou sadu funkcí a [kapacitu](api-management-capacity.md)jednotek. Následující tabulka shrnuje klíčové funkce, které jsou k dispozici v každé z vrstev. Některé funkce můžou pracovat různě nebo mít v závislosti na vrstvě různé možnosti. V takových případech jsou rozdíly vyvolány v článcích dokumentace popisujících tyto jednotlivé funkce.

| Funkce                                                                                      | Využití | Vývojář      | Basic          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integrace Azure AD<sup>1</sup>                                                             | Ne                            | Ano            | Ne             | Ano            | Ano            |
| Podpora Virtual Network (VNet)                                                               | Ne                            | Ano            | Ne             | Ne             | Ano            |
| Nasazení ve více oblastech                                                                      | Ne                            | Ne             | Ne             | Ne             | Ano            |
| Více vlastních názvů domény                                                                 | Ne                            | Ne             | Ne             | Ne             | Ano            |
| Portál pro vývojáře<sup>2</sup>                                                                 | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Integrovaná mezipaměť                                                                               | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Integrované analytické nástroje                                                                           | Ne                            | Ano            | Ano            | Ano            | Ano            |
| [Nastavení SSL](api-management-howto-manage-protocols-ciphers.md)                             | Ano                            | Ano            | Ano            | Ano            | Ano            |
| [Externí mezipaměť](https://aka.ms/apimbyoc)                                                    | Ano                           | Ano            | Ano            | Ano            | Ano            |
| [Ověřování certifikátu klienta](api-management-howto-mutual-certificates-for-clients.md) | Ano                | Ano            | Ano            | Ano            | Ano            |
| [Zálohování a obnovení](api-management-howto-disaster-recovery-backup-restore.md)               | Ne                            | Ano            | Ano            | Ano            | Ano            |
| [Správa přes Git](api-management-configuration-repository-git.md)                        | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Rozhraní API pro přímé řízení                                                                        | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Protokoly Azure Monitor a metriky                                                               | Ne                | Ano            | Ano            | Ano            | Ano            |
| Statická IP adresa                                                               | Ne                | Ano            | Ano            | Ano            | Ano            |

<sup>1</sup> povoluje použití Azure AD (a Azure AD B2C) jako zprostředkovatele identity pro přihlášení uživatele na portálu pro vývojáře.<br/>
<sup>2</sup> včetně souvisejících funkcí, např. uživatelé, skupiny, problémy, aplikace a e-mailové šablony a oznámení.<br/>
