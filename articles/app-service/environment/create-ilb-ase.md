---
title: Vytvoření interního nástroje load balancer pomocí služby App Service Environment – Azure
description: Podrobnosti o tom, jak vytvořit a používat službu Azure App Service Environment izolovanou od internetu
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5b05755502ad5836a21080a122d2e1721825f10c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734690"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Vytváření a používání interního App Service Environment nástroje pro vyrovnávání zatížení 

Azure App Service Environment je nasazení služby Azure App Service do podsítě ve službě Azure virtual network (VNet). Služba App Service Environment (ASE) se dá nasadit dvěma způsoby: 

- Pomocí virtuální IP adresy na externí IP adresu – často se označuje jako externí služba ASE
- Pomocí virtuální IP adresy na interní IP adresu – často se označuje jako služba ASE s interním nástrojem pro vyrovnávání zatížení, protože interním koncovým bodem je interní nástroj pro vyrovnávání zatížení. 

V tomto článku najdete postup vytvoření interního nástroje pro vyrovnávání zatížení ASE. Přehled služby ASE najdete v tématu [Úvod do služby App Service Environment][Intro]. Informace o vytvoření externí služby ASE najdete v tématu [Vytvoření externí služby ASE][MakeExternalASE].

## <a name="overview"></a>Přehled 

Můžete nasadit službu ASE s koncovým bodem s přístupem k internetu nebo s IP adresou ve vaší virtuální síti. Pokud chcete nastavit IP adresu na adresu ve virtuální síti, je potřeba nasadit službu ASE s interním nástrojem pro vyrovnávání zatížení. Pokud nasadíte službu ASE s ILB, je nutné zadat název vaší služby ase. Název vaší služby ASE se používá v příponě domény pro aplikace ve vaší službě ASE.  Přípona domény pro vaše služba ASE s ILB je &lt;název služby ASE&gt;. appservicewebsites.net. Aplikace, které probíhají ve službě ASE s ILB nebudou umístěny ve veřejném DNS. 

Starší verze prostředí ILB ASE je nutné poskytnout příponu domény a výchozí certifikát pro připojení prostřednictvím protokolu HTTPS. Přípona domény shromažďovaných už při vytvoření ILB ASE a výchozí certifikát se také již shromažďují. Když nyní vytvořit prostředí ILB ASE výchozí certifikát od Microsoftu a je důvěryhodný pro prohlížeč. Budete stále moct nastavení vlastních názvů domén v aplikacích ve vaší službě ASE a certifikáty na těchto vlastních názvů domén. 

S ILB ASE můžete dělat něco jako například:

-   V cloudu, které máte přístup prostřednictvím site-to-site nebo ExpressRoute zabezpečené hostování intranetových aplikací.
-   Ochrana aplikací se zařízením WAF
-   Hostovat v cloudu aplikace, které nejsou uvedené na veřejných serverech DNS
-   Vytvářet back-endové aplikace izolované od internetu, které umožňují zabezpečenou integraci vašich front-endových aplikací

### <a name="disabled-functionality"></a>Zakázané funkce ###

Služba ASE s interním nástrojem pro vyrovnávání zatížení neumožňuje některé kroky:

-   Nemůžete používat zabezpečení SSL založené na protokolu IP.
-   Nemůžete přiřadit IP adresy konkrétním aplikacím.
-   Nemůžete zakoupit certifikát prostřednictvím portálu Azure a používat ho s určitou aplikací. Certifikáty můžete získat přímo od certifikační autority a potom je můžete používat ve svých aplikacích. Nemůžete je ale získat na portálu Azure.

## <a name="create-an-ilb-ase"></a>Vytvoření služby ASE s interním nástrojem pro vyrovnávání zatížení ##

Při vytváření služby ASE s interním nástrojem pro vyrovnávání zatížení postupujte takto:

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Web** > **App Service Environment**.

2. Vyberte své předplatné.

3. Vyberte nebo vytvořte skupinu prostředků.

4. Zadejte název služby App Service Environment.

