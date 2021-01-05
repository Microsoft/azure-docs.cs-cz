---
title: Hybridní připojení
description: Naučte se vytvářet a používat hybridní připojení v Azure App Service k přístupu k prostředkům v různorodých sítích.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/08/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 16f6a0660fa9aa20f636ee412f3f337bd5dea9b5
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825981"
---
# <a name="azure-app-service-hybrid-connections"></a>Hybridní připojení Azure App Service

Hybrid Connections je služba v Azure a funkce v Azure App Service. Jako služba má využití a možnosti nad rámec těch, které se používají v App Service. Další informace o Hybrid Connections a jejich používání mimo App Service najdete v tématu [Azure Relay Hybrid Connections][HCService].

V rámci App Service lze Hybrid Connections použít pro přístup k prostředkům aplikací v libovolné síti, která může provádět odchozí volání do Azure přes port 443. Hybrid Connections poskytuje přístup z vaší aplikace do koncového bodu TCP a nepovoluje pro přístup k aplikaci nový způsob přístupu. Jak se používá v App Service, každé hybridní připojení se koreluje s jedinou kombinací hostitele TCP a portu. Díky tomu budou mít vaše aplikace přístup k prostředkům v jakémkoli operačním systému, pokud je to koncový bod TCP. Funkce Hybrid Connections neví ani nezáleží na tom, jaký je protokol aplikace nebo k čemu přistupujete. Jednoduše poskytuje přístup k síti.  

## <a name="how-it-works"></a>Jak to funguje ##
Hybrid Connections vyžaduje, aby byl nanasazený přenosový agent, kde může dosáhnout i pro požadovaný koncový bod i pro Azure. Přenosový agent, Správce hybridního připojení (HCM), volá Azure Relay přes port 443. Z webu webové aplikace se App Service infrastruktura také připojuje k Azure Relay jménem vaší aplikace. Přes připojená připojení může aplikace získat přístup k požadovanému koncovému bodu. Připojení používá TLS 1,2 pro zabezpečení a klíče sdíleného přístupového podpisu (SAS) pro ověřování a autorizaci.    

![Diagram toku vysoké úrovně hybridního připojení][1]

Když vaše aplikace vytvoří požadavek DNS, který odpovídá nakonfigurovanému koncovému bodu hybridního připojení, odchozí přenosy TCP se přesměrují prostřednictvím hybridního připojení.  

> [!NOTE]
> To znamená, že byste se měli pokusit vždy použít název DNS pro hybridní připojení. Některý klientský software neprovede vyhledávání DNS, pokud koncový bod místo toho používá IP adresu. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service výhody hybridního připojení ###

K dispozici je několik výhod Hybrid Connections možností, včetně:

- Aplikace můžou bezpečně přistupovat k místním systémům a službám.
- Tato funkce nevyžaduje koncový bod přístupný z Internetu.
- Nastavení je rychlé a snadné. Nevyžadují se žádné brány.
- Každé hybridní připojení se shoduje s jedním hostitelem: kombinací portů, které jsou užitečné pro zabezpečení.
- Obvykle nevyžaduje otvory brány firewall. Připojení jsou všechna odchozí přes standardní webové porty.
- Vzhledem k tomu, že je tato funkce na úrovni sítě, je nezávislá k jazyku, který používá vaše aplikace, a technologii, kterou koncový bod používá.
- Dá se použít k poskytnutí přístupu v několika sítích z jedné aplikace. 
- Podporuje se v GA pro nativní aplikace pro Windows a je ve verzi Preview pro aplikace pro Linux. Pro aplikace pro kontejner Windows se nepodporuje.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Věci, které nemůžete dělat s Hybrid Connections ###

Mezi věci, které nemůžete Hybrid Connections, patří:

- Připojte jednotku.
- Použijte protokol UDP.
- Přístup ke službám založeným na TCP, které používají dynamické porty, jako je pasivní režim FTP nebo rozšířený pasivní režim.
- Podporuje protokol LDAP, protože může vyžadovat protokol UDP.
- Podporuje službu Active Directory, protože se nemůžete připojit k doméně App Service pracovního procesu. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Přidání a vytvoření Hybrid Connections v aplikaci ##

Pokud chcete vytvořit hybridní připojení, otevřete [Azure Portal][portal] a vyberte svou aplikaci. Vyberte **sítě**  >  **Konfigurace koncových bodů hybridního připojení**. Tady vidíte Hybrid Connections, které jsou pro vaši aplikaci nakonfigurované.  

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
| Základní | 5 podle plánu |
| Standard | 25 na plán |
| PremiumV2 | 200 na aplikaci |
| Isolated | 200 na aplikaci |

Uživatelské rozhraní App Serviceho plánu ukazuje, kolik Hybrid Connections se používá a co aplikace.  

![Snímek obrazovky s vlastnostmi plánu App Service][6]

Pokud chcete zobrazit podrobnosti, vyberte hybridní připojení. Můžete zobrazit všechny informace, které jste viděli v zobrazení aplikace. Můžete si také prohlédnout, kolik dalších aplikací ve stejném plánu používá hybridní připojení.

Počet koncových bodů hybridního připojení, které se dají použít v plánu App Service, je omezený. Každé používané hybridní připojení se ale dá použít v jakémkoli počtu aplikací v tomto plánu. Například jedno hybridní připojení, které se používá v pěti samostatných aplikacích v plánu App Service, se počítá jako jedno hybridní připojení.

### <a name="pricing"></a>Ceny ###

Kromě toho, že App Service požadavek na SKLADOVOU položku plánu, je k použití Hybrid Connections k dispozici další náklady. U každého naslouchacího procesu používaného hybridním připojením se účtuje poplatek. Naslouchací proces je Správce hybridního připojení. Pokud jste měli pět Hybrid Connections podporovaná dvěma správci hybridního připojení, bude to 10 posluchačů. Další informace najdete v tématu [Service Bus ceny][sbpricing].

