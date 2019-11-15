---
title: Nasadit Azure Proxy aplikací služby AD pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak zajistit zabezpečený přístup k interním aplikacím pro vzdálené procesy nasazením a konfigurací Proxy aplikací služby Azure Active Directory ve Azure Active Directory Domain Services spravované doméně.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c0fcb8c2c5f9afa7fabe2ffa63a715ec24aa4a26
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720489"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Nasazení Azure Proxy aplikací služby AD pro zabezpečený přístup k interním aplikacím ve Azure AD Domain Services spravované doméně

Pomocí Azure AD Domain Services (Azure služba AD DS) můžete starší verze aplikací, které běží místně, nasouvat do Azure. Proxy aplikace Azure Active Directory (AD) pak pomáhá podporovat vzdálené pracovníky tím, že tyto interní aplikace bezpečně publikují jako součást spravované domény Azure služba AD DS, aby k nim bylo možné přistup přes Internet.

Pokud se Proxy aplikací služby AD Azure ještě nepoužíváte a chcete získat další informace, přečtěte si téma [jak poskytnout zabezpečený vzdálený přístup k interním aplikacím](../active-directory/manage-apps/application-proxy.md).

V tomto článku se dozvíte, jak vytvořit a nakonfigurovat konektor Azure Proxy aplikací služby AD, který poskytuje zabezpečený přístup k aplikacím ve spravované doméně Azure služba AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
    * K použití Proxy aplikací služby AD Azure je nutná **licence Azure AD Premium** .
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte instanci Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Vytvoření virtuálního počítače s Windows připojeným k doméně

Pokud chcete směrovat provoz do aplikací spuštěných ve vašem prostředí, nainstalujte komponentu konektoru Azure Proxy aplikací služby AD. Tento konektor Azure Proxy aplikací služby AD musí být nainstalovaný na virtuálních počítačích s Windows serverem, které jsou připojené ke spravované doméně Azure služba AD DS. U některých aplikací můžete nasadit víc serverů, na kterých je nainstalovaný konektor. Tato možnost nasazení poskytuje větší dostupnost a pomáhá zvládnout těžší ověřování.

Virtuální počítač, na kterém běží konektor Azure Proxy aplikací služby AD, musí být ve stejné nebo partnerské virtuální síti, ve které jste povolili Azure služba AD DS. Virtuální počítače, které pak hostují aplikace, které publikujete pomocí proxy aplikace, musí být nasazené taky na stejné službě Azure Virtual Network.

Pokud chcete vytvořit virtuální počítač pro konektor Azure Proxy aplikací služby AD, proveďte následující kroky:

1. [Vytvořte vlastní organizační jednotku](create-ou.md). Můžete delegovat oprávnění ke správě této vlastní organizační jednotky pro uživatele v rámci spravované domény Azure služba AD DS. Virtuální počítače pro Azure Proxy aplikací služby AD a, které spouštějí vaše aplikace, musí být součástí vlastní organizační jednotky, nikoli výchozí organizační jednotka *počítače AAD DC* .
1. [Doména se připojí k virtuálním počítačům][create-join-windows-vm], který spouští konektor Azure proxy aplikací služby AD, a těch, které spouštějí vaše aplikace, do spravované domény Azure služba AD DS. Vytvořte tyto účty počítačů ve vlastní organizační jednotce z předchozího kroku.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Stáhnout konektor Azure Proxy aplikací služby AD

