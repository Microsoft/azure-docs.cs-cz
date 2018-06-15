---
title: Prostředí služby App Service ILB integrovat Azure Application Gateway
description: Návod k integraci aplikace v ILB App Service Environment s aplikační brány
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
ms.openlocfilehash: 31aea1d19ed6da856bb5fc634a919819513cb6b2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30833580"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Prostředí služby App Service ILB integrovat Azure Application Gateway #

[App Service Environment](./intro.md) nasazení služby Azure App Service v podsíti virtuální sítě Azure zákazníka. Dá se nasadit pomocí veřejných nebo privátních koncový bod pro přístup k aplikaci. Nasazení služby App Service Environment s privátní koncového bodu (to znamená, k interní pro vyrovnávání zatížení) se nazývá ILB App Service Environment.  

Webová aplikace brány firewall nápovědy zabezpečení webových aplikací zkontrolováním příchozí webové přenosy blokování SQL příspěvky; skriptování mezi servery, malwaru nahrávání & aplikace DDoS a jiným útokům. Je také zkontroluje, zda obsahuje odpovědi ze serveru back endové webové pro prevenci ztráty dat (DLP). Zařízení firewall webových aplikací můžete získat z Azure marketplace, nebo můžete použít [Azure Application Gateway][appgw].

Azure Application Gateway je virtuální zařízení, které poskytuje Vyrovnávání zatížení vrstvy 7, snižování zátěže protokolu SSL a ochranu brány firewall (firewall webových aplikací) webové aplikace. Můžete ho naslouchat na veřejné IP adresy a trasy přenosem do vaší aplikace koncového bodu. Následující informace popisuje, jak integrovat aplikace v ILB App Service Environment bránu firewall webových aplikací nakonfigurované aplikace.  

Integrace aplikační brány s ILB App Service Environment se na úrovni aplikace. Při konfiguraci aplikační brány s ILB App Service Environment, provádíte ho pro konkrétní aplikace v ILB App Service Environment. Tento postup umožňuje hostování zabezpečené víceklientské aplikace v jednom ILB App Service Environment.  

![Aplikační brána odkazující na aplikaci na ILB App Service Environment][1]

Tento návod vám ukáže, jak:

* Vytvoření služby Azure Application Gateway.
* Nakonfigurujte aplikační bránu tak, aby odkazoval na aplikace v ILB App Service Environment.
* Konfigurace aplikace přijmout vlastní název domény.
* Upravte veřejný název hostitele DNS, který odkazuje na aplikační brány.

## <a name="prerequisites"></a>Požadavky

Chcete-li integrovat aplikační brány s ILB App Service Environment, je třeba:

* Služby App Service Environment ILB.
* Aplikace spuštěné v ILB App Service Environment.
* Názvu internetové domény směrovatelné pro použití s vaší aplikace v ILB App Service Environment.
* ILB adresu, která používá ILB App Service Environment. Tyto informace jsou na portálu služby App Service Environment pod **nastavení** > **IP adresy**:

    ![Příklad seznamu IP adres používaných ILB App Service Environment][9]
    
* Veřejný název DNS, který se používá později tak, aby odkazoval na aplikační brány. 

Podrobnosti o tom, jak vytvořit ILB App Service Environment najdete v tématu [vytváření a používání služby ILB App Service Environment][ilbase].

Tento článek předpokládá, že chcete aplikační brány ve stejné Azure virtuální síti, kde je nasazená služba App Service Environment. Než začnete k vytvoření aplikační brány, vybrat nebo vytvořit podsíť, která se bude používat k hostování brány. 

Měli byste použít podsíť, která není jednu s názvem GatewaySubnet. Když vložíte Aplikační brána v GatewaySubnet, budete moci vytvořit bránu virtuální sítě později. 

Brány také nelze vložit do podsítě, která používá ILB App Service Environment. Služba App Service Environment je jediné, co může být v této podsíti.

## <a name="configuration-steps"></a>Kroky konfigurace ##

