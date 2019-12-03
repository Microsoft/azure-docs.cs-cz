---
title: Hybridní připojení
description: Naučte se vytvářet a používat hybridní připojení v Azure App Service k přístupu k prostředkům v různorodých sítích.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: ffc5ee32541cfbbda2ae54fd229c1436f133d730
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671508"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service Hybrid Connections #

Hybrid Connections je služba v Azure a funkce v Azure App Service. Jako služba má využití a možnosti nad rámec těch, které se používají v App Service. Další informace o Hybrid Connections a jejich používání mimo App Service najdete v tématu [Azure Relay Hybrid Connections][HCService].

V rámci App Service lze Hybrid Connections použít pro přístup k prostředkům aplikace v jiných sítích. Poskytuje přístup z vaší aplikace do koncového bodu aplikace. Nepovoluje přístup k vaší aplikaci alternativní možností. Jak se používá v App Service, každé hybridní připojení se koreluje s jedinou kombinací hostitele TCP a portu. To znamená, že koncový bod hybridního připojení může být v jakémkoli operačním systému a libovolné aplikaci, za předpokladu, že přistupujete k portu naslouchání TCP. Funkce Hybrid Connections neví ani nezáleží na tom, jaký je protokol aplikace nebo k čemu přistupujete. Poskytuje jenom přístup k síti.  


## <a name="how-it-works"></a>Jak to funguje ##
Funkce Hybrid Connections se skládá ze dvou odchozích volání do Azure Service Bus Relay. Existuje připojení z knihovny na hostiteli, kde je aplikace spuštěná v App Service. K dispozici je také připojení z Správce hybridního připojení (HCM) k Service Bus Relay. HCM je předávací služba, kterou nasadíte v rámci sítě hostující prostředek, ke kterému se pokoušíte získat přístup. 

Přes dvě připojená připojení má vaše aplikace tunel TCP na pevný hostitel: kombinace portů na druhé straně HCM. Připojení používá TLS 1,2 pro zabezpečení a klíče sdíleného přístupového podpisu (SAS) pro ověřování a autorizaci.    

![Diagram toku vysoké úrovně hybridního připojení][1]

Když vaše aplikace vytvoří požadavek DNS, který odpovídá nakonfigurovanému koncovému bodu hybridního připojení, odchozí přenosy TCP se přesměrují prostřednictvím hybridního připojení.  

> [!NOTE]
> To znamená, že byste se měli pokusit vždy použít název DNS pro hybridní připojení. Některý klientský software neprovede vyhledávání DNS, pokud koncový bod místo toho používá IP adresu. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service výhody hybridního připojení ###

K dispozici je několik výhod Hybrid Connections možností, včetně:

- Aplikace můžou bezpečně přistupovat k místním systémům a službám.
- Tato funkce nevyžaduje koncový bod přístupný z Internetu.
- Nastavení je rychlé a snadné. 
- Každé hybridní připojení se shoduje s jedním hostitelem: kombinací portů, které jsou užitečné pro zabezpečení.
- Obvykle nevyžaduje otvory brány firewall. Připojení jsou všechna odchozí přes standardní webové porty.
- Vzhledem k tomu, že je tato funkce na úrovni sítě, je nezávislá k jazyku, který používá vaše aplikace, a technologii, kterou koncový bod používá.
- Dá se použít k poskytnutí přístupu v několika sítích z jedné aplikace. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Věci, které nemůžete dělat s Hybrid Connections ###

Mezi věci, které nemůžete Hybrid Connections, patří:

- Připojte jednotku.
- Použijte protokol UDP.
- Přístup ke službám založeným na TCP, které používají dynamické porty, jako je pasivní režim FTP nebo rozšířený pasivní režim.
- Podporuje protokol LDAP, protože může vyžadovat protokol UDP.
- Podporuje službu Active Directory, protože se nemůžete připojit k doméně App Service pracovního procesu.

### <a name="prerequisites"></a>Předpoklady ###
 - Služba Windows App Service je povinná. Je k dispozici pouze v systému Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Přidání a vytvoření Hybrid Connections v aplikaci ##

