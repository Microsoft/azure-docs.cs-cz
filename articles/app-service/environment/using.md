---
title: Použití prostředí App Service Environment
description: Naučte se používat App Service Environment k hostování izolovaných aplikací.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba06a0f9d520f445965329203aecd6c576666737
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209725"
---
# <a name="using-an-app-service-environment"></a>Použití App Service Environment

App Service Environment (pomocného programu) je nasazení Azure App Service jednoho tenanta, které se přímo zakládá do Azure Virtual Network (VNet) vašeho výběru. Je to systém, který používá jenom jeden zákazník. Aplikace nasazené do pomocného mechanismu služeb podléhají funkcím sítě, které se vztahují k podsíti pomocného mechanismu. V aplikacích nejsou žádné další funkce, které je třeba povolit, aby se na ně vztahovaly tyto síťové funkce. 

## <a name="create-an-app-in-an-ase"></a>Vytvoření aplikace v pomocném mechanismu služby

K vytvoření aplikace v pomocném mechanismu řízení použijte stejný postup jako při normálním vytváření aplikace, ale s malým rozdílem. Když vytváříte nový plán App Service:

- Místo výběru geografického umístění, do kterého chcete aplikaci nasadit, si jako umístění zvolíte pomocného objekt pro vytváření.
- Všechny plány App Service vytvořené v pomocném mechanismu služby můžou být jenom v cenové úrovni izolované verze v2.

Pokud nemáte pomocného mechanismu řízení, můžete ho vytvořit podle pokynů v části [vytvoření App Service Environment][MakeASE].

Vytvoření aplikace v pomocném mechanismu řízení:

1. Vyberte **vytvořit prostředek**  >  **web a mobilní zařízení**  >  **webovou aplikaci**.

1. Vyberte předplatné.

1. Zadejte název nové skupiny prostředků nebo vyberte použít existující a v rozevíracím seznamu vyberte **použít existující** a vyberte jednu z nich.

1. Zadejte název aplikace. Pokud jste už v pomocném mechanismu řízení vybrali plán App Service, bude název domény pro aplikaci odpovídat názvu domény pomocného mechanismu řízení:

    ![Vytvoření aplikace v pomocném mechanismu služby][1]

1. Vyberte svůj typ publikování, zásobník a operační systém.

1.  Vyberte oblast. V tomto případě je potřeba vybrat existující App Service Environment v3.  Během vytváření aplikace nemůžete vytvořit ASEv3. 

1. Vyberte existující plán App Service v pomocném mechanismu služby nebo vytvořte nový. Pokud vytváříte novou aplikaci, vyberte požadovanou velikost pro plán App Service. Jediná SKU, kterou si můžete vybrat pro vaši aplikaci, je cenová SKU s izolovaným v2.

    ![Izolované cenové úrovně v2][2]

    > [!NOTE]
    > Aplikace pro Linux a aplikace pro Windows nemůžou být ve stejném plánu App Service, ale můžou být ve stejném App Service Environment.
    >

1. Vyberte **Další: monitorování**  Pokud chcete povolit App Insights s vaší aplikací, můžete to udělat během vytváření toku. 

1.  Vybrat **Další: značky** přidejte libovolné značky, které chcete do aplikace.  

1. Vyberte **zkontrolovat + vytvořit**, ujistěte se, že jsou informace správné, a pak vyberte **vytvořit**.

## <a name="how-scale-works"></a>Jak funguje škálování

Každá aplikace App Service běží v plánu App Service. App Service prostředí uchovávají App Service plány a App Service plány uchovávají aplikace. Při škálování aplikace můžete také škálovat plán App Service a všechny aplikace v tomto plánu.

Při škálování plánu App Service se požadovaná infrastruktura automaticky přidá. Při přidávání infrastruktury je časová prodleva pro škálování operací. Při škálování plánu App Service počká všechny další operace škálování, které požadují stejný operační systém a velikost, a to až do dokončení prvního. Po dokončení operace blokování škálování se zpracují všechny požadavky ve frontě současně. Operace škálování na jedné velikosti a operačním systému nebude blokovat škálování dalších kombinací velikosti a operačního systému. Pokud jste například naplánovali plán Windows I2v2 App Service, všechny ostatní požadavky na škálování Windows I2v2 v tomto pomocném programu budou zařazeny do fronty až do dokončení.   

Ve víceklientské App Service je škálování okamžité, protože fond prostředků je snadno dostupný pro jeho podporu. V pomocném mechanismu služby neexistuje žádná taková vyrovnávací paměť a prostředky se přidělují podle potřeby.

