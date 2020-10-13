---
title: Integrace s Application Gateway
description: Naučte se, jak integrovat aplikaci do interního nástroje App Service Environment pomocí Application Gateway v tomto komplexním podrobném návodu.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962073"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrace služby App Service Environment s interním nástrojem pro vyrovnávání zatížení se službou Azure Application Gateway #

[App Service Environment](./intro.md) je nasazení Azure App Service v podsíti virtuální sítě Azure zákazníka. Dá se nasadit s veřejným nebo soukromým koncovým bodem pro přístup k aplikaci. Nasazení App Service Environment s privátním koncovým bodem (to znamená interní nástroj pro vyrovnávání zatížení) se označuje jako App Service Environment interního nástroje.  

Brány firewall webových aplikací vám pomůžou zabezpečit webové aplikace tím, že kontrolují příchozí webový provoz, aby blokovali injektáže SQL, skriptování mezi weby, nahrávající malware & DDoS aplikací a další útoky. Také kontroluje odpovědi z back-endové webové servery na ochranu před únikem informací (DLP). Zařízení WAF můžete získat z webu Azure Marketplace nebo můžete použít [azure Application Gateway][appgw].

Azure Application Gateway je virtuální zařízení, které poskytuje ochranu vyrovnávání zatížení vrstvy 7, snižování zátěže TLS/SSL a bránu firewall webových aplikací (WAF). Může naslouchat na veřejné IP adrese a směrovat provoz do koncového bodu aplikace. Následující informace popisují, jak integrovat WAF konfigurovanou Aplikační bránu do aplikace v interního nástroje App Service Environment.  

Integrace služby Application Gateway s App Service Environment interního nástroje je na úrovni aplikace. Když konfigurujete Aplikační bránu pomocí App Service Environment interního nástroje, provádíte ji pro konkrétní aplikace v App Service Environment interního nástroje. Tato technika umožňuje hostování zabezpečených víceklientské aplikací v jednom interního nástroje App Service Environment.  

![Aplikační brána ukazující na aplikaci na interního nástroje App Service Environment][1]

Tento návod vám ukáže, jak:

* Vytvořte Application Gateway Azure.
* Nakonfigurujte Application Gateway tak, aby odkazoval na aplikaci v App Service Environment interního nástroje.
* Nakonfigurujte svou aplikaci tak, aby dodržovala vlastní název domény.
* Upravte název veřejného hostitele DNS, který odkazuje na vaši Aplikační bránu.

## <a name="prerequisites"></a>Požadavky

K integraci svého Application Gateway s App Service Environment interního nástroje potřebujete:

* INTERNÍHO nástroje App Service Environment.
* Aplikace spuštěná v interního nástroje App Service Environment.
* Název internetové IP domény, který se má používat s vaší aplikací v interního nástroje App Service Environment.
* Adresa interního nástroje, kterou používá vaše interního nástroje App Service Environment. Tyto informace jsou na portálu App Service Environment v části **Nastavení**  >  **IP adresy**:

    ![Příklad seznamu IP adres, které používá App Service Environment interního nástroje][9]
    
* Veřejný název DNS, který se později používá k nasměrování na Application Gateway. 

Podrobnosti o tom, jak vytvořit App Service Environment interního nástroje, najdete v tématu [Vytvoření a použití App Service Environment interního nástroje][ilbase].

V tomto článku se předpokládá, že chcete Application Gateway ve stejné službě Azure Virtual Network, ve které je nasazený App Service Environment. Než začnete vytvářet Application Gateway, vyberte nebo vytvořte podsíť, kterou budete používat k hostování brány. 

Měli byste použít podsíť, která není ta s názvem GatewaySubnet. Pokud vložíte Application Gateway do GatewaySubnet, nebudete moct později vytvořit bránu virtuální sítě. 

Bránu nemůžete umístit ani do podsítě, kterou používá interního nástroje App Service Environment. App Service Environment je jediná věc, kterou může být v této podsíti.

