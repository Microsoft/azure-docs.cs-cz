---
title: Místní historie vydaných verzí agenta ochrany pro hesla Azure AD
description: Historii změn pro vydání verze dokumentů a chování
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 89d64a28d2fe43464995e434c9f3807047b29492
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913632"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>Ve verzi Preview: Historie verzí agenta ochrany heslo pro Azure AD

|     |
| --- |
| Ochrana hesel Azure AD je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="12250"></a>1.2.25.0

Datum vydání: 11/01/2018

Opravy:

* Řadič domény proxy serveru a agent služby by měl už neselže kvůli chybám certifikátu vztahu důvěryhodnosti.
* Služba agenta a proxy řadič domény nemá další opravy pro počítače kompatibilní se standardem FIPS.
* Služba proxy bude nyní fungovat správně v protokolu TLS 1.2 – jen síťovém prostředí.
* Menší výkon a odolnost opravy
* Zdokonalené funkce protokolování

Změny:

* Minimální požadovaná úroveň operačního systému pro službu proxy serveru je nyní systému Windows Server 2012 R2. Minimální požadovanou úroveň operačního systému pro službu agenta DC zůstal ve Windows serveru 2012.
* Algoritmus pro ověření hesla používá tabulku normalizace rozšířené znaky. Výsledkem může být odmítnuta, hesla, která byla přijata v předchozích verzích.

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
* Menší výkon a odolnost opravy
* Zdokonalené funkce protokolování

## <a name="11103"></a>1.1.10.3

Datum vydání: 6/15/2018

Počáteční veřejné verze preview

## <a name="next-steps"></a>Další postup

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
