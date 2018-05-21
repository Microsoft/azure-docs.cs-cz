---
title: Vytvoření a používání interního nástroje pro vyrovnávání zatížení ve službě Azure App Service Environment
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
ms.date: 03/20/2018
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 6e09bdc336821720c970f8b8daf13f52b0a69ed0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Vytvoření a používání interního nástroje pro vyrovnávání zatížení ve službě App Service Environment #

 Azure App Service Environment je nasazení služby Azure App Service do podsítě ve virtuální síti Azure. Služba App Service Environment (ASE) se dá nasadit dvěma způsoby: 

- Pomocí virtuální IP adresy na externí IP adresu – často se označuje jako externí služba ASE
- Pomocí virtuální IP adresy na interní IP adresu – často se označuje jako služba ASE s interním nástrojem pro vyrovnávání zatížení, protože interním koncovým bodem je interní nástroj pro vyrovnávání zatížení. 

V tomto článku najdete postup vytvoření interního nástroje pro vyrovnávání zatížení ASE. Přehled služby ASE najdete v tématu [Úvod do služby App Service Environment][Intro]. Informace o vytvoření externí služby ASE najdete v tématu [Vytvoření externí služby ASE][MakeExternalASE].

## <a name="overview"></a>Přehled ##

Můžete nasadit službu ASE s koncovým bodem s přístupem k internetu nebo s IP adresou ve vaší virtuální síti. Pokud chcete nastavit IP adresu na adresu ve virtuální síti, je potřeba nasadit službu ASE s interním nástrojem pro vyrovnávání zatížení. Pokud nasazujete službu ASE s interním nástrojem pro vyrovnávání zatížení, je potřeba splnit tyto požadavky:

-   Vaše doména, kterou používáte při vytváření aplikací
-   Certifikát používaný pro protokol HTTPS
-   Správa služby DNS pro vaši doménu

Díky tomu budete moct provádět třeba tyto úkony:

-   Nabízet v cloudu zabezpečené hostování intranetových aplikací, ke kterým můžete získat přístup pomocí sítě VPN typu site-to-site nebo Azure ExpressRoute
-   Hostovat v cloudu aplikace, které nejsou uvedené na veřejných serverech DNS
-   Vytvářet back-endové aplikace izolované od internetu, které umožňují zabezpečenou integraci vašich front-endových aplikací

### <a name="disabled-functionality"></a>Zakázané funkce ###

Služba ASE s interním nástrojem pro vyrovnávání zatížení neumožňuje některé kroky:

-   Nemůžete používat zabezpečení SSL založené na protokolu IP.
-   Nemůžete přiřadit IP adresy konkrétním aplikacím.
-   Nemůžete zakoupit certifikát prostřednictvím portálu Azure a používat ho s určitou aplikací. Certifikáty můžete získat přímo od certifikační autority a potom je můžete používat ve svých aplikacích. Nemůžete je ale získat na portálu Azure.

## <a name="create-an-ilb-ase"></a>Vytvoření služby ASE s interním nástrojem pro vyrovnávání zatížení ##

Při vytváření služby ASE s interním nástrojem pro vyrovnávání zatížení postupujte takto:

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Web a mobilní zařízení** > **App Service Environment**.

2. Vyberte své předplatné.

3. Vyberte nebo vytvořte skupinu prostředků.

4. Vyberte nebo vytvořte virtuální síť.

    * Pokud vyberete novou virtuální síť, můžete zadat její název a umístění. Pokud v této službě ASE chcete hostovat aplikace pro Linux, v současné době se podporuje pouze těchto 6 oblastí: **USA – západ, USA – východ, Západní Evropa, Severní Evropa, Austrálie – východ a Jihovýchodní Asie**. 

5. Pokud vyberete stávající virtuální síť, je potřeba vytvořit podsíť, která bude obsahovat službu ASE. Nezapomeňte nastavit dostatečnou velikost podsítě, aby umožnila budoucí růst služby ASE. Doporučujeme velikost `/25`, která nabízí 128 adres a dokáže pojmout maximální velikost služby ASE. Minimální velikost, kterou můžete vybrat, je `/28`. Podle potřeby infrastruktury se dá tato velikost rozšířit pouze na maximální počet 3 instancí.

    * Ve vašich plánech služby App Service dojde k překročení výchozí maximální hodnoty 100 instancí.

    * Škálování se přiblíží 100, ale s rychlejším škálováním front-endu.

