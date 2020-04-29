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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78190309"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect agent zřizování cloudu: automatický upgrade

Ujistěte se, že vaše Azure Active Directory (Azure AD) Connect Cloud zřizovacího agenta je vždycky aktuální, a to pomocí funkce automatického upgradu.

Agent se nainstaluje tady: "program files\Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe"

Verzi ověříte tak, že kliknete pravým tlačítkem na spustitelný soubor a vyberete vlastnosti a pak podrobnosti.

![Verze souboru agenta](media/how-to-automatic-upgrade/agent1.png)

Aktualizace agenta se nainstaluje tady: "program files\Azure AD Connect Updater\AzureADConnectAgentUpdater.exee agent"

Verzi ověříte tak, že kliknete pravým tlačítkem na spustitelný soubor a vyberete vlastnosti a pak podrobnosti.

![Verze aktualizačního agenta](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Odinstalace agenta
Pokud chcete agenta odebrat, přejděte na **Odinstalovat nebo změnit program** a odinstalujte následující:

- **Aktualizace agenta Microsoft Azure AD Connect**
- **Agent zřizování pro Microsoft Azure AD připojení**
- **Balíček zřizovacího agenta Microsoft Azure AD Connect**

![Odebrání agenta](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

