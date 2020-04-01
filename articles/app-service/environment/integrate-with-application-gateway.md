---
title: Integrace s Application Gateway
description: V tomto komplexním návodu se dozvíte, jak integrovat aplikaci do prostředí služby ILB App Service s aplikační bránou.
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e4838597c50898748eb4b33e81ff22eaeea37b30
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476889"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrace služby App Service Environment s interním nástrojem pro vyrovnávání zatížení se službou Azure Application Gateway #

[Prostředí služby App Service](./intro.md) je nasazení služby Azure App Service v podsíti virtuální sítě Azure zákazníka. Lze nasadit s veřejným nebo soukromým koncovým bodem pro přístup k aplikacím. Nasazení prostředí služby App Service s privátním koncovým bodem (tj. interním vydělávačem zatížení) se nazývá prostředí služby ILB App Service Environment.  

Brány firewall webových aplikací pomáhají zabezpečit webové aplikace kontrolou příchozího webového provozu, aby zablokovaly injektáže SQL, skriptování mezi weby, nahrávání malwaru & aplikace DDoS a další útoky. Také kontroluje odpovědi z back-endových webových serverů pro prevenci ztráty dat (DLP). Zařízení WAF můžete získat z webu Azure Marketplace nebo můžete použít [Azure Application Gateway][appgw].

Aplikační brána Azure je virtuální zařízení, které poskytuje vyrovnávání zatížení vrstvy 7, snižování zátěže TLS/SSL a ochranu brány firewall webových aplikací (WAF). Může naslouchat na veřejné IP adrese a směrovat provoz do koncového bodu aplikace. Následující informace popisují, jak integrovat aplikační bránu konfigurovanou WAF s aplikací v prostředí služby ILB App Service.  

Integrace aplikační brány s prostředím služby ILB App Service Environment je na úrovni aplikace. Když nakonfigurujete aplikační bránu s prostředím služby ILB App Service, děláte to pro konkrétní aplikace v prostředí služby ILB App Service. Tato technika umožňuje hostování zabezpečených víceklientských aplikací v jednom prostředí služby ILB App Service.  

![Brána aplikace směřující na aplikaci v prostředí služby ILB App Service][1]

Tento návod vám ukáže, jak:

* Vytvořte aplikační bránu Azure.
* Nakonfigurujte aplikační bránu tak, aby ukazovala na aplikaci v prostředí služby ILB App Service.
* Nakonfigurujte aplikaci tak, aby byla ctít vlastní název domény.
* Upravte název veřejného hostitele DNS, který odkazuje na bránu aplikace.

## <a name="prerequisites"></a>Požadavky

Chcete-li integrovat aplikační bránu s prostředím služby ILB App Service, potřebujete:

* Prostředí služby aplikace ILB.
* Aplikace spuštěná v prostředí služby ILB App Service.
* Název domény směrovatelné na internetu, který se má použít s vaší aplikací v prostředí služby ILB App Service.
* Adresa ILB, kterou používá vaše prostředí služby ILB App Service. Tyto informace jsou na portálu Prostředí služby App Service v části **Nastavení** > **IP adres**:

    ![Příklad seznamu IP adres používaných prostředím služby ILB App Service Environment][9]
    
* Veřejný název DNS, který se později použije k zobrazení zobrazení podle aplikace. 

Podrobnosti o tom, jak vytvořit prostředí služby ILB App Service, naleznete v [tématu Vytváření a používání prostředí služby ILB App Service Environment][ilbase].

Tento článek předpokládá, že chcete aplikační brány ve stejné virtuální síti Azure, kde se nasadí prostředí služby App Service. Než začnete vytvářet aplikační bránu, vyberte nebo vytvořte podsíť, kterou budete používat k hostování brány. 

Měli byste použít podsíť, která není ta s názvem GatewaySubnet. Pokud vložíte aplikační bránu do GatewaySubnet, nebudete moci později vytvořit bránu virtuální sítě. 

Bránu také nelze umístit do podsítě, kterou používá prostředí služby ILB App Service. Prostředí služby App Service je jediná věc, která může být v této podsíti.

