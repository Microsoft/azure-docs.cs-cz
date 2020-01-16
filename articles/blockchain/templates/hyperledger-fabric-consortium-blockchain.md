---
title: Nasazení šablony řešení konsorcia prostředků infrastruktury pro hlavní knihu v Azure
description: Nasazení a konfigurace šablony řešení sítě konsorcia prostředků infrastruktury pro hlavní knihu v Azure
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: 3e7dcd3cdcfa636c0b23ac6643bd7732e7f8ada0
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029163"
---
# <a name="hyperledger-fabric-consortium-network"></a>Síť konsorcia infrastruktury pro hlavní knihu

K nasazení a konfiguraci sítě konsorcia prostředků infrastruktury hlavní knihy v Azure můžete použít šablonu řešení v rámci hlavní knihy.

> [!IMPORTANT]
> [Prostředky infrastruktury hlavní knihy v šabloně Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-hyperledger-fabric) budou zastaralé. Místo toho použijte [prostředky infrastruktury hlavní knihy ve službě Azure Kubernetes](hyperledger-fabric-consortium-azure-kubernetes-service.md) .  

Po přečtení tohoto článku:

- Získejte praktické znalosti o blockchain, prostředcích infrastruktury hlavní knihy a složitějších architekturách sítě konsorcia.
- Přečtěte si, jak nasadit a nakonfigurovat síť konsorcia infrastruktury pro hlavní knihu v rámci Azure Portal

## <a name="about-blockchain"></a>O blockchain

