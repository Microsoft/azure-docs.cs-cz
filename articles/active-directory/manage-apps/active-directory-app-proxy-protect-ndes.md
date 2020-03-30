---
title: Integrace s proxy aplikace služby AD na serveru NDES
titleSuffix: Azure Active Directory
description: Pokyny k nasazení proxy aplikace služby Azure Active Directory k ochraně serveru NDES.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032254"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrace s proxy aplikací Azure AD na serveru NDES (Network Device Enrollment Service)

Proxy aplikace Azure Active Directory (AD) umožňuje publikovat aplikace uvnitř sítě. Jedná se o aplikace, jako jsou weby služby SharePoint, Microsoft Outlook Web App a další webové aplikace. Poskytuje také zabezpečený přístup uživatelům mimo vaši síť prostřednictvím Azure.

Pokud jste s proxy aplikacemi Azure AD tezcí, které chcete získat další informace, přečtěte si další informace o [vzdáleném přístupu k místním aplikacím prostřednictvím proxy aplikace Azure AD](application-proxy.md).

Azure AD Proxy aplikace je postavena na Azure. Poskytuje obrovské množství šířky pásma sítě a serverové infrastruktury pro lepší ochranu před distribuovanými útoky denial-of-service (DDOS) a vynikající dostupností. Kromě toho není nutné otevírat externí porty brány firewall do místní sítě a není vyžadován žádný server DMZ. Veškerý provoz pochází příchozí. Úplný seznam odchozích portů najdete [v tématu Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

> Azure AD Proxy aplikace je funkce, která je k dispozici jenom v případě, že používáte Premium nebo základní edice Služby Azure Active Directory. Další informace najdete v [tématu Ceny služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Pokud máte licence Enterprise Mobility Suite (EMS), máte nárok na použití tohoto řešení.
> Konektor proxy aplikací Azure AD se nainstaluje jenom na Windows Server 2012 R2 nebo novějším. To je také požadavek serveru NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instalace a registrace konektoru na serveru NDES

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako správce aplikace adresáře, který používá proxy aplikace. Pokud je například doména klienta contoso.com, měl by být admin@contoso.com správce nebo jakýkoli jiný alias správce v této doméně.
1. V pravém horním rohu vyberte své uživatelské jméno. Ověřte, zda jste přihlášeni k adresáři, který používá proxy aplikace. Pokud potřebujete změnit adresáře, vyberte **Přepnout adresář** a zvolte adresář, který používá proxy aplikace.
1. V levém navigačním panelu vyberte **Azure Active Directory**.
1. V části **Manage**vyberte **Proxy aplikace**.
1. Vyberte **službu Stáhnout konektor .**

    ![Stáhněte si konektor služby vidět podmínky služby](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Přečtěte si smluvní podmínky. Až budete připraveni, vyberte **Přijmout podmínky & stáhnout**.
1. Zkopírujte instalační soubor konektoru proxy aplikace Azure AD na server NDES. 
   > Konektor můžete nainstalovat na libovolný server v rámci podnikové sítě s přístupem k NDES. Nemusíte jej instalovat na samotný server NDES.
1. Spusťte instalační soubor, například *AADApplicationProxyConnectorInstaller.exe*. Přijměte licenční podmínky softwaru.
1. Během instalace budete vyzváni k registraci konektoru s proxy aplikací v adresáři Azure AD.
   * Zadejte pověření pro globálního správce nebo správce aplikace v adresáři Azure AD. Přihlašovací údaje globálního správce azure ad nebo správce aplikace se mohou lišit od vašich přihlašovacích údajů Azure na portálu.

        > [!NOTE]
        > Globální účet správce nebo účet správce aplikace používaný k registraci konektoru musí patřit do stejného adresáře, kde povolíte službu Proxy aplikace.
        >
        > Například pokud je *doména*Azure AD contoso.com , `admin@contoso.com` globální/aplikační správce by měl být nebo jiný platný alias v této doméně.

   * Pokud je pro server, na který instalujete konektor, zapnutá konfigurace rozšířeného zabezpečení aplikace Internet Explorer, může být registrační obrazovka zablokována. Chcete-li povolit přístup, postupujte podle pokynů v chybové zprávě nebo během procesu instalace vypněte rozšířené zabezpečení aplikace Internet Explorer.
   * Pokud se registrace konektoru nezdaří, [přečtěte si článek Poradce při potížích s proxy aplikací](application-proxy-troubleshoot.md).
1. Na konci instalace se zobrazí poznámka pro prostředí s odchozíproxy server. Chcete-li nakonfigurovat konektor proxy aplikace Azure AD tak, aby fungoval `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`přes odchozí proxy server, spusťte zadaný skript, například .
1. Na stránce Proxy aplikace na webu Azure portal je nový konektor uveden se stavem *Active*, jak je znázorněno v následujícím příkladu:

    ![Nový konektor proxy aplikací Azure AD zobrazený jako aktivní na webu Azure Portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Chcete-li zajistit vysokou dostupnost pro aplikace ověřování prostřednictvím proxy aplikace Azure AD, můžete nainstalovat konektory na více virtuálních počítačích. Opakováním stejných kroků uvedených v předchozí části nainstalujte konektor na jiné servery spojené se spravovanou doménou Azure AD DS.

1. Po úspěšné instalaci se vraťte na portál Azure.

1. Vyberte **podnikové aplikace**.

   ![zajistit, abyste zapojili správné zúčastněné strany](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Vyberte **+Nová aplikace**a pak **vyberte Místní aplikace**. 

1. V poli **Přidat vlastní místní aplikaci**nakonfigurujte následující pole:

   * **Název**: Zadejte název aplikace.
   * **Interní adresa URL**: Zadejte interní adresu URL/FQDN serveru NDES, na kterém jste nainstalovali konektor.
   * **Předběžné ověření**: Vyberte **možnost Předávací .** Není možné použít žádnou formu předběžného ověření. Protokol používaný pro žádosti o certifikát (SCEP) takovou možnost neposkytuje.
   * Zkopírujte zadanou **externí adresu URL** do schránky.

1. Výběrem **možnosti +Přidat** aplikaci uložíte.

1. Otestujte, zda můžete přistupovat k serveru NDES prostřednictvím proxy aplikace Azure AD vložením odkazu, který jste zkopírovali v kroku 10 do prohlížeče. Měla by se zobrazit výchozí úvodní stránka iis.

1. Jako poslední test přidejte cestu *mscep.dll* k existující adrese URL, kterou jste vložili v předchozím kroku:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Měla by se zobrazit **chyba PROTOKOLU HTTP 403 – zakázaná** odpověď.

1. Změňte adresu URL NDES poskytnutou (přes Microsoft Intune) na zařízení, tato změna může být buď v Centru konfigurace koncového bodu Microsoft nebo v Intune Cloud.

   * V centru konfigurace přejděte do registračního bodu certifikátu (CRP) a upravte adresu URL. Tato adresa URL je to, co zařízení volají a představují svou výzvu.
   * Pro Intune Cloud Only, známé také jako Intune Standalone, buď upravte nebo vytvořte nové zásady SCEP a přidejte novou adresu URL.

## <a name="next-steps"></a>Další kroky

Díky proxy aplikace Azure AD integrované s NDES publikujte aplikace, ke kterým mají uživatelé přístup. Další informace naleznete v [tématu publikování aplikací pomocí proxy aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).
