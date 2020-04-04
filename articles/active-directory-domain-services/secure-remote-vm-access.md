---
title: Zabezpečený vzdálený přístup k virtuálním počítači ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak zabezpečit vzdálený přístup k virtuálním počítačům pomocí serveru NPS (Network Policy Server) a Azure Multi-Factor Authentication pomocí nasazení služby Vzdálená plocha ve spravované doméně Služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8bc36dfdf3010b2bde485228f6ee110b0b826d31
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654755"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Zabezpečený vzdálený přístup k virtuálním počítačům ve službě Azure Active Directory Domain Services

Chcete-li zabezpečit vzdálený přístup k virtuálním počítačům (VM), které běží ve spravované doméně Služby Azure Active Directory Domain Services (Azure AD DS), můžete použít služby Vzdálená plocha (RDS) a server síťových zásad (NPS). Azure AD DS ověřuje uživatele, kteří požadují přístup prostřednictvím prostředí RDS. Pro rozšířené zabezpečení můžete integrovat Azure Multi-Factor Authentication poskytnout další výzvu k ověření během událostí přihlášení. Azure Multi-Factor Authentication používá rozšíření pro server NPS k poskytování této funkce.

> [!IMPORTANT]
> Doporučený způsob, jak se bezpečně připojit k virtuálním počítačům ve spravované doméně Azure AD DS, je použití Azure Bastion, plně platformně spravované služby PaaS, kterou zřídíte ve virtuální síti. Hostitel bastionu poskytuje zabezpečené a bezproblémové připojení protokolu RDP (RDP) k vašim virtuálním počítačům přímo na portálu Azure přes SSL. Když se připojíte přes hostitele bastionu, vaše virtuální počítače nepotřebují veřejnou IP adresu a nemusíte používat skupiny zabezpečení sítě k vystavení přístupu k PROTOKOLU RDP na portu TCP 3389.
>
> Důrazně doporučujeme používat Azure Bastion ve všech oblastech, kde je podporována. V oblastech bez dostupnosti Azure Bastion postupujte podle pokynů popsaných v tomto článku, dokud azure bastion je k dispozici. Dávejte pozor při přiřazování veřejných IP adres virtuálním počítačům, které jsou připojeny k Azure AD DS, kde je povolen veškerý příchozí provoz RDP.
>
> Další informace najdete v tématu [Co je Azure Bastion?][bastion-overview].

Tento článek ukazuje, jak nakonfigurovat rds v Azure AD DS a volitelně použít rozšíření Azure Multi-Factor Authentication NPS.