## <a name="configuration-steps"></a>Postup konfigurace ##

1. V Azure Portal přejít na **Nový**  >  **síťový**  >  **Application Gateway**.

2. V oblasti **základy** :

   a. Do pole **název**zadejte název Application Gateway.

   b. Jako **úroveň**vyberte **WAF**.

   c. V poli **předplatné**vyberte stejné předplatné, které používá App Service Environment virtuální síť.

   d. V části **Skupina prostředků**vytvořte nebo vyberte skupinu prostředků.

   e. V poli **umístění**vyberte umístění virtuální sítě App Service Environment.

   ![Základy vytváření nových Application Gateway][2]

3. V oblasti **Nastavení** :

   a. Pro **virtuální síť**vyberte App Service Environment virtuální síť.

   b. V poli **podsíť**vyberte podsíť, ve které musí být nasazen Application Gateway. Nepoužívejte GatewaySubnet, protože tím zabráníte vytváření bran VPN.

   c. Jako **typ IP adresy**vyberte **veřejné**.

   d. V případě **veřejné IP adresy**vyberte veřejnou IP adresu. Pokud ho ještě nemáte, vytvořte ho hned teď.

   e. V případě **protokolu**vyberte **http** nebo **https**. Pokud konfigurujete pro protokol HTTPS, je nutné zadat certifikát PFX.

   f. V případě **brány firewall webových aplikací**můžete bránu firewall povolit a také ji nastavit pro **detekci** nebo **prevenci** , jak se to hodí.

   ![Nové nastavení vytváření Application Gateway][3]
    
4. V části **Souhrn** zkontrolujte nastavení a vyberte **OK**. Dokončení instalace může trvat déle než 30 minut. Application Gateway  

5. Po dokončení instalace Application Gateway přejít na portál Application Gateway. Vyberte **back-end fond**. Přidejte interního nástroje adresu pro svůj interního nástroje App Service Environment.

   ![Konfigurovat back-end fond][4]

6. Po dokončení procesu konfigurace fondu back-end vyberte **sondy stavu**. Vytvořte sondu stavu pro název domény, který chcete použít pro vaši aplikaci. 

   ![Konfigurace sond stavu][5]
    
7. Po dokončení procesu konfigurace sond stavu vyberte **Nastavení http**. Upravte stávající nastavení, vyberte **použít vlastní test paměti**a vyberte test, který jste nakonfigurovali.

   ![Konfigurace nastavení HTTP][6]
    
8. V části **přehled** Application Gateway a ZKOPÍRUJTE veřejnou IP adresu, kterou používá Application Gateway. Nastavte tuto IP adresu jako záznam pro název domény aplikace nebo použijte název DNS pro tuto adresu v záznamu CNAME. Je snazší vybrat veřejnou IP adresu a zkopírovat ji z uživatelského rozhraní veřejné IP adresy místo toho, abyste ji zkopírovali z odkazu v části **přehled** Application Gateway. 

   ![Portál Application Gateway][7]

9. Nastavte název vlastní domény vaší aplikace ve vašem interního nástroje App Service Environment. Na portálu přejdete na aplikaci a v části **Nastavení**vyberte **vlastní domény**.

   ![Nastavení vlastního názvu domény v aplikaci][8]

K dispozici jsou informace o nastavení vlastních názvů domén pro webové aplikace v článku [Nastavení vlastních názvů domén pro vaši webovou aplikaci][custom-domain]. U aplikace v interního nástroje App Service Environment ale není k dispozici žádné ověření názvu domény. Vzhledem k tomu, že jste vlastníkem DNS, který spravuje koncové body aplikace, můžete v něm libovolně umístit. Vlastní název domény, který přidáváte v tomto případě, nemusí být ve službě DNS, ale stále musí být nakonfigurovaný s vaší aplikací. 

Až se instalace dokončí a máte povolenou krátkou dobu, než se změny DNS rozšíří, můžete k aplikaci přistupovat pomocí vlastního názvu domény, který jste vytvořili. 


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
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md