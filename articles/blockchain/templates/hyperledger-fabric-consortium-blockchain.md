---
title: Sítě konsorcia Hyperledger Fabric v Azure
description: Šablona řešení k nasazení a konfiguraci sítě konsorcia Hyperledger Fabric
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: 884f6a81796341e274027095691585212d5a3749
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830295"
---
# <a name="hyperledger-fabric-consortium-network"></a>Sítě konsorcia Hyperledger Fabric

Šablona řešení consortium Hyperledger Fabric můžete použít k nasazení a konfiguraci sítě konsorcia Hyperledger Fabric v Azure.

Po přečtení tohoto článku:

- Získat praktické znalosti blockchain, Hyperledger Fabric a složitější consortium architektury sítě
- Zjistěte, jak nasadit a konfigurovat síť Hyperledger Fabric consortium z webu Azure portal

## <a name="about-blockchain"></a>Informace o blockchainech

Pokud jste ještě v komunitě blockchain, tato šablona řešení je skvělé příležitosti, další informace o technologii snadné a konfigurovat způsobem v Azure. Blockchain je základní technologie těžba; je však mnohem víc než jenom podpůrnou technologii pro virtuální měnu. Je složeného existující databázi, distribuovaného systému a kryptografické technologie, která umožňuje zabezpečenou více stran výpočet s záruky neměnnosti, ověřitelnosti, umožňuje audity a odolnost vůči útokům. Různé protokoly využívat různé mechanismy k poskytování těchto atributů. [Hyperledger Fabric](https://github.com/hyperledger/fabric) je jeden takový protokol.

## <a name="consortium-architecture-on-azure"></a>Consortium architekturu v Azure

Pokud chcete povolit Hyperledger Fabric v Azure, existují dva typy nasazení, které jsou podporovány. Tato nasazení jsou navrženy tak, aby vyhovovaly různé topologie založené na požadovanou cílovou.

- **Jeden virtuální počítač, server pro vývojáře** – tento typ nasazení je navržena jako vývojové prostředí pro sestavování a testování řešení založených na Hyperledger Fabric.
- **Několik virtuálních počítačů, horizontální navýšení kapacity nasazení** – tento typ nasazení je určená pro prostředí, které modelují konsorcium různých účastníci využívající sdíleném prostředí.

V obou nasazení stavební bloky, se kterým je základní Hyperledger Fabric jsou stejné.  Rozdíly v nasazení se, jak tyto komponenty při horizontálním škálování.

- **Certifikační Autorita uzly**: Uzlu se systémem certifikační autority, která se používá ke generování certifikátů, které se používají pro identity v síti.
- **Uzly orderer**: Uzel komunikační službou implementace záruky doručení, jako je například celkový pořadí vysílání nebo atomické transakce.
- **Navázání partnerského vztahu mezi uzly**: Uzel, který potvrdí transakce a udržuje stav a kopie distribuované účetní knihy.
- **CouchDB uzly**: Uzel, který můžete spustit službu CouchDB, který může obsahovat stavu databáze a poskytují bohaté dotazování na data chaincode, rozšíření z jednoduchého klíč/hodnota do úložiště typu JSON.

### <a name="single-virtual-machine-architecture"></a>Architektura jednoho virtuálního počítače

Jak už bylo zmíněno dříve jedné virtuální architektura počítače je vytvořená pro vývojáře nízké nároky na serveru, který slouží k vývoji aplikací. Zobrazí všechny kontejnery běží v jednom virtuálním počítači. Pořadí služba používá [jednotlivé](https://github.com/hyperledger/fabric/tree/master/orderer) pro tuto konfiguraci. Tato konfigurace je *není* odolnost proti chybám řazení služby, ale byla navržena jako jednoduchý pro účely vývoje.

![Architektura samostatného virtuálního počítače](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Architektura více virtuálních počítačů

Více virtuální počítač, škálovatelnou architekturu, je sestavené s vysokou dostupností a škálování v jádru jednotlivých komponent. Tato architektura je mnohem vhodnější pro nasazení v produkčním prostředí na podnikové úrovni.

![Architektura více virtuálních počítačů](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Začínáme

Pokud chcete začít, potřebujete předplatné Azure, který podporuje nasazení několik virtuálních počítačů a účty úložiště úrovně standard. Pokud nemáte předplatné Azure, můžete si [vytvořit si bezplatný účet Azure](https://azure.microsoft.com/free/).

Jakmile budete mít předplatné, přejděte [webu Azure portal](https://portal.azure.com). Vyberte **vytvořit prostředek > Blockchain > Hyperledger Fabric Consortium**.

![Šablona Marketplace jeden člen Blockchain Hyperledger Fabric](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Nasazení

V **Hyperledger Fabric Consortium** šablony, vyberte **vytvořit**.

Šablona nasazení vás provede konfiguraci několika uzly [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) sítě. Tok nasazení je rozdělen na čtyři kroky: Základní informace o nastavení sítě konsorcia, konfigurace prostředků infrastruktury a volitelné součásti.

### <a name="basics"></a>Základy

V **Základy**, zadejte hodnoty pro standardní parametry pro každé nasazení. Předplatné, skupinu prostředků a základní virtuální počítače, jako vlastnosti.

![Základy](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Předpona prostředků**| Předpona názvu prostředků, které jsou zřízené jako součást svého nasazení |6 znaků nebo méně |
**Uživatelské jméno**| Uživatelské jméno správce pro jednotlivé virtuální počítače nasazené pro tento člen |1 – 64 znaků. |
**Typ ověřování**| Metodu k ověření k virtuálnímu počítači |Heslo nebo SSH veřejný klíč|
**Heslo (typ ověřování = heslo)**|Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat kombinaci tří z následujících typů znaků: 1 velké písmeno, 1 malé písmeno, 1 číslici a 1 speciální znak<br /><br />Všechny virtuální počítače mají zpočátku stejné heslo, můžete změnit heslo po zřízení|12 – 72 znaků|
**Klíč SSH (typ ověřování = veřejný klíč SSH)**|Klíč zabezpečeného prostředí používá pro vzdálené přihlášení ||
**Předplatné** |Předplatné, do které chcete nasadit ||
**Skupina prostředků** |Skupinu prostředků, do které chcete nasadit sítě konsorcia ||
**Umístění** |Oblasti Azure, do které chcete nasadit prvního člena v ||

Vyberte **OK**.

### <a name="consortium-network-settings"></a>Nastavení sítě konsorcia

V **nastavení sítě**zadejte vstupy pro vytvoření nebo připojení existující consortium sítě a konfigurace nastavení vaší organizace.

![Nastavení sítě konsorcia](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Konfigurace sítě**|Můžete vytvořit novou síť nebo se připojit ke stávající. Pokud se rozhodnete *připojte se k existující*, budete muset zadat další hodnoty. |Nové sítě <br/> Připojte se k existující |
**Heslo HLF certifikační Autority**|Heslo použité pro certifikáty generované infrastrukturou certifikačních autorit, které jsou vytvořeny jako součást svého nasazení. Heslo musí obsahovat kombinaci tří z následujících typů znaků: 1 velké písmeno, 1 malé písmeno, 1 číslici a 1 speciální znak.<br /><br />Všechny virtuální počítače mají zpočátku stejné heslo, můžete změnit heslo po zřízení.|1 - 25 znaků |
**Nastavení organizace** |Můžete přizpůsobit název vaší organizace a certifikát nebo mají výchozí hodnoty, který se má použít.|Výchozí <br/> Rozšířený  |
**Nastavení sítě VPN**| Zřízení bránu tunelového propojení sítě VPN pro přístup k virtuálním počítačům | Ano <br/> Ne |

Vyberte **OK**.

### <a name="fabric-specific-settings"></a>Nastavení prostředků infrastruktury

V **konfigurace infrastruktury**, odpovídající konfigurací výkonu a velikosti síti a určete vstupy pro dostupnost sítě. Například číslo orderer a partnerské uzly, stálost modul používaný každý uzel a velikost virtuálního počítače.

![Nastavení prostředků infrastruktury](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Typ škálování**|Typ nasazení jeden virtuální počítač s několika kontejnery nebo více virtuálních počítačů v modelu horizontální navýšení kapacity.|Jeden virtuální počítač nebo víc virtuálních počítačů |
**Typ disku virtuálního počítače**|Typ úložiště, zálohování, každý z nasazených uzlů. <br/> Další informace o službě storage najdete v tématu [Úvod do Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) a [Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|SSD úrovně Standard <br/> Premium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Nasazení více virtuálních počítačů (Další nastavení)

![Nastavení prostředků infrastruktury pro nasazení více virtuálních počítačů](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Název parametru | Popis | Povolené hodnoty |
|---|---|---|
**Počet uzlů orderer** |Počet uzlů, které pořadí (organizace) transakce do bloku. <br />Další podrobnosti o řazení služby, najdete Hyperledger [dokumentace](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) |1 – 4 |
**Velikost virtuálního počítače uzlu orderer** |Velikost virtuálního počítače pro uzly orderer v síti|Standard Bs<br />Standard Ds<br />Standard FS |
**Počet partnerské uzly**| Uzly, které jsou vlastněny consortium členy, které provádějí transakce a Udržovat stav a kopii hlavní knihy.<br />Další podrobnosti o řazení služby, najdete Hyperledger [dokumentaci](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1 – 4 |
**Trvalost stavu uzlu** |Trvalost modul používaný partnerské uzly. Můžete nakonfigurovat tento modul za partnerský uzel. Viz podrobnosti níže pro více partnerské uzly.|CouchDB </br>LevelDB |
**Velikost virtuálního počítače uzlu peer** |Velikost virtuálního počítače použít pro všechny uzly v síti|Standard Bs<br />Standard Ds<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Konfigurace uzlu několika partnera

Tato šablona umožňuje vybrat modul trvalost za partnerský uzel. Například pokud máte tři partnerské uzly můžete použít CouchDB na jednom a LevelDB na další dvě.

![Konfigurace uzlu několika partnera](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Vyberte **OK**.

### <a name="deploy"></a>Nasazení

V **Souhrn**, zkontrolujte vstupy zadán a ke spuštění základních ověřovacích před nasazením.

![Souhrn](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Přečtěte si podmínky právní informace a ochrana osobních údajů a vyberte **nákupní** k nasazení. V závislosti na počtu virtuálních počítačů zřizuje nasazení doba se může lišit od několika minut na desítky minut.

## <a name="next-steps"></a>Další postup

Nyní jste připraveni soustředit na vývoj chaincode proti síť Hyperledger consortium blockchainu a aplikace.
