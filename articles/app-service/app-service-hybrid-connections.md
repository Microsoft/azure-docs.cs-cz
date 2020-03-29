---
title: Hybridní připojení
description: Zjistěte, jak vytvořit a používat hybridní připojení ve službě Azure App Service pro přístup k prostředkům v různorodých sítích.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: ec842530f3cae26b869a649617f279d204b98fcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047776"
---
# <a name="azure-app-service-hybrid-connections"></a>Hybridní připojení služby Azure App Service

Hybridní připojení je služba v Azure i funkce ve službě Azure App Service. Jako služba má použití a možnosti nad rámec těch, které se používají ve službě App Service. Další informace o hybridních připojeních a jejich využití mimo službu App Service najdete v [tématu Hybridní připojení azure relay][HCService].

V rámci služby App Service lze hybridní připojení použít pro přístup k prostředkům aplikací v jiných sítích. Poskytuje přístup z vaší aplikace ke koncovému bodu aplikace. Neumožňuje alternativní možnost přístupu k aplikaci. Jak se používá ve službě App Service, každé hybridní připojení koreluje s jedním hostitelem TCP a kombinací portů. To znamená, že koncový bod hybridního připojení může být v libovolném operačním systému a libovolné aplikaci za předpokladu, že přistupujete k portu naslouchání Protokolu TCP. Funkce Hybridní připojení neví ani se nestará o to, co je aplikační protokol nebo k čemu přistupujete. Je to prostě poskytuje přístup k síti.  


## <a name="how-it-works"></a>Jak to funguje ##
Funkce Hybridní připojení se skládá ze dvou odchozích volání k přenosu služby Azure Service Bus Relay. Je připojení z knihovny na hostiteli, kde vaše aplikace běží ve službě App Service. K dispozici je také připojení z Hybrid Connection Manager (HCM) do service bus relay. HCM je přenosová služba, kterou nasadíte v rámci sítě hostující prostředek, ke kterému se pokoušíte získat přístup. 

Prostřednictvím dvou spojených připojení má vaše aplikace tunelové propojení TCP na kombinaci pevného hostitele:port na druhé straně HCM. Připojení používá TLS 1.2 pro zabezpečení a sdílené přístupové podpisy (SAS) klíče pro ověřování a autorizaci.    

![Diagram hybridního připojení na vysoké úrovni toku][1]

Když vaše aplikace vytvoří požadavek DNS, který odpovídá nakonfigurovanému koncovému bodu hybridního připojení, bude odchozí přenos protokolu TCP přesměrován prostřednictvím hybridního připojení.  

> [!NOTE]
> To znamená, že byste se měli pokusit vždy použít název DNS pro hybridní připojení. Některý klientský software neprovádí vyhledávání DNS, pokud koncový bod místo toho používá adresu IP. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Výhody hybridního připojení služby App Service ###

Možnost hybridních připojení má řadu výhod, včetně:

- Aplikace mají bezpečný přístup k místním systémům a službám.
- Tato funkce nevyžaduje koncový bod přístupný z internetu.
- Nastavení je rychlé a snadné. 
- Každé hybridní připojení odpovídá jedné kombinaci host:port, která je užitečná pro zabezpečení.
- To obvykle nevyžaduje firewall díry. Všechna připojení jsou odchozí přes standardní webové porty.
- Vzhledem k tomu, že funkce je úroveň sítě, je agnostik na jazyk používaný vaší aplikací a technologie používané koncovým bodem.
- Lze jej použít k poskytování přístupu ve více sítích z jedné aplikace. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Věci, které nelze dělat s hybridními připojeními ###

Mezi věci, které s hybridními připojeními nemůžete dělat, patří:

- Namontujte disk.
- Použijte UDP.
- Přístup ke službám založeným na protokolu TCP, které používají dynamické porty, například pasivní režim FTP nebo rozšířený pasivní režim.
- Podpora LDAP, protože může vyžadovat UDP.
- Podporujte službu Active Directory, protože se nelze připojit k pracovníkovi služby App Service.

