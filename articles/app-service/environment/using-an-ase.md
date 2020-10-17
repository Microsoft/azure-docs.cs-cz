---
title: Použití a Správa App Service Environment
description: Naučte se vytvářet, publikovat a škálovat aplikace v App Service Environment. Vyhledá všechny běžné úkoly v tomto článku.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1e6bace9652ff68bb4cc28d482016b7e7510154b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150194"
---
# <a name="use-an-app-service-environment"></a>Použití prostředí App Service Environment

App Service Environment (pomocným mechanismem) je nasazení Azure App Service do podsítě v instanci Azure Virtual Network zákazníka. Pomocného programu se skládá z těchto:

- **Front-endy**: kde se http nebo https ukončí v App Service Environment
- **Pracovní procesy**: prostředky, které hostují vaše aplikace
- **Databáze**: uchovává informace, které definují prostředí.
- **Storage**: používá se k hostování aplikací publikovaných zákazníky.

Pro přístup k aplikacím můžete nasadit pomocného programu s externí nebo interní virtuální IP adresou (VIP). Nasazení s externí virtuální IP adresou se běžně označuje jako *externí POmocného mechanismu*. Nasazení s interní VIP se nazývá *interního nástroje POmocného* programu pro vyrovnávání zatížení, protože používá interní nástroj pro vyrovnávání zatížení (interního nástroje). Další informace o pomocném mechanismu pro interního nástroje najdete v tématu [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Vytvoření aplikace v pomocném mechanismu služby

K vytvoření aplikace v pomocném mechanismu řízení použijte stejný postup jako při normálním vytváření aplikace, ale s malým rozdílem. Když vytváříte nový plán App Service:

- Místo výběru geografického umístění, do kterého chcete aplikaci nasadit, si jako umístění zvolíte pomocného objekt pro vytváření.
- Všechny plány App Service vytvořené v pomocném mechanismu řízení se můžou nacházet jenom v izolované cenové úrovni.

Pokud nemáte pomocného mechanismu řízení, můžete ho vytvořit podle pokynů v části [vytvoření App Service Environment][MakeExternalASE].

Vytvoření aplikace v pomocném mechanismu řízení:

1. Vyberte **vytvořit prostředek**  >  **web a mobilní zařízení**  >  **webovou aplikaci**.

1. Zadejte název aplikace. Pokud jste už v pomocném mechanismu řízení vybrali plán App Service, bude název domény pro aplikaci odpovídat názvu domény pomocného mechanismu řízení:

    ![Výběr názvu aplikace][1]

1. Vyberte předplatné.

1. Zadejte název nové skupiny prostředků nebo vyberte použít existující a v rozevíracím seznamu vyberte **použít existující** a vyberte jednu z nich.

1. Vyberte váš operační systém.

1. Pomocí následujících kroků vyberte existující plán App Service, nebo vytvořte nový.

    a. V nabídce Azure Portal levé straně vyberte **vytvořit prostředek > webovou aplikaci**.

    b. Vyberte předplatné.

    c. Vyberte nebo vytvořte skupinu prostředků.

    d. Zadejte název vaší webové aplikace.

    e. Vyberte **kód** nebo **DockerContainer**.

    f. Vyberte zásobník modulu runtime.

    například Vyberte **Linux** nebo **Windows**. 

    h. V rozevíracím seznamu **oblast** vyberte svůj pomocný seznam. 

    i. Vyberte nebo vytvořte nový plán App Service. Pokud vytváříte nový plán App Service, vyberte odpovídající velikost **izolované** SKU.

    ![Izolované cenové úrovně][2]

    > [!NOTE]
    > Aplikace pro Linux a aplikace pro Windows nemůžou být ve stejném plánu App Service, ale můžou být ve stejném App Service Environment.
    >

1. Vyberte **zkontrolovat + vytvořit**, ujistěte se, že jsou informace správné, a pak vyberte **vytvořit**.

## <a name="how-scale-works"></a>Jak funguje škálování

Každá aplikace App Service běží v plánu App Service. App Service prostředí uchovávají App Service plány a App Service plány uchovávají aplikace. Při škálování aplikace můžete také škálovat plán App Service a všechny aplikace v tomto plánu.

Při škálování plánu App Service se požadovaná infrastruktura automaticky přidá. Při přidávání infrastruktury je časová prodleva pro škálování operací. Pokud provedete několik operací škálování v pořadí, na první požadavek na škálování infrastruktury se bude zpracovávat a ostatní se zařadí do fronty. Až se první operace škálování dokončí, ostatní požadavky na infrastrukturu požádají o veškerou spolupráci. A při přidání infrastruktury se App Service plány přiřazují podle potřeby. Vytvoření nového plánu App Service je sám operace škálování, protože vyžaduje další hardware.

Ve víceklientské App Service je škálování okamžité, protože fond prostředků je snadno dostupný pro jeho podporu. V pomocném mechanismu služby neexistuje žádná taková vyrovnávací paměť a prostředky se přidělují podle potřeby.

V pomocném mechanismu služby můžete škálovat App Service plánu až 100 instancí. Pomocného nástroje může mít až 201 celkových instancí napříč všemi App Service plány v tomto pomocném panelu.

## <a name="ip-addresses"></a>IP adresy

App Service může aplikaci přidělit vyhrazenou IP adresu. Tato funkce je k dispozici po konfiguraci protokolu SSL založeného na protokolu IP, jak je popsáno v tématu [vytvoření vazby existujícího vlastního certifikátu TLS/SSL na Azure App Service][ConfigureSSL]. V pomocném mechanismu interního nástroje nemůžete přidat další IP adresy, které se budou používat pro protokol SSL založený na protokolu IP.

Pomocí externího pomocného mechanismu služby můžete nakonfigurovat protokol SSL založený na protokolu IP pro vaši aplikaci stejným způsobem jako ve víceklientské App Service. V pomocném uživatelském rozhraní je vždycky jedna volná adresa, až 30 IP adres. Pokaždé, když ho použijete, přidá se další, aby byla adresa vždycky k dispozici. K přidělení další IP adresy je nutná časová prodleva. Tato prodleva brání v rychlém úspěchu přidávání IP adres.

## <a name="front-end-scaling"></a>Škálování front-endu

Při horizontálním navýšení kapacity App Service plány se pracovní procesy přidají automaticky pro jejich podporu. Všechny pomocné uživatelské rozhraní se vytvoří se dvěma front-endy. Front-endy automaticky naškáluje sazbu jednoho front-endu pro každou sadu 15 App Servicech plánů instancí. Například pokud máte tři App Service plány s pěti instancemi, měli byste mít celkem 15 instancí a tři front-endy. Pokud budete škálovat na celkem 30 instancí, budete mít čtyři front-endy. Tento model pokračuje při horizontálním navýšení kapacity.

Počet front-endu, které jsou přiděleny ve výchozím nastavení, je vhodný pro střední zatížení. Poměr můžete snížit na méně než jeden front-end pro každé pět instancí. Můžete také změnit velikost front-endy. Ve výchozím nastavení se jedná o jedno jádro. V Azure Portal můžete místo toho změnit jejich velikost na dvě nebo čtyři jádra.

Pro změnu poměru nebo front-endové velikosti se účtuje poplatek. Další informace najdete v tématu [Azure App Service ceny][Pricing]. Pokud chcete zlepšit kapacitu zatížení vašeho pomocného programu pro čtení, získáte lepší vylepšení při prvním škálování na front-endy se dvěma jádry před úpravou poměru stupnice. Změna základní velikosti front-endu způsobí upgrade pomocného mechanismu řízení a měla by se provádět mimo běžnou pracovní dobu.

Prostředky front-endu jsou koncovým bodem HTTP/HTTPS pro pomocného bodu služby. S výchozí konfigurací front-endu je využití paměti na front-endu konzistentně v rozmezí od 60%. Hlavním důvodem pro horizontální navýšení kapacity front-endu je využití procesoru, které je primárně řízeno přenosy protokolu HTTPS.

## <a name="app-access"></a>Přístup k aplikacím

V externím pomocném mechanismu pro vytváření aplikací je přípona domény používaná k vytvoření aplikace *. &lt; asename &gt; . p.azurewebsites.NET*. Pokud má váš pomocný přístup s názvem _External-_ pojmenování a Vy hostte aplikaci s názvem _Contoso_ v tomto pomocném mechanismu, dostanete se na tyto adresy URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Informace o tom, jak vytvořit externí pomocného mechanismu řízení, najdete v tématu [vytvoření App Service Environment][MakeExternalASE].

V interního nástroje pomocném mechanismu pro vytváření aplikací je přípona domény používaná k vytvoření aplikace *. &lt; asename &gt; . appserviceenvironment.NET*. Pokud je váš správce _přihlášený jako interního nástroje-pomocného_ mechanismu a v tomto pomocném mechanismu je hostitelem aplikace s názvem _Contoso_ , dostanete se na tyto adresy URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Informace o tom, jak vytvořit interního nástroje pomocného mechanismu pro vytváření, najdete v tématu [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE].

Adresa URL SCM se používá pro přístup ke konzole Kudu nebo k publikování vaší aplikace pomocí Nasazení webu. Informace o konzole Kudu naleznete v tématu [Konzola Kudu pro Azure App Service][Kudu]. Konzola Kudu poskytuje webové uživatelské rozhraní pro ladění, nahrávání souborů, úpravy souborů a mnoho dalšího.

### <a name="dns-configuration"></a>Konfigurace DNS 

Když použijete externí pomocného Správce služby, aplikace vytvořené ve vašem pomocném mechanismu se zaregistrují s Azure DNS. V externím pomocném mechanismu pro vaše aplikace neexistují žádné další kroky, které by měly být veřejně dostupné. Pomocí pomocného mechanismu interního nástroje musíte spravovat vlastní DNS. Můžete to udělat na svém vlastním serveru DNS nebo Azure DNS privátních zónách.

Postup konfigurace DNS na vlastním serveru DNS pomocí pomocného mechanismu pro interního nástroje:

1. Vytvořte zónu pro &lt; název pomocného mechanismu &gt; . appserviceenvironment.NET
1. Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
1. Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
1. vytvoření zóny v &lt; názvu pomocného mechanismu &gt; . appserviceenvironment.NET s názvem SCM
1. Vytvořte v zóně SCM záznam A, který odkazuje na IP adresu interního nástroje.

Postup při konfiguraci DNS v privátních zónách Azure DNS:

1. vytvořit privátní zónu Azure DNS s názvem &lt; pomocného jména &gt; . appserviceenvironment.NET
1. Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
1. Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
1. Vytvořte v této zóně záznam A, který odkazuje *. SCM na IP adresu interního nástroje.

Nastavení DNS pro výchozí příponu vaší domény pro přístup k uživateli neomezuje vaše aplikace tak, aby byly dostupné jenom pro tyto názvy. V pomocném mechanismu interního nástroje můžete nastavit vlastní název domény bez ověřování v aplikacích. Pokud budete chtít vytvořit zónu s názvem *contoso.NET*, můžete to udělat a nasměrovat ji na interního nástroje IP adresu. Vlastní název domény funguje pro žádosti o aplikace, ale pro web SCM ne. Web SCM je k dispozici pouze na adrese * &lt; AppName &gt; . SCM. &lt; asename &gt; . appserviceenvironment.NET*. 

Zóna s názvem *. &lt; asename &gt; . appserviceenvironment.NET* je globálně jedinečný. Od května 2019 mohou zákazníci zadat příponu interního nástroje pomocného programu pro přístup k doméně. Pokud jste chtěli použít *. contoso.com* pro příponu domény, mohli byste tak učinit a zahrnovat web SCM. S tímto modelem byly problémy, včetně; Správa výchozího certifikátu SSL, nedostatečného jednotného přihlašování s webem SCM a požadavek na použití certifikátu se zástupnými znaky. Proces upgradu výchozího certifikátu interního nástroje pomocného programu pro pořízení byl také narušen a způsobil, že aplikace bude restartována. Aby bylo možné tyto problémy vyřešit, bylo chování pomocného programu interního nástroje změněno tak, aby používalo příponu domény na základě názvu pomocného programu a s příponou vlastněné společností Microsoft. Změna chování pomocného mechanismu interního nástroje má vliv pouze na interního nástroje služby ASE, které byly provedeny po 2019. května. Stávající interního nástroje služby ASE musí stále spravovat výchozí certifikát pro přihlašovací seznam a jejich konfiguraci DNS.

## <a name="publishing"></a>Publikování

V rámci služby řízení přihlašování jako u víceklientské App Service můžete publikovat pomocí těchto metod:

- Nasazení webu
- FTP
- Průběžná integrace (CI)
- Přetažení v konzole Kudu
- Integrované vývojové prostředí (IDE), jako je například Visual Studio, zatmění nebo IntelliJ nápad

S externím pomocným mechanismem budou tyto možnosti publikování fungovat stejným způsobem. Další informace najdete v tématu [nasazení v Azure App Service][AppDeploy].

Pomocí pomocného mechanismu pro interního nástroje jsou koncové body publikování dostupné jenom prostřednictvím interního nástroje. INTERNÍHO nástroje se nachází v privátní IP adrese v podsíti pro pomocné službě ve virtuální síti. Pokud nemáte přístup k síti interního nástroje, nemůžete v tomto pomocném panelu publikovat žádné aplikace. Jak je uvedeno v části [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE], musíte nakonfigurovat DNS pro aplikace v systému. Tento požadavek zahrnuje koncový bod SCM. Pokud koncové body nejsou správně definované, nemůžete publikovat. K tomu, aby bylo možné do interního nástroje Publikovat přímo, musí mít síťový přístup i vaše IDEs.

Bez dalších změn nebudou internetové systémy CI, jako je GitHub a Azure DevOps, fungovat s pomocným mechanismem interního nástroje, protože koncový bod publikování není přístupný na internetu. Publikování na interního nástroje pomocného mechanismu pro Azure můžete povolit z Azure DevOps tím, že ve virtuální síti nainstalujete samoobslužného agenta pro vydanou verzi, který obsahuje ovládací prvek interního nástroje. Alternativně můžete také použít systém CI, který využívá model Pull, jako je třeba Dropbox.

Koncové body pro publikování pro aplikace ve službě ASE s interním nástrojem pro vyrovnávání zatížení používají doménu, pomocí které byla služba ASE s interním nástrojem pro vyrovnávání zatížení vytvořená. Můžete ji zobrazit v publikačním profilu aplikace a v podokně portálu aplikace (v části **Přehled**  >  **Essentials** a také ve **vlastnostech**).

## <a name="storage"></a>Storage

Pomocného programu má 1 TB úložiště pro všechny aplikace v pomocném formuláři. Plán App Service v izolované cenové SKU má limit 250 GB. V rámci pomocného mechanismu se 250 GB úložiště přidají za App Service plánu až do velikosti 1 TB. Můžete mít více App Service plánů než jenom čtyři, ale za omezení 1 TB se nepřidalo žádné další úložiště.

## <a name="logging"></a>protokolování

Pomocí Azure Monitor můžete integrovat své pomocného mechanismu pro odesílání protokolů o pomocném programu do Azure Storage, Azure Event Hubs nebo Log Analytics. Tyto položky jsou protokolovány Dnes:

| Status | Zpráva |
|---------|----------|
| Pomocného mechanismu není v pořádku. | Zadaný pomocného mechanismu není v pořádku kvůli neplatné konfiguraci virtuální sítě. Pozastavený pomocného mechanismu bude pozastaven, pokud stav není v pořádku. Ujistěte se, že jsou uvedené pokyny, které jsou tady definované: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Podsíť pomocného mechanismu je skoro mimo prostor. | Zadaný přihlášený se nachází v podsíti, která nemá skoro dostatek místa. Existují {0} zbývající adresy. Po vyčerpání těchto adres se pomocného mechanismu nebude moct škálovat.  |
| Pomocného mechanismu se blíží celkovému limitu instancí. | Zadaný pomocného mechanismu se blíží celkovému limitu instancí pomocného mechanismu. V současné době obsahuje {0} App Service instance plánů s maximálním počtem 201 instancí. |
| Pomocného mechanismu se nemůže spojit se závislostí. | Zadaný přidaný přístup k tomuto mechanismu se nemůže spojit {0} .  Ujistěte se, že jsou uvedené pokyny, které jsou tady definované: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Pozastavený pomocný | Zadaný pomocného mechanismu je pozastaven. Pozastavení pomocného mechanismu může být způsobené nedostatkem účtu nebo konfigurací neplatné virtuální sítě. Vyřešte hlavní příčinu a obnovte pomocného mechanismu řízení a pokračujte v obsluze provozu. |
| Byl spuštěn upgrade pomocného mechanismu. | Začaly se upgradovat na zadanou pomocného objektem. Očekává zpoždění operací škálování. |
| Upgrade pomocného mechanismu se dokončil. | Upgrade platformy na zadaný pomocného objektu se dokončil. |
| Operace škálování se zahájily. | App Service plán ( {0} ) zahájil škálování. Požadovaný stav: {1} mám {2} pracovní procesy.
| Operace škálování se dokončily. | Škálování plánu ( {0} ) App Service bylo dokončeno. Aktuální stav: {1} jsem {2} zaměstnanci. |
| Operace škálování se nezdařily. | Škálování schématu App Service ( {0} ) se nezdařilo. Aktuální stav: {1} jsem {2} zaměstnanci. |

Postup povolení protokolování v přihlašovacím MECHANISMech:

1. Na portálu přejdete na **nastavení diagnostiky**.
1. Vyberte **Přidat nastavení diagnostiky**.
1. Zadejte název pro integraci protokolu.
1. Vyberte a nakonfigurujte umístění protokolu, které chcete.
1. Vyberte **AppServiceEnvironmentPlatformLogs**.

![Nastavení diagnostického protokolu pomocného mechanismu][4]

Pokud provádíte integraci s Log Analytics, můžete protokoly zobrazit tak, že vyberete **protokoly** z portálu pro pomocné služby a vytvoříte dotaz na **AppServiceEnvironmentPlatformLogs**. Protokoly se vysílají jenom v případě, že má váš správce událostí událost, která ho spustí. Pokud vaše pomocného mechanismu takovou událost neobsahuje, nebudou žádné protokoly. Pokud chcete rychle zobrazit příklad protokolů v pracovním prostoru Log Analytics, proveďte operaci škálování s jedním z plánů App Service ve vašem pomocném programu. Pak můžete spustit dotaz na **AppServiceEnvironmentPlatformLogs** a zobrazit tyto protokoly. 

**Vytvoření výstrahy**

Pokud chcete vytvořit výstrahu proti svým protokolům, postupujte podle pokynů v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../../azure-monitor/platform/alerts-log.md). V krátkém případě:

