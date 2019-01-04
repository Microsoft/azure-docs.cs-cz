---
title: Hybridní připojení - službě Azure App Service | Dokumentace Microsoftu
description: Jak vytvořit a používat Hybrid Connections pro přístup k prostředkům v různých sítích
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 40ff05e9fbc00747145c653878010ad9da0c37ec
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653386"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service Hybrid Connections #

Hybridní připojení je služba v Azure i funkce ve službě Azure App Service. Jako služba se používá a možnosti nad rámec těch, které se používají ve službě App Service. Další informace o hybridních připojeních a jejich použití mimo službu App Service najdete v tématu [Azure Relay Hybrid Connections][HCService].

V rámci služby App Service Hybrid Connections umožňuje přístup k prostředkům aplikace v jiných sítích. Poskytuje přístup z vaší aplikace na koncový bod aplikace. Neumožňuje alternativní možnosti pro přístup k aplikaci. Používá se ve službě App Service, koreluje každé hybridní připojení na jedné kombinaci hostitele a port TCP. To znamená, že koncový bod hybridní připojení může být pro všechny operační systémy a jakékoli aplikace, je k dispozici mají přístup k portu naslouchání TCP. Funkce Hybrid Connections neznáte nebo care co aplikační protokol, nebo co přistupujete. Jednoduše poskytuje přístup k síti.  


## <a name="how-it-works"></a>Jak to funguje ##
Funkce hybridní připojení se skládá ze dvou odchozích volání do Azure Service Bus Relay. Existuje připojení z knihovny na hostiteli, kde se vaše aplikace běží ve službě App Service. Je také připojení ze Správce hybridního připojení (HCM) k předávání přes Service Bus. HCM je služba relay, který nasadíte v rámci sítě, který je hostitelem prostředku, kterou se pokoušíte získat přístup. 

Přes dvě připojení připojené k doméně má vaše aplikace na druhé straně HCM tunelové připojení protokolu TCP na kombinaci pevný port hostitele:. Připojení používá protokol TLS 1.2 pro zabezpečení a klíče sdíleného přístupového podpisu (SAS) pro ověřování a autorizaci.    

![Diagram toku vysoké úrovně hybridní připojení][1]

Pokud vaše aplikace odešle požadavek DNS, který odpovídá konfigurovaný koncový bod hybridní připojení, odchozí TCP provoz bude přesměrován prostřednictvím daného hybridního připojení.  

> [!NOTE]
> To znamená, že snažte se vždy použijte název DNS pro hybridní připojení. Některé klientský software není nutné vyhledávání DNS, pokud koncový bod používá adresu IP místo.
>


### <a name="app-service-hybrid-connection-benefits"></a>Výhody hybridních připojení služby aplikace ###

Existuje mnoho výhod pro schopnost hybridních připojení, včetně:

- Aplikace můžou přistupovat k místním systémům a služby zabezpečené.
- Tato funkce nevyžaduje, aby koncový bod přístupné z Internetu.
- Je rychlé a snadné nastavení. 
- Každé hybridní připojení odpovídá na kombinaci jeden port hostitele: užitečná pro zabezpečení.
- Obvykle nevyžaduje děr brány firewall. Přes standardní webovými porty jsou veškerý odchozí připojení.
- Protože je funkce úrovně sítě, je závislá na jazyk používaný vaší aplikací a technologie používané koncový bod.
- Slouží k poskytování přístupu ve více sítí z jedné aplikace. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Věci, které není možné provádět pomocí hybridních připojení ###

Věci, které není možné provádět pomocí hybridních připojení patří:

- Připojte jednotku.
- Pomocí protokolu UDP.
- Přístup na základě protokolu TCP služeb, které používají dynamické porty, třeba pasivní režim FTP nebo rozšířený pasivní režim.
- Podpora LDAP, protože ho vyžadují UDP.
- Podporu Active Directory, protože nelze připojení k doméně pracovník s App Service.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Přidat a vytvořit hybridní připojení v aplikaci ##

Chcete-li vytvořit hybridní připojení, přejděte [webu Azure portal] [ portal] a vyberte svou aplikaci. Vyberte **sítě** > **nakonfigurovat koncové body hybridního připojení**. Tady vidíte hybridní připojení, které jsou nakonfigurované pro vaši aplikaci.  

![Snímek obrazovky s hybridní připojení seznamu][2]

Chcete-li přidat nové hybridní připojení, **[+] přidat hybridní připojení**.  Zobrazí se vám seznam hybridní připojení, který jste už vytvořili. Chcete-li přidat jeden nebo více z nich do vaší aplikace, vyberte ty a pak vyberte **přidat vybrané hybridní připojení**.  

![Snímek obrazovky s hybridní připojení portálu][3]

Pokud chcete vytvořit nové hybridní připojení, vyberte **vytvořit nové hybridní připojení**. Zadejte: 

