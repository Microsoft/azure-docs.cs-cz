---
title: Konsorcium Hyperledger Fabric
description: Pomocí šablony řešení Hyperledger Fabric Consortium můžete nasadit a nakonfigurovat síť jednoho člena
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: c08557156848d4e7fcf0b1adbe6c8faa4ee00c82
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231368"
---
# <a name="hyperledger-fabric-single-member-network"></a>Síť Hyperledger Fabric jednoho člena

Šablona řešení Hyperledger Fabric Consortium můžete nasadit a nakonfigurovat síť Hyperledger Fabric jeden člen (s několika uzly).

Po přečtení tohoto článku:

- Získat praktické znalosti blockchain, Hyperledger Fabric a složitější consortium architektury sítě
- Zjistěte, jak nasadit a nakonfigurovat jedním členem sítě konsorcia Hyperledger Fabric z webu Azure portal

## <a name="about-blockchain"></a>Informace o blockchainech

Pokud jste ještě v komunitě blockchain, tato šablona řešení je skvělé příležitosti, další informace o technologii snadné a konfigurovat způsobem v Azure. Blockchain je základní technologie těžba; je však mnohem víc než jenom podpůrnou technologii pro virtuální měnu. Je složeného existující databázi, distribuovaného systému a kryptografické technologie, která umožňuje zabezpečenou více stran výpočet s záruky neměnnosti, ověřitelnosti, umožňuje audity a odolnost vůči útokům. Různé protokoly využívat různé mechanismy k poskytování těchto atributů. [Hyperledger Fabric](https://github.com/hyperledger/fabric) je jeden takový protokol.

## <a name="consortium-architecture-on-azure"></a>Consortium architekturu v Azure

Tato šablona nasadí topologii pro testování a simulovat produkční prostředí pro uživatele v rámci jedné organizace (jeden člen). Toto nasazení se skládá z několika uzly sítě v jedné oblasti a brzy mají být rozbaleny do více oblastí.

Síť se skládá ze tří typů uzlů:

1. **Člen uzlu**: uzlu se systémem služby členství prostředků infrastruktury, která registruje a spravuje členy sítě. Tento uzel může být Clusterované pro zajištění škálovatelnosti a vysoké dostupnosti; Ale v tomto testovacím prostředí se dá použít uzlu jeden člen.
2. **Uzly orderer**: uzlu se systémem komunikační služby implementace záruky doručení, jako je například celkový počet pořadí všesměrového vysílání nebo atomické transakce.
3. **Navázání partnerského vztahu mezi uzly**: uzel, který potvrdí transakce a udržuje stav a kopie distribuované účetní knihy.

## <a name="getting-started"></a>Začínáme

Pokud chcete začít, budete potřebovat předplatné Azure, který podporuje nasazení několik virtuálních počítačů a účty úložiště úrovně standard. Pokud nemáte předplatné Azure, můžete si [vytvořit si bezplatný účet Azure](https://azure.microsoft.com/free/).

Ve výchozím nastavení podporují většinu typů předplatného topologie nasazení malého rozsahu bez nutnosti zvýšení kvóty. Nejmenší možné nasazení více než jeden člen budete potřebovat:

- 5 virtuálních počítačů (5 jader)
- 1 virtuální sítě
- Nástroj pro vyrovnávání zatížení 1
- 1 veřejnou IP adresu

Jakmile budete mít předplatné, přejděte [webu Azure portal](https://portal.azure.com). Vyberte **+** vyberte **Blockchain**a vyberte **Hyperledger Fabric jeden člen Blockchain**.

![Šablona Marketplace jeden člen Blockchain Hyperledger Fabric](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Nasazení

Pokud chcete začít, vyberte **Hyperledger Fabric jeden člen Blockchain** a klikněte na tlačítko **vytvořit** otevřete **Základy** okno v průvodci.

Šablona nasazení vás provede procesem konfigurace sítě více uzly. Tok nasazení je rozdělené do tří kroků: Základy konfigurace sítě a konfigurace prostředků infrastruktury.

### <a name="basics"></a>Základy

V **Základy** okno, zadejte hodnoty pro standardní parametry pro každé nasazení. Předplatné, skupinu prostředků a základní virtuální počítače, jako vlastnosti.

![Základy](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Název parametru| Popis| Povolené hodnoty|Výchozí hodnota
---|---|---|---
**Předpona prostředků**| Řetězec použitý jako základ pro vytváření názvů nasazených prostředků.|6 znaků nebo méně|Není k dispozici
**Uživatelské jméno virtuálního počítače**| Uživatelské jméno správce pro jednotlivé virtuální počítače nasazené pro tento člen.|1 – 64 znaků.|azureuser
**Typ ověřování**| Metodu k ověření k virtuálnímu počítači.|Heslo nebo SSH veřejný klíč|Heslo
**Heslo (typ ověřování = heslo)**|Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat kombinaci tří z následujících typů znaků: 1 velké písmeno, 1 malé písmeno, 1 číslici a 1 speciální znak.<br /><br />Všechny virtuální počítače mají zpočátku stejné heslo, můžete změnit heslo po zřízení.|12 – 72 znaků|Není k dispozici
**Klíč SSH (typ ověření veřejného klíče =)**|Klíč zabezpečeného prostředí použitý ke vzdálenému přihlášení.||Není k dispozici
**Omezení přístupu pomocí IP adresy**|Nastavení k určení typu, zda je koncový bod přístupu klientů s omezeným přístupem nebo ne.|Ano/Ne| Ne
**Povolené IP adresy nebo podsítě (omezení přístupu pomocí IP adresy = Ano)**|IP adresa nebo sadu IP adres, který je povolen přístup k koncový bod klienta při řízení přístupu je povolené.||Není k dispozici
**Předplatné** |Předplatné, do které chcete nasadit.
**Skupina prostředků** |Skupina prostředků, do které chcete nasadit sítě konsorcia.||Není k dispozici
**Umístění** |Oblasti Azure, do které chcete nasadit první člen ** s nároky na síť.

### <a name="network-size-and-performance"></a>Velikost síťových a výkonu

Dále v **velikost a výkon sítě** zadejte vstupy pro velikost sítě consortium. Například počet členství, orderer a partnerské uzly. Zvolte možnosti infrastruktury a velikost virtuálního počítače.

![Velikost síťových a výkonu](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Název parametru| Popis| Povolené hodnoty|Výchozí hodnota
---|---|---|---
**Počet uzlů členství**|Počet uzlů, na kterých běží služby členství. Další podrobnosti o službě členství, podívejte se na zabezpečení a členství služby v rámci Hyperledger [dokumentaci](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Tato hodnota je momentálně omezené jenom na 1 uzel, ale plánujeme podporují horizontální navýšení kapacity pomocí clusteringu v příští revize.|1| 1
**Počet uzlů Orderer** |Počet uzlů, které pořadí (organizace) transakce do bloku.--> to je příkaz rozvláčný a chaoticky. Další podrobnosti o řazení služby, najdete Hyperledger [dokumentaci](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html).<br /><br />Tato hodnota je momentálně omezené jenom na 1 uzel. |1 |1
**Počet partnerské uzly**| Uzly, které jsou vlastněny consortium členy, které provádějí transakce a Udržovat stav a kopii hlavní knihy.<br /><br />Další podrobnosti o řazení služby, najdete Hyperledger [dokumentaci](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 – 9
**Výkon úložiště**|Typ úložiště, zálohování, každý z nasazených uzlů. Další informace o službě storage najdete v tématu [Úvod do Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) a [Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Úrovně Standard nebo Premium|Standard
**Velikost virtuálního počítače** |Velikost virtuálního počítače použít pro všechny uzly v síti|Standardní A<br />Standardní D<br />Standardní D-v2<br />Standardní řada F<br />Standard DS<br />a Standard služby FS|Standardní D1_v2

### <a name="fabric-specific-settings"></a>Nastavení prostředků infrastruktury

Nakonec v části **nastavení prostředků infrastruktury**, zadejte nastavení konfigurace související s prostředky infrastruktury.

![Nastavení prostředků infrastruktury](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Název parametru| Popis| Povolené hodnoty|Výchozí hodnota
---|---|---|---
**Spuštění uživatelského jména**| Počáteční autorizovaný uživatel, který se zaregistruje do služby člena v nasazené sítě.|9 nebo méně znaků|Správce
**Heslo uživatele Bootstrap pro prostředky infrastruktury certifikační Autority**|Heslo správce používá k zabezpečení účtu certifikační Autority infrastruktury importovat do uzlu členství.<br /><br />Heslo musí obsahovat jedno velké písmeno, jedno malé písmeno a jednu číslici.|12 znaků|Není k dispozici

### <a name="deploy"></a>Nasazení

V **Souhrn**, zkontrolujte vstupy zadán a ke spuštění základních ověřovacích před nasazením.

![Souhrn](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Přečíst si právní informace a ochrana osobních údajů podmínky a klikněte na tlačítko **nákupní** k nasazení. V závislosti na počtu virtuálních počítačů zřizuje nasazení doba se může lišit od několika minut na desítky minut.

### <a name="accessing-nodes"></a>Přístup k uzly

Po dokončení nasazení **přehled** se zobrazí.

![Nasazení](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Pokud se na obrazovce se nezobrazí automaticky (možná protože přesouvat na portálu pro správu při nasazení byla spuštěna), vždycky najdete ho na kartě skupiny prostředků v levém navigačním panelu. Klikněte na název skupiny prostředků, který jste zadali v kroku 1, přejděte **přehled** stránky.

Přehledu jsou uvedeny všechny prostředky, které se nasadily pomocí šablony řešení. Můžete je prozkoumat kdykoli, ale z této obrazovky můžete také použít _výstupních parametrů_ vygenerovaná šablona. Tyto parametry výstup vám poskytne užitečné informace při připojení k síti Hyperledger Fabric.

Výstupní parametry, nejprve klikněte na tlačítko na **nasazení** karty v okně skupiny prostředků. Zobrazí se historie nasazení.

![Historie nasazení](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Z historie nasazení klikněte na tlačítko při prvním nasazení v seznamu a podívejte se na podrobnosti.

![Podrobnosti nasazení](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Shrnutí nasazení, za nímž následuje tři užitečné výstupních parametrů se zobrazí na obrazovce s podrobnostmi:

- _Koncový bod rozhraní API_ je možné po nasazení aplikace v síti.
- _PŘEDPONU_ , označované také jako _nasazení předponu_ , jednoznačně identifikuje vašich prostředků a nasazení. Použije se při použití nástroje příkazového řádku založené na.
- _SSH k první virtuální počítač_ poskytuje vám předem sestavený příkaz "SSH" se všemi správné parametry požadované pro připojení k první virtuální počítač v síti. Pro Hyperledger Fabric bude uzel Fabric certifikační Autority.

Můžete se vzdáleně připojit k virtuálním počítačům pro každý uzel přes protokol SSH s klíč správce zadané uživatelské jméno a heslo nebo SSH. Vzhledem k tomu, že virtuální počítače uzlů nemají vlastní veřejné IP adresy, je potřeba projít nástroje pro vyrovnávání zatížení a zadejte číslo portu. Příkaz SSH pro přístup k první uzel transakce je třetí výstup šablony ** SSH k první virtuální počítač (pro ukázkové nasazení: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Pokud chcete vrátit do transakce další uzly, zvyšovat číslo portu jednou (například první uzel transakce je na port 3000, druhá je na 3001, třetí je na 3002, atd.).

## <a name="next-steps"></a>Další postup

Nyní jste připraveni soustředit na vývoj chaincode proti síť Hyperledger consortium blockchainu a aplikace.
