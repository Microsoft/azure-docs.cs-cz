---
title: 'Azure Active Directory Domain Services: Nasazení Proxy aplikace služby Azure Active Directory | Microsoft Docs'
description: Použít proxy aplikace služby Azure AD na spravované domény Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 13141549b5b1b77f45dd6fa1c7560b9f9d0e9b72
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Nasazení Azure AD Application Proxy na spravované doméně služby Azure AD Domain Services
Proxy aplikace služby Azure Active Directory (AD) umožňuje podporují zaměstnanci na vzdálených pracovištích a publikování aplikací místní přístup přes internet. S Azure AD Domain Services můžete nyní navýšení a shift starší aplikace spuštěné místně službám infrastruktury Azure. Potom můžete publikovat tyto aplikace pomocí Azure AD Application Proxy poskytnout zabezpečený vzdálený přístup na uživatele ve vaší organizaci.

Pokud jste ještě Azure AD Application Proxy, další informace o této funkci se v následujícím článku: [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../active-directory/manage-apps/application-proxy.md).


## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:

1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Licence Azure AD Basic nebo Premium** je potřeba použít Azure AD Application Proxy.
4. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Úloha 1 – povolení služby Azure AD Application Proxy pro váš adresář Azure AD
Proveďte následující kroky k povolení Azure AD Application Proxy pro váš adresář Azure AD.