6. Vyberte položky **Virtuální síť/Umístění** > **Konfigurace virtuální sítě**. U položky **Typ VIP** nastavte možnost **Interní**.

7. Zadejte název domény. Tato doména se bude používat pro aplikace vytvořené v této službě ASE. Platí určitá omezení. Nesmí se používat tyto domény:

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;nazev_ase&gt;.p.azurewebsites.net

   Existuje funkce označovaná jako Vlastní názvy domén, která umožňuje namapovat na webovou aplikaci existující název DNS. Další informace o této funkci najdete v dokumentu [Mapování existujícího názvu DNS na webovou aplikaci][customdomain]. Vlastní název domény pro aplikace se nesmí překrývat s názvem domény používaným vaší službou ASE. Pro službu ASE s interním nástrojem pro vyrovnávání zatížení s názvem domény _contoso.com_ není možné použít vlastní názvy domény pro aplikace podobné těmto:

    * www.contoso.com

    * abcd.def.contoso.com

    * abcd.contoso.com

   Pokud znáte vlastní názvy domén pro vaše aplikace, vyberte si pro službu ASE s interním nástrojem pro vyrovnávání zatížení doménu, která nebude s těmito vlastními názvy domén v konfliktu. V tomto příkladu můžete použít pro službu ASE třeba doménu *contoso-internal.com*, ta totiž nebude v konfliktu s vlastními názvy domén, které končí *.contoso.com*.

8. Vyberte **OK** a potom vyberte **Vytvořit**.

    ![Vytvoření služby ASE][1]

V okně **Virtuální síť** najdete možnost **Konfigurace virtuální sítě**. U ní můžete vybrat virtuální IP adresu typu Externí nebo Interní. Výchozí hodnota je **Externí**. Pokud vyberete možnost **Externí**, vaše služba ASE bude používat virtuální IP adresu s přístupem na internet. Pokud vyberete možnost **Interní**, vaše služba ASE bude mít nakonfigurovaný interní nástroj pro vyrovnávání zatížení s IP adresou v rámci vaší virtuální sítě.

Po výběru možnosti **Interní** nebude možné přidat do služby ASE další IP adresy. Místo toho je potřeba zadat doménu služby ASE. Ve službě ASE s externí virtuální IP adresou se název služby ASE používá v doméně pro aplikace vytvořené v této službě ASE.

Pokud u položky **Typ VIP** nastavíte hodnotu **Interní**, váš název služby ASE se nepoužije v názvu domény pro danou službu ASE. Doménu potom zadáte explicitně. Pokud máte doménu *contoso.corp.net* a vytvoříte v této službě ASE aplikaci s názvem *timereporting*, tato aplikace bude mít adresu URL timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Vytvoření aplikace ve službě ASE s interním nástrojem pro vyrovnání zatížení ##

Aplikaci ve službě ASE s interním nástrojem pro vyrovnání zatížení vytvoříte stejným způsobem jako v běžné službě ASE.

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Web a mobilní zařízení** > **Webová aplikace**.

2. Zadejte název aplikace.

3. Vyberte předplatné.

4. Vyberte nebo vytvořte skupinu prostředků.

5. Vyberte váš operační systém. 

    * Pokud chcete vytvořit aplikaci pro Linux s využitím vlastního kontejneru Dockeru, můžete podle zde uvedených pokynů jednoduše použít vlastní kontejner. 

6. Vyberte nebo vytvořte plán služby App Service. Pokud chcete vytvořit nový plán služby App Service, vyberte jako umístění svoji službu ASE. Vyberte fond pracovních procesů, ve kterém chcete plán služby App Service vytvořit. Po vytvoření plánu služby App Service vyberte jako umístění svoji službu ASE a zvolte fond pracovních procesů. Po zadání názvu aplikace dojde k nahrazení domény pod názvem aplikace doménou vaší služby ASE.

