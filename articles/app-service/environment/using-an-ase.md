---
title: Použití prostředí App Service – Azure
description: Jak vytvářet, publikovat a škálovat aplikace v prostředí Azure App Service
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cabefcc53106a53459975fc26513dc59ae7d3372
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073213"
---
# <a name="use-an-app-service-environment"></a>Použití App Serviceho prostředí #

Azure App Service Environment je nasazení Azure App Service do podsítě ve virtuální síti Azure zákazníka. Skládá se z těchto:

- **Front-endy**: Front-endy končí, když se HTTP/HTTPS ukončí v prostředí App Service (pomocného mechanismu).
- **Pracovní procesy**: Pracovní procesy jsou prostředky, které hostují vaše aplikace.
- **Databáze**: Databáze obsahuje informace, které definují prostředí.
- **Úložiště**: Úložiště se používá k hostování aplikací publikovaných zákazníky.

> [!NOTE]
> Existují dvě verze App Service Environment: ASEv1 a ASEv2. V ASEv1 je nutné prostředky spravovat, aby je bylo možné použít. Informace o tom, jak nakonfigurovat a spravovat ASEv1, najdete v tématu [Konfigurace prostředí App Service v1][ConfigureASEv1]. Zbývající část tohoto článku se zaměřuje na ASEv2.
>
>