1. Přihlaste se jako správce v [portál Azure](http://portal.azure.com).

2. Klikněte na tlačítko **Azure Active Directory** se zprovoznit přehled adresáře. Klikněte na tlačítko **podnikové aplikace, které**.

    ![Vyberte adresář Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Klikněte na tlačítko **proxy aplikace**. Pokud nemáte předplatné Azure AD Basic nebo Azure AD Premium, zobrazí se možnost povolit zkušební verzi. Přepnutí **povolení Proxy aplikace?** k **povolit** a klikněte na tlačítko **Uložit**.

    ![Povolit Proxy aplikace](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Chcete-li stáhnout konektor, klikněte na tlačítko **konektor** tlačítko.

    ![Stažení konektoru](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na stránce pro stahování, přijměte licenční podmínky a smlouvy o ochraně osobních údajů a klikněte na **Stáhnout** tlačítko.

    ![Potvrďte stahování](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Úloha 2 – zřídit připojené k doméně Windows servery nasaďte konektor proxy aplikace služby Azure AD
Je nutné připojený k doméně systému Windows Server virtuálních počítačů na které můžete nainstalovat konektor proxy aplikace služby Azure AD. Některé aplikace můžete zřídit několik serverů, na kterých je nainstalován konektor. Tuto volbu nasazení vám dává větší dostupnosti a pomáhá zpracování větší zátěže pak ověřování.

Zřídit servery konektoru na stejné virtuální síti (nebo připojení/peered virtuální sítě), ve kterých jste povolili vaší spravované domény služby Azure AD Domain Services. Podobně servery, které hostují aplikace, které publikujete pomocí Proxy aplikace je potřeba nainstalovat na stejnou virtuální síť Azure.

Chcete-li zřídit na serverech konektoru, postupujte podle úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Úloha 3 – instalace a registrace konektoru Proxy aplikace Azure AD
Dříve zřízení virtuálního počítače s Windows serverem a připojený k spravované doméně. V této úloze nainstalujete konektor proxy aplikace služby Azure AD na tomto virtuálním počítači.

1. Zkopírujte instalační balíček konektoru na virtuální počítač, na který nainstalujete konektor Proxy Azure AD webových aplikací.

2. Spustit **AADApplicationProxyConnectorInstaller.exe** na virtuálním počítači. Přijměte licenční podmínky pro software.

    ![Přijměte podmínky pro instalaci](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Během instalace zobrazí se výzva k registraci konektoru k Proxy aplikace z adresáře služby Azure AD.
    * Zadejte vaše **přihlašovací údaje Azure AD globálního správce**. Klient globálního správce se může lišit od vašich přihlašovacích údajů ke službě Microsoft Azure.
    * Účet správce použitý k registraci konektoru musí patřit do stejného adresáře, kde jste povolili službu Proxy aplikace. Například, pokud doména klienta je contoso.com, Správce by měl být admin@contoso.com nebo jiný platný alias v této doméně.
    * Pokud konfigurace rozšířeného zabezpečení aplikace Internet Explorer je zapnutá pro server který konektor instalujete, mohou být blokovány na obrazovce pro registraci. Chcete-li povolit přístup, postupujte podle pokynů v chybové zprávě. Ujistěte se, že je rozšířené zabezpečení aplikace Internet Explorer vypnuto.
    * Pokud registrace konektoru selže, podívejte se do článku [Poradce při potížích s proxy aplikace](../active-directory/active-directory-application-proxy-troubleshoot.md).

    ![Nainstalovaný konektor](./media/app-proxy/app-proxy-connector-installed.png)
4. Aby konektor funguje správně, spusťte Azure AD Application Proxy Connector Poradce při potížích. Měli byste vidět úspěšné sestavy po spuštění Poradce při potížích.

    ![Poradce při potížích s úspěch](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Měli byste vidět nově nainstalovaný konektor uvedené na stránce proxy aplikace v adresáři služby Azure AD.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Můžete se rozhodnout postup instalace konektorů na několika serverech zaručit vysoká dostupnost pro ověřování aplikacích publikovaných prostřednictvím Azure AD Application Proxy. Proveďte stejné kroky uvedené výše, aby konektor nainstalovat na jiné servery připojené k vaší spravované domény.
>
>

## <a name="next-steps"></a>Další kroky
Máte nenastavili Proxy aplikace služby Azure AD a integraci s vaší spravované domény služby Azure AD Domain Services.

* **Migrovat aplikace na virtuálních počítačích Azure:** můžete navýšení a shift aplikace z místních serverů na virtuálních počítačích Azure připojené k vaší spravované domény. Díky tomu pomáhá vám zbavit infrastruktury nákladů na běžící servery místně.

* **Publikování aplikací pomocí proxy aplikace služby Azure AD:** publikování aplikací běžících na virtuálních počítačích Azure pomocí Azure AD Application Proxy. Další informace najdete v tématu [publikování aplikací pomocí proxy aplikace služby Azure AD](../active-directory/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Poznámka: nasazení - publikování integrované ověřování systému Windows (integrované ověřování systému Windows) aplikací pomocí proxy aplikace služby Azure AD
Povolte jednotné přihlašování pro vaše aplikace používá integrované ověřování systému Windows (IWA) udělení oprávnění konektory Proxy aplikace zosobňovat uživatele a odesílat a přijímat tokeny jejich jménem. Nakonfigurujte omezené delegování protokolu Kerberos (použitím KCD) pro konektor pro přidělení požadovaných oprávnění pro přístup k prostředkům na spravované domény. Pro zvýšení zabezpečení pomocí použitím KCD mechanismus založené na prostředcích spravované domény.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Povolit založené na prostředcích omezené delegování protokolu Kerberos pro konektor proxy aplikace služby Azure AD
Konektor Proxy aplikace služby Azure na by měl být nakonfigurovaný omezeného delegování protokolu Kerberos použitím (KCD), je možné zosobňovat uživatele spravované domény. Na spravované doméně služby Azure AD Domain Services nemáte oprávnění správce domény. Proto **tradiční použitím KCD úrovni účtu nelze konfigurovat ve spravované doméně**.

Použít použitím KCD založené na prostředcích, jak je popsáno v tomto [článku](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Musíte být členem skupiny 'správci AAD řadič domény, ke správě spravované doméně pomocí rutin prostředí AD PowerShell.
>
>

Použijte rutinu Get-ADComputer prostředí PowerShell k načtení nastavení pro počítač, na kterém je nainstalovaný konektor proxy aplikace služby Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Po tomto datu použijte rutinu Set-ADComputer nastavit založené na prostředcích použitím KCD server prostředku.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Pokud jste nasadili více konektorů Proxy aplikace na vaší spravované domény, musíte nakonfigurovat založené na prostředcích použitím KCD pro každou konektor instanci.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Nakonfigurovat omezené delegování protokolu Kerberos na spravované doméně](active-directory-ds-enable-kcd.md)
* [Omezené delegování přehled protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