* Otevřete stránku výstrahy na portálu pro pomocné služby.
* Vybrat **nové pravidlo výstrahy**
* Vyberte prostředek, který bude vaším Log Analytics pracovním prostorem.
* Nastavte podmínku pomocí vlastního prohledávání protokolu tak, aby používala dotaz, jako je například "AppServiceEnvironmentPlatformLogs | kde ResultDescription obsahuje "Začínáme s škálováním" nebo cokoli, co chcete. Nastavte prahovou hodnotu podle potřeby. 
* Podle potřeby přidejte nebo vytvořte skupinu akcí. Skupina akcí je místo, kde můžete definovat odpověď na výstrahu, například odeslání e-mailu nebo zprávy SMS.
* Pojmenujte upozornění a uložte je.

## <a name="upgrade-preference"></a>Předvolba upgradu

Pokud máte více služby ASE, možná budete chtít, aby některé služby ASE byly upgradovány ještě před ostatními. V rámci objektu pomocného **třída hostingenvironment správce prostředků** objekt můžete nastavit hodnotu **upgradePreference**. Nastavení **upgradePreference** se dá nakonfigurovat pomocí šablony, ARMClient nebo https://resources.azure.com . Tři možné hodnoty jsou:

- **Žádné**: Azure provede upgrade pomocného mechanismu v žádné konkrétní dávce. Tato hodnota je výchozí.
- **Brzy**: vaše pomocného programu bude upgradován v první polovině App Service upgradu.
- **Pozdě**: vaše pomocného programu bude upgradován v druhé polovině App Service upgradu.