Pokud chcete vytvořit hybridní připojení, otevřete [Azure Portal][portal] a vyberte svou aplikaci. Vyberte **sítě** > **nakonfigurovat koncové body hybridního připojení**. Tady vidíte Hybrid Connections, které jsou pro vaši aplikaci nakonfigurované.  

![Snímek obrazovky se seznamem hybridních připojení][2]

Chcete-li přidat nové hybridní připojení, vyberte **[+] přidat hybridní připojení**.  Zobrazí se seznam Hybrid Connections, které jste už vytvořili. Chcete-li do aplikace přidat jednu nebo více z nich, vyberte požadované položky a pak vyberte **Přidat vybrané hybridní připojení**.  

![Snímek obrazovky s portálem hybridního připojení][3]

Pokud chcete vytvořit nové hybridní připojení, vyberte **vytvořit nové hybridní připojení**. Zadejte: 

- Název hybridního připojení
- Název hostitele koncového bodu
- Port koncového bodu.
- Service Bus obor názvů, který chcete použít.

![Snímek obrazovky s dialogovým oknem vytvořit nové hybridní připojení][4]

Každé hybridní připojení je svázáno s oborem názvů Service Bus a každý Service Bus obor názvů je v oblasti Azure. Je důležité, abyste se pokusili použít Service Bus obor názvů ve stejné oblasti jako vaši aplikaci, aby nedocházelo k vyvolané latenci sítě.

Pokud chcete odebrat hybridní připojení z vaší aplikace, klikněte na něj pravým tlačítkem myši a vyberte **Odpojit**.  

Když se do aplikace přidá hybridní připojení, zobrazí se vám podrobnosti jednoduše tak, že je vyberete. 

