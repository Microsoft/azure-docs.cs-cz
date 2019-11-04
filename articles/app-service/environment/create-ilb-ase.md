---
title: Vytvoření interního nástroje pro vyrovnávání zatížení pomocí App Service Environment – Azure
description: Podrobnosti o tom, jak vytvořit a používat službu Azure App Service Environment izolovanou od internetu
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 07b47374484cf954b1fc4279c93dddcc6cec7e61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470568"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Vytvoření a použití interní Load Balancer App Service Environment 

Azure App Service Environment je nasazení Azure App Service do podsítě ve službě Azure Virtual Network (VNet). Služba App Service Environment (ASE) se dá nasadit dvěma způsoby: 

- Pomocí virtuální IP adresy na externí IP adresu – často se označuje jako externí služba ASE
- Pomocí virtuální IP adresy na interní IP adresu – často se označuje jako služba ASE s interním nástrojem pro vyrovnávání zatížení, protože interním koncovým bodem je interní nástroj pro vyrovnávání zatížení. 

V tomto článku najdete postup vytvoření interního nástroje pro vyrovnávání zatížení ASE. Přehled služby pomocného mechanismu řízení najdete v tématu [Úvod do App Service prostředí][Intro]. Informace o tom, jak vytvořit externí pomocného mechanismu řízení, najdete v tématu [Vytvoření externího POmocného mechanismu][MakeExternalASE]

## <a name="overview"></a>Přehled 

Můžete nasadit službu ASE s koncovým bodem s přístupem k internetu nebo s IP adresou ve vaší virtuální síti. Pokud chcete nastavit IP adresu na adresu ve virtuální síti, je potřeba nasadit službu ASE s interním nástrojem pro vyrovnávání zatížení. Když nasadíte pomocného mechanismu pro interního nástroje, musíte zadat název svého pomocného mechanismu. Název pomocného mechanismu se používá v příponě domény pro aplikace ve vašem pomocném mechanismu.  Přípona domény pro interního nástroje pomocného modulu pro pojmenování je &lt;&gt;. appserviceenvironment.net. Aplikace, které jsou vytvořené v interního nástroje pomocném mechanismu, se neumísťují do veřejné služby DNS. 

Starší verze interního nástroje pomocného programu pro připojení k protokolu HTTPS vyžadují, abyste zadali příponu domény a výchozí certifikát. Přípona domény se už neshromažďuje při vytváření pomocného mechanismu interního nástroje a výchozí certifikát se už nebude shromažďovat. Když teď vytvoříte interního nástroje pomocného programu pro vytváření stavů, poskytne se Microsoftu certifikát, který je důvěryhodný pro prohlížeč. Pořád budete moct v aplikacích pro pomocného uživateli nastavit vlastní názvy domén a nastavit certifikáty na tyto vlastní názvy domén. 

Pomocí pomocného programu interního nástroje můžete provádět následující akce:

-   Umožňuje bezpečně hostovat intranetové aplikace v cloudu, ke kterým přistupujete prostřednictvím sítě Site-to-site nebo ExpressRoute.
-   Ochrana aplikací pomocí zařízení s WAF
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

4. Zadejte název vašeho App Service Environment.

