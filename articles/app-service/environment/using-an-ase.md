---
title: Použití služby Azure App Service environment
description: Jak vytvářet, publikovat a škálovat aplikace ve službě Azure App Service environment
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
ms.openlocfilehash: b3550c771b4c2916987c66f318010e5bb246fa39
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446846"
---
# <a name="use-an-app-service-environment"></a>Pomocí služby App Service environment #

## <a name="overview"></a>Přehled ##

Azure App Service Environment je nasazení služby Azure App Service do podsítě ve virtuální síti Azure zákazníka. Skládá se z:

- **Front-endů**: front-endů jsou, kde končí HTTP/HTTPS ve službě App Service environment (ASE).
- **Pracovní procesy**: zaměstnanci jsou prostředky, které jsou hostiteli vaše aplikace.
- **Databáze**: databáze obsahuje informace, které definují prostředí.
- **Úložiště**: úložiště se používá k hostování aplikací publikovaných zákazníka.

> [!NOTE]
> Existují dvě verze služby App Service Environment: ASEv1 a ASEv2. Ve verzi ASEv1 musí spravovat prostředky, abyste mohli používat. Zjistěte, jak konfigurovat a spravovat ASEv1, najdete v článku [konfigurace App Service environment v1][ConfigureASEv1]. Zbývající část tohoto článku se zaměřuje na ASEv2.
>
>

