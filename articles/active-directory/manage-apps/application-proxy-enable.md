---
title: Proxy aplikací Azure AD – Začínáme nainstalovat konektor | Dokumentace Microsoftu
description: Zapněte Proxy aplikace na webu Azure Portal a nainstalujte konektory pro reverzní proxy server.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 59ca9ca7711904fe7882aac4878bd62c597645d8
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034962"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Začínáme s Proxy aplikace a nainstalujte konektor
Tento článek vás provede postupem, který umožňuje povolit proxy aplikace u cloudového adresáře služby Microsoft Azure AD.

Pokud si nejste vědomi výhody zabezpečení a produktivitu Proxy aplikací přináší pro vaši organizaci, přečtěte si více o [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

## <a name="application-proxy-prerequisites"></a>Požadavky na proxy aplikace
Předtím, než budete moct povolit a používat služby proxy aplikace, musíte mít:

* [Základní nebo prémiové předplatné služby Microsoft Azure AD](../fundamentals/active-directory-whatis.md) a adresář služby Azure AD, u kterého jste globální správce.
* Serveru se systémem Windows Server 2012 R2 nebo 2016, na který nainstalujete konektor Proxy aplikace. Server musí být schopný se připojit k Proxy aplikace služby v cloudu a místních aplikací, které publikujete.
  * Pro jednotné přihlašování pro aplikace publikované pomocí omezeného delegování protokolu Kerberos tento počítač by měl být připojených k doméně ve stejné doméně AD jako aplikace, které publikujete. Informace najdete v tématu [KCD pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).
* Protokol TLS 1.2 používá podkladový operační systém. Chcete-li změnit na TLS 1.2, postupujte podle kroků v [povolení protokolu TLS 1.2](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites#enable-tls-12-for-azure-ad-connect). Když je obsah pro Azure AD Connect, tento postup je stejný pro všechny klienty .NET.

Pokud vaše organizace používá proxy servery pro připojení k Internetu, přečtěte si [práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md) podrobnosti o tom, jak je nakonfigurovat před zahájením práce s Proxy aplikací.

## <a name="open-your-ports"></a>Otevřete váš porty

Příprava prostředí pro Azure AD Application Proxy, musíte nejprve povolit komunikaci s datovými centry Azure. Pokud stojí v cestě brána firewall, zajistěte, aby byla otevřená a konektor mohl proxy aplikace posílat požadavky prostřednictvím protokolu HTTPS (TCP).

1. Otevřít následující porty **odchozí** provozu:

   | Číslo portu | Jak se používá |
   | --- | --- |
   | 80 | Stahuje se odvolání certifikátu seznamy (CRL) při ověřování certifikátu SSL |
   | 443 | Veškerá odchozí komunikace se službou Proxy aplikací |

   Pokud vaše brána firewall vynucuje přenos v závislosti na zdroji uživatelů, otevřete tyto porty pro provoz služby Windows, na kterých běží jako síťové služby.

   > [!IMPORTANT]
   > Tabulka odráží požadavcích na porty u verze konektoru 1.5.132.0 a novější. Pokud máte starší verzi konektoru, je také potřeba povolit následující porty 80 a 443: 5671 8080, 9350 9090-9091 9352, 10100 – 10120.
   >
   >Informace o aktualizaci na nejnovější verzi konektoru najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md#automatic-updates).

2. Pokud vaše brána firewall nebo proxy server umožňuje přidávání na seznam povolených DNS, můžete seznam povolených připojení k msappproxy.net a servicebus.windows.net. Pokud ne, je potřeba povolit přístup k [rozsahy IP adres Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují každý týden.

3. Společnost Microsoft používá k ověření certifikátů čtyři adresy. Povolit přístup k následujícím adresám URL, pokud jste tak dosud neučinili pro ostatní produkty:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. Váš konektor potřebuje přístup k login.windows.net a login.microsoftonline.com pro proces registrace.


## <a name="install-and-register-a-connector"></a>Instalace a registrace konektoru
1. Přihlaste se jako správce [webu Azure portal](https://portal.azure.com/).
2. V rámci své uživatelské jméno v pravém horním rohu se zobrazí váš aktuální adresář. Pokud potřebujete změnit adresář, vybráním této ikony.
3. Přejděte na **Azure Active Directory** > **Proxy aplikací**.

   ![Přejděte na Proxy aplikací](./media/application-proxy-enable/app_proxy_navigate.png)

4. Vyberte **stáhnout konektor**.

   ![Stáhnout konektor](./media/application-proxy-enable/download_connector.png)

5. Spusťte aplikaci **AADApplicationProxyConnectorInstaller.exe** na serveru, který jste připravili podle uvedených požadavků.
6. Nainstalujte podle pokynů instalačního průvodce. Při instalaci zobrazí se výzva k registraci konektoru proxy aplikace vašeho tenanta Azure AD.

   * Zadejte vaše přihlašovací údaje globálního správce pro službu Azure AD. Klient globálního správce se může lišit od vašich přihlašovacích údajů ke službě Microsoft Azure.
   * Ujistěte se, že správce, který zaregistruje konektor, je ve stejném adresáři, ve kterým jste povolili službu proxy aplikací. Pokud je třeba doména tenanta contoso.com, správce by měl být admin@contoso.com nebo jakýkoli jiný alias v této doméně.
   * Pokud **konfigurace rozšířeného zabezpečení Internet Exploreru** je nastavena na **na** na serveru, kam instalujete konektor, nezobrazí na obrazovce registrace. Chcete-li získat přístup, postupujte podle pokynů v chybové zprávě. Ujistěte se, že je rozšířené zabezpečení aplikace Internet Explorer vypnuto.

V zájmu vyšší dostupnosti byste měli nasadit aspoň dva konektory. Každý konektor musí být zaregistrovaný samostatně.

## <a name="test-that-the-connector-installed-correctly"></a>Test, který je konektor nainstalovaný správně

Můžete potvrdit, že nový konektor nainstalován správně tak, že zkontrolujete jeho buď na portálu Azure portal nebo na serveru. 

Na webu Azure Portal, přihlaste se k vašemu tenantovi a přejděte do **Azure Active Directory** > **Proxy aplikací**. Všechny konektory a skupiny konektorů se zobrazí na této stránce. Vyberte konektor, zobrazí se jeho podrobnosti nebo přesunout do skupiny různých konektoru. 

Na serveru najdete v seznamu aktivních služeb konektoru a connector updater. Tyto dvě služby by měla začít spouštět okamžitě, ale pokud ne, je zapnout: 

   * **Microsoft AAD Application Proxy Connector** umožňuje propojení

   * **Microsoft AAD Application Proxy Connector Updater** je služba pro automatickou aktualizaci. Aktualizátoru kontroluje nové verze konektoru a aktualizuje konektoru podle potřeby.

   ![Služby konektoru proxy aplikace – snímek obrazovky](./media/application-proxy-enable/app_proxy_services.png)

Informace o konektory a způsob jejich nejnovější informace najdete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).


## <a name="next-steps"></a>Další postup
Nyní jste připraveni k [publikování aplikací pomocí proxy aplikace](application-proxy-publish-azure-portal.md).

Pokud máte aplikace, které jsou v různých sítích nebo jiné umístění, pomocí skupin konektorů uspořádat různé konektory do logických jednotek. Další informace získáte v článku [Práce s konektory proxy aplikací](application-proxy-connector-groups.md).
