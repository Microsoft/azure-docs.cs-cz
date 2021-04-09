---
title: Zabezpečený vzdálený přístup k virtuálnímu počítači v Azure AD Domain Services | Microsoft Docs
description: Naučte se zabezpečit vzdálený přístup k virtuálním počítačům pomocí serveru NPS (Network Policy Server) a služby Azure AD Multi-Factor Authentication s nasazením vzdálené plochy ve Azure Active Directory Domain Services spravované doméně.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618922"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Zabezpečený vzdálený přístup k virtuálním počítačům v Azure Active Directory Domain Services

K zabezpečení vzdáleného přístupu k virtuálním počítačům, které běží ve spravované doméně Azure Active Directory Domain Services (Azure služba AD DS), můžete použít službu Vzdálená plocha (RDS) a server NPS (Network Policy Server). Azure služba AD DS ověřuje uživatele při žádosti o přístup prostřednictvím prostředí RDS. Pro zvýšení zabezpečení můžete integrovat Multi-Factor Authentication služby Azure AD a poskytnout tak dodatečné výzvy k ověření během přihlašovacích událostí. Azure AD Multi-Factor Authentication k poskytnutí této funkce používá rozšíření pro server NPS.

> [!IMPORTANT]
> Doporučený způsob, jak bezpečně se připojit k virtuálním počítačům ve spravované doméně Azure služba AD DS, je použití Azure bastionu, plně spravované služby PaaS spravované platformou, kterou zřizujete v rámci vaší virtuální sítě. Bastionu hostitel poskytuje zabezpečené a bezproblémové připojení protokol RDP (Remote Desktop Protocol) (RDP) k virtuálním počítačům přímo v Azure Portal přes SSL. Když se připojujete přes hostitele bastionu, virtuální počítače nepotřebují veřejnou IP adresu a nemusíte používat skupiny zabezpečení sítě k vystavení přístupu k protokolu RDP na portu TCP 3389.
>
> Důrazně doporučujeme, abyste používali Azure bastionu ve všech oblastech, kde je podpora podporovaná. V oblastech bez dostupnosti Azure bastionu postupujte podle kroků popsaných v tomto článku, dokud nebude k dispozici Azure bastionu. Ujistěte se, že přiřazujete veřejné IP adresy virtuálním počítačům připojeným k Azure služba AD DS, kde je povolený veškerý příchozí provoz protokolu RDP.
>
> Další informace najdete v tématu [co je Azure bastionu?][bastion-overview].

V tomto článku se dozvíte, jak nakonfigurovat službu Vzdálená plocha v Azure služba AD DS a volitelně použít rozšíření Azure AD Multi-Factor Authentication NPS.