7. Vyberte **Vytvořit**. Pokud chcete, aby se aplikace zobrazovala na řídicím panelu, zaškrtněte políčko **Připnout na řídicí panel**.

    ![Vytvoření plánu služby App Service][2]

    V části **Název aplikace** se aktualizuje název domény na doménu vaší služby ASE.

## <a name="post-ilb-ase-creation-validation"></a>Ověření po vytvoření služby ASE s interním nástrojem pro vyrovnávání zatížení ##

Služba ASE s interním nástrojem pro vyrovnávání zatížení se trochu liší od služby bez interního nástroje pro vyrovnávání zatížení. Jak už jsme zmínili, je potřeba spravovat vlastní službu DNS. Taky je potřeba poskytnout vlastní certifikát pro připojení HTTPS.

Po vytvoření vaší služby ASE se v názvu domény zobrazí doména, kterou jste zadali. V nabídce **Nastavení** se objeví nová položka s názvem **Certifikát interního nástroje pro vyrovnávání zatížení**. Služba ASE se vytvoří s certifikátem, který nemá určenou doménu služby ASE s interním nástrojem pro vyrovnávání zatížení. Pokud službu ASE použijete s tímto certifikátem, váš prohlížeč bude hlásit, že je certifikát neplatný. Tento certifikát usnadňuje testování protokolu HTTPS, ale je potřeba nahrát váš vlastní certifikát spojený s vaší doménou služby ASE s interním nástrojem pro vyrovnávání zatížení. Tento krok je nutný bez ohledu na to, jestli máte certifikát podepsaný svým držitelem, nebo jste ho získali od certifikační autority.

![Název domény služby ASE s interním nástrojem pro vyrovnávání zatížení][3]

Vaše služba ASE s interním nástrojem pro vyrovnávání zatížení potřebuje platný certifikát SSL. Použijte interní certifikační autority, kupte si certifikát od externího vystavitele nebo použijte certifikát podepsaný svým držitelem. Bez ohledu na zdroj certifikátu SSL musí být správně nakonfigurované následující atributy certifikátu:

* **Předmět:** Tento atribut musí být nastavený na hodnotu *.vase-zdejsi-korenova-domena.
* **Alternativní název předmětu**: Tento atribut musí obsahovat hodnotu **.vase-zdejsi-korenova-domena* i **.scm.vase-zdejsi-korenova-domena*. Připojení SSL k webu SCM/Kudu spojenému s každou aplikací používá adresu ve formátu *nazev-vasi-aplikace.scm.vase-zdejsi-korenova-domena*.

Převeďte/uložte certifikát SSL jako soubor .pfx. Soubor .pfx musí obsahovat všechny zprostředkující a kořenové certifikáty. Zabezpečte ho pomocí hesla.

Pokud chcete vytvořit certifikát podepsaný svým držitelem, můžete tady použít příkazy prostředí PowerShell. Nezapomeňte použít název domény služby ASE s interním nástrojem pro vyrovnávání zatížení, ne název domény *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Certifikát generovaný těmito příkazy prostředí PowerShell označují prohlížeče příznakem, protože certifikát nevytvořila certifikační autorita, která se nachází v řetězu certifikátů vašeho prohlížeče. Pokud chcete získat certifikát, kterému bude váš prohlížeč důvěřovat, opatřete si ho od komerční certifikační autority v řetězu certifikátů vašeho prohlížeče. 

![Nastavení certifikátu interního nástroje pro vyrovnávání zatížení][4]

Pokud chcete nahrát vlastní certifikáty a otestovat přístup, postupujte takto:

1. Po vytvoření služby ASE přejděte do uživatelského rozhraní služby ASE. Vyberte položky **ASE** > **Nastavení** > **Certifikát interního nástroje pro vyrovnávání zatížení**.

2. Pokud chcete nastavit certifikát interního nástroje pro vyrovnávání zatížení, vyberte soubor certifikátu .pfx a zadejte heslo. Zpracování tohoto kroku nějakou dobu trvá. Zobrazí se zpráva s informací, že probíhá operace nahrávání.

