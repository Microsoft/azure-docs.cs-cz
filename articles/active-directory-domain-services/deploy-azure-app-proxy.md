---
title: 'Azure Active Directory Domain Services: Nasadit Azure Proxy aplikací služby AD | Microsoft Docs'
description: Použití Azure Proxy aplikací služby AD ve Azure Active Directory Domain Services spravovaných doménách
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 21693926bae681cf15d31dca06344dfa5d865e3b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613012"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Nasazení Azure Proxy aplikací služby AD ve spravované doméně Azure AD Domain Services
Proxy aplikace Azure Active Directory (AD) pomáhá podporovat vzdálené pracovní procesy publikováním místních aplikací, které jsou k dispozici prostřednictvím Internetu. S Azure AD Domain Services teď můžete nasouvat starší verze aplikací, které běží místně, do služby infrastruktury Azure. Tyto aplikace pak můžete publikovat pomocí Proxy aplikací služby AD Azure, abyste uživatelům ve vaší organizaci zajistili zabezpečený vzdálený přístup.

Pokud s Proxy aplikací služby AD Azure začínáte, přečtěte si další informace o této funkci v následujícím článku: [Jak zajistit zabezpečený vzdálený přístup k místním aplikacím](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K provedení úkolů uvedených v tomto článku budete potřebovat:

1. Platné **předplatné Azure**.
2. **Adresář služby Azure AD** – buď synchronizovaný s místním adresářem, nebo s adresářem jenom pro Cloud.
3. K použití Proxy aplikací služby AD Azure je nutná **licence Azure AD Premium** .
4. **Azure AD Domain Services** musí být povolený pro adresář služby Azure AD. Pokud jste to ještě neudělali, postupujte podle všech úkolů popsaných v [průvodci Začínáme](tutorial-create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Úloha 1 – povolení služby Azure Proxy aplikací služby AD pro adresář služby Azure AD
Provedením následujících kroků povolíte Azure Proxy aplikací služby AD pro adresář služby Azure AD.

1. Přihlaste se jako správce v [Azure Portal](https://portal.azure.com).

2. Kliknutím na **Azure Active Directory** otevřete přehled adresáře. Klikněte na **podnikové aplikace**.

3. Klikněte na **proxy aplikace**.

4. Chcete-li stáhnout konektor, klikněte na tlačítko **konektor** .

5. Na stránce pro stažení přijměte licenční podmínky a ujednání o ochraně osobních údajů a klikněte na tlačítko **Stáhnout** .

    ![Potvrdit stažení](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Úloha 2: zřízení Windows serveru připojeného k doméně pro nasazení konektoru služby Azure Proxy aplikací služby AD
Budete potřebovat virtuální počítače připojené k doméně, na kterých můžete nainstalovat konektor Azure Proxy aplikací služby AD. U některých aplikací se můžete rozhodnout zřídit několik serverů, na kterých je konektor nainstalovaný. Tato možnost nasazení poskytuje větší dostupnost a pomáhá zvládnout těžší ověřování.

Zajistěte, aby servery konektoru byly ve stejné virtuální síti (nebo připojené nebo připojené virtuální síti), ve které jste povolili Azure AD Domain Services spravované domény. Podobně servery hostující aplikace, které publikujete prostřednictvím proxy aplikace, musí být nainstalované ve stejné službě Azure Virtual Network.

Pokud chcete zřídit servery konektorů, postupujte podle pokynů uvedených v článku s názvem [připojení virtuálního počítače s Windows ke spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Úloha 3 – instalace a registrace konektoru Azure Proxy aplikací služby AD
Dřív jste zřídili virtuální počítač s Windows serverem a připojili jste ho ke spravované doméně. V této úloze nainstalujete konektor Azure Proxy aplikací služby AD do tohoto virtuálního počítače.

1. Zkopírujte instalační balíček konektoru do virtuálního počítače, do kterého instalujete konektor proxy webových aplikací služby Azure AD.

2. Na virtuálním počítači spusťte **aplikaci aadapplicationproxyconnectorinstaller. exe** . Přijměte licenční podmínky pro software.

    ![Přijmout podmínky pro instalaci](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Během instalace budete vyzváni k registraci konektoru s proxy aplikací adresáře služby Azure AD.
   * Zadejte svoje **přihlašovací údaje Správce aplikací Azure AD**. Váš tenant správce aplikace se může lišit od přihlašovacích údajů Microsoft Azure.
   * Účet správce použitý k registraci konektoru musí patřit do stejného adresáře, kde jste povolili službu proxy aplikací. Pokud je například doména tenanta contoso.com, správce by měl být admin@contoso.com nebo jakýkoli jiný platný alias v této doméně.
   * Pokud je pro server, na který konektor instalujete, zapnutá konfigurace rozšířeného zabezpečení aplikace Internet Explorer, může být registrační obrazovka zablokovaná. Pokud chcete přístup udělit, postupujte podle pokynů v chybové zprávě. Ujistěte se, že je rozšířené zabezpečení aplikace Internet Explorer vypnuto.
   * Pokud registrace konektoru selže, podívejte se do článku [Poradce při potížích s proxy aplikace](../active-directory/manage-apps/application-proxy-troubleshoot.md).

     ![Nainstalovaný konektor](./media/app-proxy/app-proxy-connector-installed.png)
4. Pokud chcete, aby konektor správně fungoval, spusťte Poradce při potížích s konektorem Azure Proxy aplikací služby AD. Po spuštění Poradce při potížích by se měla zobrazit zpráva o úspěšné sestavě.

    ![Úspěšnost Poradce při potížích](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Měl by se zobrazit nově nainstalovaný konektor uvedený na stránce proxy aplikace ve vašem adresáři služby Azure AD.

    ![V Azure Portal zobrazuje nainstalovaný konektor jako dostupný.](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Můžete se rozhodnout nainstalovat konektory na více serverech, abyste zajistili vysokou dostupnost pro ověřování aplikací publikovaných prostřednictvím Azure Proxy aplikací služby AD. Pomocí stejných kroků uvedených výše nainstalujte konektor na další servery připojené ke spravované doméně.
>
>

## <a name="next-steps"></a>Další kroky
Nastavili jste Azure Proxy aplikací služby AD a integruje ji s vaší Azure AD Domain Services spravovanou doménou.

* **Migrujte své aplikace na virtuální počítače Azure:** Své aplikace můžete napravit z místních serverů na virtuální počítače Azure připojené ke svojí spravované doméně. To vám pomůže se zbavit se nákladů na infrastrukturu pro spuštěné servery v místním prostředí.

* **Publikování aplikací s využitím Azure Proxy aplikací služby AD:** Publikujte aplikace běžící na virtuálních počítačích Azure pomocí Proxy aplikací služby AD Azure. Další informace najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Poznámka k nasazení – publikování aplikací IWA (integrovaných ověřování systému Windows) pomocí Azure Proxy aplikací služby AD
Povolte jednotné přihlašování k vašim aplikacím pomocí integrovaného ověřování systému Windows (IWA) tím, že udělíte konektory proxy aplikací oprávnění k zosobnění uživatelů a posílání a přijímání tokenů jejich jménem. Nakonfigurujte omezené delegování protokolu Kerberos (KCD), aby konektor udělil požadovaná oprávnění pro přístup k prostředkům ve spravované doméně. Pro zvýšení zabezpečení použijte mechanismus KCD založený na prostředku ve spravovaných doménách.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Povolení omezeného delegování protokolu Kerberos založeného na prostředku pro konektor Azure Proxy aplikací služby AD
Konektor proxy aplikací Azure by měl být nakonfigurovaný pro vynucené delegování protokolu Kerberos (KCD), aby mohl zosobnit uživatele ve spravované doméně. Ve spravované doméně Azure AD Domain Services nemáte oprávnění správce domény. Proto **na spravované doméně nejde nakonfigurovat tradiční KCD na úrovni účtu**.

Použijte KCD podle prostředků, jak je popsáno v tomto [článku](deploy-kcd.md).

> [!NOTE]
> Aby bylo možné spravovat spravovanou doménu pomocí rutin služby AD PowerShell, musíte být členem skupiny Správci AAD DC.
>
>

Pomocí rutiny PowerShellu Get-ADComputer načtěte nastavení počítače, na kterém je nainstalovaný konektor Azure Proxy aplikací služby AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso-proxy.contoso.com
```

Potom pomocí rutiny Set-ADComputer nastavte pro server prostředků KCD založenou na prostředku.
```powershell
Set-ADComputer contoso-resource.contoso.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Pokud jste ve spravované doméně nasadili více konektorů proxy aplikací, je třeba pro každou instanci konektoru nakonfigurovat KCD založenou na prostředku.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](tutorial-create-instance.md)
* [Konfigurace omezeného delegování protokolu Kerberos ve spravované doméně](deploy-kcd.md)
* [Přehled omezeného delegování protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
