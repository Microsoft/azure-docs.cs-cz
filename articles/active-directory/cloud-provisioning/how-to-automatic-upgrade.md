---
title: 'Agent zřizování cloudu Azure AD Connect: Automatický upgrade | Dokumenty společnosti Microsoft'
description: Tento článek popisuje integrovanou funkci automatického upgradu v agentovi azure ad připojení cloudu zřizování.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190309"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agent zřizování cloudu Azure AD Connect: Automatický upgrade

Ujistěte se, že vaše Azure Active Directory (Azure AD) Connect cloud zřizování agent instalace je vždy aktuální je snadné je snadné s funkcí automatického upgradu.

Agent je zde nainstalován: "Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Chcete-li ověřit verzi, klepněte pravým tlačítkem myši na spustitelný soubor a vyberte vlastnosti a podrobnosti.

![Verze souboru agenta](media/how-to-automatic-upgrade/agent1.png)

Zde je nainstalován updater agenta: "Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Chcete-li ověřit verzi, klepněte pravým tlačítkem myši na spustitelný soubor a vyberte vlastnosti a podrobnosti.

![Verze aktualivosti agenta](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Odinstalace agenta
Chcete-li agenta odebrat, přejděte na **odinstalovat nebo změnit program** a odinstalujte následující:

- **Aktualizace agenta microsoft Azure AD Connect**
- **Agent zřizování připojení Microsoft Azure AD**
- **Balíček agenta zřizování připojení Microsoft Azure AD**

![Odebrání agenta](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