3. Získejte adresu interního nástroje pro vyrovnávání zatížení pro vaši službu ASE. Vyberte položky **ASE** > **Vlastnosti** > **Virtuální IP adresa**.

4. Po vytvoření služby ASE vytvořte ve své službě ASE webovou aplikaci.

5. Pokud ještě v dané virtuální síti nemáte virtuální počítač, vytvořte ho.

    > [!NOTE] 
    > Nepokoušejte se tento virtuální počítač vytvořit ve stejné podsíti jako službu ASE, protože by došlo k chybě nebo potížím.
    >
    >

6. Nastavte službu DNS pro doménu vaší služby ASE. V doméně ve vaší službě DNS můžete použít zástupný znak. Pokud chcete provést několik jednoduchých testů, upravte ve svém virtuálním počítači soubor hostitelů a nastavte název webové aplikace na virtuální IP adresu:

    a. Pokud má vaše služba ASE název domény _.ilbase.com_ a vytvoříte webovou aplikaci s názvem _mytestapp_, bude mít adresu _mytestapp.ilbase.com_. Adresu _mytestapp.ilbase.com_ potom nastavíte tak, aby se překládala na adresu interního nástroje pro vyrovnávání zatížení. (Ve Windows je soubor hostitelů v adresáři _C:\Windows\System32\drivers\etc\_.)

    b. Pokud chcete provést test publikování nasazení webu nebo získat přístup k rozšířené konzole, vytvořte záznam pro adresu _mytestapp.scm.ilbase.com_.

7. V prohlížeči na tomto virtuálním počítači přejděte na adresu http://mytestapp.ilbase.com. (Nebo přejděte na název vaší webové aplikace ve vaší doméně.)

