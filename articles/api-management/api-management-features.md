---
title: Na základě funkcí porovnání úrovních služby Azure API Management | Dokumentace Microsoftu
description: Tento článek porovnává založených na funkcích, které nabízejí úrovních služby API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: 34c4ef2885a82b6c392b814eeb624e616e341d48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304351"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Na základě funkcí porovnání úrovních služby Azure API Management

Každý bod správy rozhraní API [cenovou úroveň](https://aka.ms/apimpricing) nabízí různé sady funkcí a na jednotku [kapacity](api-management-capacity.md). Následující tabulka shrnuje klíčové funkce, které jsou k dispozici v každé z úrovní. Některé funkce mohou fungují jinak nebo máte různé možnosti v závislosti na vrstvu. V takových případech rozdíly jsou uvedeny v dokumentaci článků, které popisují tyto jednotlivé funkce.

| Funkce                                                                                      | Využití | Developer      | Basic          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integrace se službou Azure AD<sup>1</sup>                                                             | Ne                            | Ano            | Ne             | Ano            | Ano            |
| Podpora služby Virtual Network (VNet)                                                               | Ne                            | Ano            | Ne             | Ne             | Ano            |
| Nasazení ve více oblastech                                                                      | Ne                            | Ne             | Ne             | Ne             | Ano            |
| Více vlastních názvů domény                                                                 | Ne                            | Ne             | Ne             | Ne             | Ano            |
| Portál pro vývojáře<sup>2</sup>                                                                 | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Integrovanou mezipaměť                                                                               | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Integrované analytické nástroje                                                                           | Ne                            | Ano            | Ano            | Ano            | Ano            |
| [Nastavení SSL](api-management-howto-manage-protocols-ciphers.md)                             | Ano                            | Ano            | Ano            | Ano            | Ano            |
| [Externí mezipamětí](https://aka.ms/apimbyoc)                                                    | Ano                           | Ano            | Ano            | Ano            | Ano            |
| [Ověření klientského certifikátu](api-management-howto-mutual-certificates-for-clients.md) | Ano                | Ano            | Ano            | Ano            | Ano            |
| [Zálohování a obnovení](api-management-howto-disaster-recovery-backup-restore.md)               | Ne                            | Ano            | Ano            | Ano            | Ano            |
| [Správa přes Git](api-management-configuration-repository-git.md)                        | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Přímá správa rozhraní API                                                                        | Ne                            | Ano            | Ano            | Ano            | Ano            |
| Metriky a protokoly Azure monitoru                                                               | Ne                | Ano            | Ano            | Ano            | Ano            |

<sup>1</sup> umožňuje použití služby Azure AD (a Azure AD B2C) jako identita zprostředkovatele pro uživatele přihlášení na portálu pro vývojáře.<br/>
<sup>2</sup> včetně související funkce například uživatelé, skupiny, problémy, aplikací a e-mailové šablony a oznámení.<br/>
