---
title: Integrace ILB App Service Environment ve službě Azure Application Gateway
description: Návod, jak integrovat aplikace ve vašem prostředí ILB App Service pomocí služby Application Gateway
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.openlocfilehash: 749b554b8cf99ce849e0e3ab7b3a9478d8705e54
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422990"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrace ILB App Service Environment ve službě Azure Application Gateway #

[Služby App Service Environment](./intro.md) je nasazení služby Azure App Service v podsíti virtuální sítě Azure zákazníka. Je možné nasadit s veřejných nebo privátních koncový bod pro přístup k aplikaci. Nasazení služby App Service Environment s privátní koncového bodu (to znamená, že interní nástroj pro vyrovnávání zatížení) je volána služby ILB App Service Environment.  

Webové aplikace tím, že příchozí webový provoz blokovat nahrávání malwaru SQL injektáže, skriptování napříč weby a útoky DDoS a další útoky, brány firewall webových aplikací pomáhají zabezpečit. Také prohlédne odpovědi z back endové webové servery pro prevence ztráty dat (DLP). Zařízením WAF můžete získat na webu Azure Marketplace nebo můžete použít [Azure Application Gateway][appgw].

Azure Application Gateway je virtuální zařízení, která poskytuje Vyrovnávání zatížení vrstvy 7, snižování zátěže protokolu SSL a ochrana brány firewall (WAF) webové aplikace. Může naslouchat veřejné IP adresy a provoz můžete směrovat do vašeho koncového bodu aplikace. Následující informace popisují postupy integrace nakonfigurovaná waf služby application gateway s aplikací ve službě ILB App Service Environment.  

Integrace application gateway s ILB App Service Environment je na úrovni aplikace. Při konfiguraci aplikační brány s ILB App Service Environment děláte ho pro konkrétní aplikace v ILB App Service Environment. Tato technika umožňuje hostování zabezpečených víceklientských aplikací v jednom prostředí ILB App Service.  

![Služba Application gateway, přejděte do aplikace na služby ILB App Service Environment][1]

Tento návod vám ukáže, jak:

* Vytvoření služby Azure Application Gateway.
* Nakonfigurujte aplikační bránu, aby odkazoval na aplikaci ve službě ILB App Service Environment.
* Nakonfigurujte svoji aplikaci případném dalším sdílení dodržovat vlastní název domény.
* Upravte veřejný název hostitele DNS, který odkazuje na vaše brána application gateway.

## <a name="prerequisites"></a>Požadavky

Chcete-li integrovat vaše brána Application Gateway s ILB App Service Environment, potřebujete:

* Služby ILB App Service Environment.
* Aplikace běžící v ILB App Service Environment.
* Názvu internetové domény směrovatelné pro použití s vaší aplikací ve službě App Service Environment ILB.
* Adresa ILB, která používá služby ILB App Service Environment. Tyto informace jsou na portálu služby App Service Environment v rámci **nastavení** > **IP adresy**:

    ![Příklad seznamu IP adresy používané služby ILB App Service Environment][9]
    
* Veřejný název DNS, který se použije v pozdější tak, aby odkazoval na vaše brána Application Gateway. 

Podrobnosti o tom, jak vytvořit služby ILB App Service Environment najdete v tématu [vytváření a používání služby ILB App Service Environment][ilbase].

Tento článek předpokládá, že chcete službu Application Gateway ve stejné virtuální síti Azure nasazená služba App Service Environment. Než začnete k vytvoření služby Application Gateway, vyberte nebo vytvořte podsíť, kterou použijete k hostování brány. 

Měli byste použít podsíť, která není jednu s názvem GatewaySubnet. Když vložíte do podsítě brány Application Gateway, nebudete moct vytvořit bránu virtuální sítě později. 

Brána také nelze vložit do podsítě, která používá služby ILB App Service Environment. App Service Environment je jediné, co může být v této podsíti.

## <a name="configuration-steps"></a>Postup konfigurace ##

1. Na webu Azure Portal, přejděte na **nový** > **sítě** > **Application Gateway**.