### <a name="prerequisites"></a>Požadavky ###
 - Je vyžadována služba Aplikace pro Windows. Je k dispozici pouze v systému Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Přidání a vytvoření hybridních připojení v aplikaci ##

Pokud chcete vytvořit hybridní připojení, přejděte na [portál Azure][portal] a vyberte aplikaci. Vyberte**možnost Konfigurace koncových bodů hybridního připojení** **v** > síti . Tady najdete hybridní připojení, která jsou nakonfigurovaná pro vaši aplikaci.  

![Snímek obrazovky se seznamem hybridního připojení][2]

Chcete-li přidat nové hybridní připojení, vyberte **možnost [+] Přidat hybridní připojení**.  Zobrazí se seznam hybridních připojení, které jste již vytvořili. Pokud chcete do aplikace přidat jednu nebo víc, vyberte ty, které chcete, a pak vyberte **Přidat vybrané hybridní připojení**.  

![Snímek obrazovky portálu Hybridní připojení][3]

Pokud chcete vytvořit nové hybridní připojení, vyberte **Vytvořit nové hybridní připojení**. Zadejte: 

- Název hybridního připojení.
- Název hostitele koncového bodu.
- Port koncového bodu.
- Obor názvů služby Service Bus, který chcete použít.

![Snímek obrazovky s dialogovým oknem Vytvořit nové hybridní připojení][4]

Každé hybridní připojení je svázané s oborem názvů Service Bus a každý obor názvů Service Bus je v oblasti Azure. Je důležité pokusit se použít obor názvů Service Bus ve stejné oblasti jako vaše aplikace, abyste se vyhnuli latenci vyvolané sítí.

Pokud chcete hybridní připojení z aplikace odebrat, klikněte na něj pravým tlačítkem myši a vyberte **odpojit**.  

Když je do aplikace přidáno hybridní připojení, můžete v ní zobrazit podrobnosti jednoduše tak, že ho vyberete. 

