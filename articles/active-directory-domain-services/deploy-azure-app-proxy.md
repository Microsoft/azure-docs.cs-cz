---
title: Nasazení proxy aplikace Azure AD pro službu Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak zajistit zabezpečený přístup k interním aplikacím pro vzdálené pracovníky nasazením a konfigurací proxy aplikací Služby Azure Active Directory ve spravované doméně služby Azure Active Directory Domain Services.
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
ms.openlocfilehash: c6e4e6a45fbbeab64184d8ae4b0684ba055d7735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613981"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Nasazení proxy aplikací Azure AD pro zabezpečený přístup k interním aplikacím ve spravované doméně služby Azure AD Domain Services

Se službou Azure AD Domain Services (Azure AD DS) můžete zvedat a přesouvat starší aplikace spuštěné místně do Azure. Proxy aplikace Azure Active Directory (AD) pak pomáhá podporovat vzdálené pracovníky tím, že bezpečně publikuje tyto interní aplikace součástí spravované domény Azure AD DS, aby k nim bylo možné přistupovat přes internet.

Pokud tečujete s proxy aplikací Azure AD a chcete se dozvědět víc, přečtěte si další informace najdete v [tématu Jak zajistit zabezpečený vzdálený přístup k interním aplikacím](../active-directory/manage-apps/application-proxy.md).

Tento článek ukazuje, jak vytvořit a nakonfigurovat konektor proxy aplikací Azure AD, který poskytuje zabezpečený přístup k aplikacím ve spravované doméně Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
    * K použití proxy aplikace Azure AD Premium je **vyžadována licence Azure AD** Premium.
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Vytvoření virtuálního virtuálního ms pro Windows přilehlého k doméně

Chcete-li směrovat provoz do aplikací spuštěných ve vašem prostředí, nainstalujte komponentu konektoru proxy aplikací Azure AD. Tento konektor proxy aplikací Azure AD musí být nainstalovaný na virtuálních počítačích (VM) Windows Serveru, který je spojený se spravovanou doménou Azure AD DS. U některých aplikací můžete nasadit více serverů, z nichž každý má nainstalovaný konektor. Tato možnost nasazení poskytuje větší dostupnost a pomáhá zpracovávat těžší ověřování zatížení.

Virtuální počítač, který spouští konektor proxy aplikací Azure AD, musí být ve stejné nebo partnerské virtuální síti, ve které jste povolili Azure AD DS. Virtuální počítače, které pak hostují aplikace, které publikujete pomocí proxy aplikace, musí být také nasazeny ve stejné virtuální síti Azure.

Pokud chcete vytvořit virtuální počítač pro konektor proxy aplikací Azure AD, proveďte následující kroky:

1. [Vytvořte vlastní ou .](create-ou.md) Oprávnění ke správě této vlastní individuální oužiny můžete delegovat uživatelům ve spravované doméně Azure AD DS. Virtuální počítače pro proxy aplikace Azure AD a které spouštějí vaše aplikace, musí být součástí vlastní ou, nikoli *výchozí* ou.
1. [Připojení k virtuálním počítačům][create-join-windows-vm], a to jak ten, který spouští konektor proxy aplikací Azure AD, a ty, které spouštějí vaše aplikace, do spravované domény Azure AD DS. Vytvořte tyto účty počítačů ve vlastní hlavní výuce z předchozího kroku.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Stažení konektoru proxy aplikace Azure AD

Proveďte následující kroky ke stažení konektoru proxy aplikací Azure AD. Instalační soubor, který stáhnete, se zkopíruje do virtuálního počítače proxy aplikace v další části.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí uživatelského účtu, který má oprávnění *správce rozlehlé sítě* ve službě Azure AD.
1. Vyhledejte a vyberte **službu Azure Active Directory** v horní části portálu a pak zvolte **Podnikové aplikace**.
1. Z nabídky na levé straně vyberte **Proxy aplikace.** Chcete-li vytvořit první konektor a povolit proxy aplikace, vyberte odkaz pro **stažení konektoru**.
1. Na stránce pro stažení přijměte licenční podmínky a smlouvu o ochraně osobních údajů a vyberte **možnost Přijmout podmínky & stažení**.

    ![Stažení konektoru proxy aplikace Azure AD](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instalace a registrace konektoru proxy aplikací Azure AD

S virtuálním počítačem připraveným k použití jako konektor proxy aplikací Azure AD teď zkopírujte a spusťte instalační soubor stažený z webu Azure Portal.

1. Zkopírujte instalační soubor konektoru proxy aplikace Azure AD do virtuálního počítače.
1. Spusťte instalační soubor, například *AADApplicationProxyConnectorInstaller.exe*. Přijměte licenční podmínky softwaru.
1. Během instalace budete vyzváni k registraci konektoru s proxy aplikací v adresáři Azure AD.
   * Zadejte přihlašovací údaje pro globálního správce v adresáři Azure AD. Přihlašovací údaje globálního správce azure ad se mohou lišit od vašich přihlašovacích údajů Azure na portálu

        > [!NOTE]
        > Globální účet správce používaný k registraci konektoru musí patřit do stejného adresáře, kde povolíte službu Proxy aplikace.
        >
        > Například pokud je *aaddscontoso.com*doména Azure AD `admin@aaddscontoso.com` , globální správce by měl být nebo jiný platný alias v této doméně.

   * Pokud je pro virtuální počítač, do kterého instalujete konektor, zapnutá konfigurace rozšířeného zabezpečení aplikace Internet Explorer, může být obrazovka registrace blokována. Chcete-li povolit přístup, postupujte podle pokynů v chybové zprávě nebo během procesu instalace vypněte rozšířené zabezpečení aplikace Internet Explorer.
   * Pokud se registrace konektoru nezdaří, [přečtěte si článek Poradce při potížích s proxy aplikací](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. Na konci instalace se zobrazí poznámka pro prostředí s odchozíproxy server. Chcete-li nakonfigurovat konektor proxy aplikace Azure AD tak, aby fungoval `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`přes odchozí proxy server, spusťte zadaný skript, například .
1. Na stránce Proxy aplikace na webu Azure portal je nový konektor uveden se stavem *Active*, jak je znázorněno v následujícím příkladu:

    ![Nový konektor proxy aplikací Azure AD zobrazený jako aktivní na webu Azure Portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Chcete-li zajistit vysokou dostupnost pro aplikace ověřování prostřednictvím proxy aplikace Azure AD, můžete nainstalovat konektory na více virtuálních počítačích. Opakováním stejných kroků uvedených v předchozí části nainstalujte konektor na jiné servery spojené se spravovanou doménou Azure AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Povolení delegování omezeného prostředků založeného na prostředku

Pokud chcete používat jednotné přihlašování k aplikacím pomocí integrovaného ověřování systému Windows (IWA), udělte konektorům proxy aplikací Azure AD oprávnění k vydávání se za uživatele a odesílání a přijímání tokenů jejich jménem. Chcete-li udělit tato oprávnění, nakonfigurujete delegování omezeného protokolu Kerberos (KCD) pro konektor pro přístup k prostředkům ve spravované doméně Azure AD DS. Vzhledem k tomu, že nemáte oprávnění správce domény ve spravované doméně Azure AD DS, tradiční kcd na úrovni účtu nelze nakonfigurovat na spravované doméně. Místo toho použijte kcd na základě prostředků.

Další informace naleznete [v tématu Configure Kerberos constrained delegation (KCD) in Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Abyste spouštěli následující rutiny Prostředí PowerShell, musíte být přihlášeni k uživatelskému účtu, který je členem *skupiny správců Azure AD DC* ve vašem tenantovi Azure AD.
>
> Účty počítače pro váš virtuální počítač proxy aplikace a virtuální počítače aplikace musí být ve vlastní hlavní výtěr, kde máte oprávnění ke konfiguraci kcd založené na prostředcích. KCD na základě prostředků nelze nakonfigurovat pro účet počítače ve vestavěném kontejneru *Počítače řadiče domény AAD.*

Pomocí [get-ADComputer][Get-ADComputer] načíst nastavení pro počítač, ve kterém je nainstalován konektor proxy aplikace Azure AD. Z vašeho virtuálního počítače pro správu připojovaný k doméně a přihlášen jako uživatelský účet, který je členem *skupiny správců Azure AD DC,* spusťte následující rutiny.

Následující příklad získá informace o účtu počítače s názvem *appproxy.aaddscontoso.com*. Zadejte svůj vlastní název počítače pro virtuální počítač proxy aplikace Azure AD nakonfigurované v předchozích krocích.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Pro každý aplikační server, který spouští aplikace za proxy aplikace Azure AD pomocí rutiny [Set-ADComputer][Set-ADComputer] PowerShell ke konfiguraci kcd založené na prostředcích. V následujícím příkladu je konektoru proxy aplikací Azure AD udělena oprávnění k používání *počítače appserver.aaddscontoso.com:*

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Pokud nasadíte více konektorů proxy aplikací Azure AD, musíte nakonfigurovat kcd na základě prostředků pro každou instanci konektoru.

## <a name="next-steps"></a>Další kroky

Díky proxy aplikací Azure AD integrované s Azure AD DS publikujte aplikace, ke kterým mají uživatelé přístup. Další informace naleznete v [tématu publikování aplikací pomocí proxy aplikace Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
