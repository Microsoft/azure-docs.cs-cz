---
title: Proxy aplikace Azure AD - Začínáme nainstalovat konektor | Microsoft Docs
description: Zapněte Proxy aplikace na portálu Azure a nainstalujte konektory pro reverzní proxy server.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 58bcb3cbdf389393beb58a9f50b3e77aa2b3aae8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Začínáme s Proxy aplikace a nainstalujte konektor
Tento článek vás provede postupem, který umožňuje povolit proxy aplikace u cloudového adresáře služby Microsoft Azure AD.

Pokud si nejste ještě vědět, zabezpečení a produktivitu výhody přináší Proxy aplikace pro vaši organizaci, další informace o [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

## <a name="application-proxy-prerequisites"></a>Požadavky na proxy aplikace
Předtím, než budete moct povolit a používat služby proxy aplikace, musíte mít:

* [Základní nebo prémiové předplatné služby Microsoft Azure AD](../active-directory-whatis.md) a adresář služby Azure AD, u kterého jste globální správce.
* Serveru se systémem Windows Server 2012 R2 nebo 2016, na který nainstalujete konektor Proxy aplikace. Server musí být možné se připojit k službám Proxy aplikace v cloudu a místní aplikace, které jsou publikování.
  * Pro jednotné přihlašování pro vaše publikované aplikace pomocí omezené delegování Kerberos tento počítač by měl být-připojené do domény ve stejné doméně AD jako aplikace, které jsou publikování. Informace najdete v tématu [použitím KCD pro jednotné přihlašování pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md).

Pokud vaše organizace používá proxy servery pro připojení k Internetu, přečtěte si [pracují se stávající místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md) podrobnosti o tom, jak je nakonfigurovat před zahájením práce s Proxy aplikace.

## <a name="open-your-ports"></a>Otevřít vaše porty

Při přípravě svého prostředí pro Azure AD Application Proxy, musíte nejprve povolit komunikaci datových center Azure. Pokud stojí v cestě brána firewall, zajistěte, aby byla otevřená a konektor mohl proxy aplikace posílat požadavky prostřednictvím protokolu HTTPS (TCP).

1. Otevřít následující porty pro **odchozí** provozu:

   | Číslo portu | Jak se používají |
   | --- | --- |
   | 80 | Stahování odvolání certifikátu seznamy (CRL) při ověřování certifikátu SSL |
   | 443 | Všechny odchozí komunikace se službou Proxy aplikace |

   Pokud brána firewall vynucuje přenos v závislosti na zdroji uživatelů, otevřete tyto porty pro přenos ze služby Windows, které běží jako síťové služby.

   > [!IMPORTANT]
   > Změna portu požadavky pro konektor verze 1.5.132.0 a novějších. Pokud máte starší verzi konektoru, musíte také povolit následující porty kromě 80 a 443: 5671, 8080, 9090-9091 9350 9352, 10100 – 10120.
   >
   >Informace o aktualizaci vašeho konektory na nejnovější verzi najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md#automatic-updates).

2. Pokud brána firewall nebo proxy server umožňuje vytvoření seznamu povolených DNS, můžete seznam povolených adres připojení k msappproxy.net a servicebus.windows.net. Pokud ne, musíte povolit přístup k [rozsahy IP Datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které jsou aktualizovány každý týden.

3. Společnost Microsoft používá čtyři adresy k ověření certifikátů. Povolit přístup k následujícím adresám URL, pokud jste tak dosud neučinili pro ostatní produkty:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Vaše konektor potřebuje přístup k login.windows.net a login.microsoftonline.com pro proces registrace.

5. Použití [Azure AD Application proxy serveru konektoru porty nástroj pro testování](https://aadap-portcheck.connectorporttest.msappproxy.net/) k ověření, že vaše konektoru může kontaktovat službu Proxy aplikace. Minimálně Ujistěte se, že oblasti střed USA a oblasti nejblíže k vám mají všechny zelené značky zaškrtnutí. Kromě toho další zelené značky zaškrtnutí znamená větší odolnost proti chybám.

## <a name="install-and-register-a-connector"></a>Instalace a registrace konektoru
1. Přihlaste se jako správce v [portál Azure](https://portal.azure.com/).
2. Aktuální adresář se zobrazí pod své uživatelské jméno v pravém horním rohu. Pokud potřebujete změnit adresáře, vyberte tuto ikonu.
3. Přejděte na **Azure Active Directory** > **Proxy aplikace**.

   ![Přejděte na Proxy aplikace](./media/application-proxy-enable/app_proxy_navigate.png)

4. Vyberte **stáhnout konektor**.

   ![Stáhnout konektor](./media/application-proxy-enable/download_connector.png)

5. Spusťte aplikaci **AADApplicationProxyConnectorInstaller.exe** na serveru, který jste připravili podle uvedených požadavků.
6. Nainstalujte podle pokynů instalačního průvodce. Během instalace zobrazí se výzva k registraci konektoru k Proxy aplikace klienta služby Azure AD.

   * Zadejte vaše přihlašovací údaje globálního správce pro službu Azure AD. Klient globálního správce se může lišit od vašich přihlašovacích údajů ke službě Microsoft Azure.
   * Ujistěte se, že správce, který zaregistruje konektor, je ve stejném adresáři, ve kterým jste povolili službu proxy aplikací. Pokud je třeba doména tenanta contoso.com, správce by měl být admin@contoso.com nebo jakýkoli jiný alias v této doméně.
   * Pokud **konfigurace rozšířeného zabezpečení aplikace Internet Explorer** je nastaven na **na** na serveru, kam instalujete konektor, nemusíte vidět na obrazovce pro registraci. Chcete-li získat přístup, postupujte podle pokynů v chybové zprávě. Ujistěte se, že je rozšířené zabezpečení aplikace Internet Explorer vypnuto.

V zájmu vyšší dostupnosti byste měli nasadit aspoň dva konektory. Každý konektor musí být zaregistrovaný samostatně.

## <a name="test-that-the-connector-installed-correctly"></a>Test, který je konektor nainstalovaný správně

Můžete potvrdit, že nový konektor nainstalován správně kontrolou ji buď na portálu Azure nebo na serveru. 

Na portálu Azure, přihlaste se ke klientovi a přejděte do **Azure Active Directory** > **Proxy aplikace**. Zobrazí všechny konektory a konektor skupiny na této stránce. Vyberte konektor, zobrazí se jeho podrobnosti nebo přesunout do skupiny různých konektor. 

Na serveru zkontrolujte seznam aktivních služeb pro konektor a aktualizační konektor. Tyto dvě služby by měl spustit okamžitě, ale pokud ne, je zapnout: 

   * **Microsoft AAD Application Proxy Connector** umožňuje propojení

   * **Microsoft AAD Application Proxy Connector Updater** je služba pro automatickou aktualizaci. Aktualizační kontroluje nové verze konektoru a aktualizuje konektoru podle potřeby.

   ![Služby konektoru proxy aplikace – snímek obrazovky](./media/application-proxy-enable/app_proxy_services.png)

Informace o konektory a jak se nejnovější informace najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).


## <a name="next-steps"></a>Další postup
Nyní jste připraveni k [publikování aplikací pomocí proxy aplikace](application-proxy-publish-azure-portal.md).

Pokud máte aplikace, které jsou na samostatné sítě nebo jiné umístění, použijte konektor skupiny různých konektory uspořádat do logických jednotek. Další informace získáte v článku [Práce s konektory proxy aplikací](application-proxy-connector-groups.md).
