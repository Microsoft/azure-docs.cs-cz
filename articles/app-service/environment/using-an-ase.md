---
title: Pomocí služby Azure App Service environment
description: Jak vytvářet, publikovat a škálování aplikací v Azure App Service environment
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 66ef20616df77dc809a79e516a53133a80759dc7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355300"
---
# <a name="use-an-app-service-environment"></a>Použití služby App Service environment #

## <a name="overview"></a>Přehled ##

Azure App Service Environment je nasazení služby Azure App Service na podsíť ve virtuální síti Azure zákazníka. Obsahuje:

- **Front-končí**: skončení front jsou, kde protokolu HTTP nebo HTTPS ukončí ve službě App Service environment (App Service Environment).
- **Pracovníci**: zaměstnanci jsou prostředky, které umožňuje hostování vašich aplikací.
- **Databáze**: databáze obsahuje informace, které definují prostředí.
- **Úložiště**: úložiště se používá k hostování aplikace publikována zákazníka.

> [!NOTE]
> Existují dvě verze App Service Environment: ASEv1 a ASEv2. V ASEv1 musí spravovat prostředky, abyste mohli používat. Další informace o konfiguraci a správě ASEv1 naleznete v tématu [konfigurace v1 prostředí App Service][ConfigureASEv1]. Zbývající část tohoto článku se zaměřuje na ASEv2.
>
>