Pokud používáte https://resources.azure.com , nastavte hodnotu **upgradePreferences** pomocí těchto kroků:

1. Přejít na resources.azure.com a přihlaste se pomocí svého účtu Azure.
1. Projděte si materiály k předplatným \/ \[ název předplatné \] \/ resourceGroups název \/ \[ skupiny prostředků \] \/ poskytovatelé název \/ Microsoft. Web \/ hostingEnvironments \/ \[ pomocné jméno \] .
1. V horní části vyberte **čtení i zápis** .
1. Vyberte **Upravit**.
1. Nastavte **upgradePreference** na jednu ze tří hodnot, které chcete.
1. Vyberte možnost **opravit**.

![prostředky – zobrazení Azure com][5]

Funkce **upgradePreferences** je nejužitečnější, když máte více služby ASE, protože "předčasné" služby ASE bude upgradován před "pozdě" služby ase. Pokud máte více služby ASE, měli byste nastavit vývoj a testování služby ASE tak, aby byly "nejdříve" a v produkčním služby ASE být "pozdě".

## <a name="pricing"></a>Ceny

SKU s cenami s názvem *Isolated* je určena pouze pro použití s služby ase. Všechny plány App Service hostované v pomocném formuláři jsou v izolované cenové SKU. Izolované sazby pro plány App Service se můžou v jednotlivých oblastech lišit.