1. V portálu Azure přejděte do **nový** > **sítě** > **Application Gateway**.

2. V **Základy** oblasti:

   a. Pro **název**, zadejte název služby Application Gateway.

   b. Pro **vrstvy**, vyberte **firewall webových aplikací**.

   c. Pro **předplatné**, vyberte stejné předplatné, které využívá virtuální sítě služby App Service Environment.

   d. Pro **skupiny prostředků**, vytvořte nebo vyberte skupinu prostředků.

   e. Pro **umístění**, vyberte umístění virtuální sítě služby App Service Environment.

   ![Základní informace o vytvoření nové aplikační brány][2]

3. V **nastavení** oblasti:

   a. Pro **virtuální síť**, vyberte virtuální sítě služby App Service Environment.

   b. Pro **podsíť**, vyberte podsíť, kde aplikační brány musí být nasazena. Nepoužívejte GatewaySubnet, protože nebude možné vytvoření brány VPN.

   c. Pro **IP adres jako typu**, vyberte **veřejné**.

   d. Pro **veřejnou IP adresu**, vyberte veřejnou IP adresu. Pokud nemáte, vytvořte ho.

   e. Pro **protokol**, vyberte **HTTP** nebo **HTTPS**. Pokud konfigurujete pro protokol HTTPS, musíte zadat certifikát PFX.

   f. Pro **brány firewall webových aplikací**, můžete povolit bránu firewall a také nastavit pro buď **detekce** nebo **prevence** podle svých potřeb.

   ![Nové nastavení vytvoření aplikační brány][3]
    
4. V **Souhrn** , zkontrolujte nastavení a vyberte **OK**. Application Gateway může trvat o něco víc než 30 minut na dokončení instalace.  

5. Po dokončení instalace Application Gateway, přejděte na portál Application Gateway. Vyberte **fond back-end**. Přidáte adresu ILB pro ILB App Service Environment.

   ![Nakonfigurujte fond back-end][4]

6. Po dokončení procesu konfigurace back-end fondu, vyberte **testy stavu**. Vytvořte test stavu pro název domény, který chcete použít pro vaši aplikaci. 

   ![Konfigurace sond stavu][5]
    
7. Po dokončení procesu konfigurace vaší sondy stavu, vyberte **nastavení HTTP**. Upravit stávající nastavení, vyberte **testu pomocí vlastního**a vyberte test, který jste nakonfigurovali.

   ![Konfigurace nastavení protokolu HTTP][6]
    
8. Přejděte do služby Application Gateway **přehled** části a zkopírujte veřejnou IP adresu, která používá aplikační brány. Nastavit tuto IP adresu jako záznamu A pro název domény vaší aplikace, nebo použijte název DNS pro tuto adresu v záznamu CNAME. Je snazší vyberte veřejnou IP adresu a zkopírujte jej z uživatelského rozhraní veřejnou IP adresu než zkopírovat na odkaz ve službě Application Gateway **přehled** části. 

   ![Portál Application Gateway][7]

9. Nastavte vlastní název domény pro vaši aplikaci ve službě ILB App Service Environment. Přejděte do vaší aplikace na portálu a v části **nastavení**, vyberte **vlastní domény**.

   ![Nastavit vlastní název domény v aplikaci][8]

Informace o nastavení vlastní názvy domén pro webové aplikace v článku [nastavení vlastní názvy domén pro vaši webovou aplikaci][custom-domain]. Ale pro aplikace v ILB App Service Environment, není k dispozici žádné ověření na názvu domény. Vzhledem k tomu, že jste vlastníkem DNS, který spravuje aplikace koncových bodů, které můžete vložit všechno zde. Vlastní název domény, které přidáte v takovém případě nemusí být v DNS, ale stále nutné nakonfigurovat s vaší aplikací. 

Po dokončení instalace a mít povoleno krátkou dobu pro změny DNS rozšíření, dostanete pomocí názvu vlastní domény, který jste vytvořili vaší aplikace. 


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