5. Vyberte virtuální typ interní IP.

    ![Vytvoření služby ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Vyberte sítě

7. Vyberte nebo vytvořte virtuální síť. Pokud vytvoříte nové sítě VNet, tady, budou definovat s rozsahem adres 192.168.250.0/23. K vytvoření virtuální sítě s jinou adresu rozsahu, nebo v jiné skupině prostředků než služby ASE, pomocí portálu pro vytvoření virtuální sítě Azure. 

8. Vyberte nebo vytvořte prázdnou podsíť. Pokud chcete vybrat podsíť, musí být prázdný a není delegovaný. Velikost podsítě nelze změnit po vytvoření služby ASE. Doporučujeme velikost `/24`, která nabízí 256 adres a dokáže pojmout maximální velikost služby ASE a vyhovět potřebám škálování. 

    ![Síťové služby ASE][1]

7. Vyberte **zkontrolovat a vytvořit** vyberte **vytvořit**.

## <a name="create-an-app-in-an-ilb-ase"></a>Vytvoření aplikace ve službě ASE s interním nástrojem pro vyrovnání zatížení ##

Aplikaci ve službě ASE s interním nástrojem pro vyrovnání zatížení vytvoříte stejným způsobem jako v běžné službě ASE.

1. Na webu Azure Portal, vyberte **vytvořit prostředek** > **webové** > **webovou aplikaci**.

1. Zadejte název aplikace.

1. Vyberte předplatné.

1. Vyberte nebo vytvořte skupinu prostředků.

1. Vyberte publikovat, zásobník modulu Runtime a operačního systému.

1. Vyberte umístění, kde se nachází existující služba ASE s ILB.  Můžete také vytvořit nové služby ASE během vytváření aplikace tak, že vyberete plán služby App Service izolované. Pokud chcete vytvořit novou službu ASE, vyberte oblasti, kterou chcete službu ASE vytvořit v.

1. Vyberte nebo vytvořte plán služby App Service. 

1. Vyberte **zkontrolovat a vytvořit** vyberte **vytvořit** Jakmile budete připraveni.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Webové úlohy, služba Functions a služba ASE s interním nástrojem pro vyrovnávání zatížení 

Služba ASE s interním nástrojem pro vyrovnávání zatížení podporuje službu Functions i webové úlohy, ale nepodporuje portál pro práci s nimi, a potřebujete síťový přístup k webu SCM.  To znamená, že váš prohlížeč musí mít hostitele, který buď je ve virtuální síti, nebo je k ní připojený. Pokud má vaše služba ASE s ILB název domény, který nemá *appserviceenvironment.net*, budete muset získat váš prohlížeč důvěřovat certifikátu protokolu HTTPS používá web scm.

## <a name="dns-configuration"></a>Konfigurace DNS 

Pokud používáte externí virtuální IP adresu, službu DNS spravuje Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Použití s ILB ASE příponu domény závisí na název služby ASE. Přípona domény je  *&lt;název služby ASE&gt;. appserviceenvironment.net*. IP adresa pro vaše ILB je na portálu v části **IP adresy**. 

Postup konfigurace serveru DNS:

- Vytvoření zóny pro  *&lt;název služby ASE&gt;. appserviceenvironment.net*
- Vytvořit záznam v této zóně, který odkazuje * ILB IP adresu 
- Vytvořte zónu v  *&lt;název služby ASE&gt;. scm.appserviceenvironment.net* s názvem Správce řízení služeb
- Vytvoření záznamu A v zóně správce řízení služeb, který odkazuje na ILB IP adresu

## <a name="publish-with-an-ilb-ase"></a>Publikování pomocí služby ASE s interním nástrojem pro vyrovnávání zatížení

Pro každou vytvořenou aplikaci existují dva koncové body. Ve službě ASE s ILB, máte *&lt;název aplikace&gt;.&lt; Doména služby ASE ILB&gt;* a  *&lt;název aplikace&gt;.scm.&lt; Doména služby ASE ILB&gt;* . 

Pomocí názvu webu SCM se dostanete do konzoly Kudu na portálu Azure s názvem **Rozšířený portál**. Konzola Kudu umožňuje zobrazit proměnné prostředí, prozkoumávat disk, používat konzolu a další možnosti. Další informace najdete v tématu [Konzola Kudu pro službu Azure App Service][Kudu]. 

Internetové systémy kontinuální integrace, například GitHub a Azure DevOps, budou nadále fungovat se službou ASE s interním nástrojem pro vyrovnávání zatížení, pokud je agent sestavení přístupný z internetu a nachází se ve stejné síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení. Takže pokud se používá Azure DevOps a agent sestavení je vytvořený ve stejné virtuální síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení (může být i v jiné podsíti), bude moct přijmout změny kódu z gitu Azure DevOps a nasadit ho do služby ASE s interním nástrojem pro vyrovnávání zatížení. Pokud si nechcete vytvořit vlastního agenta sestavení, budete muset použít systém kontinuální integrace (CI), který používá model vyžádání, například Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Tato doména se objevuje v profilu publikování aplikace a v okně portálu aplikace (**Přehled** > **Essentials** a také **Vlastnosti**). Pokud máte službu ASE s příponou domény  *&lt;název služby ASE&gt;. appserviceenvironment.net*a aplikaci s názvem *test*, použijte *test.&lt; Název služby ASE&gt;. appserviceenvironment.net* pro FTP a *mytest.scm.contoso.net* pro nasazení webu.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Nakonfigurujte službu ASE se zařízením WAF ##

Zařízení brány firewall (WAF) webové aplikace můžete zkombinovat s vaše služba ASE s ILB vystavit pouze aplikace, že chcete k Internetu a ponechte zbývající z dostupná jenom ve virtuální síti. To vám umožní sestavovat zabezpečené vícevrstvých aplikací mimo jiné.

Další informace o tom, jak konfigurovat vaše služba ASE s ILB se zařízením WAF najdete v tématu [konfigurace firewallu webových aplikací s App Service environment][ASEWAF]. Tento článek vysvětluje, jak ve službě ASE používat virtuální zařízení Barracuda. Další možností je použít službu Azure Application Gateway. Služba Application Gateway dokáže pomocí základních pravidel OWASP zabezpečit všechny aplikace, které za ni umístíte. Další informace o službě Application Gateway najdete v tématu [Úvod do brány firewall webových aplikací Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>Vytvořená před. května 2019 služeb ase s ILB

Služeb ase s ILB, které byly provedeny před. května 2019 je nutné nastavit příponu domény při vytváření služby ASE. Jsou také vyžaduje odeslání výchozí certifikát, který je založen na tuto příponu domény. Navíc se starší prostředí ILB ASE nelze provést jednotné přihlašování ke konzole Kudu s aplikacemi v této službě ILB ASE. Při konfiguraci DNS pro starší služba ASE s ILB, je nutné nastavit záznam zástupný znak A v zóně, která odpovídá vaší příponu domény. 

## <a name="get-started"></a>Začínáme ##

* Pokud chcete začít používat službu ASE, prostudujte si téma [Úvod do služby App Service Environment][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
