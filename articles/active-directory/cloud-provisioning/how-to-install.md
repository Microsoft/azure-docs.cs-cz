---
title: Instaluje se agent zřizování cloudu Azure AD Connect.
description: Toto téma popisuje krok za krokem, jak nainstalovat zřizovacího agenta.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846228"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>Nainstalovat Azure AD Connect agenta zřizování cloudu
Tento dokument vás provede procesem instalace Azure AD Connect agentem zřizování a jeho počáteční konfigurací v Azure Portal.

>[!IMPORTANT]
>Následující pokyny k instalaci předpokládají, že byly splněny všechny [požadavky](how-to-prerequisites.md) .

Instalace a konfigurace Azure AD Connect zřizování se provádí v následujících krocích:
    
- [Instalace agenta](#install-the-agent)
- [Ověřit instalaci agenta](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalace agenta

1. Přihlaste se k serveru, který budete používat s oprávněními podnikového správce.
2. Přejděte na Azure Portal, na levé straně vyberte Azure Active Directory.
3. Klikněte na **Spravovat zřizování (Preview)** a vyberte **zkontrolovat všechny agenty**.
3. Stáhněte si Azure AD Connect agenta zřizování z Azure Portal.
![Obrazovka Vítejte](media/how-to-install/install9.png)</br>
3. Spusťte zřizování služby Azure AD Connect (AADConnectProvisioningAgent. Installer).
3. Na úvodní obrazovce **přijměte** licenční podmínky a klikněte na **nainstalovat**.</br>
![Obrazovka Vítejte](media/how-to-install/install1.png)</br>

4. Po dokončení této operace se spustí Průvodce konfigurací nástroje.  Přihlaste se pomocí účtu globálního správce služby Azure AD.
5. Na obrazovce **připojit ke službě Active Directory** klikněte na **Přidat adresář** a pak se přihlaste pomocí účtu správce služby Active Directory.  Tato operace přidá váš místní adresář.  Klikněte na **Další**.</br>
![Obrazovka Vítejte](media/how-to-install/install3.png)</br>

6. Na obrazovce **Konfigurace byla dokončena** klikněte na **Potvrdit**.  Tato operace provede registraci a restart agenta.</br>
![Obrazovka Vítejte](media/how-to-install/install4.png)</br>

7. Po dokončení této operace by se měla zobrazit oznámení, že **vaše ověření bylo úspěšné.**  Můžete kliknout na tlačítko **konec**.</br>
![Obrazovka Vítejte](media/how-to-install/install5.png)</br>
8. Pokud se stále zobrazuje úvodní úvodní obrazovka, klikněte na **Zavřít**.


## <a name="verify-agent-installation"></a>Ověřit instalaci agenta
K ověření agenta dochází v Azure Portal a na místním serveru, na kterém je spuštěný agent.

### <a name="azure-portal-agent-verification"></a>Ověřování agenta Azure Portal
Pokud chcete ověřit, že se agent zobrazuje v Azure, postupujte podle těchto kroků:

1. Přihlaste se k portálu Azure.
2. Na levé straně vyberte **Azure Active Directory**, klikněte na **Azure AD Connect** a ve středu vyberte **Spravovat zřizování (Preview)** .</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  Na obrazovce **zřizování Azure AD (Preview)** klikněte na **zkontrolovat všechny agenty**.
![zřizování Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **obrazovce místní zřizovací agenti** se zobrazí agenti, které jste nainstalovali.  Ověřte, zda je příslušný agent a označen jako **aktivní**.
agenti zřizování ![](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Ověření portu
Pokud chcete ověřit, že Azure naslouchá na portu 443 a že s ním může komunikovat agent, můžete použít následující:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Tento test ověří, jestli můžou vaši agenti komunikovat s Azure přes port 443.  Otevřete prohlížeč a přejděte na adresu URL na serveru, na kterém je nainstalovaný agent.
![Služby](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na místním serveru
Pokud chcete ověřit, jestli je agent spuštěný, postupujte takto:

1.  Přihlaste se k serveru pomocí účtu správce.
2.  Otevřete **služby** tak, že k němu přejdete nebo přejdete na Start/Run/Services. msc.
3.  V části **služby** se ujistěte, že **Microsoft Azure AD připojit** nástroj aktualizace agenta a že je spuštěný **Agent Microsoft Azure AD připojení zřizování** a že je ve stavu **spuštěno**.
![Služby](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agent byl nainstalován, ale musí být nakonfigurován a povolen předtím, než spustí synchronizaci uživatelů.  Pokud chcete nakonfigurovat nového agenta, přečtěte si téma [Azure AD Connect zřízení nové konfigurace agenta](how-to-configure.md).



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
 