Pomocí následujících kroků stáhněte konektor Azure Proxy aplikací služby AD. Instalační soubor, který jste stáhli, se zkopíruje do vašeho virtuálního počítače proxy aplikace v následující části.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí uživatelského účtu, který má ve službě Azure AD oprávnění *Enterprise Administrators* .
1. Vyhledejte a vyberte **Azure Active Directory** v horní části portálu a pak zvolte **podnikové aplikace**.
1. V nabídce na levé straně vyberte **proxy aplikace** . Pokud chcete vytvořit první konektor a Povolit proxy aplikací, vyberte odkaz pro **stažení konektoru**.
1. Na stránce pro stažení přijměte licenční podmínky a smlouvy o ochraně osobních údajů a pak vyberte **přijmout podmínky & stáhnout**.

    ![Stažení konektoru proxy serveru Aplikace Azure AD](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instalace a registrace konektoru Azure Proxy aplikací služby AD

Když je virtuální počítač připravený k použití jako konektor Azure Proxy aplikací služby AD, teď zkopírujte a spusťte instalační soubor stažený z Azure Portal.

1. Zkopírujte instalační soubor konektoru služby Azure Proxy aplikací služby AD do svého virtuálního počítače.
1. Spusťte instalační soubor, například *aplikaci aadapplicationproxyconnectorinstaller. exe*. Přijměte licenční podmínky pro software.
1. Během instalace budete vyzváni k registraci konektoru s proxy aplikací v adresáři služby Azure AD.
   * Zadejte přihlašovací údaje globálního správce v adresáři služby Azure AD. Přihlašovací údaje globálního správce Azure AD se můžou lišit od přihlašovacích údajů Azure na portálu.

        > [!NOTE]
        > Globální účet správce použitý k registraci konektoru musí patřit do stejného adresáře, ve kterém povolíte službu proxy aplikací.
        >
        > Pokud je například doména Azure AD *contoso.com*, globální správce by měl `admin@contoso.com` nebo jiný platný alias v této doméně.

   * Pokud je pro virtuální počítač, na který konektor instalujete, zapnutá konfigurace rozšířeného zabezpečení aplikace Internet Explorer, může být registrační obrazovka zablokovaná. Pokud chcete povolit přístup, postupujte podle pokynů v chybové zprávě nebo vypněte rozšířené zabezpečení aplikace Internet Explorer během procesu instalace.
   * Pokud se registrace konektoru nepovede, přečtěte si téma [řešení potíží s proxy aplikací](../active-directory/manage-apps/application-proxy-troubleshoot.md)
1. Na konci instalace se zobrazí Poznámka pro prostředí s odchozím proxy serverem. Pokud chcete, aby konektor Azure Proxy aplikací služby AD fungoval prostřednictvím odchozího proxy serveru, spusťte zadaný skript, například `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`.
1. Na stránce proxy aplikace v Azure Portal je nový konektor uveden se stavem *aktivní*, jak je znázorněno v následujícím příkladu:

    ![Nový konektor Azure Proxy aplikací služby AD zobrazený jako aktivní v Azure Portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Pokud chcete zajistit vysokou dostupnost pro aplikace, které ověřují prostřednictvím Proxy aplikací služby AD Azure, můžete konektory nainstalovat na víc virtuálních počítačů. Opakujte stejný postup uvedený v předchozí části a nainstalujte konektor na jiné servery připojené k spravované doméně Azure služba AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Povolení omezeného delegování založeného na prostředku Kerberos

Pokud chcete používat jednotné přihlašování k aplikacím pomocí integrovaného ověřování systému Windows (IWA), udělte konektorům Azure Proxy aplikací služby AD oprávnění k zosobnění uživatelů a posílání a přijímání tokenů vaším jménem. Pro udělení těchto oprávnění nakonfigurujete omezené delegování protokolu Kerberos (KCD) pro konektor pro přístup k prostředkům ve spravované doméně Azure služba AD DS. Protože nemáte oprávnění správce domény ve spravované doméně Azure služba AD DS, nejde na spravované doméně nakonfigurovat tradiční KCD na úrovni účtu. Místo toho použijte KCD podle prostředků.

Další informace najdete v tématu [Konfigurace omezeného delegování protokolu Kerberos (KCD) v Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Abyste mohli spouštět následující rutiny PowerShellu, musíte být přihlášeni k uživatelskému účtu, který je členem skupiny *Správci Azure AD DC* v TENANTOVI Azure AD.
>
> Účty počítačů pro virtuální počítač konektoru proxy aplikací a virtuální počítače aplikace musí být ve vlastní organizační jednotce, kde máte oprávnění ke konfiguraci KCD založených na prostředcích. KCD na základě prostředků nejde nakonfigurovat pro účet počítače na integrovaném kontejneru *AAD DC Computers* .

Použijte [příkaz Get-ADComputer][Get-ADComputer] k načtení nastavení pro počítač, na kterém je nainstalovaný konektor Azure proxy aplikací služby AD. Z virtuálního počítače pro správu připojeného k doméně a přihlášený jako uživatelský účet, který je členem skupiny *Azure AD DC Administrators* , spusťte následující rutiny.

Následující příklad načte informace o účtu počítače s názvem *appproxy.contoso.com*. Zadejte vlastní název počítače pro virtuální počítač Azure Proxy aplikací služby AD nakonfigurovaný v předchozích krocích.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.contoso.com
```

Pro každý aplikační server, na kterém běží aplikace za Azure Proxy aplikací služby AD použijte ke konfiguraci KCD na základě prostředků rutinu PowerShellu [set-ADComputer][Set-ADComputer] . V následujícím příkladu má konektor Azure Proxy aplikací služby AD udělena oprávnění k používání počítače s *AppServer.contoso.com* :

```powershell
Set-ADComputer appserver.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Pokud nasadíte více konektorů služby Azure Proxy aplikací služby AD, je nutné pro každou instanci konektoru nakonfigurovat KCD založenou na prostředku.

## <a name="next-steps"></a>Další kroky

S Azure Proxy aplikací služby AD integrovány se službou Azure služba AD DS můžete publikovat aplikace pro uživatele, kteří budou mít přístup. Další informace najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
