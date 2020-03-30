---
title: Vytvoření služby ASE ILB s ARM
description: Zjistěte, jak vytvořit prostředí služby App Service pomocí interního nástroje pro vyrovnávání zatížení (ILB ASE) pomocí šablon Azure Resource Manager. Plně izolujte své aplikace od internetu.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 98345e8585a3f6653659e0d41eb5c3308a0a6634
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80057419"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Vytvoření a použití prostředí služby Internal Load Balancer App Service Environment 

Prostředí služby Azure App Service je nasazení služby Azure App Service do podsítě ve virtuální síti Azure (VNet). Služba App Service Environment (ASE) se dá nasadit dvěma způsoby: 

- Pomocí virtuální IP adresy na externí IP adresu – často se označuje jako externí služba ASE
- Pomocí virtuální IP adresy na interní IP adresu – často se označuje jako služba ASE s interním nástrojem pro vyrovnávání zatížení, protože interním koncovým bodem je interní nástroj pro vyrovnávání zatížení. 

V tomto článku najdete postup vytvoření interního nástroje pro vyrovnávání zatížení ASE. Přehled služby ASE najdete v [tématu Úvod do prostředí služby App Service][Intro]. Informace o vytvoření externí služby ASE najdete v tématu [Vytvoření externí služby ASE][MakeExternalASE].

## <a name="overview"></a>Přehled 

Můžete nasadit službu ASE s koncovým bodem s přístupem k internetu nebo s IP adresou ve vaší virtuální síti. Pokud chcete nastavit IP adresu na adresu ve virtuální síti, je potřeba nasadit službu ASE s interním nástrojem pro vyrovnávání zatížení. Při nasazení služby ASE s ILB, je nutné zadat název služby ASE. Název služby ASE se používá v příponě domény pro aplikace ve službě ASE.  Příponou domény služby ASE &lt;služby&gt;ILB je název služby ASE .appserviceenvironment.net. Aplikace, které jsou vyrobeny ve službě ASE ILB nejsou umístěny ve veřejné DNS. 

Dřívější verze služby ASE ILB vyžadovaly poskytnutí přípony domény a výchozího certifikátu pro připojení HTTPS. Přípona domény se již neshromažďuje při vytváření služby ASE služby ILB a výchozí certifikát se také již neshromažďuje. Když nyní vytvoříte službu ASE ILB, výchozí certifikát poskytuje společnost Microsoft a prohlížeč mu důvěřuje. Stále můžete nastavit vlastní názvy domén v aplikacích ve službě ASE a nastavit certifikáty na těchto vlastních doménových jmen. 

Se službou ASE ilb můžete například:

-   Hostujte intranetové aplikace bezpečně v cloudu, ke kterému přistupujete prostřednictvím webu k webu nebo ExpressRoute.
-   Ochrana aplikací pomocí zařízení WAF
-   Hostovat v cloudu aplikace, které nejsou uvedené na veřejných serverech DNS
-   Vytvářet back-endové aplikace izolované od internetu, které umožňují zabezpečenou integraci vašich front-endových aplikací

### <a name="disabled-functionality"></a>Zakázané funkce ###

Služba ASE s interním nástrojem pro vyrovnávání zatížení neumožňuje některé kroky:

-   Nemůžete používat zabezpečení SSL založené na protokolu IP.
-   Nemůžete přiřadit IP adresy konkrétním aplikacím.
-   Nemůžete zakoupit certifikát prostřednictvím portálu Azure a používat ho s určitou aplikací. Certifikáty můžete získat přímo od certifikační autority a potom je můžete používat ve svých aplikacích. Nemůžete je ale získat na portálu Azure.

## <a name="create-an-ilb-ase"></a>Vytvoření služby ASE s interním nástrojem pro vyrovnávání zatížení ##

Při vytváření služby ASE s interním nástrojem pro vyrovnávání zatížení postupujte takto:

1. Na portálu Azure vyberte **Vytvořit** > **prostředí služby****Web** > App Service .

2. Vyberte své předplatné.

3. Vyberte nebo vytvořte skupinu prostředků.

4. Zadejte název prostředí služby App Service.

