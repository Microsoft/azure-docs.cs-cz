---
title: Instalace agenta zřizování cloudů Azure AD Connect
description: Tento článek popisuje, jak nainstalovat agenta azure ad připojení cloudu zřizování.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263345"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalace agenta zřizování cloudů Azure AD Connect
Tento dokument vás provede procesem instalace pro agenta Azure Active Directory (Azure AD) Connect zřizování a jak ho zpočátku nakonfigurovat na webu Azure Portal.

>[!IMPORTANT]
>Následující pokyny k instalaci předpokládají, že byly splněny všechny [požadavky.](how-to-prerequisites.md)

Instalace a konfigurace zřizování Azure AD Connect se provádí v následujících krocích:
    
- [Instalace agenta](#install-the-agent)
- [Ověření instalace agenta](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalace agenta
Chcete-li nainstalovat agenta, postupujte takto.

1. Přihlaste se k serveru, který budete používat s oprávněními správce rozlehlé sítě.
1. Přejděte na web Azure Portal. Na levé straně vyberte **Azure Active Directory**.
1. Vyberte **Spravovat zřizování (náhled)** > **Zkontrolujte všechny agenty**.
1. Stáhněte si agenta zřizování Azure AD Connect z webu Azure Portal.

   ![Stažení místního agenta](media/how-to-install/install9.png)</br>
1. Spusťte instalační program zřizování Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. Na obrazovce **Microsoft Azure AD Connect Provisioning Agent Package** přijměte licenční podmínky a vyberte **Instalovat**.

   ![Obrazovka balíčku balíčku zprostředkovatele zřizování připojení Microsoft Azure AD](media/how-to-install/install1.png)</br>

1. Po dokončení této operace se spustí průvodce konfigurací. Přihlaste se pomocí účtu globálního správce Azure AD.
1. Na obrazovce **Připojit službu Active Directory** vyberte přidat **adresář**. Poté se přihlaste pomocí účtu správce služby Active Directory. Tato operace přidá místní adresář. Vyberte **další**.

   ![Obrazovka Připojení služby Active Directory](media/how-to-install/install3.png)</br>

1. Na obrazovce **Dokončení konfigurace** vyberte **Potvrdit**. Tato operace registruje a restartuje agenta.

   ![Obrazovka dokončení konfigurace](media/how-to-install/install4.png)</br>

1. Po dokončení této operace byste se měli zobrazit oznámení **Konfigurace agenta byla úspěšně ověřena.** Vyberte **možnost Ukončit**.

   ![Tlačítko Konec](media/how-to-install/install5.png)</br>
1. Pokud se stále zobrazí úvodní obrazovka **balíčku agenta zřizování připojení Microsoft Azure AD,** vyberte **Zavřít**.

## <a name="verify-agent-installation"></a>Ověření instalace agenta
K ověření agenta dochází na webu Azure Portal a na místním serveru, na který agenta spouštějí.

### <a name="azure-portal-agent-verification"></a>Ověření agenta portálu Azure
Chcete-li ověřit agent a to se zobchází v Azure, postupujte takto.

1. Přihlaste se k portálu Azure.
1. Na levé straně vyberte **Azure Active Directory** > **Azure AD Connect**. Ve středu vyberte **Spravovat zřizování (náhled).**

   ![portál Azure](media/how-to-install/install6.png)</br>

1.  Na obrazovce **Zřizování Azure AD (náhled)** vyberte **Zkontrolovat všechny agenty**.

    ![Zkontrolovat možnost všech agentů](media/how-to-install/install7.png)</br>
 
1. Na **obrazovce Místní zřizovací agenti** uvidíte agenty, které jste nainstalovali. Ověřte, zda je dotyčný agent k dispozici a zda je označen *jako aktivní*.

   ![Obrazovka Místních agentů zřizování](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Ověření portu
Chcete-li ověřit, že Azure naslouchá na portu 443 a že s ním váš agent může komunikovat, postupujte takto.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Tento test ověří, že vaši agenti mohou komunikovat s Azure přes port 443. Otevřete prohlížeč a přejděte na předchozí adresu URL ze serveru, na kterém je agent nainstalován.

![Ověření dosažitelnosti portu](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Na místním serveru
Chcete-li ověřit, zda je agent spuštěn, postupujte takto.

1.  Přihlaste se k serveru pomocí účtu správce.
1.  Spusťte **služby** tak, že na něj přejdete nebo přejdete na **Spustit** > **soubor** > **Services.msc**.
1.  V části **Služby**zkontrolujte, jestli jsou k dispozici **zprostředkovateli microsoft azure a připojení k připojení** a že je *spuštěný*agent Microsoft Azure **AD Connect.**

    ![Obrazovka Služby](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Agent byl nainstalován, ale musí být nakonfigurován a povolen, než začne synchronizovat uživatele. Pokud chcete nakonfigurovat nového agenta, přečtěte [si tématu Vytvoření nové konfigurace pro zřizování na základě cloudového připojení Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
 
