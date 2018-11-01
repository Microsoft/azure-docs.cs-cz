---
title: Místní historie vydaných verzí agenta ochrany pro hesla Azure AD
description: Historii změn pro vydání verze dokumentů a chování
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: c52c84a1311c30c19356bb8a1287b203faf476fc
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50743258"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Ve verzi Preview: Historie verzí agenta ochrany heslo pro Azure AD

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12100"></a>1.2.10.0

Datum vydání: 8/17/2018

Opravy:

* Register-AzureADPasswordProtectionProxy a zaregistrujte AzureADPasswordProtectionForest teď podporují ověřování službou Multi-Factor Authentication
* Register-AzureADPasswordProtectionProxy vyžaduje WS2012 nebo novější řadič domény v doméně, aby zabránil chybám při šifrování.
* Služba agenta pro řadič domény je spolehlivější o vyžádání nové zásady hesla z Azure při spuštění.
* Služba agenta řadiče domény z Azure každou hodinu v případě potřeby bude požadovat nové zásady hesel, ale bude teď udělat na náhodně vybrané počáteční čas.
* Služba agenta řadiče domény už způsobí neomezenou zpoždění v nový řadič domény oznámení o inzerovaném programu při instalaci na serveru před jeho povýšení jako replika.
* Služba agenta pro řadič domény bude nyní případném dalším sdílení dodržovat nastavení "Povolit ochranu hesla na Windows Server Active Directory"
* Oba řadiče domény a agent proxy instalační programy teď podporují místní upgrade, při upgradu na budoucí verze.

> [!WARNING]
> Místní upgrade z verze 1.1.10.3 nepodporuje a způsobí chybu instalace. Na upgrade na verzi 1.2.10 nebo novější, je nutné nejprve úplně odinstalovat softwaru řadič domény proxy serveru a agent služby, potom nainstalujte novou verzi úplně od začátku. Vyžaduje se opětovná registrace ochrany hesla Azure AD službu proxy serveru.  Není potřeba znovu zaregistrovat doménové struktury.

> [!NOTE]
> Místní upgrady softwaru agenta pro řadič domény bude vyžadovat restartování.

* Řadič domény proxy serveru a agent služby teď podporují běží na serveru nakonfigurovaný tak, aby pouze používat algoritmy odpovídající standardu FIPS.
* Zdokonalené funkce protokolování
* Menší výkon a odolnost opravy

## <a name="11103"></a>1.1.10.3

Datum vydání: 6/15/2018

Počáteční veřejné verze preview

## <a name="next-steps"></a>Další postup

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