5. Vyberte virtuální typ IP interního.

    ![Vytvoření služby ASE](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Vybrat síť

7. Vyberte nebo vytvořte virtuální síť. Pokud zde vytvoříte novou virtuální síť, bude definována s rozsahem adres 192.168.250.0/23. Pokud chcete vytvořit virtuální síť s jiným rozsahem adres nebo v jiné skupině prostředků než služba ASE, použijte portál pro vytváření virtuální sítě Azure. 

8. Vyberte nebo vytvořte prázdnou podsíť. Pokud chcete vybrat podsíť, musí být prázdná a není delegována. Velikost podsítě nelze po vytvoření rozhraní ASE změnit. Doporučujeme velikost `/24`, která nabízí 256 adres a dokáže pojmout maximální velikost služby ASE a vyhovět potřebám škálování. 

    ![ASE sítě][1]

7. Vyberte **Zkontrolovat a Vytvořit** a pak vyberte **Vytvořit**.

## <a name="create-an-app-in-an-ilb-ase"></a>Vytvoření aplikace ve službě ASE s interním nástrojem pro vyrovnání zatížení ##

Aplikaci ve službě ASE s interním nástrojem pro vyrovnání zatížení vytvoříte stejným způsobem jako v běžné službě ASE.

1. Na portálu Azure vyberte Vytvořit > **webovou** > **webovou aplikaci pro** **prostředky**.

1. Zadejte název aplikace.

1. Vyberte předplatné.

1. Vyberte nebo vytvořte skupinu prostředků.

1. Vyberte možnost Publikovat, Zásobník runtime a operační systém.

1. Vyberte umístění, kde je umístění existující službou ASE ILB.  Můžete také vytvořit novou službu ASE během vytváření aplikace výběrem izolované služby App Service plán. Chcete-li vytvořit novou zprávu ase, vyberte oblast, ve které má být vytvořena.

1. Vyberte nebo vytvořte plán služby App Service. 

1. Vyberte **Zkontrolovat a Vytvořit** a pak vyberte **Vytvořit,** až budete připraveni.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Webové úlohy, služba Functions a služba ASE s interním nástrojem pro vyrovnávání zatížení 

Služba ASE s interním nástrojem pro vyrovnávání zatížení podporuje službu Functions i webové úlohy, ale nepodporuje portál pro práci s nimi, a potřebujete síťový přístup k webu SCM.  To znamená, že váš prohlížeč musí mít hostitele, který buď je ve virtuální síti, nebo je k ní připojený. Pokud má vaše služba ASE ILB název domény, který nekončí *appserviceenvironment.net*, budete muset přimět prohlížeč, aby důvěřoval certifikátu HTTPS používanému vaším webem scm.

## <a name="dns-configuration"></a>Konfigurace DNS 

Pokud používáte externí virtuální IP adresu, službu DNS spravuje Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Přípona domény použitá se službou ASE ILB závisí na názvu služby ASE. Přípona domény * &lt;je název&gt;služby ASE .appserviceenvironment.net*. IP adresa vašeho ILB je na portálu pod **IP adresami**. 

Postup konfigurace služby DNS:

- vytvoření zóny * &lt;pro název&gt;ase .appserviceenvironment.net*
- Vytvoření záznamu A v této zóně, který odkazuje * na adresu IP ILB
- Vytvoření záznamu A v této zóně, který odkazuje na adresu IP služby ILB
- vytvoření zóny * &lt;v názvu&gt;ase .appserviceenvironment.net* s názvem scm
- vytvoření záznamu A v zóně scm, který odkazuje * na ip adresu ILB

## <a name="publish-with-an-ilb-ase"></a>Publikování pomocí služby ASE s interním nástrojem pro vyrovnávání zatížení

Pro každou vytvořenou aplikaci existují dva koncové body. Ve službách ASE ILB máte * &lt;název&gt;aplikace .&lt; ILB ASE&gt; Doména* a * &lt;&gt;název&lt; aplikace .scm. Doména služby&gt;ASE služby ILB*. 

Pomocí názvu webu SCM se dostanete do konzoly Kudu na portálu Azure s názvem **Rozšířený portál**. Konzola Kudu umožňuje zobrazit proměnné prostředí, prozkoumávat disk, používat konzolu a další možnosti. Další informace najdete v tématu [Konzola Kudu pro službu Azure App Service][Kudu]. 

Internetové systémy kontinuální integrace, například GitHub a Azure DevOps, budou nadále fungovat se službou ASE s interním nástrojem pro vyrovnávání zatížení, pokud je agent sestavení přístupný z internetu a nachází se ve stejné síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení. Takže pokud se používá Azure DevOps a agent sestavení je vytvořený ve stejné virtuální síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení (může být i v jiné podsíti), bude moct přijmout změny kódu z gitu Azure DevOps a nasadit ho do služby ASE s interním nástrojem pro vyrovnávání zatížení. Pokud si nechcete vytvořit vlastního agenta sestavení, budete muset použít systém kontinuální integrace (CI), který používá model vyžádání, například Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Tato doména se zobrazí v profilu publikování aplikace a v okně portálu aplikace (**Přehled** > **Essentials** a také **vlastnosti**). Pokud máte službu ASE ILB s názvem *mytest* * &lt;&gt;* přípony domény ASE .appserviceenvironment.net a aplikaci s názvem mytest , použijte *mytest.&lt; Název&gt;ase .appserviceenvironment.net* pro FTP a *mytest.scm.contoso.net* pro nasazení na webu.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurace služby ASE ILB pomocí zařízení WAF ##

Můžete kombinovat webové aplikace firewall (WAF) zařízení s ILB ASE vystavit pouze aplikace, které chcete k internetu a zachovat zbytek přístupné pouze z virtuální sítě. To umožňuje mimo jiné vytvářet zabezpečené vícevrstvé aplikace.

Další informace o konfiguraci služby ILB Se pomocí zařízení WAF najdete v [tématu Konfigurace brány firewall webové aplikace pomocí prostředí služby App Service][ASEWAF]. Tento článek vysvětluje, jak ve službě ASE používat virtuální zařízení Barracuda. Další možností je použít službu Azure Application Gateway. Služba Application Gateway dokáže pomocí základních pravidel OWASP zabezpečit všechny aplikace, které za ni umístíte. Další informace o službě Application Gateway najdete v tématu [Úvod do brány firewall webových aplikací Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASEs před květnem 2019

ILB ASEs, které byly provedeny před květnem 2019, vyžadovaly nastavení přípony domény během vytváření služby ASE. Také vyžadovali, abyste nahráli výchozí certifikát založený na této příponě domény. Se starší službou ASE ILB také nelze provádět jednotné přihlašování ke konzoli Kudu s aplikacemi v této službě ASE ILB. Při konfiguraci služby DNS pro starší službu ASE ILB je třeba nastavit zástupný znak Záznam V zóně, která odpovídá příponě vaší domény. 

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