Kromě ceny vašich App Servicech plánů existuje paušální sazba samotného mechanismu řízení. Paušální míra se nemění podle velikosti vašeho pomocného mechanismu. Platí pro infrastrukturu pomocného programu s výchozí mírou škálování jednoho dalšího front-endu pro každých 15 App Servicech plánů.

Pokud není výchozí míra škálování jednoho front-endu pro každých 15 App Servicech instancí plánu dostatečně rychlá, můžete upravit poměr, ve kterém jsou přidány přední konce nebo velikost front-endy. Při úpravách poměru nebo velikosti platíte za základní jádra, která se ve výchozím nastavení nepřidala.

Pokud například upravíte poměr stupnice na hodnotu 10, přidá se front-end pro každých 10 instancí ve vašich App Servicech plánech. Paušální poplatek pokrývá míru škálování jednoho front-endu pro každých 15 instancí. S poměrem stupnice 10 se platíte za třetí front-end, který je přidaný pro 10 App Servicech plánů. Nemusíte platit za to, když máte víc než 15 instancí, protože se přidalo automaticky.

Pokud upravíte velikost front-endu na dvě jádra, ale neupravíte poměr, platíte za další jádra. Pomocného programu se vytvoří se dvěma front-endy, takže i pod prahovou hodnotou automatického škálování, která by platila pro dvě další jádra, pokud velikost zvýšíte na front-endové přední zakončení.

Další informace najdete v tématu [Azure App Service ceny][Pricing].

## <a name="delete-an-ase"></a>Odstranění pomocného mechanismu

Postup odstranění pomocného mechanismu řízení:

1. V horní části podokna **App Service Environment** vyberte **Odstranit** .

1. Zadejte název pomocného programu pro potvrzení, že ho chcete odstranit. Když odstraníte pomocného mechanismu řízení, odstraníte také veškerý obsah v něm.

    ![Odstranění pomocného mechanismu][3]

1. Vyberte **OK**.

## <a name="ase-cli"></a>ROZHRANÍ PŘÍKAZOVÉHO ŘÁDKU

Existují možnosti příkazového řádku pro správu do pomocného mechanismu služby.  Příkazy AZ CLI jsou uvedeny níže.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md