- Název hybridního připojení.
- Název hostitele koncového bodu.
- Port koncového bodu.
- Obor názvů Service Bus, které chcete použít.

![Snímek obrazovky vytvořit nové hybridní připojení dialogové okno][4]

Každé hybridní připojení se váže na obor názvů služby Service Bus a každý obor názvů služby Service Bus v rámci oblasti Azure. Je důležité se pokouší použít obor názvů služby Service Bus ve stejné oblasti jako aplikace, aby se zabránilo latenci způsobenou sítě.

Pokud chcete odstranit hybridní připojení z vaší aplikace, pravým tlačítkem myši a vyberte **odpojit**.  

Když hybridní připojení se přidá do vaší aplikace, uvidíte v něm informace jednoduše tak, že ho vyberete. 

![Podrobnosti o snímek obrazovky s hybridní připojení][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Vytvoření hybridního připojení na portálu Azure Relay ###

Kromě portálu prostředí z vaší aplikace, můžete vytvořit hybridní připojení z v rámci portálu Azure Relay. Pro hybridní připojení používané služby App Service musí být:

* Vyžaduje autorizaci klientů.
* Máte položku metadat s názvem koncový bod, který obsahuje kombinaci: port hostitele jako hodnotu.

## <a name="hybrid-connections-and-app-service-plans"></a>Plány hybrid Connections a služby App Service ##

App Service Hybrid Connections jsou dostupné jenom v Basic, Standard, Premium a s izolovanou cenou skladové položky. Existují omezení vázané na cenový plán.  

| Cenový plán | Počet hybridních připojení, které jsou použitelné v plánu |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolated | 200 |

Plán služby App Service uživatelského rozhraní ukazuje, kolik hybridní připojení se používají a jaké aplikace.  

![Vlastnosti plánu snímek obrazovky s App Service][6]

Výběr hybridního připojení zobrazíte podrobnosti. Zobrazí se všechny informace, které jste viděli na zobrazení aplikace. Můžete také zobrazit, kolik aplikací v rámci stejného plánu jsou pomocí toto hybridní připojení.

Platí omezení na počet koncových bodů hybridní připojení, které lze použít v plánu služby App Service. Každé hybridní připojení používají, ale je možné napříč libovolným počtem aplikací v tomto plánu. Například jedno hybridní připojení, který se používá v pěti samostatných aplikací v plánu služby App Service se počítá jako jedno hybridní připojení.

### <a name="pricing"></a>Ceny ###

Kromě zde probíhá požadavek na SKU plánu služby App Service je další náklady na pomocí hybridních připojení. Je poplatek za každý naslouchací proces hybridního připojení používá. Naslouchací proces je správce hybridního připojení. Pokud máte pět Hybrid Connections podporuje dvě správci hybridních připojení, který by 10 naslouchacích procesů. Další informace najdete v tématu [cenách služby Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Správce hybridního připojení ##

Funkce Hybrid Connections vyžaduje přenosový agent v síti, který je hostitelem vašeho koncového bodu hybridní připojení. Tento agent relay se nazývá hybridní připojení správce (HCM). Stáhnout HCM, z vaší aplikace v [webu Azure portal][portal]vyberte **sítě** > **konfiguracekoncovébodyhybridníhopřipojení**.  

Tento nástroj spustí ve Windows serveru 2012 a novějších verzích. HCM běží jako služba a připojí k Azure Relay na portu 443 odchozí.  

Po instalaci HCM, můžete spustit HybridConnectionManagerUi.exe použít uživatelské rozhraní nástroje. Tento soubor je v instalačním adresáři správce hybridního připojení. Ve Windows 10, můžete také pouze vyhledat *uživatelského rozhraní správce hybridního připojení* vyhledávacího pole.  

![Snímek obrazovky Správce hybridního připojení][7]

Při spuštění uživatelského rozhraní HCM, první věc, kterou vidíte je tabulka obsahující seznam všech hybridní připojení, které jsou nakonfigurovány k této instanci HCM. Pokud chcete provést změny, začíná ověřením v Azure. 

K přidání jednoho nebo více hybridní připojení k vaší HCM:

1. Spuštění uživatelského rozhraní HCM.
2. Vyberte **nakonfigurovat jiné hybridní připojení**.
![Snímek obrazovky konfigurace nové hybridní připojení][8]

1. Přihlaste se pomocí svého účtu Azure.
1. Zvolte předplatné.
1. Vyberte hybridní připojení, který chcete HCM předat.
![Snímek obrazovky s hybridními připojeními][9]

1. Vyberte **Uložit**.

Nyní je vidět hybridní připojení, které jste přidali. Můžete také vybrat nakonfigurované hybridní připojení zobrazíte podrobnosti.

![Snímek obrazovky podrobností o hybridní připojení][10]

Hybridní připojení je nakonfigurován s účelem HCM vyžaduje:

- TCP přístup k Azure přes port 443.
- TCP přístup ke koncovému bodu hybridní připojení.
- Možnost provádět look-ups DNS na hostitele koncového bodu a obor názvů služby Service Bus.

> [!NOTE]
> Azure Relay spoléhá na webové sokety pro připojení. Tato možnost je pouze k dispozici v systému Windows Server 2012 nebo novější. Proto HCM nepodporuje žádnou starší než Windows Server 2012.
>

### <a name="redundancy"></a>Redundance ###

Každý HCM může podporovat více hybridní připojení. Navíc všechny daného hybridního připojení může podporovat více HCMs. Výchozí chování je pro směrování provozu přes nakonfigurované HCMs pro libovolný daný koncový bod. Pokud chcete vysoké dostupnosti na hybridní připojení z vaší sítě, spusťte několik HCMs na samostatných počítačích. Algoritmus distribuce zatížení používaný serverem se službou Relay vytvoříte za účelem distribuce provozu do HCMs je náhodné přiřazení. 

### <a name="manually-add-a-hybrid-connection"></a>Ručně přidat hybridní připojení ###

Povolit někdo mimo předplatné hostovat instanci služby HCM pro jedno hybridní připojení, sdílejte připojovací řetězec brány pro hybridní připojení s nimi. Zobrazí se připojovací řetězec brány ve vlastnostech hybridní připojení v [webu Azure portal][portal]. Chcete-li použít tento řetězec, vyberte **zadejte ručně** HCM a vložte připojovací řetězec brány.

![Ručně přidat hybridní připojení][11]

### <a name="upgrade"></a>Upgrade ###

Jsou k dispozici pravidelné aktualizace na správce hybridního připojení k řešení problémů nebo poskytnutí vylepšení. Po vydání upgrady jsou automaticky otevíraného okna se zobrazí v Uživatelském rozhraní HCM. Použitím upgradu použít změny a HCM restartovat. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Přidání hybridního připojení do vaší aplikace prostřednictvím kódu programu ##

Rozhraní API služby jste si poznamenali níže je možné přímo ke správě hybridních připojení připojené k vaší aplikace. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Objekt JSON přidružený k hybridní připojení vypadá takto:

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

Jeden ze způsobů použití těchto informací se armclient, které můžete získat [ARMClient] [ armclient] projektu z Githubu. Tady je příklad existující hybridní připojení se připojuje k vaší aplikace. Vytvořte soubor JSON na výše uvedené schéma jako:

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

Pokud chcete používat toto rozhraní API, je třeba poslat klíči a propojení ID prostředku. Pokud jste uložili vaše informace s hctest.json název souboru, vydávání tohoto příkazu se připojit k aplikaci hybridní připojení: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>Řešení potíží ##

Stav "Připojena" znamená, že aspoň jeden HCM má nakonfigurovanou toto hybridní připojení a je možné navázat komunikaci s Azure. Pokud stav pro hybridní připojení neříkají **připojeno**, hybridní připojení není nakonfigurovaný na jakékoli HCM, který má přístup k Azure.

Primární důvod, proč klienti nemohou připojit k jejich koncový bod je, protože byl zadaný koncový bod s použitím IP adresy místo názvu DNS. Pokud aplikace nemá přístup na požadovaný koncový bod a použili jste IP adresu, přejdete k používání název DNS, který je platný v hostiteli se spuštěným HCM. Také zkontrolujte, jestli název DNS překládá správně na hostiteli, kde je spuštěný HCM. Ujistěte se, že připojení z hostitele se spuštěným HCM ke koncovému bodu hybridní připojení.  

Ve službě App Service může být nástroj tcpping vyvolat pomocí konzoly nástroje Rozšířené nástroje (Kudu). Tento nástroj můžete říct, pokud máte přístup ke koncovému bodu TCP, ale jeho nezjistíte Pokud máte přístup ke koncovému bodu hybridní připojení. Když použijete nástroj v konzole proti koncovým bodem hybridního připojení, pouze potvrzujete, že používá kombinaci: port hostitele.  

## <a name="biztalk-hybrid-connections"></a>Hybridní připojení BizTalk ##

Počáteční formulář této funkce jmenovala BizTalk Hybrid Connections. Tato funkce 31. května 2018 se nepovedlo ukončení životnosti a operace ukončil. Hybridní připojení BizTalk byly odebrány ze všech aplikací a nejsou přístupné prostřednictvím portálu nebo rozhraní API. Pokud stále máte tyto starší připojení nakonfigurovaná v správce hybridního připojení, se zobrazí stav vyřazeno a zobrazit příkazem End životnosti v dolní části.

![Hybridní připojení BizTalk v HCM][12]


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