![Přehled služby Vzdálená plocha (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Požadavky

Chcete-li tento článek dokončit, potřebujete následující zdroje:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Podsíť úloh vytvořená ve vaší virtuální síti *Služby* Azure Active Directory Domain Services.
    * V případě potřeby [nakonfigurujte virtuální síť pro spravovanou doménu služby Azure Active Directory Domain Services][configure-azureadds-vnet].
* Uživatelský účet, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Nasazení a konfigurace prostředí Vzdálené plochy

Chcete-li začít, vytvořte minimálně dva virtuální počítače Azure, které běží windows server 2016 nebo Windows Server 2019. Pro redundanci a vysokou dostupnost prostředí vzdálené plochy (RD) můžete později přidat a vyvážit zatížení dalších hostitelů.

Navrhované nasazení rds zahrnuje následující dva virtuální počítače:

* *RDGVM01* - Spouští server služby Zprostředkovatel připojení k VP, server pro přístup k webu VP a server služby Brána VP.
* *RDSHVM01* - Spustí hostitelský server relací VP.

Ujistěte se, že virtuální počítače jsou nasazené do podsítě *úloh* vaší virtuální sítě Azure AD DS a pak se připojte k virtuálním počítačům do spravované domény Azure AD DS. Další informace najdete v tématu [jak vytvořit a připojit virtuální počítač windows server ke spravované doméně Azure AD DS][tutorial-create-join-vm].

Nasazení prostředí VP obsahuje řadu kroků. Stávající ho průvodce nasazením v Oblasti Venkova lze použít bez jakýchkoli konkrétních změn, které by se měly použít ve spravované doméně Služby Azure AD DS:

1. Přihlaste se k virtuálním účtům vytvořeným pro prostředí VP pomocí účtu, který je součástí *skupiny Administrators azure ad dc,* jako je *například contosoadmin*.
1. Chcete-li vytvořit a nakonfigurovat službu RdS, použijte [stávajícího průvodce nasazením prostředí Vzdálené plochy][deploy-remote-desktop]. Podle potřeby distribuujte součásti serveru RD mezi virtuální počítače Azure.
1. Chcete-li poskytnout přístup pomocí webového prohlížeče, [nastavte pro uživatele webový klient Vzdálené plochy][rd-web-client].

S vzp nasazené do spravované domény Azure AD DS, můžete spravovat a používat službu, jako byste s místní doménou Služby AD DS.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Nasazení a konfigurace serveru NPS a rozšíření azure mfa nps

Pokud chcete zvýšit zabezpečení uživatelského prostředí pro přihlášení, můžete volitelně integrovat prostředí VP s azure multi-factor authentication. Při této konfiguraci uživatelé obdrží další výzvu během přihlášení k potvrzení jejich identity.

K zajištění této funkce je ve vašem prostředí nainstalován další server síťových zásad (NPS) spolu s rozšířením server NPS azure multi-factor authentication. Toto rozšíření se integruje s Azure AD požádat a vrátit stav výzvy vícefaktorové ověřování.

Uživatelé musí být [zaregistrováni, aby mohli používat Azure Multi-Factor Authentication][user-mfa-registration], což může vyžadovat další licence Azure AD.

Pokud chcete vícefaktorové ověřování Azure integrovat do prostředí vzdálené plochy Azure AD DS, vytvořte server NPS a nainstalujte rozšíření:

1. Vytvořte další virtuální počítač se systémem Windows Server 2016 nebo 2019, například *NPSVM01*, který je připojený k podsíti *úloh* ve virtuální síti Azure AD DS. Připojte virtuální počítač ke spravované doméně Azure AD DS.
1. Přihlaste se k virtuálnímu počítači NPS jako účet, který je součástí *skupiny Správci řadiče domény Azure AD,* jako je *například contosoadmin*.
1. Ve **Správci serveru**vyberte **Přidat role a funkce a**nainstalujte roli *Zásady sítě a služba Access Services.*
1. Pomocí existujícího článku s návody [nainstalujte a nakonfigurujte rozšíření Azure MFA NPS][nps-extension].

S nainstalovaným serverem NPS a rozšířením Server NPS azure s vícefaktorovým ověřováním dokončete další část a nakonfigurujte ji pro použití v prostředí VP.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Integrace brány vzdálené plochy a vícefaktorového ověřování Azure

Chcete-li integrovat rozšíření Server NPS azure vícefaktorového ověřování, použijte existující článek s návody k [integraci infrastruktury brány vzdálené plochy pomocí rozšíření NPS (Network Policy Server) a Azure AD][azure-mfa-nps-integration].

Následující další možnosti konfigurace jsou potřebné k integraci se spravovanou doménou Azure AD DS:

1. [Neregistrujte server NPS ve službě Active Directory][register-nps-ad]. Tento krok se nezdaří ve spravované doméně Azure AD DS.
1. V [kroku 4 konfigurace zásad sítě][create-nps-policy]zaškrtněte také políčko Ignorovat **vlastnosti telefonického připojení uživatelského účtu**.
1. Pokud používáte Windows Server 2019 pro server NPS a rozšíření Server NPS azure s vícefaktorovým ověřováním, spusťte následující příkaz a aktualizujte zabezpečený kanál, aby server NPS mohl správně komunikovat:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Uživatelé jsou nyní vyzváni k zadání dalšího faktoru ověřování při přihlášení, jako je například textová zpráva nebo výzva v aplikaci Microsoft Authenticator.

## <a name="next-steps"></a>Další kroky

Další informace o zlepšení odolnosti nasazení naleznete v tématu [Služba Vzdálená plocha – vysoká dostupnost][rds-high-availability].

Další informace o zabezpečení přihlášení uživatele najdete v tématu [Jak to funguje: Azure Multi-Factor Authentication][concepts-mfa].

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
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
