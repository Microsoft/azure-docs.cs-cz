---
title: 'Azure AD Connect agent zřizování cloudu: automatický upgrade | Microsoft Docs'
description: Tento článek popisuje integrovanou funkci automatického upgradu v Azure AD Connect agenta zřizování cloudu.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c0b47dd358826b843143aaf23c469d852e93b3
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613396"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect agent zřizování cloudu: automatický upgrade

Ujistěte se, že vaše Azure Active Directory (Azure AD) Connect Cloud zřizovacího agenta je vždycky aktuální, a to pomocí funkce automatického upgradu.

Agent se nainstaluje zde: "program files\Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe"

Verzi ověříte tak, že kliknete pravým tlačítkem na spustitelný soubor a vyberete vlastnosti a pak podrobnosti.

![Verze souboru agenta](media/how-to-automatic-upgrade/agent-1.png)

Aktualizace agenta se nainstaluje tady: "program files\Azure AD Connect zřizování agent Updater\AzureADConnectAgentUpdater.exe"

Verzi ověříte tak, že kliknete pravým tlačítkem na spustitelný soubor a vyberete vlastnosti a pak podrobnosti.

![Verze aktualizačního agenta](media/how-to-automatic-upgrade/agent-2.png)

## <a name="uninstall-the-agent"></a>Odinstalace agenta
Pokud chcete agenta odebrat, přejděte na **Odinstalovat nebo změnit program** a odinstalujte následující:

- **Aktualizace agenta Microsoft Azure AD Connect**
- **Agent zřizování pro Microsoft Azure AD připojení**
- **Balíček zřizovacího agenta Microsoft Azure AD Connect**

![Odebrání agenta](media/how-to-automatic-upgrade/agent-3.png)

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)