App Service Environment (ASEv1 a ASEv2) můžete nasadit vnější nebo vnitřní VIP pro přístup k aplikaci. Nasazení s externí VIP se obvykle nazývá externí App Service Environment. Interní verze se nazývá App Service Environment ILB, protože používá interní nástroj (ILB). Chcete-li další informace o App Service Environment ILB, přečtěte si téma [vytvoření a použití App Service Environment ILB][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Vytvoření webové aplikace v App Service Environment ##

K vytvoření webové aplikace v App Service Environment, použít stejný postup jako při jeho vytváření normálně, ale s několika malé rozdíly. Když vytvoříte nový plán služby App Service:

- Místo výběru zeměpisného umístění, ve které chcete nasadit aplikaci, vyberte App Service Environment jako vaše umístění.
- Všechny plány služby App Service, které jsou vytvořené v App Service Environment musí být v izolovaná cenová úroveň.

Pokud nemáte App Service Environment, můžete vytvořit pomocí následujících pokynů v [vytvoření služby App Service environment][MakeExternalASE].

K vytvoření webové aplikace v App Service Environment:

1. Vyberte **vytvořit prostředek** > **Web + mobilní** > **webové aplikace**.

2. Zadejte název webové aplikace. Pokud jste již vybrali plán aplikační služby App Service Environment, název domény pro aplikace zobrazuje název domény App Service Environment.

    ![Výběr názvu webové aplikace][1]

3. Vyberte předplatné.

4. Zadejte název pro novou skupinu prostředků, nebo vyberte **použít existující** a vyberte z rozevíracího seznamu.

5. Vyberte váš operační systém. 

    * Hostování Linux aplikace v App Service Environment je nová funkce preview, takže doporučujeme Linux aplikace nepřidávejte do app Service Environment právě probíhající úlohy v produkčním prostředí. 
    * Přidání Linux aplikace do app Service Environment znamená, že App Service Environment bude také v režimu preview. 

5. Vybrat existující plán aplikační služby ve vaší App Service Environment, nebo vytvořte novou pomocí následujících kroků:

    a. Vyberte **vytvořit nový**.

    b. Zadejte název pro plán služby App Service.

    c. Vyberte vaše App Service Environment ve **umístění** rozevíracího seznamu. Hostování Linux aplikace v App Service Environment je povolena pouze v oblastech, 6, v tuto chvíli: **západní Evropa, západní USA, Východ USA, Severní Evropa, Austrálie – východ, Asie a Tichomoří – jihovýchod.** 

    d. Vyberte **izolovaná** cenová úroveň. Vyberte **vyberte**.

    e. Vyberte **OK**.
    
    ![Izolované cenové úrovně][2]

    > [!NOTE]
    > Linux webové aplikace a webové aplikace pro Windows, nelze stejný plán služby App Service, ale může být ve stejné App Service Environment. 
    >

6. Vyberte **Vytvořit**.

## <a name="how-scale-works"></a>Jak škálování funguje ##

Všechny aplikace služby App Service běží v plán služby App Service. Kontejner modelu je prostředí podržte plány služby App Service a plány služby App Service uchování aplikace. Při změně měřítka aplikace, můžete škálovat plán služby App Service a proto škálování všechny aplikace v stejný plán.

V ASEv2 když škálovat plán služby App Service potřebné infrastruktury je automaticky přidán. Při přidání infrastruktury je prodlevu pro operace škálování. Potřebné infrastruktury v ASEv1, je nutné přidat před vytvořením nebo škálovat plán služby App Service. 

Škálování je obvykle okamžitou ve víceklientské služby App Service, protože fond zdrojů je snadno dostupné pro její podporu. App Service Environment neexistuje žádný takový vyrovnávací paměti a prostředky se přidělují při nutné.

V App Service Environment můžete škálovat instance až 100. Ty 100 instance může být v jedné jednoho plánu služby App Service nebo rozdělené mezi více plány služby App Service.

## <a name="ip-addresses"></a>IP adresy ##

Služby App Service má schopnost přidělit vyhrazenou IP adresu do aplikace. Tato funkce je dostupná, po dokončení konfigurace SSL založenou na protokolu IP, jak je popsáno v [vazby stávající vlastní certifikát SSL pro službu Azure web apps][ConfigureSSL]. V App Service Environment, existuje však významné výjimky. Nelze přidat další IP adres, který se má použít pro protokolem SSL založenou na protokolu IP v App Service Environment ILB.

ASEv1 je nutné přidělit IP adresy jako prostředky, abyste mohli používat. V ASEv2 je použít z aplikace stejným způsobem jako v víceklientské služby App Service. Je vždy jedna adresa k výměně za chodu v ASEv2 až 30 IP adresy. Pokaždé, když používáte jednu, jiné se přidá tak, aby vždy snadno dostupné pro použití adresu. Době zpoždění je potřeba přidělit jinou IP adresu, což zabraňuje přidání IP adres rychle po sobě.

## <a name="front-end-scaling"></a>Škálování front-endu ##

V ASEv2 při škálování plánu služby App Service, pracovníci se automaticky přidají do je podporují. Každý App Service Environment se vytvoří s dva elementy front end. Kromě toho před končí automaticky horizontální navýšení kapacity s rychlostí jeden front-endu pro každých 15 instance v plánu služby App Service. Například pokud máte instancí 15, pak máte tři front-end. Pokud je škálovat na 30 instance, pak máte čtyři front-elementy end a tak dále.

Tento počet elementů front end musí být víc než dost pro většinu scénářů. Však můžete škálovat s vyšší rychlostí. Poměr nedostatek jeden vpředu ukončit pro každých pět instancí, můžete změnit. Není k dispozici zdarma pro změnu poměr. Další informace najdete v tématu [Azure App Service – ceny][Pricing].

Koncový bod HTTP nebo HTTPS pro App Service Environment jsou front-endové prostředky. S konfigurací front-end výchozí je využití paměti na front-endu konzistentně přibližně 60 procent. Na front-end nejsou spouštěny úloh zákazníka. Klíčovým faktorem pro front-end s ohledem na škálování je procesor, který doprovází především komunikaci přes protokol HTTPS.

## <a name="app-access"></a>Přístup k aplikacím ##

Externí App Service Environment se liší od víceklientské služby App Service domény, který se používá při vytváření aplikace. Obsahuje název App Service Environment. Další informace o tom, jak vytvořit externí App Service Environment najdete v tématu [vytvoření služby App Service environment][MakeExternalASE]. Název domény externího App Service Environment vypadá jako *.&lt; asename&gt;. p.azurewebsites.net*. Například pokud je název vaší App Service Environment _externí App Service Environment_ a hostování aplikace volá _contoso_ v tom, že App Service Environment, dostanete se na následující adresy URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Adresa URL contoso.scm.external-ase.p.azurewebsites.net slouží k přístupu ke konzole Kudu nebo pro publikování aplikace pomocí webového nasazení. Informace v konzole Kudu najdete v tématu [konzoly Kudu pro službu Azure App Service][Kudu]. Konzole Kudu poskytuje webového uživatelského rozhraní pro ladění, nahrávání souborů, úpravy souborů a mnoho dalšího.

V App Service Environment ILB určíte domény v době nasazení. Další informace o tom, jak vytvořit App Service Environment ILB najdete v tématu [vytvoření a použití App Service Environment ILB][MakeILBASE]. Pokud zadáte název domény _ilb ase.info_, aplikace v tomto App Service Environment používala tuto doménu během vytváření aplikace. Pro aplikaci s názvem _contoso_, se adresy URL:

- contoso.ilb ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publikování ##

Stejně jako u víceklientské aplikace služby App Service Environment můžete publikovat pomocí:

- Nasazení webu.
- FTP.
- Nepřetržité integrace.
- Přetáhnout myší v konzole Kudu.
- IDE, jako je například Visual Studio, Eclipse nebo IntelliJ IDEA.

S externí App Service Environment tyto možnosti publikování všech chovají stejně. Další informace najdete v tématu [nasazení v Azure App Service][AppDeploy]. 

Hlavní rozdíl oproti publikování je s ohledem na ILB App Service Environment. S ILB App Service Environment jsou všechny dostupné pouze prostřednictvím ILB publikování koncových bodů. ILB je v privátní IP v podsíti App Service Environment ve virtuální síti. Pokud nemáte přístup k síti pro ILB, nelze publikovat všechny aplikace na tomto App Service Environment. Jak jsme uvedli v [vytvoření a použití App Service Environment ILB][MakeILBASE], musíte nakonfigurovat DNS pro aplikace v systému. Koncový bod SCM, který zahrnuje. Pokud nejsou definovány správně, nelze publikovat. Vaše integrovaného vývojového prostředí je také třeba mít přístup k síti pro ILB Chcete-li publikovat přímo na.

Internetové systémy položek konfigurace, například Githubu a Visual Studio Team Services, nefungují s ILB App Service Environment, protože publikování koncový bod není dostupný na Internetu. Místo toho je potřeba použít systém kontinuální integrace, který používá model na vyžádání, jako je Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Zobrazí se v profilu publikování aplikace a v okně portálu aplikace (v **přehled** > **Essentials** a také v **vlastnosti**). 

## <a name="pricing"></a>Ceny ##

Ceny názvem SKU **izolovaná** vytvořený pro použití pouze s ASEv2. Všechny plány služby App Service, které jsou hostované v ASEv2 jsou v izolované ceny SKU. Izolované sazby plán služby App Service se může lišit podle oblastí. 

Kromě ceny pro váš plán služby App Service je míra dvojrozměrném pro App Service Environment, sám sebe. Rychlost, jakou dvojrozměrném nemění velikost vašeho App Service Environment a platí za App Service Environment infrastruktury na výchozí počet 1 Další škálování front-endu pro každých 15 instancích plánu služby App Service.  

Pokud není dostatečně Rychlé škálování výchozí počet 1 front-endu pro každých 15 instancích plánu služby App Service, můžete upravit poměr, na které front-end se přidají nebo velikost front-end.  Při změně poměr nebo velikost, platí pro front-endu jádra, které by ve výchozím nastavení přidat.  

Například pokud nastavíte měřítko zvětšení na 10, front-end přidá se pro každých 10 instancí v plánu služby App Service. Ploché poplatek zahrnuje škálování výši jeden front-endu pro každých 15 instance. S poměrem škálování 10 platí poplatek za třetí front-end, který se přidá pro 10 instancích plánu služby App Service. Nemusíte platit pro něj, když se dostanete 15 instance, protože byl přidán automaticky.

Pokud velikost Nastaví front-end na 2 jádra, ale neměnit poměr pak platí pro další jader.  App Service Environment se vytvoří s 2 front-end, tak i pod automatické škálování prahovou hodnotou, bude platit za 2 navíc jádra Pokud zvýšil na velikost 2 jádra front-end.

Další informace najdete v tématu [Azure App Service – ceny][Pricing].

## <a name="delete-an-ase"></a>Odstranit App Service Environment ##

Chcete-li odstranit App Service Environment: 

1. Použití **odstranit** v horní části **App Service Environment** okno. 

2. Zadejte název vaší App Service Environment potvrďte, že chcete odstranit. Pokud odstraníte App Service Environment, je odstranit veškerý obsah v něm také. 

    ![Odstranění App Service Environment][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