Pro přístup k aplikaci můžete nasadit pomocného pomocného programu (ASEv1 a ASEv2) s externí nebo interní VIP. Nasazení s externí virtuální IP adresou se běžně označuje jako externí pomocný objekt pro pořízení. Interní verze se nazývá interního nástroje pomocného nástroje pro vyrovnávání zatížení, protože používá interní nástroj pro vyrovnávání zatížení (interního nástroje). Další informace o pomocném mechanismu pro interního nástroje najdete v tématu [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Vytvoření aplikace v pomocném mechanismu služby ##

Pokud chcete vytvořit aplikaci v pomocném mechanismu řízení, použijte stejný proces jako při normálním vytvoření, ale s malým rozdílem. Když vytváříte nový plán App Service:

- Místo výběru geografického umístění, do kterého chcete aplikaci nasadit, si jako umístění zvolíte pomocného objekt pro vytváření.
- Všechny plány App Service vytvořené v pomocném objektu pro vytváření se musí nacházet v izolované cenové úrovni.

Pokud nemáte pomocného mechanismu řízení, můžete ho vytvořit podle pokynů v tématu [vytvoření App Serviceho prostředí][MakeExternalASE].

Vytvoření aplikace v pomocném mechanismu řízení:

1. Vyberte **vytvořit prostředek** > **web a mobilní zařízení** > **webovou aplikaci**.

2. Zadejte název aplikace. Pokud jste už v pomocném mechanismu řízení vybrali plán App Service, bude název domény pro aplikaci odpovídat názvu domény pomocného mechanismu.

    ![Výběr názvu aplikace][1]

1. Vyberte předplatné.

1. Zadejte název nové skupiny prostředků nebo vyberte použít existující a v rozevíracím seznamu vyberte **použít existující** a vyberte jednu z nich.

1. Vyberte váš operační systém. 

1. Pomocí následujících kroků vyberte existující plán App Service, nebo vytvořte nový.

    a. Vyberte **vytvořit novou**.

    b. Zadejte název plánu App Service.

    c. V rozevíracím seznamu **umístění** vyberte svůj pomocný modul pro čtení. 
    
    d. Vyberte **izolovanou** cenovou úroveň. Vyberte **vyberte**.

    e. Vyberte **OK**.
    
    ![Izolované cenové úrovně][2]

    > [!NOTE]
    > Aplikace pro Linux a aplikace pro Windows nemůžou být ve stejném plánu App Service, ale můžou být ve stejném App Service Environment. 
    >

2. Vyberte **Vytvořit**.

## <a name="how-scale-works"></a>Jak funguje škálování ##

Každá aplikace App Service běží v plánu App Service. Model kontejnerů je prostředí, ve kterém jsou App Service plány, a App Service plány uchovávají aplikace. Při škálování aplikace můžete škálovat App Service plán, a tak škálovat všechny aplikace ve stejném plánu.

V ASEv2 se při škálování plánu App Service automaticky přidá požadovaná infrastruktura. Při přidávání infrastruktury je časová prodleva pro škálování operací. V ASEv1 je nutné přidat potřebnou infrastrukturu, aby bylo možné vytvořit nebo škálovat plán App Service. 

Ve víceklientské App Service je škálování obvykle okamžité, protože fond prostředků je snadno dostupný pro jeho podporu. V pomocném mechanismu služby neexistuje žádná taková vyrovnávací paměť a prostředky se přidělují podle potřeby.

V pomocném mechanismu služby můžete škálovat až 100 instancí. Tyto instance 100 můžou být všechny v jednom App Service plánování nebo distribuované napříč několika App Service plány.

## <a name="ip-addresses"></a>IP adresy ##

App Service má možnost pro aplikaci přidělit vyhrazenou IP adresu. Tato funkce je k dispozici po konfiguraci protokolu SSL založeného na protokolu IP, jak je popsáno v tématu [vytvoření vazby existujícího vlastního certifikátu SSL na Azure App Service][ConfigureSSL]. V pomocném mechanismu může však být významná výjimka. Nemůžete přidat další IP adresy, které se použijí pro protokol SSL založený na protokolu IP v interního nástroje pomocném mechanismu pro čtení.

V ASEv1 je potřeba přidělit IP adresy jako prostředky, aby je bylo možné použít. V ASEv2 je budete používat z vaší aplikace stejně jako ve víceklientské App Service. V ASEv2 je vždy jedna náhradní adresa až 30 IP adres. Pokaždé, když ho použijete, přidá se další, aby byla adresa vždycky snadno dostupná pro použití. K přidělení další IP adresy se vyžaduje časová prodleva, která zabrání v rychlém úspěchu přidávání IP adres.

## <a name="front-end-scaling"></a>Škálování front-endu ##

Při horizontálním navýšení kapacity App Service se pracovní procesy přidávají do ASEv2, které je budou podporovat. Všechny pomocné uživatelské rozhraní se vytvoří se dvěma front-endy. Kromě toho přední konec automaticky škáluje na jeden front-end pro každých 15 instancí v rámci plánů App Service. Pokud máte například 15 instancí, pak máte tři front-endy. Pokud budete škálovat na 30 instancí, budete mít čtyři přední konce a tak dále.

Tento počet front-endu by měl být pro většinu scénářů dostatečný. Horizontální navýšení kapacity ale můžete škálovat rychleji. Poměr můžete změnit na tak nízké jako jeden front-end pro každé pět instancí. Změna poměru je zpoplatněná. Další informace najdete v tématu [Azure App Service ceny][Pricing].

Prostředky front-endu jsou koncovým bodem HTTP/HTTPS pro pomocného bodu služby. S výchozí konfigurací front-endu je využití paměti na front-endu konzistentně v rozmezí od 60%. Úlohy zákazníků neběží na front-endu. Klíčovým faktorem pro front-end s ohledem na škálování je procesor, který je řízen primárně pomocí přenosu HTTPS.

## <a name="app-access"></a>Přístup k aplikacím ##

V externím pomocném mechanismu pro vytváření se doména, která se používá při vytváření aplikací, liší od víceklientské App Service. Obsahuje název pomocného mechanismu. Další informace o tom, jak vytvořit externí pomocného mechanismu řízení, najdete v tématu [vytvoření App Serviceho prostředí][MakeExternalASE]. Název domény v externím pomocném objektu pro řízení vypadá takto *.&lt; asename&gt;. p.azurewebsites.NET*. Pokud se například váš přístup k pojmenování nazývá _External-_ prohlášeno a v tomto pomocném mechanismu je hostitelem aplikace s názvem _Contoso_ , dostanete se na následující adresy URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Adresa URL contoso.scm.external-ase.p.azurewebsites.net se používá pro přístup ke konzole Kudu nebo k publikování aplikace pomocí nástroje nasazení webu. Informace o konzole Kudu naleznete v tématu [Konzola Kudu pro Azure App Service][Kudu]. Konzola Kudu poskytuje webové uživatelské rozhraní pro ladění, nahrávání souborů, úpravy souborů a mnoho dalšího.

V interního nástroje pomocném modulu pro pořízení se určí doména v době nasazení. Další informace o tom, jak vytvořit interního nástroje pomocného mechanismu pro vytváření, najdete v tématu [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE]. Pokud zadáte název domény _ILB-ase.info_, aplikace v tomto pomocném formuláři tuto doménu použijí během vytváření aplikace. Pro aplikaci s názvem _Contoso_jsou adresy URL:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publikování ##

Stejně jako u víceklientské App Service můžete v pomocném programu publikovat pomocí:

- Nasazení webu.
- ADRESA.
- Průběžná integrace.
- Přetáhněte konzolu Kudu.
- Rozhraní IDE, jako je například Visual Studio, zatmění nebo IntelliJ nápad.

U externího pomocného mechanismu služby se tyto možnosti publikování chovají stejně. Další informace najdete v tématu [nasazení v Azure App Service][AppDeploy]. 

Hlavní rozdíl mezi publikováním je v souvislosti s interního nástroje pomocným mechanismem řízení. Pomocí pomocného mechanismu pro interního nástroje jsou všechny koncové body publikování dostupné jenom prostřednictvím interního nástroje. INTERNÍHO nástroje se nachází v privátní IP adrese v podsíti pro pomocné službě ve virtuální síti. Pokud nemáte přístup k síti interního nástroje, nemůžete v tomto pomocném panelu publikovat žádné aplikace. Jak je uvedeno v části [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE], musíte nakonfigurovat DNS pro aplikace v systému. Který zahrnuje koncový bod SCM. Pokud nejsou správně definované, nemůžete publikovat. K tomu, aby bylo možné publikovat přímo do služby interního nástroje, musí mít i vaše IDEs přístup k síti.

Nemusíte používat internetové systémy CI, jako je GitHub a Azure DevOps, s pomocným mechanismem interního nástroje, protože koncový bod publikování není dostupný na internetu. V případě Azure DevOps to můžete obejít tak, že v interní síti nainstalujete samoobslužného agenta pro hostování, ve kterém se může spojit s interního nástroje. Alternativně můžete také použít systém CI, který využívá model Pull, jako je třeba Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Můžete ji zobrazit v publikačním profilu aplikace a v okně portálu aplikace (v části **Přehled** > **Essentials** a také ve **vlastnostech**). 

## <a name="pricing"></a>Ceny ##

SKU ceny s názvem **Isolated** byla vytvořena pouze pro použití s ASEv2. Všechny plány App Service hostované v ASEv2 jsou v izolované cenové SKU. Tarify plánu izolované App Service se můžou v jednotlivých oblastech lišit. 

Kromě ceny za vaše App Service plány existuje paušální sazba pro samotný správce. Paušální míra se nemění podle velikosti vašeho pomocného mechanismu pro čtení a platí pro infrastrukturu pomocného programu s výchozí mírou škálování 1 dalších front-endu pro každých 15 App Servicech plánů.  

Pokud výchozí míra škálování 1 front-endu pro každých 15 App Servicech instancí plánu není dostatečně rychlá, můžete upravit poměr, ve kterém jsou přidané přední konce, nebo velikost front-endy.  Při úpravách poměru nebo velikosti platíte za základní jádra, která se ve výchozím nastavení nepřidala.  

Pokud například upravíte poměr stupnice na hodnotu 10, přidá se front-end pro každých 10 instancí ve vašich App Servicech plánech. Paušální poplatek pokrývá míru škálování jednoho front-endu pro každých 15 instancí. S poměrem stupnice 10 se platíte za třetí front-end, který je přidaný pro 10 App Servicech plánů. Nemusíte platit za to, když máte víc než 15 instancí, protože se přidalo automaticky.

Pokud jste upravili velikost front-endu na 2 jádra, ale neupravíte poměr, platíte za další jádra.  Pomocného programu se vytvoří se dvěma front-endy, takže i pod prahovou hodnotou automatického škálování, která by platila za 2, pokud jste zvýšili velikost na 2 jádra front-endy.

Další informace najdete v tématu [Azure App Service ceny][Pricing].

## <a name="delete-an-ase"></a>Odstranění pomocného mechanismu ##

Postup odstranění pomocného mechanismu řízení: 

1. V horní části okna **App Service Environment** použijte **Odstranit** . 

1. Zadejte název pomocného programu pro potvrzení, že ho chcete odstranit. Když odstraníte pomocného mechanismu řízení, odstraníte i jeho obsah. 

    ![Odstranění pomocného mechanismu][3]

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
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