Pokud s komunitou blockchain začínáte, je tato šablona řešení skvělou příležitostí k tomu, abyste se seznámili s technologií v Azure snadno a konfigurovatelným způsobem. Blockchain je základní technologie za Bitcoin; je však mnohem více než pouze aktivátor pro virtuální měnu. Je to složený ze stávajících databází, distribuovaných systémů a kryptografických technologií, které umožňují zabezpečený výpočet více stran Díky zárukám týkajícím se neměnnosti, ověřování, auditování a odolnosti proti útokům. Různé protokoly využívají různé mechanismy pro poskytování těchto atributů. [Prostředek infrastruktury hlavní knihy](https://github.com/hyperledger/fabric) je jeden takový protokol.

## <a name="consortium-architecture-on-azure"></a>Architektura konsorcia v Azure

Pokud chcete povolit prostředky infrastruktury hlavní knihy v Azure, existují dva typy primárních nasazení, které jsou podporované. Tato nasazení jsou navržena tak, aby vyhovovala různým topologiím na základě požadovaného cíle.

- **Jeden virtuální počítač, server pro vývojáře** – tento typ nasazení je navržen jako vývojové prostředí, které se používá k vytváření a testování řešení založených na prostředcích infrastruktury hlavní knihy.
- **Několik virtuálních počítačů, nasazení škálované na více** instancí – tento typ nasazení je navržený pro prostředí, která modelují konsorcium různých účastníků využívajících sdílené prostředí.

V obou nasazeních jsou stavební bloky, které tvoří jádro infrastruktury hlavní knihy, stejné.  Rozdíly v nasazeních představují způsob horizontálního navýšení kapacity těchto komponent.

- **Uzly CA**: uzel, na kterém je spuštěná certifikační autorita, která se používá ke generování certifikátů používaných pro identity v síti.
- **Uzly**: uzel, na kterém je spuštěná komunikační služba implementující záruku doručení, jako je například celkové všesměrové vysílání objednávky nebo atomické transakce.
- **Partnerské uzly**: uzel, který potvrdí transakce a udržuje stav a kopii distribuované hlavní knihy.
- **Uzly CouchDB**: uzel, který může spustit službu CouchDB, která může uchovávat databázi stavu a poskytovat bohatou dotazování na data chaincode, která se šíří z jednoduchého klíče/hodnoty do úložiště typu JSON.

### <a name="single-virtual-machine-architecture"></a>Architektura jediného virtuálního počítače

Jak už bylo zmíněno dříve, pro vývojáře je k dispozici jenom jedna architektura virtuálního počítače, která bude sloužit k vývoji aplikací. Všechny zobrazené kontejnery jsou spuštěny na jednom virtuálním počítači. Služba objednávání používá pro tuto konfiguraci [samostatné](https://github.com/hyperledger/fabric/tree/master/orderer) . Tato *konfigurace není* objednaná služba řazení odolná proti chybám, ale je navržená tak, aby byla pro účely vývoje odlehčená.

![Architektura jediného virtuálního počítače](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Několik architektur virtuálních počítačů

Několik virtuálních počítačů, architektura škálování na více instancí, je postavená na úrovni vysoké dostupnosti a škálování každé komponenty v jádru. Tato architektura je pro nasazení produkčních prostředí mnohem vhodnější.

![Několik architektur virtuálních počítačů](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Začínáme

Začněte tím, že budete potřebovat předplatné Azure, které může podporovat nasazení několika virtuálních počítačů a standardních účtů úložiště. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/).

Jakmile budete mít předplatné, pokračujte na [Azure Portal](https://portal.azure.com). Vyberte **vytvořit prostředek > Blockchain > konsorcium prostředků infrastruktury pro hlavní knihu**.

![Šablona blockchain na webu pro jeden člen pro hlavní knihu prostředků](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Nasazení

V šabloně **sdružení prostředků infrastruktury pro hlavní kniha** vyberte **vytvořit**.

Nasazení šablony vás provede konfigurací sítě s více uzly v [hlavní knize 1,3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) . Tok nasazení je rozdělen do čtyř kroků: základy, nastavení sítě konsorcia, konfigurace prostředků infrastruktury a volitelné součásti.

### <a name="basics"></a>Základy

V nástroji **základy**zadejte hodnoty pro standardní parametry pro jakékoli nasazení. Například vlastnosti, předplatné, skupina prostředků a základní vlastnosti virtuálního počítače.

![Základy](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Předpona prostředku** | Předpona názvu pro prostředky zřízené v rámci nasazení |6 znaků nebo méně |
**Uživatelské jméno** | Uživatelské jméno správce pro každý virtuální počítač nasazený pro tohoto člena |1-64 znaků |
**Typ ověřování** | Metoda, která se má ověřit pro virtuální počítač |Heslo nebo veřejný klíč SSH|
**Heslo (typ ověřování = heslo)** |Heslo pro účet správce pro každý nasazený virtuální počítač. Heslo musí obsahovat tři z následujících typů znaků: 1 velké písmeno, 1 malé písmeno, 1 číslo a 1 speciální znak.<br /><br />Všechny virtuální počítače mají zpočátku stejné heslo, ale po zřízení můžete heslo změnit.|12-72 znaků|
**Klíč SSH (typ ověřování = veřejný klíč SSH)** |Klíč zabezpečeného prostředí používaný pro vzdálené přihlášení ||
**Předplatné** |Předplatné, ke kterému se má nasadit ||
**Skupina prostředků** |Skupina prostředků, do které se má nasadit síť konsorcia ||
**Umístění** |Oblast Azure, do které se má první člen nasadit ||

Vyberte **OK**.

### <a name="consortium-network-settings"></a>Nastavení sítě konsorcia

V **nastavení sítě**zadejte vstupy pro vytvoření nebo připojení existující sítě konsorcia a nakonfigurujte nastavení vaší organizace.

![Nastavení sítě konsorcia](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Konfigurace sítě** |Můžete zvolit vytvoření nové sítě nebo připojení k existující síti. Pokud zvolíte *připojit k existujícímu*, budete muset zadat další hodnoty. |Nová síť <br/> Připojit se k existujícímu |
**Heslo pro certifikační autoritu HLF** |Heslo používané pro certifikáty vygenerované certifikačními autoritami, které jsou vytvořeny jako součást nasazení. Heslo musí obsahovat tři z následujících typů znaků: 1 velké písmeno, 1 znak malého písmena, 1 číslice a 1 speciální znak.<br /><br />I když všechny virtuální počítače mají stejné heslo, můžete po zřízení změnit heslo.|1-25 znaků |
**Nastavení organizace** |Můžete přizpůsobit název a certifikát vaší organizace nebo použít výchozí hodnoty.|Výchozí <br/> Rozšířený |
**Nastavení sítě VPN** | Zřízení brány tunelového připojení VPN pro přístup k virtuálním počítačům | Ano <br/> Ne |

Vyberte **OK**.

### <a name="fabric-specific-settings"></a>Nastavení specifické pro prostředek infrastruktury

V části **Konfigurace prostředků infrastruktury**můžete nakonfigurovat velikost a výkon sítě a zadat vstupy pro dostupnost sítě. Jako je například pořadí čísel a rovnocenných uzlů, modul trvalosti používaný každým uzlem a velikost virtuálního počítače.

![Nastavení prostředků infrastruktury](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Typ škálování** |Typ nasazení buď jednoho virtuálního počítače s několika kontejnery, nebo několika virtuálních počítačů v modelu škálování na více instancí.|Jeden virtuální počítač nebo virtuální počítač s více virtuálními počítači |
**Typ disku virtuálního počítače** |Typ úložiště, který zálohuje všechny nasazené uzly. <br/> Další informace o dostupných typech disků najdete v části [Výběr typu disku](../../virtual-machines/windows/disks-types.md).|SSD úrovně Standard <br/> Premium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Nasazení více virtuálních počítačů (další nastavení)

![Nastavení prostředků infrastruktury pro nasazení s několika virtuálními počítači](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Počet uzlů pořadí** |Počet uzlů, které řadí (organizují) transakce do bloku. <br />Další podrobnosti o službě řazení najdete v [dokumentaci k](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) hlavní knize. |1 – 4 |
**Velikost virtuálního počítače uzlu pořadí** |Velikost virtuálního počítače používaná pro uzly pořadí v síti|BS úrovně Standard,<br />Služba DS úrovně Standard,<br />Standard FS |
**Počet partnerských uzlů** | Uzly vlastněné členy konsorcia, které spouštějí transakce a udržují stav a kopii hlavní knihy.<br />Další podrobnosti o službě řazení najdete v [dokumentaci k](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)hlavní knize.|1 – 4 |
**Trvalost stavu uzlu** |Modul trvalosti používaný partnerskými uzly. Tento modul můžete nakonfigurovat na jeden partnerský uzel. Další podrobnosti najdete v podrobnostech o několika partnerských uzlech.|CouchDB <br />LevelDB |
**Velikost virtuálního počítače rovnocenného uzlu** |Velikost virtuálního počítače použitá pro všechny uzly v síti|BS úrovně Standard,<br />Služba DS úrovně Standard,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Konfigurace více rovnocenných uzlů

Tato šablona vám umožní vybrat si modul trvalosti na partnerský uzel. Například pokud máte tři rovnocenné uzly, můžete použít CouchDB v jedné a LevelDB na druhé straně.

![Konfigurace více rovnocenných uzlů](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Vyberte **OK**.

### <a name="deploy"></a>Nasazení

V části **Souhrn**zkontrolujte zadané vstupy a spusťte základní ověřování před nasazením.

![Souhrn](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Přečtěte si právní a osobní údaje a vyberte **koupit** k nasazení. V závislosti na počtu zřízených virtuálních počítačů se může čas nasazení v několika minutách lišit až na desítky minut.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni se soustředit na vývoj aplikací a chaincode ve vaší blockchain síti pro konsorcium hlavní knihy.
