---
title: Použití a Správa App Service Environment
description: Jak vytvářet, publikovat a škálovat aplikace v prostředí Azure App Service. Najde běžné úkoly v jednom dokumentu.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565634"
---
# <a name="use-an-app-service-environment"></a>Použití App Serviceho prostředí #

App Service Environment (pomocného uživatele) je nasazení Azure App Service do podsítě ve virtuální síti Azure zákazníka. Pomocného programu se skládá z těchto:

- **Front-endy**: front-endy, kde se http/https ukončí v prostředí App Service.
- **Pracovní procesy**: pracovní procesy jsou prostředky, které hostují vaše aplikace.
- **Databáze**: databáze obsahuje informace, které definují prostředí.
- **Úložiště**: úložiště se používá k hostování aplikací publikovaných zákazníky.

Pro přístup k aplikaci můžete nasadit pomocného programu s externí nebo interní VIP. Nasazení s externí virtuální IP adresou se běžně označuje jako externí pomocný objekt pro pořízení. Interní verze se nazývá interního nástroje pomocného nástroje pro vyrovnávání zatížení, protože používá interní nástroj pro vyrovnávání zatížení (interního nástroje). Další informace o pomocném mechanismu pro interního nástroje najdete v tématu [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Vytvoření aplikace v pomocném mechanismu služby ##

Pokud chcete vytvořit aplikaci v pomocném mechanismu řízení, použijte stejný proces jako při normálním vytvoření, ale s malým rozdílem. Když vytváříte nový plán App Service (ASP):

- Místo výběru geografického umístění, do kterého chcete aplikaci nasadit, si jako umístění zvolíte pomocného objekt pro vytváření.
- Všechny plány App Service vytvořené v pomocném mechanismu řízení se můžou nacházet jenom v izolované cenové úrovni.

Pokud nemáte pomocného mechanismu řízení, můžete ho vytvořit podle pokynů v tématu [vytvoření App Serviceho prostředí][MakeExternalASE].

Vytvoření aplikace v pomocném mechanismu řízení:

1. Vyberte **vytvořit prostředek** > **web a mobilní zařízení** > **webovou aplikaci**.

2. Zadejte název aplikace. Pokud jste už v pomocném mechanismu řízení vybrali plán App Service, bude název domény pro aplikaci odpovídat názvu domény pomocného mechanismu.

    ![Výběr názvu aplikace][1]

1. Vyberte předplatné.

1. Zadejte název nové skupiny prostředků nebo vyberte použít existující a v rozevíracím seznamu vyberte **použít existující** a vyberte jednu z nich.

1. Vyberte váš operační systém. 

1. Pomocí následujících kroků vyberte existující plán App Service, nebo vytvořte nový.

    a. V nabídce Azure Portal levé straně vyberte **vytvořit prostředek > webovou aplikaci**.

    b. Vyberte předplatné.
    
    c. Vyberte nebo vytvořte skupinu prostředků.
    
    d. Zadejte název vaší webové aplikace.
    
    e. Vyberte kód nebo DockerContainer.
    
    f. Vyberte zásobník modulu runtime.
    
    g. Vyberte Linux nebo Windows. 
    
    h. V rozevíracím seznamu **oblast** vyberte svůj pomocný seznam. 
    
    i. Vyberte nebo vytvořte nový plán App Service. Pokud vytváříte nový plán App Service, vyberte odpovídající velikost **izolované** SKU.
    
    ![Izolované cenové úrovně][2]

    > [!NOTE]
    > Aplikace pro Linux a aplikace pro Windows nemůžou být ve stejném plánu App Service, ale můžou být ve stejném App Service Environment. 
    >

2. Vyberte možnost **zkontrolovat + vytvořit** a pak vyberte možnost **vytvořit** , pokud jsou informace správné.

## <a name="how-scale-works"></a>Jak funguje škálování ##

Každá aplikace App Service běží v plánu App Service. App Service prostředí uchovávají App Service plány a App Service plány uchovávají aplikace. Při škálování aplikace můžete škálovat App Service plán, a tak škálovat všechny aplikace ve stejném plánu.

Při škálování plánu App Service je požadovaná infrastruktura automaticky přidána. Při přidávání infrastruktury je časová prodleva pro škálování operací. Pokud provedete několik operací škálování v pořadí, na první požadavek na škálování infrastruktury se bude zpracovávat a ostatní se zařadí do fronty. Když se první operace škálování dokončí, ostatní požadavky na infrastrukturu požádají o veškerou spolupráci. Po přidání infrastruktury se App Service plány přiřazují podle potřeby. Vytvoření nového plánu App Service je sám operací škálování, protože žádá o další hardware. 

Ve víceklientské App Service je škálování okamžité, protože fond prostředků je snadno dostupný pro jeho podporu. V pomocném mechanismu služby neexistuje žádná taková vyrovnávací paměť a prostředky se přidělují podle potřeby.

V pomocném mechanismu služby můžete škálovat App Service plánu až 100 instancí. Pomocného programu může mít až 201 celkových instancí napříč všemi App Service plány, které jsou v pomocném modulu pro čtení. 

## <a name="ip-addresses"></a>IP adresy ##

App Service má možnost pro aplikaci přidělit vyhrazenou IP adresu. Možnost přidělení vyhrazené IP adresy aplikaci je k dispozici po konfiguraci protokolu SSL založeného na protokolu IP, jak je popsáno v tématu [vytvoření vazby existujícího vlastního certifikátu SSL na Azure App Service][ConfigureSSL]. V interního nástroje pomocném mechanismu pro čtení nemůžete přidat další IP adresy, které se použijí pro protokol SSL založený na protokolu IP.

Pomocí externího pomocného mechanismu služby můžete nakonfigurovat protokol SSL založený na protokolu IP pro vaši aplikaci stejným způsobem jako ve víceklientské App Service. V přihlášeném uživateli je vždy jedna volná adresa až 30 IP adres. Pokaždé, když ho použijete, přidá se další, aby byla adresa vždycky snadno dostupná pro použití. K přidělení další IP adresy se vyžaduje časová prodleva, která zabrání v rychlém úspěchu přidávání IP adres.

## <a name="front-end-scaling"></a>Škálování front-endu ##

Při horizontálním navýšení kapacity App Service plány se pracovní procesy přidají automaticky pro jejich podporu. Všechny pomocné uživatelské rozhraní se vytvoří se dvěma front-endy. Front-endové automatické horizontální navýšení kapacity se navzájem škálují v poměru jedna front-endy pro každých 15 App Servicech plánů. Pokud máte tři App Service plány s pěti instancemi, měli byste mít celkem 15 instancí a tři front-endy. Pokud budete škálovat na celkem 30 instancí, budete mít čtyři front-endy a tak dále. 

Počet front-endy, které jsou přiděleny ve výchozím nastavení, je vhodný pro střední zatížení. Poměr můžete změnit tak, aby byl pro každou pět instancí v jednom front-endy. Můžete také změnit velikost front-endy. Ve výchozím nastavení se jedná o jeden Core. Velikost front-endu na portálu můžete místo toho změnit na dvě nebo čtyři základní velikosti. Pro změnu poměru nebo velikosti front-endy se účtuje poplatek. Další informace najdete v tématu [Azure App Service ceny][Pricing]. Pokud chcete zlepšit kapacitu zatížení vašeho pomocného programu pro čtení, získáte lepší vylepšení při prvním škálování na dva základní front-endy před úpravou poměru stupnice. Změna základní velikosti front-endu způsobí upgrade pomocného mechanismu řízení a měla by se provádět mimo běžnou pracovní dobu.

Prostředky front-endy jsou koncovým bodem HTTP/HTTPS pro přístupový modul pro čtení. S výchozí konfigurací front-endu je využití paměti na front-endu konzistentně v rozmezí od 60%. Hlavním důvodem pro horizontální navýšení kapacity front-endu je procesor, který je primárně řízený přenosy protokolu HTTPS.

## <a name="app-access"></a>Přístup k aplikacím ##

V externím pomocném mechanismu pro vytváření aplikací je přípona domény používaná k vytvoření aplikace *.&lt;asename&gt;. p.azurewebsites.NET*. Pokud má váš pomocný přístup s názvem _External-prohlášený_ a Vy hostete aplikaci s názvem _Contoso_ v tomto pomocném mechanismu, dostanete se na následující adresy URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Další informace o tom, jak vytvořit externí pomocného mechanismu řízení, najdete v tématu [vytvoření App Serviceho prostředí][MakeExternalASE] .

V interního nástroje pomocném modulu pro vytváření aplikací je přípona domény používaná k vytvoření aplikace *.&lt;asename&gt;. appserviceenvironment.NET*. Pokud má váš správce _přihlášený s názvem interního nástroje-pomocného_ objektu a Vy hostte aplikaci s názvem _Contoso_ v tomto mechanismu pro čtení, dostanete se na následující adresy URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Další informace o tom, jak vytvořit interního nástroje pomocného mechanismu pro vytváření, najdete v tématu [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE]. 

Adresa URL SCM se používá pro přístup ke konzole Kudu nebo k publikování aplikace pomocí nástroje nasazení webu. Informace o konzole Kudu naleznete v tématu [Konzola Kudu pro Azure App Service][Kudu]. Konzola Kudu poskytuje webové uživatelské rozhraní pro ladění, nahrávání souborů, úpravy souborů a mnoho dalšího.

## <a name="publishing"></a>Publikování ##

Stejně jako u víceklientské App Service můžete v pomocném programu publikovat pomocí:

- Nasazení webu.
- Adresa.
- Průběžná integrace.
- Přetáhněte konzolu Kudu.
- Rozhraní IDE, jako je například Visual Studio, zatmění nebo IntelliJ nápad.

U externího pomocného mechanismu služby se tyto možnosti publikování chovají stejně. Další informace najdete v tématu [nasazení v Azure App Service][AppDeploy]. 

Hlavní rozdíl mezi publikováním je v souvislosti s interního nástroje pomocným mechanismem řízení. Pomocí pomocného mechanismu pro interního nástroje jsou všechny koncové body publikování dostupné jenom prostřednictvím interního nástroje. INTERNÍHO nástroje se nachází v privátní IP adrese v podsíti pro pomocné službě ve virtuální síti. Pokud nemáte přístup k síti interního nástroje, nemůžete v tomto pomocném panelu publikovat žádné aplikace. Jak je uvedeno v části [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE], musíte nakonfigurovat DNS pro aplikace v systému. Který zahrnuje koncový bod SCM. Pokud nejsou správně definované, nemůžete publikovat. K tomu, aby bylo možné publikovat přímo do služby interního nástroje, musí mít i vaše IDEs přístup k síti.

Nemusíte používat internetové systémy CI, jako je GitHub a Azure DevOps, s pomocným mechanismem interního nástroje, protože koncový bod publikování není dostupný na internetu. Publikování na interního nástroje pomocného mechanismu můžete povolit z Azure DevOps tím, že nainstalujete samoobslužného agenta pro hostování ve virtuální síti, který obsahuje pomocného mechanismu pro čtení interního nástroje. Alternativně můžete také použít systém CI, který využívá model Pull, jako je třeba Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Můžete ji zobrazit v publikačním profilu aplikace a v okně portálu aplikace (v tématu **přehled** > **základy** a také ve **vlastnostech**). 

## <a name="storage"></a>Úložiště

Pomocného programu má 1 TB úložiště pro všechny aplikace v rámci pomocného programu. Ve výchozím nastavení má App Service plán izolované SKU limit 250 GB. Pokud máte pět nebo více App Service plánů, musíte být opatrní, abyste nepřekročili limit 1 TB pomocného mechanismu pro řízení. Pokud v jednom App Service plánu požadujete limit větší než 250 GB, obraťte se na podporu a upravte limit App Service plánu na maximálně 1 TB. Pokud se limit plánu upraví, ve všech App Servicech plánech v pomocném mechanismu řízení se stále omezuje 1 TB. 

## <a name="logging"></a>Protokolování ##

Pomocí Azure Monitor můžete integrovat své pomocného mechanismu pro odesílání protokolů o přihlašování do úložiště, centra událostí nebo Log Analytics. Položky, které jsou protokolovány dnes, jsou:

| Status | Zpráva |
|---------|----------|
| Pomocného mechanismu není v pořádku. | Zadaný pomocného mechanismu není v pořádku kvůli neplatné konfiguraci virtuální sítě. Pozastavený pomocného mechanismu bude pozastaven, pokud stav není v pořádku. Ujistěte se, že jsou uvedené pokyny, které jsou tady definované: https://docs.microsoft.com/azure/app-service/environment/network-info |
| Podsíť pomocného mechanismu je skoro mimo prostor. | Zadaný přihlášený se nachází v podsíti, která nemá skoro dostatek místa. Existují {0} zbývajících adres. Po vyčerpání těchto adres se pomocného mechanismu nebude moct škálovat.  |
| Pomocného mechanismu se blíží celkovému limitu instancí. | Zadaný pomocného mechanismu se blíží celkovému limitu instancí pomocného mechanismu. V současné době obsahuje {0} App Service plánování instancí s maximální 201 instancemi. |
| Pomocného mechanismu se nemůže spojit se závislostí. | Zadaný pomocného mechanismu řízení nemůže získat přístup k {0}.  Ujistěte se, že jsou uvedené pokyny, které jsou tady definované: https://docs.microsoft.com/azure/app-service/environment/network-info |
| Pozastavený pomocný | Zadaný pomocného mechanismu je pozastaven. Pozastavení pomocného mechanismu může být způsobené nedostatkem účtu nebo konfigurací neplatné virtuální sítě. Vyřešte hlavní příčinu a obnovte pomocného mechanismu řízení a pokračujte v obsluze provozu. |
| Byl spuštěn upgrade pomocného mechanismu. | Začaly se upgradovat na zadanou pomocného objektem. Očekávat zpoždění operací škálování |
| Upgrade pomocného mechanismu se dokončil. | Upgrade platformy na zadaný pomocného objektu se dokončil. |
| Operace škálování se zahájily. | App Service plán ({0}) zahájil škálování. Požadovaný stav: {1} I{2} pracovní procesy 
| Operace škálování se dokončily. | Škálování plánu App Service ({0}) bylo dokončeno. Aktuální stav: {1} I{2} pracovní procesy |
| Operace škálování se nezdařily. | Škálování plánu App Service ({0}) se nezdařilo. Aktuální stav: {1} I{2} pracovní procesy |

Postup povolení protokolování v přihlašovacím MECHANISMech: 

1. Přejít na nastavení diagnostiky na portálu.  
1. Vyberte Přidat nastavení diagnostiky.
1. Zadejte název pro integraci protokolu.
1. Zkontroluje a nakonfiguruje požadované cíle protokolu. 
1. Kontrolovat AppServiceEnvironmentPlatformLogs

![Nastavení diagnostického protokolu pomocného mechanismu][4]

Pokud provádíte integraci s Log Analytics, můžete zobrazit protokoly výběrem protokolů z portálu pomocného programu a vytvořením dotazu pro AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Předvolba upgradu ##

Pokud máte více služby ASE, možná budete chtít, aby se některé služby ASE před ostatními upgradovali. V rámci objektu pomocného třída HostingEnvironment Správce prostředků objekt můžete nastavit hodnotu UpgradePreference. Nastavení upgradePreference se dá nakonfigurovat pomocí šablony, ARMClient nebo https://resources.azure.com.  Možnosti tří hodnot jsou:

* None – None je výchozí hodnota a znamená, že Azure upgraduje váš pomocného správce bez konkrétní dávky.
* Předčasné navýšení znamená, že se vaše pomocného programu upgraduje v první polovině App Servicech upgradů
* Pozdě pozdě znamená, že váš pomocným mechanismem bude upgradován v druhé polovině App Service upgrady

Pokud používáte https://resources.azure.com, můžete nastavit hodnotu upgradePreferences podle:

1. Resources.azure.com a přihlášení pomocí účtu Azure
1. Procházejte odběry\/\[název předplatného\]\/resourceGroups\/\[název skupiny prostředků\]\/poskytovatelé\/Microsoft. Web\/hostingEnvironments\/\[pomocné jméno\]
1. Výběr čtení/zápisu v horní části
1. Vybrat upravit
1. Změňte hodnotu upgradePreference na cokoli, co si přejete ze tří možností.
1. Vybrat opravu

![prostředky – zobrazení Azure com][5]

Funkce upgradePreferences má ve skutečnosti smysl, pokud máte více služby ASE, protože "předčasné" upgradovanější služby ASE bude upgradován před tím, než bude "pozdě" služby ase. Pokud máte více služby ASE, měli byste mít služby ASE pro vývoj/testování nastavenou na "včasnou" a produkční služby ASE, které se nastaví jako "pozdě".

## <a name="pricing"></a>Ceny ##

SKU s cenami s názvem **Isolated** je určena jenom pro použití s pomocným mechanismem. Všechny plány App Service hostované v pomocném formuláři jsou v izolované cenové SKU. Tarify plánu izolované App Service se můžou v jednotlivých oblastech lišit. 

Kromě ceny za vaše App Service plány existuje paušální sazba pro samotný správce. Paušální míra se nemění podle velikosti vašeho pomocného mechanismu služeb a platí pro infrastrukturu pomocného programu s výchozí mírou škálování jednoho dalšího front-endu pro každých 15 App Servicech plánů.  

Pokud výchozí míra škálování jednoho front-endu pro každých 15 App Servicech instancí plánu není dostatečně rychlá, můžete upravit poměr, ve kterém jsou přidané front-endy, nebo velikost front-endy.  Při úpravách poměru nebo velikosti platíte za hlavní části, které se ve výchozím nastavení nepřidaly.  

Pokud například upravíte poměr stupnice na hodnotu 10, přidá se front-end pro každých 10 instancí ve vašich App Servicech plánech. Paušální poplatek pokrývá míru škálování jednoho front-endu pro každých 15 instancí. S poměrem stupnice 10 se platíte za třetí front-end, který je přidaný pro 10 App Servicech plánů. Nemusíte platit za to, když máte víc než 15 instancí, protože se přidalo automaticky.

Pokud jste upravili velikost front-endy na dvě jádra, ale neupravíte poměr, platíte za nadbytečné jádra.  Pomocného programu se vytvoří se dvěma front-endy, takže i pod prahovou hodnotou automatického škálování, pokud jste zvýšili velikost na dvě základní front-endy, platíte za dvě další jádra.

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
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