## <a name="app-access"></a>Přístup k aplikacím

V pomocném mechanismu pro nápovědu je přípona domény používaná pro vytváření aplikací *. &lt; asename &gt; . appserviceenvironment.NET*. Pokud je váš správce _přihlášený jako můj_ a Vy v tomto pomocném mechanismu budete hostitelem aplikace s názvem _Contoso_ , dostanete se na tyto adresy URL:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Informace o tom, jak vytvořit pomocného mechanismu řízení, najdete v tématu [vytvoření App Service Environment][MakeASE].

Adresa URL SCM se používá pro přístup ke konzole Kudu nebo k publikování vaší aplikace pomocí Nasazení webu. Informace o konzole Kudu naleznete v tématu [Konzola Kudu pro Azure App Service][Kudu]. Konzola Kudu poskytuje webové uživatelské rozhraní pro ladění, nahrávání souborů, úpravy souborů a mnoho dalšího.

### <a name="dns-configuration"></a>Konfigurace DNS 

Nástroj pro příjem dat používá soukromé koncové body. Nekonfiguruje se automaticky Azure DNS privátní zóny. Pokud chcete použít vlastní server DNS, musíte přidat následující záznamy:

1. Vytvořte zónu pro &lt; název pomocného mechanismu &gt; . appserviceenvironment.NET
1. v této zóně vytvořte záznam A, který odkazuje na příchozí IP adresu, kterou používá privátní koncový bod pro pomocného mechanismu.
1. v této zóně vytvořte záznam A, který odkazuje na příchozí IP adresu, kterou používá privátní koncový bod pro pomocného mechanismu.
1. vytvoření zóny v &lt; názvu pomocného mechanismu &gt; . appserviceenvironment.NET s názvem SCM
1. Vytvořte v zóně SCM záznam A, který odkazuje na IP adresu, kterou používá privátní koncový bod pro pomocného mechanismu.

Postup při konfiguraci DNS v privátních zónách Azure DNS:

1. vytvořit privátní zónu Azure DNS s názvem <ASE name> . appserviceenvironment.NET
1. Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
1. Vytvořte v této zóně záznam A, který odkazuje na IP adresu interního nástroje.
1. Vytvořte v této zóně záznam A, který odkazuje *. SCM na IP adresu interního nástroje.

Nastavení DNS pro výchozí příponu vaší domény pomocného mechanismu přístupu neomezuje vaše aplikace tak, aby byly přístupné jenom pro tyto názvy. V pomocném mechanismu služby můžete nastavit vlastní název domény bez ověřování v aplikacích. Pokud budete chtít vytvořit zónu s názvem *contoso.NET*, můžete to udělat a nasměrovat ji na příchozí IP adresu. Vlastní název domény funguje pro žádosti o aplikace, ale pro web SCM ne. Web SCM je k dispozici pouze na adrese *&lt; AppName &gt; . SCM. &lt; asename &gt; . appserviceenvironment.NET*. 

## <a name="publishing"></a>Publikování

V rámci služby řízení přihlašování jako u víceklientské App Service můžete publikovat pomocí těchto metod:

- Nasazení webu
- Kontinuální integrace (CI)
- Přetažení v konzole Kudu
- Integrované vývojové prostředí (IDE), jako je například Visual Studio, zatmění nebo IntelliJ nápad

Pomocí pomocného mechanismu pro přístup jsou koncové body publikování dostupné jenom prostřednictvím příchozí adresy používané privátním koncovým bodem. Pokud nemáte přístup k síti privátního koncového bodu, nemůžete v tomto pomocném panelu publikovat žádné aplikace.  K tomu, aby bylo možné do interního nástroje Publikovat přímo, musí mít síťový přístup i vaše IDEs.

Bez dalších změn nebudou internetové systémy CI, jako je GitHub a Azure DevOps, fungovat s pomocným mechanismem interního nástroje, protože koncový bod publikování není přístupný na internetu. Publikování na interního nástroje pomocného mechanismu pro Azure můžete povolit z Azure DevOps tím, že ve virtuální síti nainstalujete samoobslužného agenta pro vydanou verzi, který obsahuje ovládací prvek interního nástroje. 

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

Pokud provádíte integraci s Log Analytics, můžete protokoly zobrazit tak, že vyberete **protokoly** z portálu pro pomocné služby a vytvoříte dotaz na **AppServiceEnvironmentPlatformLogs**. Protokoly se vysílají jenom v případě, že má váš správce událostí událost, která ho spustí. Pokud vaše pomocné služby takovou událost neobsahuje, nebudou se žádné protokoly. Pokud chcete rychle zobrazit příklad protokolů v pracovním prostoru Log Analytics, proveďte operaci škálování s jedním z plánů App Service ve vašem pomocném programu. Pak můžete spustit dotaz na **AppServiceEnvironmentPlatformLogs** a zobrazit tyto protokoly. 