1. V **Základy** oblasti:

   a. Pro **název**, zadejte název služby Application Gateway.

   b. Pro **úroveň**vyberte **WAF**.

   c. Pro **předplatné**, vyberte předplatným, které používá virtuální sítě služby App Service Environment.

   d. Pro **skupiny prostředků**, vytvořte nebo vyberte skupinu prostředků.

   e. Pro **umístění**, vyberte umístění ve virtuální síti službu App Service Environment.

   ![Základní informace o vytvoření nové službě Application Gateway][2]

1. V **nastavení** oblasti:

   a. Pro **virtuální síť**, výběr virtuální sítě služby App Service Environment.

   b. Pro **podsítě**, vyberte podsíť, ve kterém musí být nasazený aplikační brány. Nepoužívejte GatewaySubnet, protože bude bránit vytvoření brány VPN.

   c. Pro **IP adres jako typu**vyberte **veřejné**.

   d. Pro **veřejnou IP adresu**, vyberte veřejnou IP adresu. Pokud ho nemáte, vytvořte nějaký.

   e. Pro **protokol**vyberte **HTTP** nebo **HTTPS**. Pokud konfigurujete pro protokol HTTPS, musíte zadat certifikát PFX.

   f. Pro **firewallu webových aplikací**, můžete povolit bránu firewall a také nastavit pro buď **detekce** nebo **ochrany před únikem informací** podle svých potřeb.

   ![Nová nastavení pro vytvoření aplikační brány][3]
    
1. V **Souhrn** , zkontrolujte nastavení a vyberte **OK**. Vaše brána Application Gateway může trvat o něco více než 30 minut na dokončení instalace.  

1. Po dokončení instalace vaše brána Application Gateway, přejděte na portálu služby Application Gateway. Vyberte **back-endový fond**. Přidání ILB adresy pro ILB App Service Environment.

   ![Konfigurace back-endový fond][4]

1. Po dokončení procesu konfigurace back endového fondu, vyberte **sondy stavu**. Vytvořte sondu stavu pro název domény, který chcete použít pro vaši aplikaci. 

   ![Konfigurace sond stavu][5]
    
1. Po dokončení procesu konfigurace vašeho sond stavu, vyberte **nastavení HTTP**. Upravit stávající nastavení, vyberte **použít vlastní sondy**a vyberte test, který jste nakonfigurovali.

   ![Konfigurace nastavení protokolu HTTP][6]
    
1. Přejděte ke službě Application Gateway **přehled** části a zkopírujte veřejnou IP adresu, která používá vaše brána Application Gateway. Nastavit tuto IP adresu jako záznam A pro název domény vaší aplikace, nebo použijte název DNS pro tuto adresu v záznamu CNAME. Je snazší vyberte veřejnou IP adresu a zkopírujte z uživatelského rozhraní pro veřejnou IP adresu spíše než zkopírovat z odkazu ve službě Application Gateway **přehled** oddílu. 

   ![Portál Application Gateway][7]

1. Nastavte vlastní název domény pro vaši aplikaci do služby ILB App Service Environment. Přejděte do vaší aplikace na portálu a v části **nastavení**vyberte **vlastní domény**.

   ![Nastavit vlastní název domény na aplikaci][8]

Nejsou k dispozici informace o nastavení vlastních názvů domén pro webové aplikace v následujícím článku [nastavení vlastních názvů domén pro vaši webovou aplikaci][custom-domain]. Ale pro aplikaci služby ILB App Service Environment, není k dispozici žádné ověření názvu domény. Protože DNS, který spravuje koncových bodů aplikace, které vlastníte, můžete umístit cokoli, co chcete, aby v něm. Vlastní název domény, který v tomto případě přidáte nemusí být ve službě DNS, ale přesto musí nakonfigurovat s vaší aplikací. 

Po dokončení instalace a jste povolili krátkém čase se změny DNS rozšíří, můžete přístup k aplikaci s použitím vlastního názvu domény, který jste vytvořili. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