![Služba Vzdálená plocha (RDS) – přehled](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete tyto prostředky:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Podsíť *úloh* vytvořená ve vaší Azure Active Directory Domain Services virtuální síti.
    * V případě potřeby [nakonfigurujte virtuální síť pro Azure Active Directory Domain Services spravovanou doménu][configure-azureadds-vnet].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Nasazení a konfigurace prostředí vzdálené plochy

Začněte tím, že vytvoříte minimálně dva virtuální počítače Azure s Windows serverem 2016 nebo Windows serverem 2019. Pokud chcete zajistit redundanci a vysokou dostupnost prostředí vzdálené plochy (RD), můžete později přidat a vyrovnávat zatížení dalších hostitelů.

Navrhované nasazení služby RDS zahrnuje tyto dva virtuální počítače:

* *RDGVM01* – spustí Server Zprostředkovatel připojení K VP, RD Web Access server a Brána VP server.
* *RDSHVM01* – spustí server Hostitel relace VP.

Ujistěte se, že jsou virtuální počítače nasazené do podsítě *úloh* služby Azure služba AD DS Virtual Network a pak je připojte k spravované doméně. Další informace najdete v tématu Postup [Vytvoření a připojení virtuálního počítače s Windows serverem ke spravované doméně][tutorial-create-join-vm].

Nasazení prostředí VP obsahuje několik kroků. Existující Průvodce nasazením VP se dá použít bez jakýchkoli konkrétních změn, které se mají použít ve spravované doméně:

1. Přihlaste se k virtuálním počítačům vytvořeným pro prostředí VP pomocí účtu, který je součástí skupiny *Azure AD DC Administrators* , například *contosoadmin*.
1. K vytvoření a konfiguraci služby RDS použijte existující [Průvodce nasazením prostředí vzdálené plochy][deploy-remote-desktop]. Distribuujte součásti serveru služby Vzdálená plocha napříč virtuálními počítači Azure podle potřeby.
    * Specifické pro Azure služba AD DS – Pokud konfigurujete licencování VP, nastavte ho na režim **podle zařízení** , ne **na uživatele** , jak je uvedeno v Průvodci nasazením.
1. Pokud chcete poskytnout přístup pomocí webového prohlížeče, [nastavte pro své uživatele webového klienta vzdálené plochy][rd-web-client].

Když je služba Vzdálená plocha nasazená do spravované domény, můžete ji spravovat a používat stejně jako v místní doméně služba AD DS.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>Nasazení a konfigurace serveru NPS a rozšíření Azure AD MFA NPS

Pokud chcete zvýšit zabezpečení uživatelského přihlašovacího prostředí, můžete volitelně integrovat prostředí VP s Azure AD Multi-Factor Authentication. V případě této konfigurace obdrží uživatelé během přihlašování další výzvu k potvrzení jejich identity.

Aby tato funkce poskytovala tuto funkci, nainstaluje se do vašeho prostředí další server NPS (Network Policy Server) spolu s rozšířením Azure AD Multi-Factor Authentication NPS. Toto rozšíření se integruje s Azure AD a vyžádá a vrátí stav výzev služby Multi-Factor Authentication.

Uživatelé musí být [zaregistrovaní, aby mohli používat Multi-Factor Authentication Azure AD][user-mfa-registration], což může vyžadovat další licence Azure AD.

Pokud chcete integrovat Multi-Factor Authentication služby Azure AD do prostředí Azure služba AD DS vzdálené plochy, vytvořte server NPS a nainstalujte rozšíření:

1. Vytvořte další virtuální počítač s Windows serverem 2016 nebo 2019, například *NPSVM01*, který je připojený k podsíti *úloh* v Azure služba AD DS Virtual Network. Připojte virtuální počítač k spravované doméně.
1. Přihlaste se k virtuálnímu počítači NPS jako účet, který je součástí skupiny *správců řadičů domény Azure AD* , například *contosoadmin*.
1. Z **Správce serveru** vyberte **Přidat role a funkce** a pak nainstalujte roli *služby síťové zásady a přístup* .
1. Pomocí existujícího článku s návody [nainstalujte a nakonfigurujte rozšíření Azure AD MFA NPS][nps-extension].

Když je nainstalované rozšíření NPS server a Azure AD Multi-Factor Authentication NPS, dokončete další část a nakonfigurujte ji pro použití s prostředím VP.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Integrace Brána vzdálené plochy a Multi-Factor Authentication Azure AD

Pokud chcete integrovat rozšíření služby Azure AD Multi-Factor Authentication NPS, použijte existující článek s postupem, jak [integrovat vaši Brána vzdálené plochy infrastrukturu pomocí rozšíření NPS (Network Policy Server) a Azure AD][azure-mfa-nps-integration].

Pro integraci se spravovanou doménou jsou potřeba následující další možnosti konfigurace:

1. [Neregistrujte server NPS ve službě Active Directory][register-nps-ad]. Tento krok se v spravované doméně nezdařil.
1. V [kroku 4 ke konfiguraci zásad sítě][create-nps-policy]zaškrtněte políčko pro **ignorování vlastností telefonického připojení uživatelského účtu**.
1. Pokud používáte Windows Server 2019 pro rozšíření serveru NPS a Azure AD Multi-Factor Authentication NPS, spusťte následující příkaz, který aktualizuje zabezpečený kanál, aby server NPS mohl správně komunikovat:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Uživatelům se teď při přihlášení zobrazí výzva k dalšímu ověřovacímu faktoru, jako je například textová zpráva nebo výzva v aplikaci Microsoft Authenticator.

## <a name="next-steps"></a>Další kroky

Další informace o vylepšení odolnosti při nasazení najdete v tématu [Služba Vzdálená plocha – vysoká dostupnost][rds-high-availability].

Další informace o zabezpečení přihlašování uživatelů najdete v tématu [jak to funguje: Azure AD Multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability