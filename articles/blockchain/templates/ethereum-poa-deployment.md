---
title: Nasazení šablony řešení Ethereem pro kontrolu pravopisu pro účely úřadu v Azure
description: Nasazení a konfigurace Ethereemé sítě konsorcia Ethereem pro více členů v Azure pomocí řešení pro kontrolu pravopisu
ms.date: 04/08/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: c3e449c1d6ebaf7c6cb2c35dc9f91d55f569447a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326178"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Nasazení šablony řešení Ethereem pro kontrolu pravopisu pro účely úřadu v Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Šablona řešení Ethereem (remembers-of-Authority Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) ) pro Azure je navržená tak, aby usnadnila nasazení, konfiguraci a řízení Ethereem sítě pro ověření ve více členských sdruženích s minimálním vědomím Azure a ethereem.

Díky několik uživatelským vstupům a nasazení jedním kliknutím prostřednictvím Azure Portal může každý člen zřídit síťové nároky pomocí Microsoft Azure výpočetních, síťových a služby úložiště po celém světě. Nároky na síť jednotlivých členů se skládají ze sady uzlů validátoru s vyrovnáváním zatížení, se kterými může aplikace nebo uživatel interagovat s odesláním Ethereem transakcí.

## <a name="concepts"></a>Koncepty

### <a name="terminology"></a>Terminologie

-   **Konsensu** – jedná se o synchronizaci dat napříč distribuovanou sítí prostřednictvím ověření bloku a vytvoření.

-   **Člen konsorcia** – entita, která se účastní konsensu v blockchain síti.

-   **Správce** – účet ethereem, který se používá ke správě účasti pro daného člena konsorcia.

-   **Validátor** – počítač přidružený k účtu ethereem, který se účastní konsensu jménem správce.

### <a name="proof-of-authority"></a>Kontrola-of-Authority

Pro ty, které jsou v komunitě blockchain novinkou, je vydání tohoto řešení skvělou příležitostí k tomu, abyste se mohli snadno a konfigurovatelným způsobem dozvědět o technologii Azure. Důkazem o práci je Sybil mechanismus, který využívá výpočetní náklady k samoobslužnému regulování sítě a umožňuje spravedlivou účast. To je ideální pro anonymní otevřené sítě blockchain, kde soutěž pro cryptocurrency propaguje zabezpečení v síti. V privátních nebo konsorciných sítích ale základní ether neobsahuje žádnou hodnotu. Alternativní protokol, ověření platnosti, je vhodnější pro povolené sítě, kde jsou všichni účastníci konsensui známí a jsou renomovaní. Bez nutnosti dolování je ověření autority efektivnější, pokud stále zůstává Byzantine odolnost proti chybám.

### <a name="consortium-governance"></a>Zásady správného řízení konsorcia

Vzhledem k tomu, že ověření stavu spoléhá na povolený seznam síťových autorit, aby byla síť v pořádku, je důležité zajistit, aby se v tomto seznamu oprávnění provedly úpravy. Každé nasazení se dodává se sadou inteligentních kontraktů a portálu pro zásady správného řízení tohoto seznamu povolených certifikátů. Jakmile navrhovaná změna dosáhne většiny hlasů členů konsorcia, je tato změna určena. To umožňuje, aby se noví účastníci konsensu mohli přidat nebo napadnout, aby je mohli odebrat transparentním způsobem, který podporuje pochybnou síť.

### <a name="admin-account"></a>Účet správce

Během nasazování uzlů pro ověření oprávnění se zobrazí výzva k zadání adresy správce Ethereem. K vygenerování a zabezpečení tohoto účtu Ethereem můžete použít několik různých mechanismů. Jakmile tuto adresu přidáte jako autoritu v síti, můžete tento účet použít k účasti na řízení. Tento účet správce bude taky sloužit k delegování konsensu v účasti na uzlech validátoru, které jsou vytvořené jako součást tohoto nasazení. Vzhledem k tomu, že se používá jenom veřejná Ethereem adresa, má každý správce flexibilitu pro zabezpečení svých privátních klíčů způsobem, který odpovídá jejich požadovanému modelu zabezpečení.

### <a name="validator-node"></a>Uzel validátoru

V protokolu ověření autority vybírají uzly validátory místo tradičních uzlů Miner. Každý validátor má jedinečnou Ethereem identitu, která se přidá do seznamu oprávnění inteligentního kontraktu. Jakmile je validátor na tomto seznamu, může se zúčastnit procesu vytváření bloku. Další informace o tomto procesu najdete v dokumentaci ke paritě na [autoritě](https://wiki.parity.io/Aura)na základě souhlasu. Každý člen konsorcia může zřídit dva nebo více uzlů validátoru v rámci pěti oblastí pro geografickou redundanci. Uzly validátoru komunikují s jinými uzly validátoru a přijdou do konsensu na stav základní distribuované hlavní knihy.
Aby se zajistila poctivá účast v síti, je pro každého člena konsorcia zakázáno používat více validátorů než první člen sítě (Pokud první člen nasadí tři validátory, každý člen může mít maximálně tři validátory).

### <a name="identity-store"></a>Úložiště identit

Vzhledem k tomu, že každý člen bude mít současně spuštěno několik uzlů validátoru a každý uzel musí mít povolenou identitu, je důležité, aby validátory mohli bezpečně získat jedinečnou aktivní identitu v síti. Abychom to usnadnili, vytvořili jsme úložiště identit, které se nasadí v předplatném každého člena, které bezpečně udržuje vygenerované Ethereem identity. Po nasazení vygeneruje kontejner orchestrace Ethereem privátní klíč pro každý validátor a uloží jej do Azure Key Vault. Před spuštěním uzlu parity nejprve získá zapůjčení nevyužité identity, aby se zajistilo, že identita nebude vyzvednuta jiným uzlem. Identita se poskytne klientovi, který mu dává oprávnění začít vytvářet bloky. Pokud dojde k výpadku hostitelského virtuálního počítače, zapůjčení identity se uvolní a umožní náhradnímu uzlu obnovit jeho identitu v budoucnu.

### <a name="bootnode-registrar"></a>Registrátor Bootnode

Aby bylo možné snadno připojit, každý člen bude hostovat sadu informací o připojení na [koncovém bodu rozhraní data API](#data-api). Tato data obsahují seznam bootnodesů, které jsou k dispozici jako partnerské uzly pro spojující člena. V rámci tohoto datového rozhraní API udržujeme tento seznam bootnode aktuální.

### <a name="bring-your-own-operator"></a>Přineste si vlastní operátor

Člen konsorcia se často bude chtít zapojit do zásad správného řízení sítě, ale nechce provozovat a spravovat jejich infrastrukturu. Na rozdíl od tradičních systémů funguje jeden operátor napříč sítí na decentralizovaný model blockchain systémů. Místo toho, aby bylo možné provozovat síť centralizovaným zprostředkovatelem, může každý člen konsorcia delegovat správu infrastruktury na operátor jejich výběru. To umožňuje hybridní model, ve kterém se každý člen může rozhodnout pro provoz své vlastní infrastruktury nebo operace delegáta pro jiného partnera. Pracovní postup delegované operace funguje takto:

1.  **Člen konsorcia** generuje adresu ethereem (drží privátní klíč).

2.  **Člen konsorcia** poskytuje veřejnou Ethereem adresu **operátoru** .

3.  **Operátor** nasadí a nakonfiguruje uzly validátoru POA pomocí našeho řešení Azure Resource Manager.

4.  **Operátor** poskytuje člena služby RPC and Management Endpoint na **konsorcium** .

5.  **Člen konsorcia** používá svůj privátní klíč k podepsání žádosti o přijetí **operátoru** ověřovacích uzlů, aby se účastnil jeho jménem.

### <a name="azure-monitor"></a>Azure Monitor

Toto řešení je také součástí Azure Monitor ke sledování statistiky uzlů a sítě. Pro vývojáře aplikací to poskytuje přehled o základních blockchain ke sledování statistik generování bloku. Operátoři sítě mohou pomocí Azure Monitor rychle zjistit a zabránit výpadkům sítě prostřednictvím statistik infrastruktury a protokolů Queryable. Další informace najdete v tématu [monitorování služby](#service-monitoring).

### <a name="deployment-architecture"></a>Architektura nasazení

#### <a name="description"></a>Popis

Toto řešení může nasazovat jednu nebo více oblastí založených na více členských Ethereem konsorciem. Ve výchozím nastavení jsou koncové body RPC a peering přístupné přes veřejnou IP adresu, aby bylo možné zjednodušené připojení mezi předplatnými a cloudy. Pro řízení přístupu na úrovni aplikace doporučujeme používat [kontrakty oprávnění parity](https://wiki.parity.io/Permissioning) . Podporujeme také sítě nasazené za sítě VPN, které využívají brány virtuální sítě pro připojení mezi předplatnými. Tato nasazení jsou složitější, takže se nejdřív doporučuje začít s modelem veřejné IP adresy.

#### <a name="consortium-member-overview"></a>Přehled členů konsorcia

Každé nasazení člena konsorcia zahrnuje:

-   Virtual Machines pro spouštění validátorů pro PoA

-   Azure Load Balancer pro distribuci požadavků RPC, partnerských vztahů a zásad správného řízení DApp

-   Azure Key Vault pro zabezpečení ověřovacích identit

-   Azure Storage pro hostování trvalých síťových informací a koordinace leasingu

-   Azure Monitor pro agregaci protokolů a statistiky výkonu

-   Brána virtuální sítě (volitelné) pro povolení připojení VPN v privátních virtuální sítě

![Architektura nasazení](./media/ethereum-poa-deployment/deployment-architecture.png)

Pro spolehlivost a modularitu využíváme kontejnery Docker. Používáme Azure Container Registry k hostování a obsluze imagí s verzemi v rámci každého nasazení. Image kontejneru se skládají z těchto:

-   Orchestrator

    -   Spustí se jednou během nasazení.

    -   Generuje identity a smlouvy zásad správného řízení.

    -   Ukládá identity do úložiště identit.

-   Klient parity

    -   Identita zapůjčení z úložiště identity

    -   Zjišťuje a připojuje se k partnerským uzlům.

-   Agent EthStats

    -   Shromažďuje místní protokoly a statistiky prostřednictvím protokolu RPC a nabízí Azure Monitor

-   DApp zásad správného řízení

    -   Webové rozhraní pro interakci se smlouvami zásad správného řízení

## <a name="how-to-guides"></a>Návody
### <a name="governance-dapp"></a>DApp zásad správného řízení

Na základě špičkového kontrolního úřadu je decentralizované řízení. DApp zásad správného řízení je sada předem nasazených [inteligentních kontraktů](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) a webové aplikace, které se používají k řízení autorit v síti.
Autority jsou rozděleny do identit správců a uzlů validátorů.
Správci mají oprávnění delegovat účast na sadě ověřovacích uzlů. Správci mohou také hlasovat jiné správce do sítě nebo z ní.

![dapp zásad správného řízení](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Decentralizované zásady správného řízení –** Změny v síťových autoritách se spravují prostřednictvím hlasování v řetězcích, a to výběrem správce.

-   **Delegování ověřovacího modulu –** Autority mohou spravovat své uzly validátoru, které jsou nastaveny v každém nasazení PoA.

-   **Auditovat historii změn –** Každá změna se zaznamená na blockchain, která poskytuje transparentnost a audit.

#### <a name="getting-started-with-governance"></a>Začínáme se zásadami správného řízení
K provedení libovolného druhu transakcí prostřednictvím DApp zásad správného řízení budete muset využít Ethereem peněženku.  Nejjednodušším přístupem je použití kapesního prohlížeče, jako je [MetaMask](https://metamask.io); vzhledem k tomu, že se jedná o chytré smlouvy nasazené v síti, můžete také automatizovat interakce se smlouvou zásad správného řízení.

Po instalaci MetaMask přejděte v prohlížeči na DApp zásad správného řízení.  Adresu URL můžete najít v e-mailu s potvrzením nasazení nebo prostřednictvím Azure Portal ve výstupu nasazení.  Pokud nemáte nainstalovanou peněženku v prohlížeči, nebudete moct provádět žádné akce. Pořád ale můžete číst stav správce.  

#### <a name="becoming-an-admin"></a>Stane se správcem
Pokud jste prvním členem, který je v síti nasazený, automaticky se stanete správcem a vaše paritní uzly budou uvedené jako validátory.  Pokud se připojujete k síti, budete se muset přihlásit jako správce o většinu (víc než 50%). existující sady pro správu.  Pokud se rozhodnete, že se nestanete správcem, vaše uzly budou pořád synchronizovat a ověřit blockchain; Nicméně nebudou zapojeny do procesu vytváření bloků. Chcete-li zahájit hlasovací proces, který se stane správcem, klikněte na položku __název__ a zadejte adresu ethereem a alias.

![Nominovat](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Kandidáty
Kliknutím na kartu __kandidáti__ zobrazíte aktuální sadu kandidátů na správce.  Jakmile kandidát dostane v rámci současných správců většinu hlasů, bude kandidát povýšen na správce.  Pokud chcete hlasovat o kandidátu, vyberte řádek a v horní části klikněte na hlasovací tlačítko.  Pokud změníte svůj názor na hlas, můžete vybrat kandidáta a kliknout na tlačítko "zrušit hlasování".

![Kandidáty](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Správc
Na kartě __Správci__ se zobrazí aktuální sada správců a poskytne vám možnost hlasovat proti.  Když správce ztratí více než 50% podporu, odeberou se jako správce v síti.  Všechny uzly validátoru, které tento správce vlastní, ztratí stav validátoru a stanou se uzly transakcí v síti.  Správce může být odebrán z libovolného počtu důvodů; je však až konsorcium k tomu, aby předem souhlasila se zásadami.

![Správc](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Validátory
Výběrem karty __validátory__ v nabídce vlevo zobrazíte aktuální nasazené uzly parity pro tuto instanci a jejich aktuální stav (typ uzlu).  Každý člen konsorcia bude mít v tomto seznamu jinou sadu validátorů, protože toto zobrazení představuje stávajícího nasazeného člena konsorcia.  Pokud se jedná o nově nasazenou instanci a ještě jste nepřidali validátory, zobrazí se vám možnost Přidat validátory.  Výběrem této možnosti se automaticky zvolí sada paritních uzlů s vyrovnáváním zatížení a přiřadí se k sadě validátorů.  Pokud jste nasadili více uzlů než povolená kapacita, zbývající uzly se stanou uzly transakcí v síti.

Adresa každého validátoru se automaticky přiřadí prostřednictvím [úložiště identit](#identity-store) v Azure.  Pokud dojde k výpadku uzlu, přijde jeho identita, což umožní jinému uzlu ve vašem nasazení provést jeho umístění.  Tím zajistíte vysokou dostupnost vaší účasti v rámci konsensu.

![Validátory](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Název konsorcia
Každý správce může aktualizovat název konsorcia zobrazený v horní části stránky.  Vyberte ikonu ozubeného kolečka v levém horním rohu pro aktualizaci názvu konsorcia.

#### <a name="account-menu"></a>Nabídka účet
V pravém horním rohu je váš alias účtu Ethereem a identicon.  Pokud jste správce, budete mít možnost aktualizovat svůj alias.

![Účet](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Nasazení Ethereem pro kontrolu-of-Authority

Tady je příklad toku nasazení více stran:

1.  Tři členové generují účet Ethereem pomocí MetaMask

2.  *Člen A* nasadí ethereem POA a poskytne jim svou Ethereem veřejnou adresu.

3.  *Člen A* poskytuje konsorcium URL pro *členské B* a *členské C* .

4.  *Členské B* a *členské C* nasazují ethereem POA a poskytují svou ethereem veřejnou adresu a adresu URL *člena*a 's Consortium.

5.  *Člen A* hlasy *člena B* jako správce

6.  *Člen a* a *člen B* – hlas *členů C* jako správce

Tento proces vyžaduje předplatné Azure, které může podporovat nasazení několika virtuálních počítačů a spravovaných disků. V případě potřeby si [vytvořte bezplatný účet Azure](https://azure.microsoft.com/free/) , který začne.

Po zabezpečení předplatného je potřeba přejít na Azure Portal. Vyberte ' + ', Marketplace (' Zobrazit vše ') a vyhledejte konsorcium Ethereem PoA.

Následující část vás provede konfigurací přístavů prvního člena v síti. Tok nasazení je rozdělen na pět kroků: základy, oblasti nasazení, velikost sítě a výkon, Ethereem nastavení, Azure Monitor.

#### <a name="basics"></a>Základy

V části **základy**zadejte hodnoty pro standardní parametry pro jakékoli nasazení, jako je například předplatné, skupina prostředků a základní vlastnosti virtuálního počítače.

Podrobný popis každého parametru je následující:

Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
---|---|---|---
Chcete vytvořit novou síť nebo připojit se k existující síti?|Vytvoření nové sítě nebo připojení k již existující síti konsorcia|Vytvořit nové spojení – existující|Vytvořit nový
E-mailová adresa (nepovinné)|Až se vaše nasazení dokončí s informacemi o vašem nasazení, obdržíte e-mailové oznámení.|Platná e-mailová adresa|Není k dispozici
Uživatelské jméno virtuálního počítače|Uživatelské jméno správce každého nasazeného virtuálního počítače (jenom alfanumerické znaky)|1-64 znaků|Není k dispozici
Typ ověřování|Metoda, která se má ověřit pro virtuální počítač.|Heslo nebo veřejný klíč SSH|Heslo
Heslo (typ ověřování = heslo)|Heslo pro účet správce pro každý nasazený virtuální počítač.  Heslo musí obsahovat 3 z následujících znaků: 1 velké písmeno, 1 malé písmeno, 1 číslo a 1 speciální znak. Všechny virtuální počítače mají zpočátku stejné heslo, ale po zřízení můžete heslo změnit.|12-72 znaků|Není k dispozici
Klíč SSH (typ ověřování = veřejný klíč)|Klíč zabezpečeného prostředí používaný pro vzdálené přihlášení||Není k dispozici
Předplatné|Předplatné, ke kterému se má nasadit síť konsorcia||Není k dispozici
Skupina prostředků|Skupina prostředků, do které se má nasadit síť konsorcia||Není k dispozici
Umístění|Oblast Azure pro skupinu prostředků.||Není k dispozici

Ukázkové nasazení je uvedené níže: ![základní okno](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Oblasti nasazení

V části oblasti nasazení zadejte vstupy pro počet oblastí pro nasazení sítě konsorcia a výběr oblastí Azure na základě počtu zadaných oblastí. Uživatel může nasadit maximálně 5 oblastí. Doporučujeme, abyste si zvolili první oblast, která bude odpovídat umístění skupiny prostředků z části základy. Pro vývojové nebo testovací sítě se doporučuje jedna oblast na člena. V případě produkčního prostředí doporučujeme nasazení do dvou nebo více oblastí pro zajištění vysoké dostupnosti.

Podrobný popis každého parametru je následující:

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
  Počet oblastí|Počet oblastí pro nasazení sítě konsorcia|1, 2, 3, 4, 5|1
  První oblast|První oblast pro nasazení sítě konsorcia|Všechny povolené oblasti Azure|Není k dispozici
  Druhá oblast|Druhá oblast pro nasazení sítě konsorcia (viditelná pouze v případě, že je vybrán počet oblastí jako 2)|Všechny povolené oblasti Azure|Není k dispozici
  Třetí oblast|Třetí oblast pro nasazení sítě konsorcia (viditelná pouze v případě, že je vybrán počet oblastí jako 3)|Všechny povolené oblasti Azure|Není k dispozici
  Čtvrtá oblast|Čtvrtá oblast pro nasazení sítě konsorcia (viditelná jenom v případě, že počet oblastí je vybraný jako 4)|Všechny povolené oblasti Azure|Není k dispozici
  Páté oblasti|Pátá oblast pro nasazení sítě konsorcia (viditelná jenom v případě, že počet oblastí je vybraný jako 5)|Všechny povolené oblasti Azure|Není k dispozici

Ukázkové nasazení je uvedené níže: ![oblasti nasazení](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Velikost a výkon sítě

Dále v části velikost sítě a výkon zadejte vstupy pro velikost sítě konsorcia, jako je počet a velikost uzlů validátoru.
Velikost úložiště uzlu validátoru určí potenciální velikost blockchain. To může být po nasazení změněno.

Podrobný popis každého parametru je následující:

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
  Počet uzlů validátoru vyrovnávání zatížení|Počet uzlů validátoru, které se mají zřídit v rámci sítě|2-15|2
  Výkon úložiště uzlu validátoru|Typ spravovaného disku, který zálohuje všechny nasazené uzly validátoru.|SSD úrovně Standard nebo Premium|SSD úrovně Standard
  Velikost virtuálního počítače uzlu validátoru|Velikost virtuálního počítače používaná pro uzly validátoru.|Standard A, Standard D, Standard D-V2, Standard F Series, Standard DS a Standard FS|D1 v2 úrovně Standard

[Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/managed-disks/)

[Podrobnosti o cenách virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Virtuální počítač a vrstva úložiště budou mít vliv na výkon sítě.  V závislosti na požadované cenové efektivitě doporučujeme použít následující SKU:

  SKU virtuálního počítače|Vrstva úložiště|Cena|Propustnost|Latence
  ---|---|---|---|---
  F1|SSD úrovně Standard|slab|slab|maximální
  D2_v3|SSD úrovně Standard|Úrovně|Úrovně|Úrovně
  F16s|Premium SSD|maximální|maximální|slab

Ukázkové nasazení je uvedené níže: ![velikost sítě a výkon](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Nastavení ethereem

Dále v části nastavení Ethereem zadejte nastavení konfigurace související s Ethereem, jako je ID sítě a heslo účtu Ethereem nebo blok Genesis.

Podrobný popis každého parametru je následující:

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
ID člena konsorcia|ID přidružené ke každému členovi zapojenému do sítě konsorcia používané ke konfiguraci adresních prostorů IP adres, aby se zabránilo kolizi. V případě privátní sítě by ID člena mělo být jedinečné v různých organizacích ve stejné síti.  Jedinečné ID člena je nutné i v případě, že je stejná organizace nasazena do více oblastí. Poznamenejte si hodnotu tohoto parametru, protože ho budete muset sdílet s dalšími připojujícími se členy, abyste se ujistili, že nedochází k žádné kolizi.|0-255|Není k dispozici
ID sítě|ID sítě pro nasazování sítě konsorcia Ethereem  Každá Ethereem síť má své vlastní ID sítě, přičemž 1 je identifikátor veřejné sítě.|5 – 999 999 999|10101010
Adresa Ethereem správce|Adresa účtu ethereem, která se používá pro zapojení do zásad správného řízení PoA  Pro vygenerování Ethereem adresy doporučujeme použít MetaMask.|42 alfanumerických znaků začínajících 0x|Není k dispozici
Rozšířené možnosti|Rozšířené možnosti pro nastavení Ethereem|Povolit nebo zakázat|Zakázat
Veřejná IP adresa (rozšířené možnosti = Povolit)|Nasadí síť za bránou virtuální sítě a odebere přístup partnerských vztahů. Pokud je vybraná tato možnost, všichni členové musí používat bránu virtuální sítě, aby připojení bylo kompatibilní.|Privátní virtuální síť veřejné IP adresy|Veřejná IP adresa
Limit bloku plynů (rozšířené možnosti = Povolit)|Počáteční limit pro blokový plyn sítě|Libovolné číselné|50000000
Zablokovat dobu trvání uzávěrky (s)|Frekvence, s níž budou vytvořeny prázdné bloky, pokud v síti nejsou žádné transakce. Vyšší frekvence bude mít rychlejší, ale zvýšené náklady na úložiště.|Libovolné číselné|15
Kontrakt oprávnění transakce (rozšířené možnosti = Povolit)|Kód pro kontrakt oprávnění transakce. Omezí nasazení a provádění inteligentních kontraktů na povolený seznam účtů Ethereem.|Bajtový kód kontraktu|Není k dispozici

Ukázkové nasazení je uvedené níže: ![nastavení ethereem](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Monitorování

Okno monitorování umožňuje nakonfigurovat prostředek Azure Monitorch protokolů pro vaši síť. Agent monitorování bude shromažďovat a obcházet užitečné metriky a protokoly ze sítě a poskytuje možnost rychle kontrolovat stav sítě nebo problémy s laděním.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
Monitorování|Možnost povolení monitorování|Povolit nebo zakázat|Povolení
Připojit k existujícím protokolům Azure Monitor|Vytvořit novou instanci protokolů Azure Monitor nebo se připojit k existující instanci|Vytvořit novou nebo připojit existující|Vytvořit nový
Umístění monitorování (připojit k existujícím protokolům Azure Monitor = vytvořit nové)|Oblast, do které bude nasazena nová instance protokolu Azure Monitor|Všechny oblasti protokolu Azure Monitor|Není k dispozici
Existující ID pracovního prostoru Log Analytics (připojit k existujícím protokolům Azure Monitor = připojit existující)|ID pracovního prostoru existující instance protokolu Azure Monitor||Není k dispozici
Existující primární klíč Log Analytics (Připojte se k existujícím protokolům Azure Monitor = připojit existující)|Primární klíč, který se používá pro připojení ke stávající instanci Azure Monitorch protokolů||Není k dispozici


Ukázkové nasazení je uvedené níže: ![Azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Souhrn

Kliknutím na okno Souhrn zkontrolujte zadané vstupy a spusťte základní ověřování před nasazením. Před nasazením si můžete stáhnout šablonu a parametry.

Přečtěte si právní a osobní údaje a kliknutím na koupit nasaďte. Pokud nasazení zahrnuje brány virtuální sítě, nasazení bude trvat 45 až 50 minut.

#### <a name="post-deployment"></a>Po nasazení

##### <a name="deployment-output"></a>Výstup nasazení

Po dokončení nasazení získáte přístup k potřebným parametrům prostřednictvím e-mailu s potvrzením nebo prostřednictvím Azure Portal. V těchto parametrech najdete tyto informace:

-   Koncový bod ethereem RPC

-   Adresa URL řídicího panelu zásad správného řízení

-   Adresa URL Azure Monitor

-   Adresa URL dat

-   ID prostředku brány virtuální sítě (volitelné)

##### <a name="confirmation-email"></a>Potvrzovací e-mail

Pokud zadáte e-mailovou adresu ([oddíl základy](#basics)), pošle se e-mail na e-mailovou adresu s informacemi o výstupu nasazení.

![e-mail pro nasazení](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portál

Po úspěšném dokončení nasazení a zřízení všech prostředků můžete zobrazit výstupní parametry ve vaší skupině prostředků.

1.  Vyhledání skupiny prostředků na portálu

2.  Navigace v *nasazeních*

3.  Vyberte nasazení na nejvyšší úrovni se stejným názvem, jako má vaše skupina prostředků.

4.  Vybrat *výstupy*

### <a name="growing-the-consortium"></a>Rozšiřování konsorcia

Chcete-li rozšířit konsorcium, musíte nejprve připojit fyzickou síť.
Použití veřejného nasazení založeného na protokolu IP je v tomto prvním kroku bezproblémové. Pokud nasazujete za síť VPN, přečtěte si část [připojení brány virtuální](#connecting-vnet-gateways) sítě, která umožňuje připojení k síti v rámci nového nasazení členů.  Až se nasazení dokončí, stane se správcem sítě [DApp zásad správného řízení](#governance-dapp) .

#### <a name="new-member-deployment"></a>Nasazení nového člena

1.  Sdílet následující informace s připojujícím se členem. Tyto informace najdete v e-mailu po nasazení nebo ve výstupu nasazení na portálu.

    -  Adresa URL dat konsorcia

    -  Počet uzlů, které jste nasadili

    -  ID prostředku brány virtuální sítě (Pokud používáte VPN)

2.  Člen nasazení by měl používat [stejné řešení](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) při nasazení jejich síťové přítomnosti, a přitom je potřeba mít na paměti následující:

    -  Vyberte *připojit existující*

    -  Vyberte stejný počet uzlů validátoru jako zbytek členů sítě, aby se zajistila korektní reprezentace.

    -  Použijte stejnou Ethereem adresu, kterou jste zadali v předchozím kroku.

    -  Předejte *adresu URL poskytnutého data konsorcia* na kartě *Nastavení ethereem*

    -  Pokud je zbytek sítě za sítí VPN, vyberte v části Upřesnit možnost *privátní virtuální síť* .

#### <a name="connecting-vnet-gateways"></a>Připojení bran virtuální sítě

Pokud jste nasadili pomocí výchozího nastavení veřejné IP adresy, můžete tento krok ignorovat. V případě privátní sítě jsou různí členové připojeni prostřednictvím připojení brány virtuální sítě. Předtím, než se člen může připojit k síti a zobrazit transakční provoz, musí stávající člen provést konečnou konfiguraci brány VPN, aby bylo možné připojení přijmout. To znamená, že uzly Ethereem spojovacího člena nebudou spuštěny, dokud není navázáno připojení. Doporučuje se vytvořit redundantní síťová připojení (síť) do konsorcia, aby se snížila pravděpodobnost jednoho bodu selhání.

Po nasazení nového člena musí stávající člen dokončit obousměrné připojení tím, že nastaví připojení brány virtuální sítě k novému členu. Pro dosažení tohoto toho bude potřebovat stávající člen:

1.  Brána virtuální sítě ResourceID připojujícího se člena (viz výstup nasazení)

2.  Sdílený klíč připojení

Aby bylo možné dokončit připojení, musí stávající člen spustit následující skript prostředí PowerShell. Doporučujeme použít Azure Cloud Shell umístěnou v pravém horním navigačním panelu na portálu.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Monitorování služeb

Portál Azure Monitor můžete najít buď pomocí odkazu v e-mailu pro nasazení, nebo vyhledáním parametru ve výstupu nasazení \[portálu OMS\_\_adresa URL\].

Portál nejprve zobrazí statistiku sítě vysoké úrovně a přehled uzlů.

![kategorie monitorování](./media/ethereum-poa-deployment/monitor-categories.png)

Vybírání **přehledu uzlů** vás přesměruje na portál a zobrazí statistiku infrastruktury pro jednotlivé uzly.

![statistiky uzlů](./media/ethereum-poa-deployment/node-stats.png)

Výběr statistiky **sítě** vás nasměruje na zobrazení statistik sítě ethereem.

![statistiky sítě](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Za těmito řídicími panely je sada Queryable nezpracovaných protokolů. Tyto nezpracované protokoly můžete použít k přizpůsobení řídicích panelů, prozkoumání selhání nebo upozorňování prahové hodnoty instalace. Níže najdete sadu ukázkových dotazů, které mohou být spuštěny v nástroji pro prohledávání protokolu:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Zobrazí seznam bloků, které byly hlášeny více než jedním validátorem. Užitečné při hledání větví zřetězení.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Získejte průměrnou hodnotu počtu partnerských uzlů pro zadaný uzel validátoru na průměr v intervalu 5 minut.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>Přístup přes SSH

Z bezpečnostních důvodů je ve výchozím nastavení odepřený přístup portu SSH pravidlem zabezpečení skupiny sítě. Chcete-li získat přístup k instancím virtuálních počítačů v síti PoA, bude nutné změnit toto pravidlo, aby \"povoleno\"

1.  Začněte v části Přehled nasazené skupiny prostředků z Azure Portal.

    ![Přehled SSH](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Vyberte skupinu zabezpečení sítě pro oblast virtuálního počítače, ke kterému chcete získat přístup.

    ![NSG SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Vyberte pravidlo \"Allow-SSH\".

    ![SSH – povolení](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Změnit \"akci\" na povoleno

    ![Povolit povolení protokolu SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Klikněte na \"Uložit\" (použití změn může trvat několik minut).

Nyní se můžete vzdáleně připojit k virtuálním počítačům pro uzly validátoru přes SSH s vaším uživatelským jménem správce a heslem/klíčem SSH.
Příkaz SSH, který se má spustit pro přístup k prvnímu uzlu validátoru, je uvedený v parametru Output nasazení šablony jako,\_SSH\_první\_VL\_NODE\_REGION1 (pro ukázkové nasazení: SSH-p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Chcete-li získat další uzly transakcí, zvyšte číslo portu o jednu (například první uzel transakce je na portu 4000).

Pokud jste nasadili do více než jedné oblasti, změňte výše uvedený příkaz na název DNS nebo IP adresu nástroje pro vyrovnávání zatížení v této oblasti. Pokud chcete najít název DNS nebo IP adresu ostatních oblastí, vyhledejte prostředek pomocí konvence pojmenování \*\*\*\*\*-lbpip-reg\#a zobrazte jeho název DNS a IP adresu.

### <a name="azure-traffic-manager-load-balancing"></a>Vyrovnávání zatížení Azure Traffic Manager

Služba Azure Traffic Manager může pomoci snižovat prostoje a zlepšit odezvu sítě PoA směrováním příchozího provozu napříč několika nasazeními v různých oblastech. Integrované kontroly stavu a automatické přesměrování vám pomůžou zajistit vysokou dostupnost koncových bodů RPC a DApp zásad správného řízení. Tato funkce je užitečná v případě, že jste nasadili více oblastí a jste připravení pro produkci.

Použít Traffic Manager k:

-   Zvyšte dostupnost sítě pro PoA pomocí automatického převzetí služeb při selhání.

-   Zvyšte rychlost odezvy sítí směrováním koncových uživatelů do umístění Azure s nejnižší latencí sítě.

Pokud se rozhodnete vytvořit profil Traffic Manager, můžete k přístupu k síti použít název DNS daného profilu. Po přidání dalších členů konsorcia do sítě je možné Traffic Manager použít také k vyrovnávání zatížení napříč nasazenými validátory.

#### <a name="creating-a-traffic-manager-profile"></a>Vytváří se profil Traffic Manager.

Po kliknutí na tlačítko \"vytvořit prostředek\" na Azure Portal vyhledejte a vyberte \"Profile Traffic Manager\".

![hledání Azure Traffic Manageru](./media/ethereum-poa-deployment/traffic-manager-search.png)

Dejte profilu jedinečný název a vyberte skupinu prostředků, která se vytvořila během nasazování PoA. Kliknutím na tlačítko vytvořit nasadíte.

![Vytvoření Traffic Manageru](./media/ethereum-poa-deployment/traffic-manager-create.png)

Po nasazení vyberte instanci ve skupině prostředků. Název DNS pro přístup k Traffic Manageru najdete na kartě Přehled.

![Vyhledání služby DNS Traffic Manageru](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Vyberte kartu koncové body a klikněte na tlačítko Přidat. Zadejte jedinečný název koncového bodu. Změňte typ cílového prostředku na veřejnou IP adresu. Pak vyberte veřejnou IP adresu první oblasti\'s vyrovnáváním zatížení.

![Směrování Traffic Manageru](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Opakujte pro každou oblast v nasazené síti. Jakmile jsou koncové body ve stavu\" \"povoleny, budou automaticky načteny a vyrovnány oblastí na název DNS Traffic Manageru. Tento název DNS teď můžete použít místo adresy URL\_\_\[KONSORCIa\] v dalších krocích dokumentu.

### <a name="data-api"></a>Rozhraní API pro data

Každý člen konsorcia hostuje nezbytné informace, aby je ostatní mohli připojit k síti. Stávající člen poskytne [CONSORTIUM_DATA_URL] před nasazením člena. Při nasazení bude připojující člen získat informace z rozhraní JSON v následujícím koncovém bodu:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpověď bude obsahovat informace, které jsou užitečné pro spojování členů (blok Genesis, ověřovací sada kontraktu ABI, bootnodes), a informace užitečné pro existujícího člena (adresy validátoru). Pro rozšiřování konsorcia mezi poskytovateli cloudu doporučujeme použití této standardizace. Toto rozhraní API vrátí odpověď ve formátu JSON s následující strukturou:
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="tutorials"></a>Kurzy

### <a name="programmatically-interacting-with-a-smart-contract"></a>Programové interakce s čipovou smlouvou

> [!WARNING]
> Nikdy Neodesílat privátní klíč Ethereem prostřednictvím sítě! Ujistěte se, že je každá transakce podepsaná místně a že se podepsaná transakce posílá přes síť.

V následujícím příkladu používáme *ethereumjs* k vygenerování ethereem adresy, *ethereumjs-TX* pro místní přihlášení a *web3* k odeslání nezpracované transakce do koncového bodu RPC pro ethereem.

Tento příklad používáme jako jednoduchý kontrakt Hello-World:

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

Tento příklad předpokládá, že kontrakt je už nasazený. *Solc* a *web3* můžete použít k programovému nasazení smlouvy. Nejprve nainstalujte následující moduly uzlů:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Tento skript nodeJS provede následující akce:

-   Vytvoření nezpracované transakce: postMsg

-   Podepsat transakci pomocí generovaného privátního klíče

-   Odeslání podepsané transakce do sítě Ethereem

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Nasazení inteligentních kontraktů pomocí Truffle

-   Nainstalovat potřebné knihovny

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   V Truffle. js přidejte následující kód k odemknutí účtu MetaMask a konfiguraci uzlu PoA jako vstupního bodu zadáním symbolické fráze (MetaMask/nastavení/zobrazení počátečních slov)

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Nasazení na síť PoA

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Ladění inteligentních kontraktů pomocí Truffle

Truffle má místní vývojovou síť, která je dostupná pro ladění inteligentních kontraktů. Úplný kurz najdete [tady](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Podpora WebAssembly (WASM)

Podpora WebAssembly je už povolená na nově nasazených sítích PoA. Umožňuje vývoj inteligentních kontraktů v jakémkoli jazyce, který předává do webového sestavení (Rust, C, C++). Další informace najdete na odkazech níže.

-   Přehled parity pro WebAssembly – <https://wiki.parity.io/WebAssembly-Home>

-   Kurz z parity tech-<https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Referenční informace

### <a name="faq"></a>Nejčastější dotazy

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Všimněte si, že síť obsahuje mnoho transakcí, které nefungovala\'t odeslat. Odkud pocházejí?

Odemknutí [osobního rozhraní API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)je nezabezpečené. Roboty naslouchat odemčeným účtům Ethereem a pokusit se o vyprázdnění prostředků. Robot předpokládá, že tyto účty obsahují reálný a pokus o odsávání zůstatku. Nepovolujte osobní rozhraní API v síti. Místo toho, aby transakce byly předem podepsány buď ručně, jako je MetaMask nebo programově, jak je uvedeno v části [programově spolupracuje s čipovou smlouvou](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Jak se SSH na virtuální počítač?

Z bezpečnostních důvodů se port SSH nezveřejňuje. Podle [tohoto průvodce povolte port SSH](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Návody nastavit člena auditu nebo uzly transakcí?

Uzly transakcí jsou sadou paritních klientů, které jsou v síti partnerského vztahu, ale nejsou zapojeny do konsensu. Tyto uzly se pořád dají použít k odeslání transakcí Ethereem a přečtení stavu inteligentního kontraktu.
Tento postup je vhodný jako mechanismus pro poskytování auditování pro členy v síti, kteří nejsou samosprávi. K tomuto účelu stačí postupovat podle kroku 2 z rozšiřování konsorcia.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Proč trvá transakce MetaMask dlouhou dobu?

Aby bylo zajištěno, že transakce jsou přijímány ve správném pořadí, každá transakce Ethereem se dostane k přírůstkové hodnotě nonce. Pokud jste účet v MetaMask použili v jiné síti, budete muset resetovat hodnotu nonce. Klikněte na ikonu nastavení (3 panely), nastavení, Resetování účtu. Historie transakcí se vymaže a teď můžete transakci znovu odeslat.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Potřebuji zadat poplatek za plyn v MetaMask?

Ether neslouží k tomu, aby byl v rámci úřadu ověření platnosti. Proto není nutné zadávat poplatky za plyn při odesílání transakcí v MetaMask.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Co mám dělat, když se moje nasazení nepovede kvůli chybě při zřizování Azure OMS?

Monitorování je volitelná funkce. Ve výjimečných případech, kdy se nasazení nezdaří kvůli nemožnostem úspěšného zřízení Azure Monitor prostředků, se můžete znovu nasadit bez Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Jsou nasazení veřejné IP adresy kompatibilní s nasazeními privátních sítí?

Ne, partnerský vztah vyžaduje obousměrnou komunikaci, takže celá síť musí být buď veřejná, nebo soukromá.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Jaká je očekávaná propustnost transakce v rámci autority pro ověření?

Propustnost transakce bude vysoce závislá na typech transakcí a topologii sítě.  Při použití jednoduchých transakcí jsme provedli průměrně 400 transakcí za sekundu pomocí sítě nasazené napříč několika oblastmi.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Návody se přihlásit k odběru událostí inteligentních kontraktů?

Ethereem Proofing-of-Authority teď podporuje webové sokety.  Pomocí e-mailu nasazení nebo výstupu nasazení vyhledejte adresu URL a port webového soketu.

## <a name="next-steps"></a>Další kroky

Začněte tím, že použijete [ethereem řešení pro kontrolu pravopisu](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) .
