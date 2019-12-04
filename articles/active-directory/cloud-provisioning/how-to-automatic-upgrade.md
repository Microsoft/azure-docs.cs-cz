---
title: 'Azure AD Connect agent zřizování cloudu: automatický upgrade | Microsoft Docs'
description: Toto téma popisuje integrovanou funkci automatického upgradu v Azure AD Connect agenta zřizování cloudu.
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
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794455"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect agent zřizování cloudu: automatický upgrade

Ujistěte se, že instalace agenta Azure AD Connectho cloudového zřizování je vždycky aktuální, a to pomocí funkce **automatického upgradu** nikdy jednodušší. Tato funkce je ve výchozím nastavení povolená a nedá se zakázat.

Agent se nainstaluje tady: **"program FILES\AZURE AD Connect zřizování Agent\AADConnectProvisioningAgent.exe"**

Svou verzi můžete ověřit tak, že kliknete pravým tlačítkem na spustitelný soubor a vyberete vlastnosti a pak zobrazíte podrobnosti.

![Verze souboru agenta](media/how-to-automatic-upgrade/agent1.png)

Aktualizace agenta se nainstaluje tady: **"program FILES\AZURE AD Connect Updater\AzureADConnectAgentUpdater.exee agent"**

Svou verzi můžete ověřit tak, že kliknete pravým tlačítkem na spustitelný soubor a vyberete vlastnosti a pak zobrazíte podrobnosti.

![Verze aktualizačního agenta](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Odinstalace agenta
Chcete-li agenta odebrat, přejděte k části **odinstalace nebo změna programu** a odinstalujte následující:

- Aktualizace agenta Microsoft Azure AD Connect
- Agent zřizování pro Microsoft Azure AD připojení
- Balíček zřizovacího agenta Microsoft Azure AD Connect

![Odebrání agenta](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

