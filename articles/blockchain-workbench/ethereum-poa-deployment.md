---
title: Consortium ethereum během testování of-Authority
description: Testování of-Authority Etherereum Consortium mohli řešení používat, nasazení a konfiguraci sítě konsorcia několika člen ethereum během
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 8/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: fcb35f23be384b3625e4e17e5dc2285b7eeb341a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530690"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum během testování of-authority W3C

[Toto řešení](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) je navržený tak, aby usnadňuje nasazení, konfigurace a řídí Etherea sítě konsorcia více členy testování of-authority s minimálními znalostmi Azure a Etherea.

Pomocí několika vstupů uživatele a nasazení jedním kliknutím na webu Azure portal můžete každý člen zřídit nároky na síť, pomocí Microsoft Azure Compute, sítě a služby úložiště po celém světě. Nároky na síť každého člena se skládá ze sady s vyrovnáváním zatížení validátoru uzlů s které aplikace nebo uživatele mohou spolupracovat při odeslání ethereum během transakce.

## <a name="concepts"></a>Koncepty

### <a name="terminology"></a>Terminologie

-   **Shody** – v rámci synchronizace dat v distribuované síti prostřednictvím ověření bloku a vytvoření.

-   **Člen Consortium** – entita, která se účastní shody v síti Blockchain.

-   **Správce** – Etherea účet, který se používá ke správě účast consortium daného člena.

-   **Program pro ověření** -počítače spojené s Ethereem účet, který je součástí shody jménem správce.

### <a name="proof-of-authority"></a>Testování autority

Pro ty z vás, kteří začínají s komunitou blockchain vydání tohoto řešení je skvělé příležitosti, další informace o technologii snadné a konfigurovat způsobem v Azure. Testování práce je odolnost Sybil mechanismus, který využívá výpočetní náklady a místním regulovat síti a umožnit veletrh účast. Tato spolupráce v sítích anonymní, otevřete blockchain kde soutěže pro kryptoměn zvýší úroveň zabezpečení v síti. V sítích privátní/consortium však základní Ether nemá žádnou hodnotu. Protokol alternativní testování autority, je vhodné pro udělili oprávnění sítě, ve kterém se ví, všichni účastníci shody a renomované. Bez nutnosti dolování testování of-authority je mnohem efektivnější zároveň zachovat Byzantine odolnost proti chybám.

### <a name="consortium-governance"></a>Zásady správného řízení W3C

Protože testování autority závisí na službě udělili oprávnění seznam úřadů sítě zachovat sítě jsou v pořádku, je potřeba poskytnout reálnou mechanismus k provádění změn do tohoto seznamu oprávnění. Každé nasazení obsahuje sadu smart smluv a portál pro zásady správného řízení ve řetězu tohoto seznamu udělili oprávnění. Jakmile navrhované změny dosáhne většinou členy consortium, změny budou přijaty. To umožňuje nové shody účastníci bude přidán nebo dojde k ohrožení bezpečnosti účastníkům odeberou transparentním způsobem, který může vést ke vzniku upřímná sítě.

### <a name="admin-account"></a>Účet správce

Během nasazení testování of-authority uzlů bude výzva adresy správce Etherea. Můžete použít několik různých mechanismů generování a zabezpečit tento účet Etherea. Jakmile tato adresa se přidá jako autorita v síti, můžete tento účet se účastnit zásad správného řízení. Tento účet správce se taky použije delegovat caiq zapojení do validátoru uzlů, které jsou vytvořeny jako součást tohoto nasazení. Vzhledem k tomu, že se používá pouze veřejné adresy Etherea, má každý správce flexibilitu k zabezpečení svých privátních klíčů tak, aby v souladu s jejich požadovanými zabezpečovacími model.

### <a name="validator-node"></a>Program pro ověření uzlu