8. V prohlížeči na tomto virtuálním počítači přejděte na adresu https://mytestapp.ilbase.com. Pokud používáte certifikát podepsaný svým držitelem, přijměte nedostatek zabezpečení.

    IP adresa vašeho interního nástroje pro vyrovnávání zatížení je uvedená v seznamu **IP adresy**. Tento seznam obsahuje také IP adresy používané externí virtuální IP adresou a k řízení příchozích přenosů.

    ![IP adresa interního nástroje pro vyrovnávání zatížení][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Webové úlohy, služba Functions a služba ASE s interním nástrojem pro vyrovnávání zatížení ##

Služba ASE s interním nástrojem pro vyrovnávání zatížení podporuje službu Functions i webové úlohy, ale nepodporuje portál pro práci s nimi, a potřebujete síťový přístup k webu SCM.  To znamená, že váš prohlížeč musí mít hostitele, který buď je ve virtuální síti, nebo je k ní připojený.  

Pokud ve službě ASE s interním nástrojem pro vyrovnávání zatížení používáte službu Azure Functions, může se vám zobrazit tato chybová zpráva: „V tuto chvíli nedokážeme načíst vaše funkce. Zkuste to prosím znovu později.“ K této chybě dochází, protože uživatelské rozhraní služby Functions používá web SCM přes protokol HTTPS a kořenový certifikát není v řetězu certifikátů prohlížeče. Webové úlohy mají podobné potíže. Pokud se chcete těmto potížím vyhnout, můžete provést jeden z těchto kroků:

- Přidejte certifikát do úložiště důvěryhodných certifikátů. To odblokujete aplikace Edge a Internet Explorer.
- Otevřete prohlížeč Chrome a nejdřív přejděte na web SCM, potom přijměte nedůvěryhodný certifikát a přejděte na portál.
- Použijte komerční certifikát, který je v řetězu certifikátů vašeho prohlížeče.  Tato možnost je nejlepší.  

## <a name="dns-configuration"></a>Konfigurace DNS ##

Pokud používáte externí virtuální IP adresu, službu DNS spravuje Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Pro danou doménu, třeba _contoso.net_, je potřeba vytvořit ve vaší službě DNS záznamy DNS, které odkazují na adresu vašeho interního nástroje pro vyrovnávání zatížení pro tyto domény:

- *.contoso.net
- *.scm.contoso.net

Pokud se vaše doména služby ASE s interním nástrojem pro vyrovnávání zatížení používá mimo tuto službu ASE k víc účelům, může být potřeba spravovat službu DNS pro každý název aplikace zvlášť. Tato metoda je náročná, protože při vytváření každé nové aplikace musíte do služby DNS přidat její název. Z toho důvodu doporučujeme používat vyhrazenou doménu.

## <a name="publish-with-an-ilb-ase"></a>Publikování pomocí služby ASE s interním nástrojem pro vyrovnávání zatížení ##

Pro každou vytvořenou aplikaci existují dva koncové body. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení máte body *&lt;název aplikace>.&lt;doména služby ASE s interním nástrojem pro vyrovnávání zatížení>* a *&lt;název aplikace>.scm.&lt;doména služby ASE s interním nástrojem pro vyrovnávání zatížení>*. 

Pomocí názvu webu SCM se dostanete do konzoly Kudu na portálu Azure s názvem **Rozšířený portál**. Konzola Kudu umožňuje zobrazit proměnné prostředí, prozkoumávat disk, používat konzolu a další možnosti. Další informace najdete v tématu [Konzola Kudu pro službu Azure App Service][Kudu]. 

Ve víceklientské službě App Service a externí službě ASE se mezi portálem Azure a konzolou Kudu používá jednotné přihlašování. Pro službu ASE s interním nástrojem pro vyrovnávání zatížení je ale k přihlášení ke konzole Kudu potřeba použít vaše přihlašovací údaje pro publikování.

Internetové systémy kontinuální integrace (CI), například GitHub a Visual Studio Team Services, budou nadále fungovat se službou ASE s interním nástrojem pro vyrovnávání zatížení, pokud je agent sestavení přístupný z internetu a nachází se ve stejné síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení. Takže pokud se používá Visual Studio Team Services a agent sestavení je vytvořený ve stejné virtuální síti jako služba ASE s interním nástrojem pro vyrovnávání zatížení (může být i v jiné podsíti), bude si moct vyžádat kód z VSTS git a nasadit ho do služby ASE s interním nástrojem pro vyrovnávání zatížení. Pokud si nechcete vytvořit vlastního agenta sestavení, budete muset použít systém kontinuální integrace (CI), který používá model vyžádání, například Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Tato doména se objevuje v profilu publikování aplikace a v okně portálu aplikace (**Přehled** > **Essentials** a také **Vlastnosti**). Pokud máte službu ASE s interním nástrojem pro vyrovnávání zatížení se subdoménou *contoso.net* a aplikaci s názvem *test*, použijte adresu *mytest.contoso.net* pro službu FTP a adresu *mytest.scm.contoso.net* pro nasazení webu.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Spojení služby ASE s interním nástrojem pro vyrovnávání zatížení se zařízením WAF ##

Služba Azure App Service poskytuje řadu prvků zabezpečení, které chrání systém. Kromě toho pomáhají určit, jestli některou aplikaci napadl hacker. Nejlepší ochranu webové aplikace poskytuje spojení hostované platformy, jako je Azure App Service, s bránou firewall webových aplikací (WAF). Služba ASE s interním nástrojem pro vyrovnávání zatížení má koncový bod aplikace izolovaný od internetu, takže se je tomuto účelu vhodná.

Další informace o konfiguraci služby ASE s interním nástrojem pro vyrovnávání zatížení pro zařízení WAF najdete v tématu [Konfigurace brány firewall webových aplikací ve službě App Service Environment][ASEWAF]. Tento článek vysvětluje, jak ve službě ASE používat virtuální zařízení Barracuda. Další možností je použít službu Azure Application Gateway. Služba Application Gateway dokáže pomocí základních pravidel OWASP zabezpečit všechny aplikace, které za ni umístíte. Další informace o službě Application Gateway najdete v tématu [Úvod do brány firewall webových aplikací Azure][AppGW].

## <a name="get-started"></a>Začínáme ##

* Pokud chcete začít používat službu ASE, prostudujte si téma [Úvod do služby App Service Environment][Intro].
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
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
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