![Snímek obrazovky s podrobnostmi o hybridních připojeních][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Vytvoření hybridního připojení na portálu Azure Relay ###

Kromě možnosti portálu z aplikace můžete vytvořit Hybrid Connections z portálu Azure Relay. Aby bylo možné používat hybridní připojení App Service, musí:

* Vyžadovat autorizaci klientů.
* Mít položku metadat s názvem koncový bod, která obsahuje kombinaci hostitel: port, jako hodnotu.

## <a name="hybrid-connections-and-app-service-plans"></a>Plány Hybrid Connections a App Service ##

App Service Hybrid Connections jsou k dispozici pouze v jednotkách Basic, Standard, Premium a Isolated Price. Existují limity vázané na cenový tarif.  

| Cenový tarif | Počet Hybrid Connections použitelný v plánu |
|----|----|
| Úroveň Basic | 5 |
| Úroveň Standard | 25 |
| Premium | 200 |
| Isolated | 200 |

Uživatelské rozhraní App Serviceho plánu ukazuje, kolik Hybrid Connections se používá a co aplikace.  

![Snímek obrazovky s vlastnostmi plánu App Service][6]

Pokud chcete zobrazit podrobnosti, vyberte hybridní připojení. Můžete zobrazit všechny informace, které jste viděli v zobrazení aplikace. Můžete si také prohlédnout, kolik dalších aplikací ve stejném plánu používá hybridní připojení.

Počet koncových bodů hybridního připojení, které se dají použít v plánu App Service, je omezený. Každé používané hybridní připojení se ale dá použít v jakémkoli počtu aplikací v tomto plánu. Například jedno hybridní připojení, které se používá v pěti samostatných aplikacích v plánu App Service, se počítá jako jedno hybridní připojení.

### <a name="pricing"></a>Ceny ###

Kromě toho, že App Service požadavek na SKLADOVOU položku plánu, je k použití Hybrid Connections k dispozici další náklady. U každého naslouchacího procesu používaného hybridním připojením se účtuje poplatek. Naslouchací proces je Správce hybridního připojení. Pokud jste měli pět Hybrid Connections podporovaná dvěma správci hybridního připojení, bude to 10 posluchačů. Další informace najdete v tématu [Service Bus ceny][sbpricing].

## <a name="hybrid-connection-manager"></a>Správce hybridního připojení ##

Funkce Hybrid Connections vyžaduje přenosového agenta v síti, který je hostitelem koncového bodu hybridního připojení. Agent Relay se nazývá Správce hybridního připojení (HCM). Pokud chcete stáhnout HCM, z vaší aplikace v [Azure Portal][portal]vyberte **síť** > **Konfigurace koncových bodů hybridního připojení**.  

Tento nástroj běží na Windows Serveru 2012 a novějším. HCM se spouští jako služba a připojuje odchozí Azure Relay na portu 443.  

Po instalaci HCM můžete spustit HybridConnectionManagerUi. exe, abyste mohli použít uživatelské rozhraní nástroje. Tento soubor je v instalačním adresáři Správce hybridního připojení. Ve Windows 10 můžete také ve vyhledávacím poli Vyhledat *správce hybridního připojení uživatelské rozhraní* .  

![Snímek obrazovky s Správce hybridního připojení][7]

Když spustíte uživatelské rozhraní HCM, první věc, kterou vidíte, je tabulka, která obsahuje seznam všech Hybrid Connections nakonfigurovaných s touto instancí HCM. Pokud chcete provádět nějaké změny, nejdřív se ověřte pomocí Azure. 

Přidání jednoho nebo více Hybrid Connections do HCM:

1. Spusťte uživatelské rozhraní HCM.
2. Vyberte **Konfigurovat jiné hybridní připojení**.
![snímek obrazovky konfigurace nového Hybrid Connections][8]

1. Přihlaste se pomocí účtu Azure, abyste mohli Hybrid Connections k dispozici ve svých předplatných. HCM nebude nadále používat váš účet Azure nad rámec těchto. 
1. Vyberte předplatné.
1. Vyberte Hybrid Connections, který má HCM Relay.
![snímku Hybrid Connections][9]

1. Vyberte **Save** (Uložit).

Teď můžete zobrazit Hybrid Connections, které jste přidali. Můžete také vybrat nakonfigurované hybridní připojení a zobrazit podrobnosti.

![Snímek obrazovky s podrobnostmi o hybridním připojení][10]

Pro podporu Hybrid Connections, ke kterému je nakonfigurovaná, vyžaduje HCM:

- Přístup TCP k Azure přes port 443.
- Přístup TCP ke koncovému bodu hybridního připojení.
- Schopnost provádět hledání DNS na hostiteli koncového bodu a oboru názvů Service Bus.

> [!NOTE]
> Azure Relay spoléhá na připojení přes webové sokety. Tato funkce je k dispozici pouze v systému Windows Server 2012 nebo novějším. Z toho důvodu se HCM nepodporuje na cokoli starší než Windows Server 2012.
>

### <a name="redundancy"></a>Redundance ###

Každý HCM může podporovat více Hybrid Connections. Kromě toho může být jakékoli dané hybridní připojení podporováno více HCMs. Výchozím chováním je směrování provozu napříč nakonfigurovaným HCMs pro libovolný koncový bod. Pokud chcete ve svém Hybrid Connections k dispozici vysokou dostupnost z vaší sítě, spusťte více HCMs na samostatných počítačích. Algoritmus distribuce zatížení používaný službou Relay k distribuci provozu do HCMs je náhodné přiřazení. 

### <a name="manually-add-a-hybrid-connection"></a>Ruční přidání hybridního připojení ###

Pokud chcete někomu mimo předplatné povolit hostování instance HCM pro dané hybridní připojení, nasdílejte připojovací řetězec brány pro hybridní připojení. Připojovací řetězec brány můžete zobrazit ve vlastnostech hybridního připojení v [Azure Portal][portal]. Pokud chcete použít tento řetězec, vyberte **zadat ručně** v HCM a vložte ho do připojovacího řetězce brány.

![Ruční přidání hybridního připojení][11]

### <a name="upgrade"></a>Aktualizace ###

Existují pravidelné aktualizace Správce hybridního připojení, které řeší problémy nebo poskytují vylepšení. Po vydání upgradů se v uživatelském rozhraní HCM zobrazí místní nabídka. Při použití upgradu se změny projeví a restartuje HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programové přidání hybridního připojení do aplikace ##

Rozhraní API zaznamenaná níže se dají použít přímo ke správě Hybrid Connections připojených k vašim aplikacím. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Objekt JSON, který je přidružený k hybridnímu připojení, vypadá takto:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Jedním ze způsobů, jak tyto informace použít, je armclient, který můžete získat z projektu GitHub [armclient][armclient] . Tady je příklad připojení již existujícího hybridního připojení k vaší aplikaci. Vytvořte soubor JSON podle výše uvedeného schématu, jako je:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Pokud chcete používat toto rozhraní API, potřebujete poslat klíč a ID prostředku Relay. Pokud jste uložili informace s názvem souboru hctest. JSON, vydejte tento příkaz k připojení hybridního připojení k vaší aplikaci: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Zabezpečení Hybrid Connections ##

Existující hybridní připojení můžete přidat k ostatním App Service Web Apps všem uživatelům, kteří mají dostatečná oprávnění pro základní Azure Service Bus Relay. To znamená, že pokud musíte zabránit ostatním v používání stejného hybridního připojení (například pokud je cílový prostředek služba, která nemá k dispozici žádné další bezpečnostní opatření, aby se zabránilo neoprávněnému přístupu), musíte uzamknout přístup k Azure. Service Bus Relay.

Kdokoli, kdo má `Reader` přístup k předávání _, uvidí hybridní_ připojení při pokusu o jeho přidání do webové aplikace na webu Azure Portal, ale nebude ho moct _Přidat_ , protože nemá oprávnění k načtení připojovacího řetězce, který se použije k navázání připojení přenosu. Aby bylo možné úspěšně přidat hybridní připojení, musí mít oprávnění `listKeys` (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`). Role `Contributor` nebo jakákoli jiná role, která zahrnuje toto oprávnění pro předávání, umožní uživatelům používat hybridní připojení a přidat ho k vlastním Web Apps.

## <a name="troubleshooting"></a>Řešení potíží ##

Stav "připojeno" znamená, že minimálně jeden HCM je nakonfigurován s tímto hybridním připojením a je schopný získat přístup k Azure. Pokud stav hybridního připojení nefunguje **, vaše**hybridní připojení není nakonfigurované na žádném HCM, které má přístup k Azure.

Primárním důvodem, proč se klienti nemohou připojit ke svému koncovému bodu, je, že koncový bod byl zadán pomocí IP adresy místo názvu DNS. Pokud vaše aplikace nemůže získat přístup k požadovanému koncovému bodu a použili jste IP adresu, přepněte se na použití názvu DNS, který je platný na hostiteli, kde je spuštěný HCM. Také ověřte, že se název DNS správně překládá na hostiteli, kde je spuštěný HCM. Potvrďte, že existuje připojení z hostitele, kde HCM běží na koncovém bodu hybridního připojení.  

V App Service lze nástroj příkazového řádku **tcpping** vyvolat z konzoly Advanced Tools (Kudu). Tento nástroj vám může sdělit, jestli máte přístup k koncovému bodu TCP, ale nezjistí, jestli máte přístup k koncovému bodu hybridního připojení. Když použijete nástroj v konzole nástroje na koncový bod hybridního připojení, potvrzujete jenom to, že používá kombinaci hostitel: port.  

Pokud pro koncový bod máte klienta příkazového řádku, můžete otestovat připojení z konzoly aplikace. Můžete například testovat přístup k koncovým bodům webového serveru pomocí oblé.

## <a name="biztalk-hybrid-connections"></a>Hybridní připojení BizTalk ##

Úvodní forma této funkce byla volána jako BizTalk Hybrid Connections. Tato funkce skončila dne 31. května 2018 a ukončila operace. Hybridní připojení BizTalk se odebrala ze všech aplikací a nejsou přístupná prostřednictvím portálu nebo rozhraní API. Pokud máte pořád tato starší připojení nakonfigurovaná v Správce hybridního připojení, zobrazí se stav zastaveno a v dolní části se zobrazí příkaz Konec životnosti.

![BizTalk Hybrid Connections v HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
