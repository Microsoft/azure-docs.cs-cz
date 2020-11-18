---
title: Integrace s Proxy aplikací služby AD na serveru NDES
titleSuffix: Azure Active Directory
description: Pokyny k nasazení Proxy aplikací služby Azure Active Directory k ochraně serveru NDES.
services: active-directory
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 0682115727068c928418d97fbf92ed32897c39d4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656747"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrace se službou Azure Proxy aplikací služby AD na serveru služby zápisu síťových zařízení (NDES)

Proxy aplikace Azure Active Directory (AD) umožňuje publikování aplikací v síti. Tyto aplikace jsou například weby služby SharePoint, webové aplikace Microsoft Outlook a další webové aplikace. Poskytuje taky zabezpečený přístup k uživatelům mimo vaši síť prostřednictvím Azure.

Pokud s Azure začínáte Proxy aplikací služby AD a chcete získat další informace, přečtěte si téma [vzdálený přístup k místním aplikacím prostřednictvím Azure proxy aplikací služby AD](application-proxy.md).

Azure Proxy aplikací služby AD je postavená na Azure. Nabízí obrovské množství šířky pásma sítě a serverové infrastruktury pro lepší ochranu před distribuovanými útoky na útoky DDOS (Denial of Service) a vynikající dostupností. Kromě toho není nutné otevírat Externí porty brány firewall v místní síti a není vyžadován žádný DMZ Server. Veškerý provoz pochází z příchozího. Úplný seznam odchozích portů najdete v tématu [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> Azure Proxy aplikací služby AD je funkce, která je dostupná jenom v případě, že používáte edice Premium nebo Basic sady Azure Active Directory. Další informace najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/). 
> Pokud máte licence pro sadu Enterprise Mobility Suite (EMS), máte nárok na použití tohoto řešení.
> Konektor Azure Proxy aplikací služby AD se nainstaluje jenom na Windows Server 2012 R2 nebo novější verzi. To je také požadavek serveru NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instalace a registrace konektoru na serveru NDES

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce aplikace adresáře, který používá proxy aplikace. Pokud je například doména tenanta contoso.com, správce by měl být admin@contoso.com v této doméně nebo jiný alias správce.
1. V pravém horním rohu vyberte své uživatelské jméno. Ověřte, že jste přihlášeni k adresáři, který používá proxy aplikace. Pokud potřebujete změnit adresáře, vyberte **Přepnout adresář** a zvolte adresář, který používá proxy aplikace.
1. V levém navigačním panelu vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **proxy aplikace**.
1. Vyberte **stáhnout službu konektoru**.

    ![Stažení služby konektoru, aby se zobrazila podmínka služby](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Přečtěte si podmínek služby. Až budete připraveni, vyberte **přijmout podmínky & stáhnout**.
1. Zkopírujte instalační soubor konektoru služby Azure Proxy aplikací služby AD na server NDES. 
   > Konektor můžete nainstalovat na libovolný server v podnikové síti s přístupem k NDES. Nemusíte ho instalovat na samotný server NDES.
1. Spusťte instalační soubor, například *AADApplicationProxyConnectorInstaller.exe*. Přijměte licenční podmínky pro software.
1. Během instalace budete vyzváni k registraci konektoru s proxy aplikací v adresáři služby Azure AD.
   * Zadejte přihlašovací údaje pro globálního správce nebo Správce aplikací ve vašem adresáři služby Azure AD. Přihlašovací údaje správce globálních nebo aplikací Azure AD se můžou lišit od přihlašovacích údajů Azure na portálu.

        > [!NOTE]
        > Globální účet správce nebo účet správce aplikací použitý k registraci konektoru musí patřit do stejného adresáře, ve kterém povolíte službu proxy aplikací.
        >
        > Pokud je například doména služby Azure AD *contoso.com*, měl by mít správce globální/aplikace `admin@contoso.com` v této doméně nebo jiný platný alias.

   * Pokud je pro server, na který konektor instalujete, zapnutá konfigurace rozšířeného zabezpečení aplikace Internet Explorer, může být registrační obrazovka zablokovaná. Pokud chcete povolit přístup, postupujte podle pokynů v chybové zprávě nebo vypněte rozšířené zabezpečení aplikace Internet Explorer během procesu instalace.
   * Pokud se registrace konektoru nepovede, přečtěte si téma [řešení potíží s proxy aplikací](application-proxy-troubleshoot.md)
1. Na konci instalace se zobrazí Poznámka pro prostředí s odchozím proxy serverem. Pokud chcete nakonfigurovat konektor Azure Proxy aplikací služby AD tak, aby fungoval přes odchozí proxy server, spusťte zadaný skript, například `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Na stránce proxy aplikace v Azure Portal je nový konektor uveden se stavem *aktivní*, jak je znázorněno v následujícím příkladu:

    ![Nový konektor Azure Proxy aplikací služby AD zobrazený jako aktivní v Azure Portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Pokud chcete zajistit vysokou dostupnost pro aplikace, které ověřují prostřednictvím Proxy aplikací služby AD Azure, můžete konektory nainstalovat na víc virtuálních počítačů. Opakujte stejný postup uvedený v předchozí části a nainstalujte konektor na jiné servery připojené k spravované doméně Azure služba AD DS.

1. Po úspěšné instalaci se vraťte na Azure Portal.

1. Vyberte **podnikové aplikace**.

   ![Ujistěte se, že máte na pravé strany.](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Vyberte **+ Nová aplikace** a pak vyberte místní **aplikace**. 

1. V části **Přidat vlastní místní aplikaci** nakonfigurujte následující pole:

   * **Název**: zadejte název aplikace.
   * **Interní adresa URL**: zadejte interní adresu URL nebo plně kvalifikovaný název domény serveru NDES, na kterém jste konektor nainstalovali.
   * **Předběžné ověření**: vyberte **průchozí**. Není možné použít žádnou formu předběžného ověřování. Protokol používaný pro žádosti o certifikát (SCEP) tuto možnost neposkytuje.
   * Zkopírujte zadanou **externí adresu URL** do schránky.

1. Vyberte **+ Přidat** a uložte svoji aplikaci.

1. Otestujte, jestli k serveru NDES máte přístup prostřednictvím proxy aplikace služby Azure AD, a to tak, že do prohlížeče vložíte odkaz, který jste zkopírovali v kroku 10. Měla by se zobrazit výchozí úvodní stránka služby IIS.

1. Jako konečný test přidejte *mscep.dll* cestu k existující adrese URL, kterou jste vložili v předchozím kroku:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Měla by se zobrazit **Chyba HTTP 403 – zakázaná** odpověď.

1. Změňte adresu URL služby NDES (prostřednictvím Microsoft Intune) na zařízení. Tato změna může být buď ve službě Microsoft Endpoint Configuration Manager, nebo v centru pro správu Microsoft Endpoint Manager.

   * V případě Configuration Manager přejděte k bodu registrace certifikátu a upravte adresu URL. Tato adresa URL je ta, která zařízení volají a prezentují své výzvy.
   * Pro samostatnou Intune buď upravte, nebo vytvořte novou zásadu SCEP a přidejte novou adresu URL.

## <a name="next-steps"></a>Další kroky

S Azure Proxy aplikací služby AD integrována se službou NDES, publikujte aplikace pro uživatele, kteří budou mít přístup. Další informace najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](./application-proxy-add-on-premises-application.md).