![Snímek obrazovky s podrobnostmi o hybridních připojeních][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Vytvoření hybridního připojení na portálu Azure Relay ###

Kromě prostředí portálu z vaší aplikace můžete vytvořit hybridní připojení z portálu Azure Relay. Aby bylo hybridní připojení používáno službou App Service, musí:

* Vyžadovat autorizaci klienta.
* Mít položku metadat s názvem koncový bod, který obsahuje kombinaci host:port jako hodnotu.

## <a name="hybrid-connections-and-app-service-plans"></a>Plány hybridních připojení a služby App Service ##

Hybridní připojení služby App Service jsou k dispozici pouze v základních, standardních, prémiových a izolovaných cenových skumátech. Existují limity vázané na cenový plán.  

| Cenový plán | Počet hybridních připojení použitelných v plánu |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Izolovaný | 200 |

U i. plánu služby App Service ukazuje, kolik hybridních připojení se používá a jaké aplikace.  

![Snímek obrazovky s vlastnostmi plánu služby App Service][6]

Podrobnosti zobrazíte výběrem možnosti Hybridní připojení. Všechny informace, které jste viděli, se zobrazí v zobrazení aplikace. Můžete také zjistit, kolik dalších aplikací ve stejném plánu používá toto hybridní připojení.

Počet koncových bodů hybridního připojení, které lze použít v plánu služby App Service, je omezen. Každé hybridní připojení, které se však používá v libovolném počtu aplikací v tomto plánu. Například jedno hybridní připojení, které se používá v pěti samostatných aplikacích v plánu služby App Service, se počítá jako jedno hybridní připojení.

### <a name="pricing"></a>Ceny ###

Kromě toho, že je požadavek sku plánu služby App Service, je další náklady na používání hybridnípřipojení. Za každý naslouchací proces používaný hybridním připojením se účtuje poplatek. Naslouchací proces je Správce hybridního připojení. Pokud jste měli pět hybridní připojení podporované dvěma správci hybridní připojení, to by bylo 10 posluchačů. Další informace naleznete v tématu [Service Bus pricing][sbpricing].

## <a name="hybrid-connection-manager"></a>Správce hybridního připojení ##

Funkce Hybridní připojení vyžaduje přenosového agenta v síti, který je hostitelem koncového bodu hybridního připojení. Tento přenosový agent se nazývá Hybrid Connection Manager (HCM). Chcete-li stáhnout HCM, vyberte z aplikace na [webu Azure Portal][portal]možnost Konfigurace**koncových bodů hybridního připojení v** **síti** > .  

Tento nástroj je spuštěn v systému Windows Server 2012 a novějším. HCM běží jako služba a připojuje odchozí Azure Relay na portu 443.  

Po instalaci HCM můžete spustit nástroj HybridConnectionManagerUi.exe a použít pro tento nástroj ui. Tento soubor je v instalačním adresáři programu Hybrid Connection Manager. Ve Windows 10 můžete taky ve vyhledávacím poli vyhledat *ui nástroje Hybrid Connection Manager.*  

![Snímek obrazovky správce hybridního připojení][7]

Při spuštění ui HCM, první věc, kterou vidíte, je tabulka, která uvádí všechny hybridní připojení, které jsou nakonfigurovány s touto instancí HCM. Pokud chcete provést nějaké změny, nejprve ověřte pomocí Azure. 

Přidání jednoho nebo více hybridních připojení do hcm:

1. Spusťte ui HCM.
2. Vyberte **Konfigurovat jiné hybridní připojení**.
![Snímek obrazovky konfigurace nových hybridních připojení][8]

1. Přihlaste se pomocí svého účtu Azure a získejte hybridní připojení k dispozici s vašimi předplatnými. HCM nenadále používá váš účet Azure nad rámec toho. 
1. Zvolte předplatné.
1. Vyberte hybridní připojení, které má HCM předávat.
![Snímek obrazovky hybridních připojení][9]

1. Vyberte **Uložit**.

Nyní můžete zobrazit hybridní připojení, které jste přidali. Můžete také vybrat nakonfigurované hybridní připojení a zobrazit podrobnosti.

![Snímek obrazovky s podrobnostmi o hybridním připojení][10]

Pro podporu hybridních připojení, se kterými je nakonfigurován, vyžaduje HCM:

- Tcp přístup k Azure přes port 443.
- Přístup TCP ke koncovému bodu hybridního připojení.
- Možnost vyhledávání DNS na hostiteli koncového bodu a v oboru názvů Service Bus.

> [!NOTE]
> Azure Relay spoléhá na webové sokety pro připojení. Tato funkce je k dispozici pouze v systému Windows Server 2012 nebo novějším. Z tohoto důvodu hcm není podporována na nic dříve než Windows Server 2012.
>

### <a name="redundancy"></a>Redundance ###

Každý HCM může podporovat více hybridních připojení. Také jakékoli dané hybridní připojení může být podporováno více HCM. Výchozí chování je směrovat provoz přes nakonfigurované HCMs pro daný koncový bod. Pokud chcete vysokou dostupnost na hybridní připojení ze sítě, spusťte více HCM na samostatných počítačích. Algoritmus rozložení zatížení používaný službou Relay k distribuci přenosů do HCM je náhodné přiřazení. 

### <a name="manually-add-a-hybrid-connection"></a>Ruční přidání hybridního připojení ###

Chcete-li povolit někomu mimo vaše předplatné, aby hostoval instanci HCM pro dané hybridní připojení, sdílejte s nimi připojovací řetězec brány pro hybridní připojení. Připojovací řetězec brány najdete ve vlastnostech hybridního připojení na [webu Azure Portal][portal]. Chcete-li tento řetězec použít, vyberte v hcm **ručně zadat** a vložte do připojovacího řetězce brány.

![Ruční přidání hybridního připojení][11]

### <a name="upgrade"></a>Upgrade ###

Existují pravidelné aktualizace správce hybridního připojení k opravě problémů nebo poskytnutí vylepšení. Po vydání upgradů se v ui hcm zobrazí vyskakovací okno. Použití upgradu použije změny a restartuje HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programové přidání hybridního připojení do aplikace ##

Níže uvedená řešení API lze použít přímo ke správě hybridních připojení připojených k vašim aplikacím. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Objekt JSON přidružený k hybridnímu připojení vypadá takto:

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

Jedním ze způsobů, jak použít tyto informace je s armclient, který můžete získat z projektu [ARMClient][armclient] GitHub. Tady je příklad připojení již existujícího hybridního připojení k vaší aplikaci. Vytvořte soubor JSON podle výše uvedeného schématu, jako je:

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

Chcete-li použít toto rozhraní API, potřebujete id odeslat klíč a prostředek přenosu. Pokud jste uložili své informace s názvem souborhctest.json, vydejte tento příkaz pro připojení hybridního připojení k aplikaci: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Zabezpečte hybridní připojení ##

Existující hybridní připojení může přidat do jiných webových aplikací služby App Service libovolný uživatel, který má dostatečná oprávnění pro základní přenos služby Azure Service Bus. To znamená, že pokud musíte zabránit ostatním uživatelům v opakovaném použití stejného hybridního připojení (například když cílový prostředek je služba, která nemá žádná další bezpečnostní opatření, která by zabránila neoprávněnému přístupu), musíte uzamknout přístup k Azure Relé sběrnice.

Každý, `Reader` kdo má přístup k přenosu bude moci _zobrazit_ hybridní připojení při pokusu o jeho přidání do své webové aplikace na webu Azure Portal, ale nebudou moci _přidat,_ protože nemají oprávnění k načtení připojovacířetězec, který se používá k navázání připojení přenosu. Aby bylo možné úspěšně přidat hybridní připojení, `listKeys` musí`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`mít oprávnění ( ). Role `Contributor` nebo jakákoli jiná role, která zahrnuje toto oprávnění v přenosu, umožní uživatelům používat hybridní připojení a přidat ho do svých vlastních webových aplikací.

## <a name="troubleshooting"></a>Řešení potíží ##

Stav "Připojeno" znamená, že alespoň jeden HCM je nakonfigurován s hybridní připojení a je možné dosáhnout Azure. Pokud stav hybridního připojení neříká **Připojeno**, hybridní připojení není nakonfigurované na žádné HCM, který má přístup k Azure.

Primární důvod, proč se klienti nemohou připojit ke svému koncovému bodu, je, že koncový bod byl určen pomocí adresy IP namísto názvu DNS. Pokud vaše aplikace nedosáhne požadovaného koncového bodu a použili jste IP adresu, přepněte na název DNS, který je platný na hostiteli, kde je hcm spuštěn. Zkontrolujte také, zda se název DNS správně překládá na hostiteli, kde je hcm spuštěn. Zkontrolujte, zda je připojení z hostitele, kde hcm běží na koncový bod hybridní připojení.  

Ve službě App Service lze nástroj příkazového řádku **pro připínání** na ovládací panel Upřesnit nástroje (Kudu). Tento nástroj vám může zjistit, zda máte přístup ke koncovému bodu TCP, ale neříká, pokud máte přístup ke koncovému bodu hybridního připojení. Při použití nástroje v konzole proti koncovému bodu hybridní připojení, potvrzujete pouze, že používá kombinaci host:port.  

Pokud máte klienta příkazového řádku pro váš koncový bod, můžete otestovat připojení z konzoly aplikace. Můžete například otestovat přístup ke koncovým bodům webového serveru pomocí curl.

## <a name="biztalk-hybrid-connections"></a>BizTalk hybridní připojení ##

Počáteční forma této funkce se nazývala BizTalk hybridní připojení. Tato schopnost skončila 31. května 2018 a ukončila provoz. Hybridní připojení BizTalk byla odebrána ze všech aplikací a nejsou přístupná prostřednictvím portálu nebo rozhraní API. Pokud máte stále tato starší připojení nakonfigurovaná ve Správci hybridních připojení, zobrazí se stav Ukončeno a v dolní části se zobrazí příkaz Konec životnosti.

![BizTalk hybridní připojení v HCM][12]


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