## <a name="configuration-steps"></a>Postup konfigurace ##

1. Na webu Azure Portal přejděte na **Novou** > **síťovou** > **aplikační bránu**.

2. V oblasti **Základy:**

   a. Do **pole Název**zadejte název aplikační brány.

   b. V **případě úrovně**vyberte možnost **WAF**.

   c. V **části Předplatné**vyberte stejné předplatné, které používá virtuální síť Prostředí služby App Service Environment.

   d. Ve **skupině Prostředků**vytvořte nebo vyberte skupinu prostředků.

   e. V **části Umístění**vyberte umístění virtuální sítě Prostředí služby App Service Environment.

   ![Základy vytváření nové aplikační brány][2]

3. V oblasti **Nastavení:**

   a. V **případě virtuální sítě**vyberte virtuální síť Prostředí služby App Service Environment.

   b. V **podsíti**vyberte podsíť, do které je třeba nasadit aplikační bránu. Nepoužívejte GatewaySubnet, protože zabrání vytváření bran VPN.

   c. Pro **typ IP adresy**vyberte možnost **Veřejné**.

   d. V **případě veřejné IP adresy**vyberte veřejnou IP adresu. Pokud ho nemáte, vytvořte si ho nyní.

   e. V **případě protokolu**vyberte možnost **HTTP** nebo **HTTPS**. Pokud konfigurujete pro protokol HTTPS, musíte zadat certifikát PFX.

   f. Pro **bránu firewall webových aplikací**můžete povolit bránu firewall a také ji nastavit pro **detekci** nebo **prevenci,** jak uznáte za vhodné.

   ![Nové nastavení vytváření brány aplikace][3]
    
4. V části **Souhrn** zkontrolujte nastavení a vyberte **OK**. Dokončení instalace může trvat o něco více než 30 minut.  

5. Po dokončení instalace aplikační brány přejděte na portál aplikační brány. Vyberte **back-endový fond**. Přidejte adresu ILB pro prostředí služby ILB App Service.

   ![Konfigurace back-endový fond][4]

6. Po dokončení procesu konfigurace back-endového fondu vyberte **sondy stavu**. Vytvořte sondu stavu pro název domény, který chcete použít pro vaši aplikaci. 

   ![Konfigurace sond stavu][5]
    
7. Po dokončení procesu konfigurace sond stavu vyberte **nastavení protokolu HTTP**. Upravte existující nastavení, vyberte **Použít vlastní sondu**a vyberte sondu, kterou jste nakonfigurovali.

   ![Konfigurace nastavení protokolu HTTP][6]
    
8. Přejděte do části **Přehled** aplikační brány a zkopírujte veřejnou IP adresu, kterou používá vaše aplikační brána. Nastavte tuto IP adresu jako záznam A pro název domény aplikace nebo použijte název DNS pro tuto adresu v záznamu CNAME. Je jednodušší vybrat veřejnou IP adresu a zkopírovat ji z hlavního nastavení veřejné IP adresy, než ji zkopírovat z odkazu v části **Přehled** aplikační brány. 

   ![Portál aplikační brány][7]

9. Nastavte vlastní název domény pro vaši aplikaci v prostředí služby ILB App Service. Přejděte do aplikace na portálu a v části **Nastavení**vyberte **Vlastní domény**.

   ![Nastavení názvu vlastní domény v aplikaci][8]

Informace o nastavení vlastních názvů domén pro webové aplikace naleznete v článku [Nastavení vlastních názvů domén pro webovou aplikaci][custom-domain]. Ale pro aplikaci v prostředí služby ILB App Service není žádné ověření na název domény. Protože vlastníte DNS, který spravuje koncové body aplikace, můžete tam umístit, co chcete. Vlastní název domény, který přidáte v tomto případě nemusí být ve vašem DNS, ale stále musí být nakonfigurován s vaší aplikací. 

Po dokončení instalace a povolení krátkého množství času pro šíření změn DNS můžete k aplikaci přistupovat pomocí vlastního názvu domény, který jste vytvořili. 


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
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