5. Vyberte typ virtuální IP adresy interní.

    ![Vytvoření služby ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Výběr sítě

7. Vyberte nebo vytvořte Virtual Network. Pokud tady vytvoříte novou virtuální síť, bude se definovat s rozsahem adres 192.168.250.0/23. Pokud chcete vytvořit virtuální síť s jiným rozsahem adres nebo jinou skupinou prostředků než pomocným mechanismem, použijte portál pro vytváření Virtual Network Azure. 

8. Vyberte nebo vytvořte prázdnou podsíť. Pokud chcete vybrat podsíť, musí být prázdná a není delegovaná. Velikost podsítě nelze po vytvoření pomocného mechanismu změnit. Doporučujeme velikost `/24`, která nabízí 256 adres a dokáže pojmout maximální velikost služby ASE a vyhovět potřebám škálování. 

    ![Sítě pomocného mechanismu][1]

7. Vyberte **zkontrolovat a vytvořit a** pak vyberte **vytvořit**.

## <a name="create-an-app-in-an-ilb-ase"></a>Vytvoření aplikace ve službě ASE s interním nástrojem pro vyrovnání zatížení ##

Aplikaci ve službě ASE s interním nástrojem pro vyrovnání zatížení vytvoříte stejným způsobem jako v běžné službě ASE.

1. V Azure Portal vyberte **vytvořit prostředek** ** > webové** > **Webová aplikace**.

1. Zadejte název aplikace.

1. Vyberte předplatné.

1. Vyberte nebo vytvořte skupinu prostředků.

1. Vyberte svůj publikační a běhový zásobník a operační systém.

1. Vyberte umístění, kde se nachází existující pomocným mechanismem interního nástroje.  Během vytváření aplikace můžete také vytvořit nový pomocného mechanismu, a to tak, že vyberete plán izolované App Service. Pokud chcete vytvořit nový pomocný objekt pro vytváření, vyberte oblast, ve které chcete vytvořit pomocný objekt pro vytváření.

1. Vyberte nebo vytvořte plán služby App Service. 

1. Vyberte možnost **zkontrolovat a vytvořit a** po skončení vyberte **vytvořit** .

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Webové úlohy, služba Functions a služba ASE s interním nástrojem pro vyrovnávání zatížení 

Služba ASE s interním nástrojem pro vyrovnávání zatížení podporuje službu Functions i webové úlohy, ale nepodporuje portál pro práci s nimi, a potřebujete síťový přístup k webu SCM.  To znamená, že váš prohlížeč musí mít hostitele, který buď je ve virtuální síti, nebo je k ní připojený. Pokud má váš interního nástroje přihlášený název domény, který nekončí na *appserviceenvironment.NET*, budete muset získat svůj prohlížeč, aby DŮVĚŘOVAL certifikátu HTTPS používaného vaší lokalitou SCM.

## <a name="dns-configuration"></a>Konfigurace DNS 

Pokud používáte externí virtuální IP adresu, službu DNS spravuje Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Přípona domény používaná s interního nástroje pomocným mechanismem řízení závisí na názvu pomocného mechanismu služby. Přípona domény je *&lt;název POmocného mechanismu&gt;. appserviceenvironment.NET*. IP adresa pro váš interního nástroje je na portálu v části **IP adresy**. 

Konfigurace DNS:

- Vytvořte zónu pro *&lt;název POmocného mechanismu&gt;. appserviceenvironment.NET*
- Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
- Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
- Vytvořte zónu v *&lt;název POmocného mechanismu&gt;. appserviceenvironment.NET* s názvem SCM
- Vytvořte v zóně SCM záznam A, který odkazuje na IP adresu interního nástroje.

## <a name="publish-with-an-ilb-ase"></a>Publikování pomocí služby ASE s interním nástrojem pro vyrovnávání zatížení

Pro každou vytvořenou aplikaci existují dva koncové body. V pomocném mechanismu interního nástroje jste *&lt;název aplikace&gt;.&lt;interního nástroje POmocného názvu domény&gt;* a *&lt;název aplikace&gt;. SCM.&lt;interního nástroje pomocného protokolu domény&gt;* . 

Pomocí názvu webu SCM se dostanete do konzoly Kudu na portálu Azure s názvem **Rozšířený portál**. Konzola Kudu umožňuje zobrazit proměnné prostředí, prozkoumávat disk, používat konzolu a další možnosti. Další informace najdete v tématu [Kudu Console for Azure App Service][Kudu]. 

Internetové systémy kontinuální integrace, například GitHub a Azure DevOps, budou nadále fungovat se službou ASE s interním nástrojem pro vyrovnávání zatížení, pokud je agent sestavení přístupný z internetu a nachází se ve stejné síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení. Takže pokud se používá Azure DevOps a agent sestavení je vytvořený ve stejné virtuální síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení (může být i v jiné podsíti), bude moct přijmout změny kódu z gitu Azure DevOps a nasadit ho do služby ASE s interním nástrojem pro vyrovnávání zatížení. Pokud si nechcete vytvořit vlastního agenta sestavení, budete muset použít systém kontinuální integrace (CI), který používá model vyžádání, například Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Tato doména se objevuje v profilu publikování aplikace a v okně portálu aplikace (**Přehled** > **Essentials** a také **Vlastnosti**). Pokud máte interního nástroje pomocného programu s příponou domény *&lt;název POmocného seznamu&gt;. appserviceenvironment.NET*a aplikace s názvem *MyTest*, použijte *MYTEST.&lt;název pomocného programu&gt;. appserviceenvironment.NET* pro FTP a  *mytest.scm.contoso.net* pro nasazení webu.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurace pomocného programu interního nástroje pomocí zařízení WAF ##

Můžete zkombinovat zařízení firewallu webových aplikací (WAF) s pomocným mechanismem interního nástroje, abyste mohli zpřístupnit jenom aplikace, které chcete používat na internetu, a ponechat si REST přístup jenom z virtuální sítě. To umožňuje vytvářet zabezpečené vícevrstvé aplikace mimo jiné.

Další informace o tom, jak nakonfigurovat interního nástroje pomocného programu pro zařízení WAF, najdete v tématu [Konfigurace brány firewall webových aplikací pomocí App Serviceho prostředí][ASEWAF]. Tento článek vysvětluje, jak ve službě ASE používat virtuální zařízení Barracuda. Další možností je použít službu Azure Application Gateway. Služba Application Gateway dokáže pomocí základních pravidel OWASP zabezpečit všechny aplikace, které za ni umístíte. Další informace o Application Gateway najdete v tématu [Úvod do firewallu webových aplikací Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>INTERNÍHO nástroje služby ASE provedený před 2019. května

INTERNÍHO nástroje služby ASE, které jste provedli dříve, než může 2019 vyžadovat nastavení přípony domény během vytváření pomocného mechanismu. Také vyžadují, abyste nahráli výchozí certifikát, který byl založen na této příponě domény. Kromě toho se starším interního NÁSTROJEem pro čtení z nich nemůžete pro konzolu Kudu provádět jednotné přihlašování pomocí aplikací v tomto interního nástroje pomocném panelu. Při konfiguraci DNS pro starší přihlašování k interního nástroje je potřeba nastavit zástupný znak A záznam v zóně, která odpovídá vaší příponě domény. 

## <a name="get-started"></a>Začít ##

* Pokud chcete začít pracovat s služby ASE, přečtěte si téma [Úvod do prostředí App Service][Intro]. 

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
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