## <a name="hybrid-connection-manager"></a>Správce hybridního připojení ##

Funkce Hybrid Connections vyžaduje přenosového agenta v síti, který je hostitelem koncového bodu hybridního připojení. Agent Relay se nazývá Správce hybridního připojení (HCM). Pokud chcete stáhnout HCM, z vaší aplikace v [Azure Portal][portal]vyberte **sítě**  >  **Konfigurace koncových bodů hybridního připojení**.  

Tento nástroj běží na Windows Serveru 2012 a novějším. HCM se spouští jako služba a připojuje odchozí Azure Relay na portu 443.  

Po instalaci HCM můžete spustit HybridConnectionManagerUi.exe pro použití uživatelského rozhraní nástroje. Tento soubor je v instalačním adresáři Správce hybridního připojení. Ve Windows 10 můžete také ve vyhledávacím poli Vyhledat *správce hybridního připojení uživatelské rozhraní* .  

![Snímek obrazovky s Správce hybridního připojení][7]

Když spustíte uživatelské rozhraní HCM, první věc, kterou vidíte, je tabulka, která obsahuje seznam všech Hybrid Connections nakonfigurovaných s touto instancí HCM. Pokud chcete provádět nějaké změny, nejdřív se ověřte pomocí Azure. 

Přidání jednoho nebo více Hybrid Connections do HCM:

1. Spusťte uživatelské rozhraní HCM.
2. Vyberte **Konfigurovat jiné hybridní připojení**.
![Snímek obrazovky s konfigurací nových Hybrid Connections][8]

1. Přihlaste se pomocí účtu Azure, abyste mohli Hybrid Connections k dispozici ve svých předplatných. HCM nebude nadále používat váš účet Azure nad rámec těchto. 
1. Zvolte předplatné.
1. Vyberte Hybrid Connections, který má HCM Relay.
![Snímek obrazovky s Hybrid Connections][9]

1. Vyberte **Uložit**.

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

### <a name="upgrade"></a>Upgrade ###

Existují pravidelné aktualizace Správce hybridního připojení, které řeší problémy nebo poskytují vylepšení. Po vydání upgradů se v uživatelském rozhraní HCM zobrazí místní nabídka. Při použití upgradu se změny projeví a restartuje HCM. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programové přidání hybridního připojení do aplikace ##

Pro Hybrid Connections existuje podpora rozhraní příkazového řádku Azure. Zadané příkazy fungují jak na úrovni plánu aplikace, tak na App Service.  Příkazy na úrovni aplikace jsou:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

Příkazy plánování App Service umožňují nastavit, který klíč dané hybridní připojení bude používat. Existují dva klíče nastavené pro každé hybridní připojení, primární a sekundární. Pomocí níže uvedených příkazů se můžete rozhodnout použít primární nebo sekundární klíč. To vám umožní přepínat klíče pro, kdy chcete pravidelně znovu vygenerovat klíče. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Zabezpečení Hybrid Connections ##

Existující hybridní připojení můžete přidat k ostatním App Service Web Apps všem uživatelům, kteří mají dostatečná oprávnění pro základní Azure Service Bus Relay. To znamená, že pokud musíte zabránit ostatním v používání stejného hybridního připojení (například pokud je cílový prostředek služba, která nemá k dispozici žádné další bezpečnostní opatření, aby se zabránilo neoprávněnému přístupu), musíte uzamknout přístup k Azure Service Bus Relay.

Kdokoli, kdo má `Reader` přístup k Relay, uvidí hybridní  připojení při pokusu o jeho přidání do webové aplikace v Azure Portal, ale nebude ho moct _Přidat_ , protože nemá oprávnění k načtení připojovacího řetězce, který se používá k navázání připojení přenosu. Aby bylo možné úspěšně přidat hybridní připojení, musí mít `listKeys` oprávnění ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ). `Contributor`Role nebo jakákoli jiná role, která zahrnuje toto oprávnění pro předávání, umožní uživatelům používat hybridní připojení a přidat ho do vlastních Web Apps.

## <a name="troubleshooting"></a>Řešení potíží ##

Stav "připojeno" znamená, že minimálně jeden HCM je nakonfigurován s tímto hybridním připojením a je schopný získat přístup k Azure. Pokud stav hybridního připojení nefunguje **, vaše** hybridní připojení není nakonfigurované na žádném HCM, které má přístup k Azure.

Primárním důvodem, proč se klienti nemohou připojit ke svému koncovému bodu, je, že koncový bod byl zadán pomocí IP adresy místo názvu DNS. Pokud vaše aplikace nemůže získat přístup k požadovanému koncovému bodu a použili jste IP adresu, přepněte se na použití názvu DNS, který je platný na hostiteli, kde je spuštěný HCM. Také ověřte, že se název DNS správně překládá na hostiteli, kde je spuštěný HCM. Potvrďte, že existuje připojení z hostitele, kde HCM běží na koncovém bodu hybridního připojení.  

V App Service lze nástroj příkazového řádku **tcpping** vyvolat z konzoly Advanced Tools (Kudu). Tento nástroj vám může sdělit, jestli máte přístup k koncovému bodu TCP, ale nezjistí, jestli máte přístup k koncovému bodu hybridního připojení. Když použijete nástroj v konzole nástroje na koncový bod hybridního připojení, potvrzujete jenom to, že používá kombinaci hostitel: port.  

Pokud pro koncový bod máte klienta příkazového řádku, můžete otestovat připojení z konzoly aplikace. Můžete například testovat přístup k koncovým bodům webového serveru pomocí oblé.


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
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