Nasazení prostředí ASE (ASEv1 a ASEv2) externí nebo interní virtuální IP adresu pro přístup k aplikaci. Nasazení s externí virtuální IP Adresou se tomu říká externí služby ASE. Interní verze se nazývá služba ASE s ILB, protože používá interní nástroj pro vyrovnávání zatížení (ILB). Další informace o služba ASE s ILB, naleznete v tématu [vytvoření a použití prostředí ILB ASE][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Vytvoření webové aplikace ve službě ASE ##

Vytvoření webové aplikace ve službě ASE, použít stejně jako když vytvoříte obvykle ale několik malých rozdílů. Když vytvoříte nový plán služby App Service:

- Místo zeměpisné umístění, ve které se má nasadit vaše aplikace, zvolte jako umístění služby ASE.
- Všechny plány služby App Service vytvoří ve službě ASE musí být v izolované cenové úrovně.

Pokud nemáte službu ASE, můžete vytvořit podle pokynů v [vytvoření služby App Service environment][MakeExternalASE].

Vytvoření webové aplikace ve službě ASE:

1. Vyberte **vytvořit prostředek** > **Web + mobilní zařízení** > **webovou aplikaci**.

1. Zadejte název webové aplikace. Pokud jste již vybrali plán služby App Service ve službě ASE, název domény pro aplikace odráží název domény služby ase.

    ![Výběr názvu webové aplikace][1]

1. Vyberte předplatné.

1. Zadejte název pro novou skupinu prostředků nebo vyberte **použít existující** a vyberte jednu z rozevíracího seznamu.

1. Vyberte váš operační systém. 

    * Hostování aplikace pro Linux v ASE je nová funkce ve verzi preview, takže doporučujeme nepřidávejte Linuxových aplikací do služby ASE, na kterém aktuálně běží úlohy v produkčním prostředí. 
    * Přidání Linuxovou aplikaci do služby ASE znamená, že služba ASE bude také v režimu náhledu. 

1. Vyberte existující plán služby App Service ve vaší službě ASE nebo vytvořte novou pomocí následujících kroků:

    a. Vyberte **vytvořit nový**.

    b. Zadejte název plánu služby App Service.

    c. Vyberte svoji službu ASE v **umístění** rozevíracího seznamu. Hostování aplikace pro Linux v ASE je povolená jenom v 6 oblastech, v tuto chvíli: **západní USA, východní USA, západní Evropa, Severní Evropa, Austrálie – východ, Asie – jihovýchod.** 

    d. Vyberte **izolované** cenovou úroveň. Vyberte **vyberte**.

    e. Vyberte **OK**.
    
    ![Izolované cenové úrovně][2]

    > [!NOTE]
    > Linuxové webové aplikace a webové aplikace Windows nemůžou být ve stejném plánu služby App Service, ale může být ve stejné službě App Service Environment. 
    >

1. Vyberte **Vytvořit**.

## <a name="how-scale-works"></a>Jak škálovat funguje ##

Každá aplikace služby App Service běží v plánu služby App Service. Kontejner modelu je prostředí podržte plány služby App Service a plány služby App Service uchování aplikace. Při škálování aplikace škálovat plán služby App Service a proto škálování všechny aplikace v rámci stejného plánu.

V ASEv2 když horizontálně snížíte kapacitu plánu služby App Service potřeby infrastruktury se automaticky přidá. Když se přidá infrastruktury je časovou prodlevu ke škálování operací. Ve verzi ASEv1 potřebné infrastruktury je nutné přidat před vytvořením nebo horizontální navýšení kapacity plánu služby App Service. 

Škálování je obvykle okamžité ve víceklientské službě App Service, protože fond zdrojů je snadno k dispozici pro její podporu. Ve službě ASE neexistuje žádný takový vyrovnávací paměti a prostředky se přidělují na potřebu.

Ve službě ASE je možné škálovat až 100 instancí. Tyto 100 instancí může být v jedné jeden plán služby App Service nebo distribuovány napříč více plánů služby App Service.

## <a name="ip-addresses"></a>IP adresy ##

App Service má schopnost přidělit vyhrazenou IP adresu do aplikace. Tato funkce je dostupná, když nakonfigurujete SSL na základě IP adresy, jak je popsáno v [vytvoření vazby existujícího vlastního certifikátu SSL k Azure web apps][ConfigureSSL]. Ve službě ASE, existuje ale významné výjimky. Nelze přidat další IP adresy se použije pro založené na protokolu IP SSL ve službě ASE s ILB.

V verzi ASEv1 je potřeba přidělit IP adresy jako prostředky, abyste mohli používat. V ASEv2 použijete je z vaší aplikace stejným způsobem jako ve víceklientské službě App Service. Je vždycky náhradních adres v ASEv2 až 30 IP adresy. Pokaždé, když použijete jednu, jiné se přidá tak, aby vždy snadno k dispozici pro použití adresy. Čas, zpoždění je nutné přidělit jinou IP adresu, který zabraňuje přidávání IP adresy rychle po sobě.

## <a name="front-end-scaling"></a>Škálováním front-endu ##

ASEv2, když v horizontálním navýšením kapacity vašich plánech služby App Service pracovní procesy jsou automaticky přidá do jejich podporu. Každé služby ASE se vytvoří s dva front-endů. Kromě toho přední automaticky ukončena sazbou jeden front-endu pro každých 15 instance pro horizontální navýšení kapacity v plánech služby App Service. Například pokud máte 15 instancí, pak máte tři front-endů. Pokud převedete na 30 instancí, pak máte čtyři front-endů a tak dále.

Tento počet front-endů by měl být víc než dost pro většinu scénářů. Můžete však horizontální navýšení kapacity rychlejší. Můžete změnit poměr tak, aby jako jedna přední nízká ukončení pro každých pět instancí. Se účtuje poplatek pro změnu poměr. Další informace najdete v tématu [Azure App Service – ceny][Pricing].

Front-endové prostředky jsou koncových bodů HTTP/HTTPS pro danou službu ASE. Ve výchozí konfiguraci front-endu je využití paměti na front-endu konzistentně přibližně 60 procent. Úloh zákazníka nejsou spuštěné na front-endu. Klíčovým faktorem pro front-endu s ohledem na škálování je využití procesoru, který doprovází primárně komunikaci přes protokol HTTPS.

## <a name="app-access"></a>Přístup k aplikacím ##

V externí služby ASE domény, který se používá při vytváření aplikací se liší od víceklientské službě App Service. Obsahuje název služby ASE. Další informace o tom, jak vytvořit externí služby ASE najdete v tématu [vytvoření služby App Service environment][MakeExternalASE]. Název domény ve službě ASE s externí bude vypadat jako *.&lt; asename&gt;. p.azurewebsites.net*. Například, pokud má vaše služba ASE název _externí služby ase_ a hostovat aplikace s názvem _contoso_ v tom, že služba ASE, můžete k němu přistoupit na následující adresy URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Adresa URL contoso.scm.external-ase.p.azurewebsites.net se používá přístup ke konzole Kudu nebo pro tuto aplikaci publikovat aplikace pomocí webu nasadit. Informace o konzole Kudu, naleznete v tématu [konzola Kudu pro službu Azure App Service][Kudu]. Konzola Kudu umožňuje webového uživatelského rozhraní pro ladění, nahrávání souborů, souborů a mnohem víc úpravy.

Ve službě ASE s ILB určit doménu v době nasazení. Další informace o tom, jak vytvořit prostředí ILB ASE najdete v tématu [vytvoření a použití prostředí ILB ASE][MakeILBASE]. Pokud zadáte název domény _ilb ase.info_, aplikace v této službě ASE používala tuto doménu během vytváření aplikace. Pro aplikaci s názvem _contoso_, jsou adresy URL:

- contoso.ilb ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publikování ##

Stejně jako u víceklientské službě App Service ve službě ASE můžete publikovat pomocí:

- Nasazení webu.
- FTP.
- Průběžná integrace.
- Přetáhnout myší v konzole Kudu.
- Rozhraní IDE, jako je Visual Studio, Eclipse nebo IntelliJ IDEA

Tyto možnosti publikování všech s externí služby ASE se chovají stejně. Další informace najdete v tématu [nasazení ve službě Azure App Service][AppDeploy]. 

Hlavní rozdíl v publikování je pro službu ASE. S ILB ASE jsou všechny dostupné jenom prostřednictvím ILB koncové body pro publikování. ILB je na privátní IP adresa v podsíti služby ASE ve virtuální síti. Pokud nemáte přístup k síti na ILB, nelze publikovat všechny aplikace v této službě ASE. Jak je uvedeno v [vytvoření a použití prostředí ILB ASE][MakeILBASE], budete muset nakonfigurovat DNS pro aplikace v systému. Který obsahuje koncový bod správce řízení služeb. Pokud nejsou definované správně, nelze publikovat. Vaše Integrovaná vývojová prostředí také nutné mít přístup k síti na ILB, aby do něj publikovat přímo.

Internetové systémy kontinuální integrace, jako jsou GitHub a Visual Studio Team Services, nefungují s ILB ASE, protože koncový bod publikování není přístup k Internetu. Místo toho je potřeba použít systém kontinuální integrace, který používá model na vyžádání, jako je Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Zobrazí se v profilu publikování aplikace a v okně portálu aplikace (v **přehled** > **Essentials** a také v **vlastnosti**). 

## <a name="pricing"></a>Ceny ##

Ceny SKU volá **izolované** byl vytvořen pouze pro použití s ASEv2. Všechny plány služby App Service, které jsou hostované v ASEv2 jsou v s izolovanou cenou SKU. Izolované sazby plánů služby App Service se může lišit podle oblasti. 

Kromě ceny pro vaše plány služby App Service je paušální sazbou za službu ASE, samotného. Paušální sazba. nezáleží nemění podle velikosti služby ASE a platí pro infrastrukturu služby ASE na výchozí počet 1 Další škálování front-endu pro každých 15 instancí plánu služby App Service.  

Pokud škálovací výchozí počet 1 front-endu pro každých 15 instancí plánu služby App Service není dostatečně rychle, můžete upravit poměr, na které front endů se přidají nebo velikost front endů.  Když upravíte poměr nebo velikost, platíte za front-endu jader, které by ve výchozím nastavení přidán.  

Například pokud nastavíte poměr měřítka až 10, front-endu se přidá pro každých 10 instancí v plánech služby App Service. Pokrývá paušální poplatek mírou škálování jednoho front-endu pro každých 15 instance. S poměrem škálování 10 zaplatit poplatek, třetí front-endu, který je přidán k 10 instancí plánu služby App Service. Není potřeba dál za něj platit kontaktovat 15 instancí, protože byl automaticky přidán.

Je-li upravit velikost front endů do 2 jádra, ale ne upravit poměr pak platíte za další jádra.  Služba ASE se vytvoří s 2 front endů, takže i pod automatické škálování prahovou hodnotou, kterou zaplatíte 2 navíc jádra, je-li zvýšit velikost na 2 jádra front endů.

Další informace najdete v tématu [Azure App Service – ceny][Pricing].

## <a name="delete-an-ase"></a>Odstranit prostředí ASE. ##

Pokud chcete odstranit službu ASE: 

1. Použití **odstranit** v horní části **služby App Service Environment** okno. 

1. Zadejte název vaší služby ASE k potvrzení, že chcete odstranit. Když odstraníte službu ASE, je odstranit veškerý obsah v ní také. 

    ![Odstranění služby ASE][3]

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
