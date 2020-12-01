---
title: Instalace agenta zřizování cloudů Azure AD Connect
description: Tento článek popisuje, jak nainstalovat agenta pro zřizování cloudu Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4ba11dabc8e805f3604afc2fc4388840ac67cf
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348530"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Instalace agenta zřizování cloudů Azure AD Connect
Tento dokument vás provede procesem instalace agenta zřizování služby Azure Active Directory (Azure AD) Connect a jeho počáteční konfigurace v Azure Portal.

>[!IMPORTANT]
>Následující pokyny k instalaci předpokládají, že byly splněny všechny [požadavky](how-to-prerequisites.md) .

Instalace a konfigurace Azure AD Connect zřizování se provádí v následujících krocích:

- [Skupinové účty spravované služby](#group-managed-service-accounts) 
- [Instalace agenta](#install-the-agent)
- [Ověřit instalaci agenta](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Skupinové účty spravované služby
Skupinový účet spravované služby je účet spravované domény, který poskytuje automatickou správu hesel, zjednodušenou správu hlavního názvu služby (SPN), schopnost delegovat správu na jiné správce a také rozšiřuje tuto funkci na více serverů.  Azure AD Connect synchronizace cloudu podporuje a doporučuje používání skupinového účtu spravované služby ke spuštění agenta.  Další informace o gMSA najdete v tématu [skupinový účet spravované služby](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) . 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Upgrade existujícího agenta na používání účtu gMSA
Chcete-li upgradovat stávajícího agenta tak, aby používal účet gMSA vytvořený během instalace, stačí aktualizovat službu agenta na nejnovější verzi spuštěním AADConnectProvisioningAgent.msi.  Tím se služba upgraduje na nejnovější verzi.  Nyní spusťte Průvodce instalací a po zobrazení výzvy zadejte přihlašovací údaje pro vytvoření účtu.



## <a name="install-the-agent"></a>Instalace agenta
Chcete-li nainstalovat agenta, postupujte podle těchto kroků.

 1. Přihlaste se k serveru, který budete používat s oprávněními podnikového správce.
 2. Přihlaste se k Azure Portal a pak přejít na **Azure Active Directory**.
 3. V nabídce vlevo vyberte **Azure AD Connect**.
 4. Vyberte **Spravovat zřizování (Preview)**  >  **Zkontrolujte všechny agenty**.
 5. Stáhněte si Azure AD Connect agenta zřizování z Azure Portal.
   ![Stáhnout místního agenta](media/how-to-install/install-9.png)</br>
 6. Spusťte instalační AADConnectProvisioningAgent.msi Azure AD Connect zřizování.
 7. Na obrazovce **Microsoft Azure AD připojení zřizovacího agenta** přijměte licenční podmínky a vyberte **nainstalovat**.
   ![Obrazovka balíčku pro zřízení zřizovacího agenta Microsoft Azure AD připojení](media/how-to-install/install-1.png)</br>
 8. Po dokončení této operace se spustí Průvodce konfigurací nástroje. Přihlaste se pomocí účtu globálního správce služby Azure AD.
 9. Na **obrazovce konfigurace účtu služby** vyberte buď **vytvořit gMSA** , nebo **použijte vlastní gMSA**.  Pokud povolíte agentovi vytvořit účet, bude název provAgentgMSA $. Pokud zadáte **použít vlastní gMSA** , zobrazí se výzva k zadání tohoto účtu.
 10. Zadáním přihlašovacích údajů správce domény vytvořte skupinový účet spravované služby, který se použije ke spuštění služby agenta. Klikněte na **Next** (Další).  
   ![Vytvořit gMSA](media/how-to-install/install-12.png)</br>
 11. Na obrazovce **připojit ke službě Active Directory** vyberte **Přidat adresář**. Pak se přihlaste pomocí účtu správce služby Active Directory. Tato operace přidá váš místní adresář. 
 12. Volitelně můžete spravovat preference řadičů domény, které bude agent používat, a to tak, že vybere **možnost vybrat prioritu řadiče domény** a objednává seznam řadičů domény.   Klikněte na **OK**.
  ![Seřazení řadičů domény](media/how-to-install/install-2a.png)</br>
 13. Vyberte **Další**.
  ![Obrazovka připojit ke službě Active Directory](media/how-to-install/install-3a.png)</br>
 14.  Na obrazovce pro **instalaci agenta** potvrďte nastavení a účet, který se vytvoří, a klikněte na **Potvrdit**.
  ![Potvrdit nastavení](media/how-to-install/install-11.png)</br>
 15. Po dokončení této operace byste měli vidět, **že se instalace agenta dokončila.** Vyberte možnost **ukončit**.
  ![Obrazovka dokončení konfigurace](media/how-to-install/install-4a.png)</br>
1. Pokud se stále zobrazuje úvodní obrazovka úvodního **balíčku Microsoft Azure AD připojení** , vyberte **Zavřít**.

## <a name="verify-agent-installation"></a>Ověřit instalaci agenta
K ověření agenta dochází v Azure Portal a na místním serveru, na kterém je spuštěný agent.

### <a name="azure-portal-agent-verification"></a>Ověřování agenta Azure Portal
Pokud chcete ověřit, jestli je agent v Azure, postupujte podle těchto kroků.

1. Přihlaste se k webu Azure Portal.
1. Na levé straně vyberte **Azure Active Directory**  >  **Azure AD Connect**. V centru vyberte **Spravovat zřizování (Preview)**.

   ![portál Azure](media/how-to-install/install-6.png)</br>

1.  Na obrazovce **Azure AD zřizování (Preview)** vyberte **zkontrolovat všechny agenty**.

    ![Možnost zkontrolovat všechny agenty](media/how-to-install/install-7.png)</br>
 
1. Na obrazovce místní **zřizovací agenti** uvidíte agenty, které jste nainstalovali. Ověřte, zda je příslušný agent a označen jako *aktivní*.

   ![Obrazovka místních zřizovacích agentů](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>Na místním serveru
Pokud chcete ověřit, jestli je agent spuštěný, postupujte podle těchto kroků.

1.  Přihlaste se k serveru pomocí účtu správce.
1.  Otevřete **služby** tak, že k němu přejdete, nebo když přejdete na **Spustit**  >  **Spustit**  >  **Services. msc**.
1.  V části **služby** se ujistěte, že je k dispozici aktualizace **agenta Microsoft Azure AD Connect** a **Microsoft Azure AD připojení zřizování** , a jejich stav je *spuštěný*.

    ![Obrazovka služby](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>Agent byl nainstalován, ale musí být nakonfigurován a povolen předtím, než bude moci spustit synchronizaci uživatelů. Pokud chcete nakonfigurovat nového agenta, přečtěte si téma [Vytvoření nové konfigurace pro Azure AD Connect cloudové zřizování](how-to-configure.md).




## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
