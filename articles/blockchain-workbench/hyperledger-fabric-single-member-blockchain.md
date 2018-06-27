---
title: Consortium Hyperledger prostředků infrastruktury
description: Pomocí této šablony řešení Hyperledger Fabric Consortium nasaďte a nakonfigurujte síť jednoho člena
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 28561f5f94044d19cfd07e99d7f7a736ec470cf1
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960351"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger Fabric jednoho člena sítě

Šablona řešení Consortium Hyperledger prostředků infrastruktury můžete použít k nasazení a konfiguraci sítě prostředků infrastruktury Hyperledger jednoho člena (více uzly).

Po přečtení tohoto článku:

- Získat praktické znalosti blockchain, Hyperledger prostředků infrastruktury a složitější consortium síťových architektur
- Zjistěte, jak nasadit a nakonfigurovat jeden člen síť consortium Hyperledger prostředků infrastruktury z v rámci portálu Azure

## <a name="about-blockchain"></a>O blockchain

Pokud jste nový blockchain komunitě, je to skvělý příležitost Další informace o technologii způsobem snadno a konfigurovat v Azure. Blockchain je základní technologií za Bitcoin; je však mnohem víc než jenom podpůrnou technologii pro virtuální měny. Je složené existující databázi, distribuovaného systému a kryptografické technologie, která umožňuje zabezpečený více stran výpočty s záruky ohledně neměnitelnosti, ověřitelnosti, auditability a odolnost proti útokům. Různé protokoly použít jiné mechanismy k poskytování těchto atributů. [Hyperledger Fabric](https://github.com/hyperledger/fabric) je jeden takový protokol.

## <a name="consortium-architecture-on-azure"></a>Architektura Consortium v Azure

Tato šablona nasadí topologii k testování a simulovat produkční pro uživatele v rámci jedné organizaci (jednoho člena). Toto nasazení se skládá z několika uzly sítě v jedné oblasti, dokdy se rozšířit do několika oblastí.

Síť se skládá ze tří typů uzlů:

1. **Člen uzlu**: uzlu se systémem služby členství prostředků infrastruktury, která se zaregistruje a spravuje členy sítě. Tento uzel můžou být Clusterované pro škálovatelnost a vysokou dostupnost, nakonec, ale v tomto testovacím prostředí, použije se uzel jednoho člena.
2. **Uzly orderer**: uzlu se systémem komunikační služby implementace záruky doručení, jako je celkový počet pořadí všesměrového vysílání nebo atomic transakce.
3. **Peer uzly**: uzel, který potvrdí transakce a udržuje stav a kopii distribuované hlavní knihy.

## <a name="getting-started"></a>Začínáme

Pokud chcete začít, budete potřebovat předplatné Azure, který podporuje nasazení několik virtuálních počítačů a účty úložiště standard storage. Pokud nemáte předplatné Azure, můžete [vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/).

Ve výchozím nastavení většina typů předplatné podporuje topologii nasazení malého rozsahu bez nutnosti o navýšení kvóty. Nejmenší možné nasazení pro jeden člen budete potřebovat:

- 5 virtuálních počítačů (5 jader)
- 1 virtuální sítě
- Nástroj pro vyrovnávání zatížení 1
- 1 veřejnou IP adresu

Jakmile máte předplatné, přejděte na [portál Azure](https://portal.azure.com). Vyberte **+**, vyberte **Blockchain**a vyberte **Hyperledger Fabric jednoho člena Blockchain**.

![Šablona Hyperledger Fabric jednoho člena Blockchain Marketplace](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Nasazení

Chcete-li začít, vyberte **Hyperledger Fabric jednoho člena Blockchain** a klikněte na tlačítko **vytvořit**. Otevře se **Základy** okno v průvodci.

Nasazení šablony budou vás provede procesem konfigurace sítě s více uzly. Tok nasazení je rozdělené do tří kroků: základy, konfigurace sítě a konfigurace prostředků infrastruktury.

### <a name="basics"></a>Základy

V části **Základy** okno, zadejte hodnoty pro standardní parametry pro všechna nasazení, jako je například předplatné, skupinu prostředků a vlastnosti základní virtuálního počítače.

![Základy](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Název parametru| Popis| Povolené hodnoty|Výchozí hodnota
---|---|---|---
**Předpona prostředků**| Řetězec se používá jako základ pro pojmenování nasazené prostředky.|6 znaků nebo méně|Není k dispozici
**Virtuální počítač uživatelské jméno**| Uživatelské jméno správce pro jednotlivé virtuální počítače nasazené pro tento člen.|1 - 64 znaků.|azureuser
**Typ ověřování**| Metoda ověřování do virtuálního počítače.|Heslo nebo SSH veřejný klíč|Heslo
**Heslo (typ ověřování = heslo)**|Heslo pro účet správce pro jednotlivé virtuální počítače nasazené. Heslo musí obsahovat 3 z následujících: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak.<br /><br />I když všechny virtuální počítače původně má stejné heslo, můžete změnit heslo po zřízení.|12 - 72 znaků|Není k dispozici
**Klíč SSH (typ ověřování = veřejný klíč)**|Klíč zabezpečeného prostředí použít pro vzdálené přihlášení.||Není k dispozici
**Omezení přístupu pomocí IP adresy**|Nastavení k určení typu, zda klientský přístup koncový bod je s omezeným přístupem, nebo ne.|Ano/Ne| Ne
**Povolené IP adresy nebo podsítě (omezení přístupu pomocí IP adresy = Yes)**|IP adresa nebo sadu IP adres, který je povolen přístup k koncový bod klienta, pokud je povolena řízení přístupu.||Není k dispozici
**Předplatné** |Odběr, do které chcete nasadit.
**Skupina prostředků** |Skupinu prostředků, do které chcete nasadit consortium sítě.||Není k dispozici
**Umístění** |Oblast Azure, do které chcete nasadit první člen ** nároků sítě s.

### <a name="network-size-and-performance"></a>Velikost sítě a výkon

V části Další **síťové velikost a výkon,** zadejte vstupy pro velikost consortium sítě, jako je počet členství, orderer a partnerské uzly. Zvolte možnosti infrastruktury a velikost virtuálního počítače.

![Velikost sítě a výkon](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Název parametru| Popis| Povolené hodnoty|Výchozí hodnota
---|---|---|---
**Počet uzlů členství**|Počet uzlů, které spouštění služby členství. Další podrobnosti o službě členství, podívejte se na zabezpečení a služby členství v rámci Hyperledger [dokumentaci](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Tato hodnota je aktuálně omezeno na 1 uzel, ale plánujeme podporu škálování pomocí clusteringu další změny.|1| 1
**Počet uzlů Orderer** |Počet uzlů, které pořadí (uspořádat)--> transakce do bloku. Toto prohlášení je rozvláčný a matoucí. Další informace o službě řazení, najdete v článku Hyperledger [dokumentaci](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html).<br /><br />Tato hodnota jsou nyní omezena na 1 uzel. |1 |1
**Počet partnerské uzly**| Uzly, které jsou vlastněny consortium členy, kteří provádějí transakce a Udržovat stav a kopii hlavní knihy.<br /><br />Další informace o službě řazení, najdete v článku Hyperledger [dokumentaci](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 - 9
**Výkon úložiště**|Typ úložiště zálohování všech nasazených uzlech. Další informace o úložišti, najdete [Úvod do Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) a [Storage úrovně Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard nebo Premium|Standard
**Velikost virtuálního počítače** |Velikost virtuálního počítače použít pro všechny uzly v síti|Standardní A<br />Standardní D<br />Standardní D-v2<br />Standardní řady F,<br />Standardní DS<br />a standardní služby FS|Standardní D1_v2

### <a name="fabric-specific-settings"></a>Konkrétní nastavení prostředků infrastruktury

Nakonec v části **nastavení prostředků infrastruktury**, zadat nastavení konfigurace související se prostředků infrastruktury.

![Nastavení prostředků infrastruktury](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Název parametru| Popis| Povolené hodnoty|Výchozí hodnota
---|---|---|---
**Zavedení uživatelské jméno**| Počáteční oprávnění uživatele, který se zaregistruje službu člen v nasazené sítě.|9 nebo méně znaků|Správce
**Zavedení uživatelské heslo pro certifikační Autoritu prostředků infrastruktury**|Heslo správce používá k zabezpečení účtu prostředků infrastruktury certifikační Autority naimportovat do uzlu členství.<br /><br />Heslo musí obsahovat jedno velké písmeno, jedno malé písmeno a jedno číslo.|12 nebo více znaků|Není k dispozici

### <a name="deploy"></a>Nasazení

V **Souhrn**, zkontrolujte vstupní hodnoty zadané a spuštění základní ověření před nasazením.

![Souhrn](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Přečtěte si podmínky právní a ochrana osobních údajů a klikněte na tlačítko **nákupu** k nasazení. Podle toho, kolik virtuálních počítačů, které se zřídí nasazení doba se může lišit od několika minut do desítkami minut.

### <a name="accessing-nodes"></a>Přístup k uzly

Po dokončení nasazení **přehled** se zobrazí.

![Nasazení](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Pokud na obrazovce se nezobrazí automaticky (možná vzhledem k tomu, že jste přesunuli kolem portálu pro správu při nasazení byla spuštěna), je vždy najít na kartě skupiny prostředků v levém navigačním panelu. Klikněte na název skupiny prostředků, který jste zadali v kroku 1 přejít na **přehled** stránky.

Přehled uvádí všechny prostředky, které byly nasazeny šablonou řešení. Je můžete prozkoumat v bude, ale na této obrazovce můžete taky Přejít _výstupní parametry_ vygenerované šablony. Tyto výstupní parametry získáte užitečné informace pro připojení k síti prostředků infrastruktury Hyperledger.

Chcete-li získat přístup k výstupní parametry, nejprve klikněte na **nasazení** kartě v okně skupiny prostředků. Zobrazí se v historii nasazení.

![Historie nasazení](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Z historie nasazení klikněte na tlačítko při prvním nasazení v seznamu a podívejte se na podrobnosti.

![Podrobnosti o nasazení](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Souhrn nasazení, za nímž následuje tři užitečné výstupní parametry se zobrazí obrazovce s podrobnostmi:

- _Koncový bod rozhraní API_ je možné po nasazení aplikace v síti.
- _PŘEDPONU_ , označovaný také _nasazení předponu_ , jednoznačně identifikuje prostředkům a vaše nasazení. Použije se při použití nástroje příkazového řádku založené na.
- _SSH na první počítač_ poskytuje můžete předem sestavených příkaz ssh se všemi správné parametry požadované pro připojení k první virtuální počítač ve vaší síti; v případě Hyperledger prostředků infrastruktury, bude uzel Fabric-CA.

Může se vzdáleně připojit ke virtuálních počítačů pro každý uzel pomocí protokolu SSH s klíč správce zadané uživatelské jméno a heslo nebo SSH. Vzhledem k tomu, že uzel virtuální počítače nemají své vlastní veřejné IP adresy, musíte přejít pomocí nástroje pro vyrovnávání zatížení a zadejte číslo portu. Příkaz SSH pro přístup do prvního uzlu transakce je třetí výstup šablony ** SSH k první virtuální počítač (pro ukázkové nasazení: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Abyste se dostali na další transakci uzly, zvýšíte číslo portu pomocí jedné (například u prvního uzlu transakce je na portu 3000, druhý je na 3001, třetí na 3002, atd.).

## <a name="next-steps"></a>Další postup

Nyní jste připraveni a zaměřit se na aplikace a vývoj chaincode proti Hyperledger consortium blockchain sítě.
