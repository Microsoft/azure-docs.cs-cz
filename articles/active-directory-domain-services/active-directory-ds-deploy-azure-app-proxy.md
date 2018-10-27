---
title: 'Azure Active Directory Domain Services: Nasazení služby Proxy aplikací Azure Active Directory | Dokumentace Microsoftu'
description: Použít Proxy aplikace služby Azure AD na spravovaných domén Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 96ef005f095e5ab774a88ec766080f647414a7ce
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155537"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Nasazení Proxy aplikací Azure AD ve spravované doméně Azure AD Domain Services
Proxy aplikací služby Azure Active Directory (AD) umožňuje podporu vzdálených pracovních procesů publikováním místních aplikací, aby byly přístupné přes internet. S Azure AD Domain Services můžete nyní lift and shift starší aplikace spuštěné v místním se službami infrastruktury Azure. Pak můžete publikovat tyto aplikace pomocí Azure AD Application Proxy, k zajištění bezpečného vzdáleného přístupu pro uživatele ve vaší organizaci.

Pokud se službou Azure AD Application Proxy teprve začínáte, přečtěte si informace o této funkci se v následujícím článku: [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete
K provádění úkolů uvedených v tomto článku, budete potřebovat:

1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Licence Azure AD Basic nebo Premium** je potřeba použít Azure AD Application Proxy.
4. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Úloha 1 – povolení služby Azure AD Application Proxy pro váš adresář Azure AD
Proveďte následující kroky, aby Azure AD Application Proxy pro váš adresář Azure AD.

1. Přihlaste se jako správce [webu Azure portal](http://portal.azure.com).

2. Klikněte na tlačítko **Azure Active Directory** zobrazíte přehledu adresáře. Klikněte na tlačítko **podnikové aplikace**.

    ![Vyberte adresář Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Klikněte na tlačítko **proxy aplikací**. Pokud nemáte předplatné Azure AD Premium nebo Azure AD Basic, zobrazí se možnost povolit zkušební verzi. Přepnout **povolit Proxy aplikací?** k **povolit** a klikněte na tlačítko **Uložit**.

    ![Povolení Proxy aplikace](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Chcete-li stáhnout konektor, klikněte na tlačítko **konektor** tlačítko.

    ![Stáhnout konektor](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na stránce pro stahování, přijměte licenční podmínky a smlouvy o ochraně osobních údajů a klikněte na tlačítko **Stáhnout** tlačítko.

    ![Potvrďte stažení](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Úloha 2 – zřízení připojených k doméně Windows serverů k nasazení konektoru Azure AD Application Proxy
Je třeba připojených k doméně systému Windows Server virtuálních počítačů na které můžete nainstalovat konektor Proxy aplikací Azure AD. U některých aplikací můžete zřídit několik serverů, na kterých je konektor nainstalovaný. Tato možnost nasazení vám poskytne větší dostupnost a vám zpracování větší zátěže pak vylepšeno ověřování.

Zřízení konektor servery ve stejné virtuální síti (nebo připojení/partnerské virtuální síti), ve které jste povolili vaší spravované doméně Azure AD Domain Services. Podobně hostitelské servery aplikací, které publikujete pomocí Proxy aplikací je potřeba nainstalovat na stejné virtuální síti Azure.

Ke zřízení servery konektoru, použijte úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Úloha 3 – instalace a registrace konektoru Proxy aplikace Azure AD
Dříve zřízení virtuálního počítače s Windows serverem a k ní připojili ke spravované doméně. V této úloze nainstalujete konektor Proxy aplikací Azure AD na tomto virtuálním počítači.

1. Zkopírujte instalační balíček konektoru k virtuálnímu počítači, na který nainstalujete konektor Proxy aplikací Azure AD Web.

2. Spustit **AADApplicationProxyConnectorInstaller.exe** na virtuálním počítači. Přijměte licenční podmínky pro software.

    ![Přijměte podmínky pro instalaci](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Při instalaci zobrazí se výzva k registraci konektoru Proxy aplikace vašeho adresáře Azure AD.
    * Zadejte vaše **přihlašovací údaje globálního správce Azure AD**. Klient globálního správce se může lišit od vašich přihlašovacích údajů ke službě Microsoft Azure.
    * Účet správce používá k registraci konektoru musí patřit do stejného adresáře, ve kterém jste povolili službu Proxy aplikací. Například pokud doména tenanta je contoso.com, Správce by měl být admin@contoso.com nebo jakýkoli jiný platný alias v této doméně.
    * Pokud konfigurace rozšířeného zabezpečení aplikace Internet Explorer je vypnuté pro server kam instalujete konektor, může být registrační obrazovka zablokovaná. Povolit přístup, postupujte podle pokynů v chybové zprávě. Ujistěte se, že je rozšířené zabezpečení aplikace Internet Explorer vypnuto.
    * Pokud registrace konektoru selže, podívejte se do článku [Poradce při potížích s proxy aplikace](../active-directory/manage-apps/application-proxy-troubleshoot.md).

    ![Nainstalovaný konektor](./media/app-proxy/app-proxy-connector-installed.png)
4. Aby konektor funguje správně, spustit Azure AD Application Proxy Connector Poradce při potížích. Po spuštění Poradce při potížích s by měla zobrazit zpráva o úspěšné.

    ![Poradce při potížích s úspěch](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Měli byste vidět nově nainstalovaný konektor najdete na stránce proxy aplikace v adresáři služby Azure AD.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Si můžou nainstalovat konektory na více serverů pro zajištění vysoké dostupnosti pro ověřování aplikace publikované prostřednictvím Proxy aplikací služby Azure AD. Proveďte stejné kroky uvedené výše, aby konektor nainstalovat na servery připojené k vaší spravované doméně.
>
>

## <a name="next-steps"></a>Další kroky
Nastavení Proxy aplikací služby Azure AD a integrovaná s vaší spravované doméně Azure AD Domain Services.

* **Migrace aplikací na virtuálních počítačích Azure:** můžete lift and shift aplikací z místních serverů do virtuálních počítačů Azure připojené k vaší spravované domény. To pomáhá vám zbavit náklady na infrastrukturu serverů v místním.

* **Publikování aplikací pomocí Proxy aplikací Azure AD:** publikovat aplikace běžící na virtuálních počítačů Azure pomocí Azure AD Application Proxy. Další informace najdete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Poznámka: nasazení pomocí Azure AD Application Proxy aplikace publikovat IWA (integrované ověřování Windows)
Povolte jednotné přihlašování pro vaše aplikace pomocí integrovaného ověřování Windows (IWA) tak, že udělíte oprávnění konektory Proxy aplikací zosobňovat uživatele a odesílat a přijímat tokeny jejich jménem. Pro konektor pro přidělení požadovaných oprávnění pro přístup k prostředkům ve spravované doméně nakonfigurujte omezené delegování protokolu Kerberos (KCD). Použijte mechanismus založené na prostředcích KCD na spravované domény pro zvýšení zabezpečení.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Povolit založené na prostředcích omezené delegování protokolu Kerberos pro konektor Proxy aplikací Azure AD
Konektor Proxy aplikací Azure musí být nakonfigurovaný pro omezené delegování protokolu Kerberos (KCD), takže je možné zosobňovat uživatele ve spravované doméně. Na spravované doméně služby Azure AD Domain Services nemáte oprávnění správce domény. Proto **tradiční KCD úroveň účtu nelze konfigurovat ve spravované doméně**.

Použít KCD založené na prostředcích, jak je popsáno v tomto [článku](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Musíte být členem skupiny "Správci AAD DC" Správa spravované domény pomocí rutin Powershellu pro AD.
>
>

Použijte rutinu Powershellu Get-ADComputer načíst nastavení pro počítače, na kterém je nainstalovaný konektor Proxy aplikací Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Po tomto datu použijte rutinu Set-ADComputer nastavení založené na prostředcích KCD pro prostředek serveru.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Pokud jste nasadili více konektorů Proxy aplikací ve vaší spravované doméně, musíte nakonfigurovat KCD založené na prostředcích pro všechny tyto instance konektoru.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Konfigurace omezeného delegování protokolu Kerberos ve spravované doméně](active-directory-ds-enable-kcd.md)
* [Omezené delegování přehled protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