**Vytvoření výstrahy**

Pokud chcete vytvořit výstrahu proti svým protokolům, postupujte podle pokynů v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../../azure-monitor/platform/alerts-log.md). V krátkém případě:

* Otevřete stránku výstrahy na portálu pro pomocné služby.
* Vybrat **nové pravidlo výstrahy**
* Vyberte prostředek, který bude vaším Log Analytics pracovním prostorem.
* Nastavte podmínku pomocí vlastního prohledávání protokolu tak, aby používala dotaz, jako je například "AppServiceEnvironmentPlatformLogs | kde ResultDescription obsahuje "Začínáme s škálováním" nebo cokoli, co chcete. Nastavte prahovou hodnotu podle potřeby. 
* Podle potřeby přidejte nebo vytvořte skupinu akcí. Skupina akcí je místo, kde můžete definovat odpověď na výstrahu, například odeslání e-mailu nebo zprávy SMS.
* Pojmenujte upozornění a uložte je.

## <a name="internal-encryption"></a>Interní šifrování

App Service Environment funguje jako černý rámeček, kde nelze zobrazit interní součásti nebo komunikaci v rámci systému. Pokud chcete povolit vyšší propustnost, šifrování není ve výchozím nastavení povolené mezi interními součástmi. Systém je zabezpečený, protože provoz je zcela nepřístupný ke sledování nebo přístupu. Pokud máte požadavek na dodržování předpisů, přestože vyžaduje úplné šifrování cesty k datům z koncového šifrování, můžete to povolit v uživatelském rozhraní **Konfigurace** pomocného mechanismu.

![Povolit interní šifrování][5]

Tím se zašifruje interní síťový provoz ve vašem přihlašování mezi front-endy a pracovními procesy, zašifruje se stránkovací soubor a také zašifruje pracovní disky. Jakmile je InternalEncryption clusterSetting povolený, může to mít dopad na výkon systému. Když provedete změnu a povolíte InternalEncryption, váš pomocný stav bude v nestabilním stavu, dokud se změna zcela nerozšíří. Dokončení šíření změny může trvat několik hodin, a to v závislosti na tom, kolik instancí jste v pomocném mechanismu. Důrazně doporučujeme, abyste tuto možnost nepovolili u pomocného mechanismu, když se používá. Pokud potřebujete tuto možnost povolit u aktivně využívaného mechanismu přihlašování, důrazně doporučujeme, abyste přepnuli provoz do prostředí zálohování, dokud se operace nedokončí.

## <a name="upgrade-preference"></a>Předvolba upgradu

Pokud máte více služby ASE, možná budete chtít, aby některé služby ASE byly upgradovány ještě před ostatními. V rámci objektu pomocného **třída hostingenvironment správce prostředků** objekt můžete nastavit hodnotu **upgradePreference**. Nastavení **upgradePreference** se dá nakonfigurovat pomocí šablony, ARMClient nebo https://resources.azure.com . Tři možné hodnoty jsou:

- **Žádné**: Azure provede upgrade pomocného mechanismu v žádné konkrétní dávce. Tato hodnota je výchozí.
- **Brzy**: vaše pomocného programu bude upgradován v první polovině App Service upgradu.
- **Pozdě**: vaše pomocného programu bude upgradován v druhé polovině App Service upgradu.

Chcete-li nakonfigurovat předvolbu upgradu, použijte uživatelské rozhraní pro **konfiguraci** pomocného uživatelského rozhraní. 

Funkce **upgradePreferences** je nejužitečnější, když máte více služby ASE, protože "předčasné" služby ASE bude upgradován před "pozdě" služby ase. Pokud máte více služby ASE, měli byste nastavit vývoj a testování služby ASE tak, aby byly "nejdříve" a v produkčním služby ASE být "pozdě".

## <a name="delete-an-ase"></a>Odstranění pomocného mechanismu

Postup odstranění pomocného mechanismu řízení:

1. V horní části podokna **App Service Environment** vyberte **Odstranit** .

1. Zadejte název pomocného programu pro potvrzení, že ho chcete odstranit. Když odstraníte pomocného mechanismu řízení, odstraníte také veškerý obsah v něm.

    ![Odstranění pomocného mechanismu][3]

1. Vyberte **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
