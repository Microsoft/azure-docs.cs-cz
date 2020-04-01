---
title: Používání a správa prostředí služby App Service
description: Zjistěte, jak vytvářet, publikovat a škálovat aplikace v prostředí služby App Service. Najděte všechny běžné úkoly v tomto článku.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 8a73c1998203a8696b67a5e7eb3af23898239265
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477631"
---
# <a name="use-an-app-service-environment"></a>Použití prostředí App Service Environment

Prostředí služby App Service (ASE) je nasazení služby Azure App Service do podsítě v instanci virtuální sítě Azure zákazníka. ASE se skládá z:

- **Front-endy**: Kde http nebo https končí v prostředí služby App Service.
- **Pracovníci**: Prostředky, které hostují vaše aplikace.
- **Databáze**: Obsahuje informace, které definují prostředí.
- **Úložiště**: Používá se k hostování aplikací publikovaných zákazníkem.

Službu ASE můžete nasadit s externí nebo interní virtuální IP (VIP) pro přístup k aplikacím. Nasazení s externí min. VIP se běžně nazývá *externí služba ASE*. Nasazení s interní min. VIRTUÁLNÍ IP se nazývá *služba ASE ILB,* protože používá interní systém vyrovnávání zatížení (ILB). Další informace o službě ILB ASE najdete v [tématu Vytvoření a použití služby ASE iLB][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Vytvoření aplikace ve selsce ase

Chcete-li vytvořit aplikaci ve správě ase, použijte stejný proces, jako když normálně vytvořit aplikaci, ale s několika malými rozdíly. Když vytvoříte nový plán služby App Service:

- Místo výběru geografické polohy, do které chcete aplikaci nasadit, zvolíte jako svou polohu službu ASE.
- Všechny plány služby App Service vytvořené ve službě ASE mohou být pouze v izolované cenové úrovni.

Pokud službu ASE nemáte, můžete ji vytvořit podle pokynů v části [Vytvořit prostředí služby App Service][MakeExternalASE].

Vytvoření aplikace ve skutážní nespoje:

1. Vyberte **možnost Vytvořit** > **web + mobilní** > **webovou aplikaci .**

1. Zadejte název aplikace. Pokud jste již ve službě ASE vybrali plán služby App Service, název domény aplikace odráží název domény služby ASE:

    ![Výběr názvu aplikace][1]

1. Vyberte předplatné.

1. Zadejte název nové skupiny prostředků nebo vyberte **Použít existující** a vyberte jeden z rozevíracího seznamu.

1. Vyberte váš operační systém.

1. Vyberte existující plán služby App Service ve službě ASE nebo vytvořte nový následujícím postupem:

    a. V nabídce na levé straně portálu Azure vyberte **Vytvořit prostředek > Web App**.

    b. Vyberte předplatné.

    c. Vyberte nebo vytvořte skupinu prostředků.

    d. Zadejte název webové aplikace.

    e. Vyberte **Kód** nebo **DockerContainer**.

    f. Vyberte zásobník za běhu.

    g. Vyberte **Linux** nebo **Windows**. 

    h. V rozevíracím seznamu **Oblast** vyberte svou ase. 

    i. Vyberte nebo vytvořte nový plán služby App Service. Pokud vytváříte nový plán služby App Service, vyberte příslušnou **velikost izolované** skladové položky.

    ![Izolované cenové úrovně][2]

    > [!NOTE]
    > Linuxové aplikace a aplikace pro Windows nemohou být ve stejném plánu služby App Service, ale mohou být ve stejném prostředí služby App Service.
    >

1. Vyberte **Zkontrolovat + vytvořit**, zkontrolujte, zda jsou informace správné, a pak vyberte **Vytvořit**.

## <a name="how-scale-works"></a>Jak měřítko funguje

Každá aplikace Služby Aplikace běží v plánu služby App Service. Prostředí služby App Service mají plány služby App Service a plány služby App Service. Při škálování aplikace můžete také škálovat plán služby App Service a všechny aplikace ve stejném plánu.

Při škálování plánu služby App Service se automaticky přidá potřebná infrastruktura. Při přidávání infrastruktury dochází k časovému zpoždění operací. Pokud provádíte několik operací škálování v pořadí, první požadavek škálování infrastruktury je zpracována a ostatní jsou zařazeny do fronty. Po dokončení první operace škálování, všechny ostatní požadavky infrastruktury pracují společně. A když je přidána infrastruktura, plány služby App Service jsou přiřazeny podle potřeby. Vytvoření nového plánu služby App Service je samo o sobě operace škálování, protože požaduje další hardware.

Ve víceklientské službě App Service je škálování okamžité, protože fond prostředků je snadno dostupný pro jeho podporu. V ase neexistuje žádná taková vyrovnávací paměť a prostředky jsou přiděleny na základě potřeby.

Ve službě ASE můžete škálovat plán služby App Service až na 100 instancí. Služba ASE může mít až 201 celkový počet instancí ve všech plánech služby App Service v této službě ASE.

## <a name="ip-addresses"></a>IP adresy

Služba App Service může aplikaci přidělit vyhrazenou IP adresu. Tato funkce je k dispozici po konfiguraci protokolu SSL založeného na protokolu IP, jak je popsáno v [části Vazba na existující vlastní certifikát TLS/SSL na službu Azure App Service][ConfigureSSL]. Ve službách ASE ILB nelze přidat další IP adresy, které se použijí pro protokol SSL založený na protokolu IP.

S externí službou ASE můžete pro vaši aplikaci nakonfigurovat protokol SSL založený na protokolu IP stejným způsobem jako ve víceklientské službě App Service. Ve snaze ase je vždy jedna náhradní adresa, až 30 IP adres. Pokaždé, když použijete jeden, další je přidán tak, aby adresa je vždy snadno dostupné. K přidělení jiné adresy IP je nutné časové zpoždění. Toto zpoždění zabraňuje přidávání IP adres v rychlém sledu.

## <a name="front-end-scaling"></a>Změna velikosti front-endu

Když navštováte své plány služby App Service, pracovníci se automaticky přidají, aby je podporovali. Každá ase je vytvořena se dvěma front-endy. Front-endy se automaticky škálují rychlostí jednoho front-endu pro každou sadu 15 instancí plánu služby App Service. Například pokud máte tři plány služby App Service s pěti instancemi, měli byste celkem 15 instancí a tři front-endy. Pokud změníte měřítko na celkem 30 instancí, máte čtyři front-endy. Tento vzor pokračuje, jak horizontální navýšení kapacity.

Počet front-endů, které jsou přiděleny ve výchozím nastavení je vhodné pro mírné zatížení. Poměr můžete snížit na jeden front-end pro každých pět instancí. Můžete také změnit velikost předních konců. Ve výchozím nastavení jsou jednojádrové. Na webu Azure Portal můžete místo toho změnit jejich velikost na dvě nebo čtyři jádra.

Za změnu poměru nebo velikosti front-endu se účtuje poplatek. Další informace najdete v [tématu Azure App Service pricing][Pricing]. Pokud chcete zlepšit únosnost vaší ase, budete mít další zlepšení tím, že nejprve škálování na dvoujádrové front-endy před úpravou poměru měřítka. Změna velikosti jádra předních konců způsobí upgrade vaší ase a měla by být provedena mimo běžnou pracovní dobu.

Prostředky front-endu jsou koncovým bodem HTTP/HTTPS pro ase. S výchozí front-end konfigurace využití paměti na front-end je konzistentně kolem 60 procent. Hlavním důvodem škálování front-endů je využití procesoru, které je primárně řízeno přenosem HTTPS.

## <a name="app-access"></a>Přístup k aplikacím

V externí matné služby služby ASE je přípona domény použitá pro vytváření aplikací *.&lt; asename&gt;.p.azurewebsites.net*. Pokud se vaše ase jmenuje _external-ase_ a hostujete aplikaci nazvanou _contoso_ v této ase, dostanete se k ní na těchto adresách URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Informace o tom, jak vytvořit externí službu ASE, naleznete [v tématu Vytvoření prostředí služby App Service][MakeExternalASE].

V ase Služby ASE ILB je přípona domény použitá pro vytváření aplikací *.&lt; asename&gt;.appserviceenvironment.net*. Pokud se vaše ase jmenuje _ilb-ase_ a hostujete aplikaci nazvanou _contoso_ v této ase, dostanete se k ní na těchto adresách URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Informace o vytvoření služby ASE ilb naleznete v [tématu Vytvoření a použití služby ASE služby ILB][MakeILBASE].

Adresa URL SCM se používá pro přístup ke konzoli Kudu nebo k publikování aplikace pomocí nasazení webu. Informace o konzoli Kudu najdete v [tématu Konzola Kudu pro službu Azure App Service][Kudu]. Konzole Kudu vám poskytuje webové uživatelské uživatelské tlačítko pro ladění, nahrávání souborů, úpravy souborů a mnoho dalšího.

## <a name="publishing"></a>Publikování

Ve službě ASE, stejně jako u víceklientské služby App Service, můžete publikovat pomocí těchto metod:

- Nasazení webu
- FTP
- Průběžná integrace (CI)
- Přetažení v konzole Kudu
- IDE, jako je visual studio, eclipse nebo intelliJ IDEA

S externí ase, tyto možnosti publikování všechny fungují stejným způsobem. Další informace najdete [v tématu Nasazení ve službě Azure App Service][AppDeploy].

Publikování se výrazně liší u služby ASE ILB, pro kterou jsou všechny koncové body publikování k dispozici pouze prostřednictvím služby ILB. ILB je na privátní IP v podsíti ASE ve virtuální síti. Pokud nemáte přístup k síti ILB, nemůžete publikovat žádné aplikace na této službě ASE. Jak je uvedeno v [části Vytvoření a použití služby ASE ILB][MakeILBASE], je nutné nakonfigurovat službu DNS pro aplikace v systému. Tento požadavek zahrnuje koncový bod SCM. Pokud koncové body nejsou definovány správně, nelze publikovat. Vaše IDC musí mít také přístup k síti ILB publikovat přímo do něj.

Bez dalších změn nefungují internetové systémy CI, jako je GitHub a Azure DevOps, se službou ASE ILB, protože koncový bod publikování není přístupný z internetu. Publikování do služby ASE ILB ze služby Azure DevOps můžete povolit instalací agenta verze s vlastním hostitelem ve virtuální síti, která obsahuje službu ASE ILB. Případně můžete také použít systém CI, který používá model vyžádat, například Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Můžete ji vidět v profilu publikování aplikace a v podokně portálu aplikace (v **přehledu** > **Essentials** a také ve **vlastnostech).**

## <a name="storage"></a>Storage

Služba ASE má úložiště o velikosti 1 TB pro všechny aplikace v systému ASE. Plán služby App Service v izolované cenové skladové položky má limit 250 GB ve výchozím nastavení. Pokud máte pět nebo více plánů služby App Service, dávejte pozor, abyste nepřekročili limit 1 TB služby ASE. Pokud potřebujete více než limit 250 GB v jednom plánu služby App Service, obraťte se na podporu a upravte limit plánu služby App Service na maximálně 1 TB. Když je limit plánu upraven, stále existuje limit 1 TB ve všech plánech služby App Service v systému ASE.

## <a name="logging"></a>protokolování

Službu ASE můžete integrovat s Azure Monitorem a odesílat protokoly o službě ASE do Azure Storage, Azure Event Hubs nebo Log Analytics. Tyto položky jsou zaznamenány dnes:

| Situaci | Zpráva |
|---------|----------|
| ASE není v pořádku | Zadaná služba ASE není v pořádku z důvodu neplatné konfigurace virtuální sítě. ASE bude pozastavena, pokud bude pokračovat stav není v pořádku. Ujistěte se, že https://docs.microsoft.com/azure/app-service/environment/network-infojsou dodržovány zde definované pokyny: . |
| Podsíť ASE je téměř bez místa | Zadaná zpráva se slídí je v podsíti, která je téměř bez místa. Jsou {0} tu zbývající adresy. Jakmile jsou tyto adresy vyčerpány, nebude možné škálovat.  |
| ASE se blíží limitu celkové instance | Zadaná zpráva služby ASE se blíží limitu celkové instance služby ASE. Aktuálně obsahuje {0} instance App Service Plan maximálně 201 instancí. |
| ASE není schopna dosáhnout závislosti. | Zadaná zpráva se správou ASE není schopna dosáhnout {0}.  Ujistěte se, že https://docs.microsoft.com/azure/app-service/environment/network-infojsou dodržovány zde definované pokyny: . |
| ASE je pozastavena | Zadaná funkce ASE je pozastavena. Pozastavení služby ASE může být způsobeno nedostatkem účtu nebo neplatnou konfigurací virtuální sítě. Vyřešte hlavní příčinu a pokračujte ve službě ASE, abyste pokračovali ve službě provozu. |
| Upgrade ase byl zahájen | Byl zahájen upgrade platformy na zadanou možnost ISE. Očekávejte zpoždění v operacích škálování. |
| Upgrade ase byl dokončen. | Byl dokončen upgrade platformy na zadanou možnost ISE. |
| Operace škálování byly zahájeny | Plán služby{0}App Service ( ) začal škálovat. Požadovaný {1} stav:{2} I pracovníci.
| Operace škálování byly dokončeny | Plán služby{0}App Service ( ) dokončil škálování. Současný {1} stav:{2} I pracovníci. |
| Operace škálování selhaly. | Plán služby{0}App Service ( ) se nepodařilo škálovat. Současný {1} stav:{2} I pracovníci. |

Povolení přihlášení ke službě ASE:

1. Na portálu přejděte na **Nastavení diagnostiky**.
1. Vyberte **Přidat diagnostické nastavení**.
1. Zadejte název pro integraci protokolu.
1. Vyberte a nakonfigurujte požadované cíle protokolu.
1. Vyberte **AppServiceEnvironmentPlatformLogs**.

![Nastavení diagnostického protokolu služby ASE][4]

Pokud integrujete s Log Analytics, můžete zobrazit protokoly výběrem **protokoly** z portálu Služby ase a vytvoření dotazu proti **AppServiceEnvironmentPlatformLogs**.

## <a name="upgrade-preference"></a>Předvolba upgradu

Pokud máte více AsEs, můžete chtít některé AsEs, které mají být upgradovány před ostatními. V rámci ase **hostingenvironment resource manager** objektu můžete nastavit hodnotu pro **upgradePreference**. Nastavení **upgradePreference** lze nakonfigurovat pomocí šablony, KLIENTA ARM nebo https://resources.azure.com. Tři možné hodnoty jsou:

- **Žádné**: Azure upgraduje vaši službu ASE v žádné konkrétní dávce. Tato hodnota je výchozí.
- **Brzy**: Vaše služba ASE bude upgradována v první polovině upgradů služby App Service.
- **Pozdě**: Vaše služba ASE bude upgradována v druhé polovině upgradů služby App Service.

Pokud používáte https://resources.azure.com, postupujte takto a nastavte hodnotu **priority:**

1. Přejděte na resources.azure.com a přihlaste se pomocí svého účtu Azure.
1. Projděte si prostředky\/\[k\]\/odběru\/\[název předplatného\]\/\/resourceGroups\/zprostředkovatele\/\[názvů skupin\]y microsoft.web hostingProstředís název služby ASE .
1. Nahoře vyberte **Číst/psát.**
1. Vyberte **Upravit**.
1. Nastavte **upgradePreference** podle toho, která ze tří hodnot, které chcete.
1. Vyberte **záplata**.

![zdroje azure com displej][5]

Funkce **upgradePreferences** dává největší smysl, pokud máte více AsEs, protože vaše "Early" AsEs budou upgradovány před vaše "Pozdní" AsEs. Pokud máte více AsEs, měli byste nastavit vývoj a testování ASEs být "Brzy" a vaše výroba AsEs být "Pozdě".

## <a name="pricing"></a>Ceny

Cenová skladová položka s názvem *Izolované* je určen pro použití pouze s ASEs. Všechny plány služby App Service, které jsou hostované ve službě ASE jsou v izolované ceny skladové položky. Izolované sazby pro plány služby App Service se mohou v jednotlivých oblastech lišit.

Kromě ceny plánů služby App Service existuje paušální sazba pro samotnou službu ASE. Paušální sazba se nemění s velikostí ase. Platí za infrastrukturu služby ASE s výchozí rychlostí škálování jednoho dalšího front-endu pro každých 15 instancí plánu služby App Service.

Pokud výchozí rychlost škálování jednoho front-endu pro každých 15 instancí plánu služby App Service není dostatečně rychlá, můžete upravit poměr, při kterém jsou přidány fronty nebo velikost front-endů. Když upravíte poměr nebo velikost, zaplatíte za přední jádra, která by ve výchozím nastavení nebyla přidána.

Pokud například upravíte poměr měřítka na 10, přidá se front-end pro každých 10 instancí v plánech služby App Service. Paušální poplatek pokrývá stupnici sazby jednoho front-endu na každých 15 instancí. S poměrem měřítka 10 zaplatíte poplatek za třetí front-end, který je přidán pro 10 instancí plánu služby App Service. Nemusíte platit za to, když se dostanete 15 instancí, protože byl přidán automaticky.

Pokud upravíte velikost předních konců na dvě jádra, ale neupravíte poměr, zaplatíte za další jádra. Služba ASE je vytvořena se dvěma předními konci, takže i pod prahovou hodnotou automatického škálování byste zaplatili za dvě další jádra, pokud byste zvětšili velikost na dvoujádrové přední konce.

Další informace najdete v [tématu Azure App Service pricing][Pricing].

## <a name="delete-an-ase"></a>Odstranění ase

Odstranění ses:

1. V horní části podokna **Prostředí služby App Service** vyberte **Odstranit.**

1. Zadejte název ase a potvrďte, že ji chcete odstranit. Když odstraníte službu ASE, odstraníte také veškerý obsah v ní.

    ![Odstranění ase][3]

1. Vyberte **OK**.

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