V protokolu testování of-authority validátoru uzly proběhnout tradiční miner uzlů. Každý program pro ověření má jedinečnou identitu Etherea, který se přidá do seznamu oprávnění smart kontraktu. Jakmile validátor, který je v tomto seznamu, se mohl účastnit v procesu vytváření bloku. Další informace o tomto procesu najdete dokumentaci k vaší Parity na [autority Round caiq](https://wiki.parity.io/Aura). Každý člen consortium můžete zřídit dvěma a více uzly program pro ověření v pěti oblastech pro geografickou redundancí. Program pro ověření uzly komunikovat s další uzly validátoru převede do caiq týkající se stavu základní distribuované účetní knihy.
K zajištění uspokojivé účast v síti, každý člen consortium zakázáno používat další validátory než prvního člena v síti (Pokud první člen nasadí tři validátory, každý člen může mít pouze až tři validátory).

### <a name="identity-store"></a>Úložiště identit

Protože každý člen bude mít více validátoru uzly, které běží současně a udělili oprávnění identity musí mít každý uzel, je důležité, validátory můžete bezpečně získat jedinečnou aktivní identitu v síti. Usnadňuje to jsme vytvořili Identity Store, který se nasadí v předplatném každého člena, které se bezpečně uchovává generované Etherea identit. Po nasazení bude Orchestrace kontejnerů generovat Etherea privátní klíč pro každý program pro ověření a ukládat ve službě Azure Key Vault. Před spuštění uzlu parita, nejprve získává zapůjčení na nevyužité identity k zajištění, že identita není vyzvednou jiného uzlu. Identita se poskytuje na klientovi, který jí oprávnění a začněte vytvářet bloky. Pokud hostování virtuálních počítačů dojde k výpadku, zapůjčení identity bude vydána, povolení nahrazení uzlu v budoucnu obnovit jeho identitu.

### <a name="bootnode-registrar"></a>Doménový Registrátor Bootnode

Umožňuje snadné připojení k jednotlivým členům bude hostovat sadu informace o připojení na [data koncový bod rozhraní API](#data-api). Tato data obsahují seznam bootnodes, které jsou k dispozici jako partnerské uzly pro spojování člena. Jako součást tohoto rozhraní API pro data budeme aktualizovat tento seznam bootnode

### <a name="bring-your-own-operator"></a>Přineste vlastní – operátor

Člen consortium často se mají zapojit do síťových zásad správného řízení, ale nechcete provozovat a udržovat infrastrukturu. Na rozdíl od tradičních systémů s jednoho operátoru napříč funguje sítě proti decentralizovaný model blockchain systémy. Místo nabírat centralizované zprostředkovatele pro provoz sítě, může každý člen consortium delegovat správu infrastruktury operátoru podle vlastního uvážení. To umožňuje hybridního modelu, kde každý člen můžete provozovat svůj vlastní infrastrukturu nebo delegovat na partnerský server pro různé operace. Pracovní postup delegované operace funguje takto:

1.  **Člen Consortium** generuje adresu Etherea (obsahuje privátní klíč)

2.  **Člen Consortium** obsahuje veřejnou adresu Etherea **– operátor**

3.  **Operátor** nasadí a nakonfiguruje uzly validátoru PoA pomocí našich řešení Azure Resource Manageru

4.  **Operátor** poskytuje koncový bod správy a RPC do **Consortium člena**

5.  **Člen Consortium** používá svým soukromým klíčem k podepisování žádost o přijetí uzly validátoru **operátor** nasadil k účasti na jejich jménem

![Přineste vlastní – operátor](./media/ethereum-poa-deployment-guide/bring-you-own-operator.png)

### <a name="azure-monitor"></a>Azure Monitor

Toto řešení také součástí Azure monitoru ke sledování statistiky uzlu. Pro vývojáře aplikací získáte přehled o základní blockchain ke sledování bloku generování statistik. Operátory sítě můžete použít Azure Monitor mohla rychle rozpoznat a zabránit výpadky sítě prostřednictvím dotazovatelné protokoly a statistiky infrastruktury. Zobrazit [monitorování služby](#service-monitoring) další podrobnosti.

### <a name="deployment-architecture"></a>Architektura nasazení

#### <a name="description"></a>Popis

Toto řešení můžete nasadit jeden nebo více oblastí na základě sítě konsorcia Etherea více členy. Ve výchozím nastavení RPC a partnerského vztahu koncové body jsou přístupné přes veřejnou IP adresu k dosažení zjednodušené připojení v rámci předplatných a cloudy. Doporučujeme využívat [kontrakty videí společnosti Parity](https://wiki.parity.io/Permissioning) aplikace úroveň řízení přístupu. Také podporujeme sítě nasadit za bránou VPN, které využívají brány virtuální sítě pro připojení mezi předplatnými. Tato nasazení jsou složitější, proto se doporučuje nejprve začít s modelem veřejné IP.

#### <a name="consortium-member-overview"></a>Přehled členů W3C

Každý člen nasazení consortium zahrnuje:

-   Virtuální M Škálovací sady (VMSS) pro spouštění PoA validátory

-   Nástroj Azure Load Balancer pro distribuci RPC, partnerský vztah a požadavky zásad správného řízení DApp

-   Azure Key Vault pro zabezpečení ověřování identit

-   Úložiště Azure pro hostování trvalé síťové informace a koordinaci zapůjčení

-   Azure Monitor pro agregaci statistiku výkonu a protokolování

-   Brány virtuální sítě (volitelné) umožňující připojení k síti VPN přes virtuální privátní sítě

![Architektura nasazení](./media/ethereum-poa-deployment-guide/deployment-architecture.png)

Můžeme využít kontejnery Dockeru, spolehlivost a modularitu. Azure Container Registry využíváme k hostování a slouží jako součást každé nasazení označené verzí bitové kopie. Image kontejnerů se skládají z:

-   Orchestrator

    -   Spuštění jednou během nasazení

    -   Generuje identit a zásad správného řízení smlouvy

    -   Úložiště identit ve Store Identity

-   Parita klienta

    -   Identita z Identity Store zapůjčení

    -   Vyhledá a připojí k partnerské uzly

-   EthStats agenta

    -   Shromažďuje místní protokoly a statistiky přes RPC a nasdílí změny do Azure monitoru

-   Zásady správného řízení DApp

    -   Webové rozhraní pro interakci s kontrakty zásad správného řízení

## <a name="governance-dapp"></a>Zásady správného řízení DApp

V srdci testování autority je decentralizovaný zásad správného řízení. Zásady správného řízení DApp je sada předem nasazené chytrých kontraktů a webovou aplikaci, která se používají k řízení orgány v síti.
Autority jsou rozdělené do validátoru uzly a správu identit.
Správci mají power delegovat caiq účast na sadu uzlů program pro ověření. Správce taky může hlasovat jiní správci do nebo ze sítě.

![zásady správného řízení dapp](./media/ethereum-poa-deployment-guide/governance-dapp.png)

-   **Zásady správného řízení – decentralizované** změny autority sítě jsou spravovány prostřednictvím hlasování o řetězec vyberte správci.

-   **Program pro ověření delegování -** autority můžete spravovat jejich validátoru uzly, které jsou nastavené v každém PoA nasazení.

-   **Historie změn auditovatelných -** každé změně je zaznamenán v blockchainu transparentnosti a umožňuje audity.

## <a name="how-to-guides"></a>Návody

### <a name="deploy-ethereum-proof-of-authority"></a>Nasazení ethereum během testování of-Authority

Tady je příklad nasazení více stran toku:

1.  Tři členy generovat účet Etherea pomocí MetaMask

2.  *Člen A* nasadí Etherea PoA, zajištění jejich Etherea veřejnou adresu

3.  *Člen A* poskytuje adresu URL consortium *člen B* a *člen C*

4.  *Člen B* a *člen C* nasazení Etherea PoA, zajištění jejich Etherea veřejnou adresu a *člen A*společnosti consortium adresy URL

5.  *Člen A* hlasy *člen B* jako správce

6.  *Člen A* a *člen B* obou hlas *člen C* jako správce

Tento proces vyžaduje předplatné Azure, které podporují nasazení několika škálovacími sadami virtuálních počítačů a spravované disky. V případě potřeby [vytvořit si bezplatný účet Azure](https://azure.microsoft.com/free/) začít.

Po přihlášení k odběru je zabezpečená, přejděte na web Azure portal. Vyberte '+', Marketplace ("Zobrazit všechny") a vyhledejte Etherea PoA Consortium.

Následující části vás provede procesem konfigurace první člen nároky na místo v síti. Tok nasazení se dělí na pět kroků: základní informace o nasazení oblastí, velikost síťových a výkonu, ethereum během nastavení Azure Monitor.

#### <a name="basics"></a>Základy

V části **Základy**, zadejte hodnoty pro standardní parametry pro každé nasazení, jako je například předplatné, skupinu prostředků a vlastnosti základního virtuálního počítače.

Podrobný popis každého parametru takto:

Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty    
---|---|---|---
Vytvořit novou síť nebo připojit k existující síti?|Vytvořit novou síť nebo se připojit ke stávající sítě konsorcia|Vytvořit nové připojení k existující|Vytvořit nový
E-mailová adresa (volitelné)|Obdržíte e-mailové oznámení po dokončení nasazení s informacemi o vašem nasazení.|Platné e-mailovou adresu|Není k dispozici
Uživatelské jméno virtuálního počítače|Uživatelské jméno správce každé nasazení virtuálního počítače (jenom alfanumerické znaky)|1 – 64 znaků|Není k dispozici
Typ ověřování|Metodu k ověření k virtuálnímu počítači.|Heslo nebo SSH veřejný klíč|Heslo
Heslo (typ ověřování = heslo)|Heslo pro účet správce pro jednotlivé virtuální počítače nasazené.  Heslo musí obsahovat 3 z těchto věcí: 1 velké písmeno, 1 malé písmeno, 1 číslici a 1 speciální znak. Všechny virtuální počítače mají zpočátku stejné heslo, můžete změnit heslo po zřízení.|12 až 72 znaků|Není k dispozici
Klíč SSH (typ ověření veřejného klíče =)|Klíč zabezpečeného prostředí použitý ke vzdálenému přihlášení.||Není k dispozici
Předplatné|Předplatné, pro které má být nasazení sítě konsorcia||Není k dispozici
Skupina prostředků|Skupina prostředků, do které chcete nasadit sítě konsorcia.||Není k dispozici
Umístění|Oblast Azure pro skupinu prostředků.||Není k dispozici

Ukázkové nasazení se zobrazí pod: ![základní okna](./media/ethereum-poa-deployment-guide/basic-blade.png)

#### <a name="deployment-regions"></a>Nasazení oblastí

V dalším kroku v rámci oblasti nasazení, zadejte vstupy pro položky nakoupené k nasazení sítě konsorcia a výběr oblastí Azure na základě počtu oblastí daný počet. Uživatele můžete nasadit v maximálně 5 oblastech. Doporučujeme, abyste vyberete první oblasti tak, aby odpovídaly umístění skupiny prostředků v části Základy. Pro vývojové nebo testovací sítě se doporučuje jedné oblasti za člena. Pro produkční prostředí doporučujeme nasadit ve dvou nebo více oblastech pro vysokou dostupnost.

Podrobný popis každého parametru takto:

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
  Číslo oblasti|Počet oblastí k nasazení sítě konsorcia|1, 2, 3, 4, 5|1
  První oblasti|První oblasti k nasazení sítě konsorcia|Všechny povolené oblastí Azure|Není k dispozici
  Druhé oblasti|Druhé oblasti k nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 2)|Všechny povolené oblastí Azure|Není k dispozici
  Třetí oblasti|Třetí oblast pro nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 3)|Všechny povolené oblastí Azure|Není k dispozici
  Čtvrtý oblasti|Čtvrtý oblast pro nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 4)|Všechny povolené oblastí Azure|Není k dispozici
  Pátý oblasti|Pátý oblast pro nasazení sítě konsorcia (viditelné pouze v případě počtu oblastí je vybrán jako 5)|Všechny povolené oblastí Azure|Není k dispozici

Ukázkové nasazení se zobrazí pod: ![oblastí nasazení](./media/ethereum-poa-deployment-guide/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Velikost síťových a výkonu 

Dále v části 'velikost síťových a výkonu' určete vstupy pro velikost sítě konsorcia, jako je počet a velikost uzlů program pro ověření.
Velikost úložiště validátoru uzlu bude určovat velikost potenciální blockchainu. To můžete změnit po nasazení.

Podrobný popis každého parametru takto:

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
  Počet uzlů program pro ověření s vyrovnáváním zatížení|Počet uzlů validátoru zřizování v rámci sítě|2-15|2
  Výkon úložiště uzel program pro ověření|Typ spravovaný disk, na kterém každý z uzlů nasazený program pro ověření.|Úrovně Standard nebo Premium|Standard
  Velikost virtuálního počítače uzlu program pro ověření|Velikost virtuálního počítače pro uzly program pro ověření.|Standardní A, D standardní, standardní D-v2, řady Standard F, Standard DS a Standard služby FS|Standard D1 v2

Ukázkové nasazení se zobrazí pod: ![velikost a výkon sítě](./media/ethereum-poa-deployment-guide/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Ethereum během nastavení

Dále v části Nastavení Etherea určete nastavení konfigurace související s Ethereem, například síťového ID a Etherea účtu heslo nebo genesis bloku.

Podrobný popis každého parametru takto:

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
ID člena W3C|ID přidružené k kolizí, aby každý člen účastnících se sítě konsorcia slouží ke konfiguraci adresní prostory IP adres. V případě privátní sítě musí být ID člena jedinečná napříč různými organizacemi ve stejné síti.  Unique – člen ID je potřeba i v případě, že stejné organizace nasadí do víc oblastí. Poznamenejte si hodnotu tohoto parametru vzhledem k tomu je potřeba sdílet s ostatními spojovacího členy zajistit, že není žádná kolizí.|0 – 255|Není k dispozici
ID sítě|ID sítě pro síť consortium ethereum během nasazení.  Každá síť Etherea má svůj vlastní ID sítě, kdy 1 je ID pro veřejnou síť.|5 - 999 999 999|10101010
Adresa správce ethereum během|Adresa účtu Etherea, který slouží k účasti v PoA zásad správného řízení.  Doporučujeme používat MetaMask ke generování adresy Etherea.|42 alfanumerických znaků počínaje 0 x|Není k dispozici
Upřesnit možnosti|Rozšířené možnosti pro ethereum během nastavení|Povolit nebo zakázat|Zakázat
Veřejná IP adresa (Upřesnit možnosti = Enable)|Nasadí sítě bránou virtuální sítě a odebere partnerského vztahu přístup. Pokud je vybraná tato možnost, všichni členové musíte použít pro připojení brány virtuální sítě jako kompatibilní.|Veřejnou IP adresu privátní virtuální sítě|Veřejná IP adresa
Transakce oprávnění kontraktu (Upřesnit možnosti = Enable)|Bajtový kód pro kontrakt videí transakce. Omezuje inteligentní smlouvy nasazení a spuštění udělili oprávnění seznamu účtů Etherea.|Bajtový kód kontraktu|Není k dispozici

Ukázkové nasazení se zobrazí pod: ![ethereum během nastavení](./media/ethereum-poa-deployment-guide/ethereum-settings.png)

#### <a name="azure-monitor"></a>Azure Monitor

Okno Azure Monitor umožňuje nakonfigurovat prostředek Azure Monitor pro vaši síť. Azure Monitor shromáždí a poskytuje schopnost rychle zkontrolovat stav sítě nebo ladění vydá surface užitečné metriky a protokoly z vaší sítě.

  Název parametru|Popis|Povolené hodnoty|Výchozí hodnoty
  ---|---|---|---
Monitorování|Možnost povolit Azure Monitor|Povolit nebo zakázat|Povolení
Připojení k existující Log Analytics|Vytvořit novou instanci Log Analytics v rámci služby Azure Monitor nebo připojit k existující instanci|Vytvořit nový nebo existující připojení|Vytvořit nový
Azure Monitor Location(Connect to Existing Azure Monitor = Create new)|Oblast, kam se nasadí nové monitorování Azure|Všechny oblasti Azure Monitor|Není k dispozici
Stávající Id pracovního prostoru Log Analytics (připojit k existující Azure Monitor = připojení k existující)|ID pracovního prostoru Log Analytics existující instance||Není k dispozici
Existujícímu primárnímu klíči Log Analytics (připojit k existující Azure Monitor = připojení k existující)|Primární klíč pro připojení k existující instanci Azure Monitor||Není k dispozici


Ukázkové nasazení se zobrazí pod: ![azure monitoru](./media/ethereum-poa-deployment-guide/azure-monitor.png)

#### <a name="summary"></a>Souhrn

Proklikejte se okno s přehledem si zadané vstupy a spustit základní ověření před nasazením. Před nasazením si můžete stáhnout šablonu a parametry.

Přečíst si právní informace a ochrana osobních údajů podmínky a klikněte na tlačítko "Koupit" nasazení. Pokud nasazení zahrnuje brány virtuální sítě, nasazení bude trvat až 45 minut 50.

#### <a name="post-deployment"></a>Po nasazení

##### <a name="deployment-output"></a>Výstup nasazení 

Až se nasazení dokončí, budete mít přístup k potřebné parametry potvrzení e-mailem nebo na webu Azure portal. V těchto parametrů najdete:

-   Koncový bod Etherea RPC

-   Adresu URL řídicího panelu zásad správného řízení

-   Adresa URL Azure Monitor

-   Adresy URL dat

-   Id prostředku brány virtuální sítě (volitelné)

##### <a name="confirmation-email"></a>Potvrzení e-mailu 

Pokud zadáte e-mailovou adresu ([části Základy](#basics)), e-mailu by se odeslaly na e-mailovou adresu se výstup informací o nasazení.

![nasazení e-mailu](./media/ethereum-poa-deployment-guide/deployment-email.png)

##### <a name="portal"></a>Portál

Po úspěšném dokončení nasazení a všechny prostředky se zřizují budete moct zobrazit výstupních parametrů, které ve vaší skupině prostředků.

1.  Vyhledejte skupinu prostředků na portálu

2.  Přejděte na *nasazení*

3.  Vyberte hlavní nasazení se stejným názvem jako vaší skupiny prostředků

4.  Vyberte *výstupy*

### <a name="growing-the-consortium"></a>Stále se rozšiřující konsorcia

Rozšířit vaše consortium, musíte nejdřív připojit fyzické síti.
Pomocí nasazení založené na veřejné IP adresy je bezproblémové tento první krok. Pokud nasazení za bránou sítě VPN, naleznete v části [připojení brány virtuální sítě](#connecting-vnet-gateways) provést připojení k síti jako součást [kroku 2](#step-2-new-admin-deployment).

#### <a name="step-1-add-the-new-admin"></a>Krok 1: Přidejte nový správce

1.  Nový správce veřejnou adresu ethereum během shromažďování.

2.  Přejděte do DApp zásad správného řízení a vytvořit nový správce s adresou Ethereem a odpovídající alias

3.  Upozornit jiné existující členy nového požadavku tak, aby můžete hlasovat pro přidání tohoto nového správce

4.  Jakmile hlas dosáhne pole 51 %, člen bude přidán jako správce

![Přidání správce](./media/ethereum-poa-deployment-guide/adding-admin.png)

#### <a name="step-2-new-admin-deployment"></a>Krok 2: Nasazení nového správce

1.  Tyto informace nasdílet spojovacího člena. Tyto informace najdete v e-mailu po nasazení nebo v nasazení portálu výstup.

    -  Adresa Url Consortium dat

    -  Počet uzlů, které jste nasadili

    -  Id prostředku brány virtuální sítě (Pokud používáte VPN)

2.  Nasazení člen by měl používat [stejné řešení](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) při nasazování jejich přítomnost sítě s dodržujte při tom následující:

    -  Vyberte *připojit stávající*

    -  Zvolte požadovaný počet uzlů validátoru jako v ostatních členů v síti k zajištění uspokojivé reprezentace

    -  Použít stejnou Etherea adresu, která byla součástí [předchozí krok](#step-1-add-the-new-admin)

    -  Předejte zadaných *adresy Url dat Consortium* na *ethereum během nastavení* kartu

    -  Pokud zbytku sítě je za bránou sítě VPN, vyberte *virtuální privátní síť* v části Upřesnit

#### <a name="step-3-delegate-validators"></a>Krok 3: Delegáta validátory

Nový správce bude muset autorizovat validátory k účasti na jejich jménem. To není možné automaticky protože pouze člen řídí jejich klíčů.\*

_\*První členskými uzly v síti může automaticky přidat, protože jsme předkompilovat jejich uzlů program pro ověření do bloku genesis v síti._

Nový správce musíte provést následující kroky:

1.  Otevřete zásady správného řízení DApp nasazen jako součást *jejich* nasazení

2.  Přejděte na kartu validátory

3.  Vyberte jednotlivé validátory, které jsou nasazené a klikněte na tlačítko **přidat**

![Přidání validátory](./media/ethereum-poa-deployment-guide/adding-validators.png)

#### <a name="connecting-vnet-gateways"></a>Připojení brány virtuální sítě

V případě privátní sítě různí členové připojeni pomocí připojení brány virtuální sítě. Člen můžete připojit k síti a sledovat provoz transakce, existujícího člena nutné provést konečnou konfiguraci na jejich VPN gateway tak, aby přijímal připojení. To znamená, že uzly Etherea spojovacího člena se nespustí, dokud se naváže spojení. Doporučujeme vytvořit redundantní síťová připojení (síť) do consortium, abyste snížili šanci na jediný bod selhání.

Po nasazení nového člena, musíte dokončit existujícího člena obousměrné připojení pomocí nastavení připojení brány virtuální sítě do nového člena. K dosažení této existujícího člena potřebovat:

1.  ResourceId člen připojení brány virtuální sítě (zobrazit výstup nasazení)

2.  Klíč sdílení připojení

Existujícího člena, musíte spustit následující skript Powershellu pro dokončení připojení. Doporučujeme, abyste pomocí Azure Cloud Shell nachází v pravém horním navigačním panelu na portálu.

![službě cloud shell](./media/ethereum-poa-deployment-guide/cloud-shell.png)

```bash
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
$Subscription=Select-AzureRmSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzureRmVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Monitorování služeb

Můžete najít na portálu Azure Monitor odkaz v e-mailu nasazení nebo vyhledání parametru ve výstupu nasazení \[OMS\_portál\_URL\].

Na portálu se zobrazí nejprve statistiky a uzel přehled vysoké úrovně sítě.

![kategorie monitorování](./media/ethereum-poa-deployment-guide/monitor-categories.png)

Výběr **uzel přehled** nasměruje na portálu zobrazit podle uzlu infrastruktury statistiky.

![Statistika uzlu](./media/ethereum-poa-deployment-guide/node-stats.png)

Výběr **Statistika sítě** nasměruje zobrazit statistiku sítě Etherea.

![Statistika sítě](./media/ethereum-poa-deployment-guide/network-stats.png)

#### <a name="sample-log-analytics-queries"></a>Ukázkové dotazy Log Analytics

Za tyto řídicí panely je sada dotazovatelné nezpracovaných protokolů. Tyto nezpracovaných protokolů můžete použít k přizpůsobení řídicích panelů, vyšetřování chyb nebo nastavit prahovou hodnotu upozornění. Tady najdete, spustili sadu ukázky dotazů, které lze v nástroji pro hledání v protokolu:

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Seznam bloků, které uživatelé nahlásili více než jeden program pro ověření. Užitečné a pomáhají najít řetězu větve.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Získáte peer průměrný počet pro uzel zadaný program pro ověření byla více než 5 minut kbelíků.

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

Z bezpečnostních důvodů se přístup k portu SSH ve výchozím nastavení zakázaný podle pravidla skupiny zabezpečení sítě. Pro přístup k virtuální počítač škálovací sadu instancí v PoA sítě, budete muset změnit toto pravidlo \"povolit\"

1.  Začněte v oddílu přehled nasazený prostředek skupiny z portálu Azure portal.

    ![SSH – přehled](./media/ethereum-poa-deployment-guide/ssh-overview.png)

2.  Vybrat skupinu zabezpečení sítě

    ![SSH nsg](./media/ethereum-poa-deployment-guide/ssh-nsg.png)

3.  Vyberte \"povolit-ssh\" pravidlo

    ![SSH – povolit](./media/ethereum-poa-deployment-guide/ssh-allow.png)

4.  Změna \"akce\" umožňující

    ![povolení technologie SSH povolit](./media/ethereum-poa-deployment-guide/ssh-enable-allow.png)

5.  Klikněte na tlačítko \"Uložit\" (změny může trvat několik minut, než použití)

Můžete teď vzdáleně připojit k virtuálním počítačům pro uzly program pro ověření pomocí protokolu SSH s klíč správce zadané uživatelské jméno a heslo nebo SSH.
Příkaz SSH ke spuštění pro přístup k uzlu první program pro ověření je uveden v parametr výstupu šablony nasazení jako "SSH\_na\_první\_VL\_uzel\_REGION1" (pro ukázkové nasazení: ssh - p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Pokud chcete vrátit do transakce další uzly, zvyšovat číslo portu jednou (první uzel transakce je třeba na portu 4000).

Pokud jste nasadili do více než jedné oblasti, změňte výše uvedený příkaz na název DNS nebo IP adresu nástroje pro vyrovnávání zatížení v dané oblasti. K vyhledání názvu DNS nebo IP adresu z jiných oblastí, najít prostředek s zásady vytváření názvů \* \* \* \* \*- lbpip reg\#a zobrazit její název DNS a IP adres vlastnosti.

### <a name="azure-traffic-manager-load-balancing"></a>Vyrovnávání zatížení Azure Traffic Manageru

Azure Traffic Manager pomáhá snižovat prostoje a vylepšovat rychlost odezvy PoA sítě pomocí směrování příchozího provozu mezi několika nasazeními v různých oblastech. Integrované kontroly stavu a automatické přesměrování pomáhají zajistit vysokou dostupnost koncových bodů protokolu RPC a DApp zásad správného řízení. Tato funkce je užitečná, pokud jste nasadili do několika oblastí a je připraveno na produkční.

Traffic Manager můžete používat na:

-   Zlepšení dostupnosti sítě PoA pomocí automatického převzetí služeb při selhání.

-   Zlepšili rychlost vaší sítě směrování koncovými uživateli Azure umístění s nejnižší síťovou latencí.

Pokud se rozhodnete vytvořit profil služby Traffic Manager, můžete použít název DNS profilu pro přístup k síti. Jednou ostatním členům consortium byly přidány k síti, Traffic Manager slouží také k vyrovnávání zatížení mezi jejich nasazené validátorů.

#### <a name="creating-a-traffic-manager-profile"></a>Vytváří se profil služby Traffic Manager

Vyhledejte a vyberte \"profil služby Traffic Manager\" po kliknutí \"vytvořit prostředek\" tlačítko na webu Azure Portal.

![Vyhledejte azure traffic Manageru](./media/ethereum-poa-deployment-guide/traffic-manager-search.png)

Zadejte jedinečný název profilu a vyberte skupinu prostředků, který jste vytvořili během nasazení PoA.

![Vytvoření traffic Manageru](./media/ethereum-poa-deployment-guide/traffic-manager-create.png)

Po nasazení, vyberte instanci ve skupině prostředků. Název DNS pro přístup k traffic Manageru můžete najít na kartě Přehled

![Vyhledání DNS traffic Manageru](./media/ethereum-poa-deployment-guide/traffic-manager-dns.png)

Vyberte kartu koncové body a klikněte na tlačítko Přidat. Změňte cílového typu prostředku veřejné IP adresy. Zvolte veřejnou IP adresu v první oblasti\'nástroje pro vyrovnávání zatížení s.

![Směrování traffic Manageru](./media/ethereum-poa-deployment-guide/traffic-manager-routing.png)

Opakujte pro každou oblast v nasazené sítě. Jakmile jsou koncové body v \"povolené\" stavu, bude možné načíst automaticky a oblasti s vyrovnáváním na název DNS traffic Manageru. Teď můžete použít tento název DNS, místo \[CONSORTIUM\_DATA\_URL\] parametr v dalších krocích dokumentu.

## <a name="data-api"></a>Rozhraní API pro data

Každý člen consortium hostuje informace potřebné pro ostatní uživatele pro připojení k síti. Existujícího člena poskytne [CONSORTIUM_DATA_URL] před jejich nasazením člen. Při nasazování spojovacího člena se načtou informace o rozhraní JSON na následující koncový bod:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpověď bude obsahovat informace užitečné při spojování členy (Genesis blok, validátor nastavený smlouvy ABI, bootnodes) i informace, které jsou užitečné pro existujícího člena (program pro ověření adresy). Doporučujeme použít tento normalizace rozšířit konsorcia napříč poskytovatelů cloudových služeb. Toto rozhraní API vrátí odpověď ve formátu JSON s následující strukturou:
```json
{
  "$id": "", 
  "type": "object", 
  "definitions": {}, 
  "$schema": "http://json-schema.org/draft-07/schema#", 
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
## <a name="development"></a>Vývoj

### <a name="programmatically-interacting-with-a-smart-contract"></a>Programové interakce s inteligentní kontraktu

> [!WARNING]
> Nikdy neodesílat váš privátní klíč Etherea v síti! Zajistěte, aby každá transakce je podepsaný místně nejprve a podepsaný transakce je odeslán přes síť.

V následujícím příkladu vytvoříme s využitím *ethereumjs peněženky* ke generování adresy Etherea *ethereumjs tx* k podepsání místně, a *web3* odesílat nezpracovaná transakce Koncový bod Etherea RPC.

V tomto příkladu použijeme tento jednoduchý Hello-World inteligentní kontrakt:

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

Tento příklad předpokládá, že kontrakt je už nasazená. Můžete použít *solc* a *web3* pro nasazení smlouvy prostřednictvím kódu programu. Nejprve nainstalujte následující moduly uzlu:
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Tento skript nodeJS provede následující:

-   Sestavit nezpracovaný transakce: postMsg

-   Podepsat transakci pomocí vygenerovaný soukromý klíč

-   Odeslat podepsaný transakce Etherea sítě

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

### <a name="debug-a-smart-contract-truffle-develop"></a>Ladění inteligentní kontraktu (Truffle vývoj)

Truffle má místní vývoj síti, která je k dispozici pro ladění inteligentní kontraktu. Úplný kurz najdete [tady](http://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Podpora WebAssembly (WASM)

Podpora WebAssembly je již povolena pro vás v nově nasazeném PoA sítích. Umožňuje vývoj smart smlouvy v libovolném jazyce tohoto transpiles k sestavení webové (Rust, C, C++). Odkazy níže získáte další informace naleznete v tématu

-   Parita WebAssembly – přehled <https://wiki.parity.io/WebAssembly-Home>

-   Kurz z Parity Tech- <https://github.com/paritytech/pwasm-tutorial>

## <a name="faq"></a>Nejčastější dotazy

### <a name="i-notice-a-bunch-of-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Všimnu si toho spoustu transakce v síti, které jsem kód nefungoval\'t odeslat. Tyto odkud jsou?

Není bezpečné k odemknutí [osobní API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html). Účty bot naslouchají odemknout Etherea účty a pokuste se vyprazdňování fondů. Robot předpokládá tyto účty obsahovat ether reálné a pokusí se jako první siphon zůstatek na účtu. Nepovolujte osobní rozhraní API v síti. Místo transakce buď ručně pomocí peněženky, jako je MetaMask nebo programově, jak je uvedeno v části programově interakci s kontraktem inteligentní přihlaste předem.

### <a name="how-to-ssh-onto-a-vm"></a>Jak SSH na virtuálním počítači?

Jsme uzamknout port SSH z bezpečnostních důvodů. Postupujte podle [Tato příručka chcete povolit SSH port](#_Accessing_Nodes_via).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Jak můžu nastavit auditu člena nebo transakce uzlů?

Uzly transakce představují sadu Parity klienty, kteří jsou v partnerském vztahu se sítí, ale nejsou součástí shody. Tyto uzly stále slouží k odeslání ethereum během transakce a čtení stavu inteligentní kontraktu.
Tento postup funguje také jako mechanismus pro poskytování auditovatelnost pro regulační orgány sítě. K dosažení to jednoduše postupujte podle kroku 2 konsorcia narůstat.

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Proč jsou transakce MetaMask trvá příliš dlouho?

K zajištění, že transakce jsou přijímány ve správném pořadí, obsahuje každou transakci Etherea narůstající nonce. Pokud jste použili účet v MetaMask na jinou síť, bude nutné resetovat hodnotu nonce. Klikněte na ikonu nastavení (3panely), nastavení, obnovit účet. Historie transakcí se vymažou a teď můžete znovu spustit transakci.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Veřejnou IP adresu nasazení jsou kompatibilní s nasazeními privátní sítě?

Ne, partnerský vztah vyžaduje obousměrná komunikace tak celá síť musí být buď veřejné nebo soukromé.

## <a name="next-steps"></a>Další postup

Začínáme s použitím [ethereum během testování of-Authority Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) řešení.
