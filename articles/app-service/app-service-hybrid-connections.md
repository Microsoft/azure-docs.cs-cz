---
title: Aplikační služba Azure hybridních připojení | Microsoft Docs
description: Jak vytvořit a používat hybridní připojení pro přístup k prostředkům v sítích různorodých
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
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: 677642e4e97523ed71ff5857ae27263743dca535
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2017
ms.locfileid: "25990815"
---
# <a name="azure-app-service-hybrid-connections"></a>Hybridní připojení služby Azure App Service #

Hybridní připojení je součástí služby v Azure a funkcí ve službě Azure App Service. Jako služba se používá a možnosti nad rámec těch, které se používají ve službě App Service. Další informace o hybridních připojení a jejich využití mimo App Service naleznete v tématu [Azure předávání hybridní připojení][HCService].

V rámci služby App Service hybridní připojení slouží pro přístup k prostředkům aplikací v jiných sítích. Poskytuje přístup z vaší aplikace pro koncový bod aplikace. Alternativní schopnost přístup k aplikaci není povolen. Jako použít ve službě App Service, každý hybridní připojení korelaci na jedné kombinaci hostitele a portu TCP. To znamená, že koncový bod hybridní připojení může být v jakémkoliv operačním systému a všechny aplikace, můžete zadat přistupují k naslouchání portu TCP. Funkce hybridní připojení neznáte nebo vědět, co je aplikační protokol, nebo co se připojujete. Jednoduše poskytuje přístup k síti.  


## <a name="how-it-works"></a>Jak to funguje ##
Funkce hybridní připojení se skládá z dvě odchozí volání předávání přes Azure Service Bus. Existuje připojení z knihovny na hostiteli, kde vaše aplikace běží ve službě App Service. Je také připojení ze Správce hybridního připojení (HCM) k předávání přes Service Bus. HCM je předávací služba, která můžete nasadit v rámci sítě k prostředku, který se pokoušíte získat přístup k hostování. 

Prostřednictvím dvou připojené k připojení má vaše aplikace na druhé straně HCM tunel ke kombinaci pevné hostitele a portu TCP. Připojení používá TLS 1.2 pro zabezpečení a klíče sdíleného přístupového podpisu (SAS) pro ověřování a autorizaci.    

![Diagram toku na základní úrovni hybridní připojení][1]

Pokud vaše aplikace provede požadavek DNS, který odpovídá koncový bod nakonfigurovaný hybridní připojení, bude přesměrován odchozí přenosy TCP prostřednictvím daného hybridního připojení.  

> [!NOTE]
> To znamená, že byste měli zkusit vždy použijte název DNS pro hybridní připojení. Některé klientský software neprovádí vyhledávání DNS, pokud koncový bod místo toho používá IP adresu.
>
>

Funkce hybridní připojení má dva typy: hybridní připojení, které jsou nabízeny jako služba v rámci předávání přes Service Bus a starší Azure BizTalk Services hybridní připojení. Druhé jsou označovány jako Classic hybridní připojení na portálu. Není k dispozici další informace o nich později v tomto článku.

### <a name="app-service-hybrid-connection-benefits"></a>Výhody služby hybridní připojení aplikace ###

Existuje několik výhod do funkce hybridních připojení, včetně:

- Aplikace můžete přístup k místním systémům službám a bezpečně.
- Tato funkce nevyžaduje, aby koncový bod přístupné z Internetu.
- Je rychlý a snadný nastavit. 
- Každé hybridní připojení odpovídá kombinaci jednoho hostitele a portu, užitečné pro zabezpečení.
- Za normálních okolností nevyžaduje díry brány firewall. Přes standardní webu porty jsou všechny odchozí připojení.
- Protože tato funkce je úroveň sítě, je lhostejné jazyk používaný aplikace a z technologie používané v koncovém bodě.
- Slouží k poskytování přístupu ve více sítí z jedné aplikace. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Kroky, které nelze provést s hybridní připojení ###

Existuje několik věcí, které nemůžete dělat s hybridní připojení, včetně:

- Připojení na jednotku.
- Pomocí protokolu UDP.
- Přístup k službám založených na protokolu TCP, které používají dynamické porty, jako je například pasivní režim FTP nebo rozšířený pasivní režim.
- Podpora protokolu LDAP, protože někdy vyžaduje UDP.
- Podpora služby Active Directory.

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Přidávání a vytváření hybridních připojení v aplikaci ##

Můžete vytvořit hybridní připojení prostřednictvím aplikaci aplikační služby na portálu Azure nebo z předávání přes Azure na portálu Azure. Doporučujeme vytvořit hybridní připojení prostřednictvím aplikace služby App Service, který chcete používat s hybridní připojení. Chcete-li vytvořit hybridní připojení, přejděte na [portál Azure] [ portal] a vyberte svou aplikaci. Vyberte **sítě** > **nakonfigurovat koncové body hybridního připojení**. Tady vidíte hybridní připojení, které jsou nakonfigurované pro vaši aplikaci.  

![Seznam snímek hybridní připojení][2]

Chcete-li přidat nové hybridní připojení, vyberte **přidat hybridní připojení**.  Zobrazí seznam hybridní připojení, který jste už vytvořili. Pokud chcete přidat jeden nebo více těchto do vaší aplikace, vyberte ty a pak vyberte **přidat vybrané hybridní připojení**.  

![Portál snímek hybridní připojení][3]

Pokud chcete vytvořit nové hybridní připojení, vyberte **vytvořit nové hybridní připojení**. Zadejte: 

- Název koncového bodu.
- Hostitele koncového bodu.
- Koncový port.
- Obor názvů sběrnice, který chcete použít.

![Snímek obrazovky vytvořit hybridní připojení dialogové okno Nový][4]

Každý hybridní připojení je vázaný na oboru názvů Service Bus a každý obor názvů sběrnice je v oblasti Azure. Je důležité pokus o použití oboru názvů Service Bus ve stejné oblasti jako vaše aplikace, aby se zabránilo latence sítě vyvolané.

Pokud chcete odebrat hybridní připojení z vaší aplikace, pravým tlačítkem myši a vyberte **odpojení**.  

Když hybridní připojení se přidá do vaší aplikace, uvidíte na něm podrobnosti jednoduše tak, že ho vyberete. 

![Podrobnosti snímek hybridní připojení][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Vytvořit hybridní připojení na portálu Azure předávání ###

Kromě portálu zkušeností z vaší aplikace můžete vytvořit hybridní připojení z portálu pro předávání přes Azure. Pro hybridní připojení pro použití službou App Service musí být:

* Vyžadovat autorizací klienta.
* Má položku metadat, s názvem koncový bod, který obsahuje kombinaci hostitele a portu jako hodnotu.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybridní připojení a služby App Service plány ##

Funkce hybridní připojení je pouze k dispozici v Basic, Standard, Premium a izolovaná ceny SKU. Existují omezení svázané s cenový plán.  

> [!NOTE] 
> Můžete vytvořit pouze nové hybridní připojení, které jsou založené na předávání přes Azure. Nelze vytvořit nový BizTalk hybridní připojení.
>

| ceny plán | Počet hybridní připojení použitelné v plánu |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolated | 200 |

Všimněte si, že plán služby App Service se dozvíte, kolik hybridní připojení se používají a které aplikace.  

![Vlastnosti schématu snímek služby App Service][6]

Vyberte hybridní připojení zobrazíte podrobnosti. Zobrazí se všechny informace, které jste viděli v zobrazení aplikace. Můžete také zjistit, kolik aplikací v stejný plán, který používáte hybridní připojení.

Existuje omezení počtu koncové body hybridního připojení, které mohou být používány plán služby App Service. Každé hybridní připojení používají, ale lze napříč libovolný počet aplikací v tomto plánu. Například jeden hybridní připojení, který se používá v pěti samostatných aplikací v rámci plánu služby App Service se počítá jako jedna hybridní připojení.

Není dalších nákladů na použití hybridní připojení. Podrobnosti najdete v tématu [ceny služby Service Bus][sbpricing].

## <a name="hybrid-connection-manager"></a>Správce hybridního připojení ##

Funkce hybridní připojení vyžaduje přenosového agenta v síti, která hostí váš koncový bod hybridní připojení. Tento přenosový agent se nazývá hybridní připojení správce (HCM). Ke stažení z aplikace v rámci HCM, [portál Azure][portal], vyberte **sítě** > **nakonfigurovatkoncovébodyhybridníhopřipojení**.  

Tento nástroj se spustí v systému Windows Server 2012 a novější. Při instalaci HCM spouští jako služba, která se připojuje k předávání přes Service Bus, podle nakonfigurované koncové body. Připojení z HCM jsou odchozí do Azure přes port 443.    

Po instalaci HCM, můžete spustit HybridConnectionManagerUi.exe pomocí uživatelského rozhraní pro nástroj. Tento soubor je v instalačním adresáři správce hybridního připojení. V systému Windows 10, můžete také právě vyhledat *uživatelského rozhraní správce hybridního připojení* vyhledávacího pole.  

![Snímek obrazovky Správce hybridního připojení][7]

Při spuštění uživatelského rozhraní HCM je první věcí, kterou vidíte tabulku, která obsahuje seznam všech hybridní připojení, které jsou nakonfigurovány k této instanci HCM. Pokud chcete provést jakékoli změny, nejprve ověřit pomocí Azure. 

Přidat jeden nebo více hybridní připojení k vaší HCM:

1. Spuštění uživatelského rozhraní HCM.
1. Vyberte **nakonfigurovat jiné hybridní připojení**.
![Snímek obrazovky konfigurace nové hybridní připojení][8]

1. Přihlaste se pomocí účtu Azure.
1. Zvolte předplatné.
1. Vyberte hybridní připojení, který chcete HCM pro předávání.
![Snímek obrazovky hybridní připojení][9]

1. Vyberte **Uložit**.

Nyní můžete vidět hybridní připojení, které jste přidali. Můžete také vybrat nakonfigurované hybridní připojení zobrazíte podrobnosti.

![Snímek obrazovky podrobnosti hybridní připojení][10]

Pro podporu hybridní připojení, je nakonfigurován s, HCM vyžaduje:

- TCP přístup k Azure přes porty 80 a 443.
- TCP přístup ke koncovému bodu hybridní připojení.
- Možnost udělat look-ups DNS v hostiteli koncový bod a oboru názvů Service Bus.

HCM podporuje nové hybridní připojení i BizTalk hybridní připojení.

> [!NOTE]
> Předávání přes Azure spoléhá na webové sokety připojení. Tato možnost je pouze k dispozici v systému Windows Server 2012 nebo novější. Z tohoto důvodu HCM není podporováno na ničem starších než Windows Server 2012.
>

### <a name="redundancy"></a>Redundance ###

Každý HCM může podporovat víc hybridní připojení. Jakékoli dané hybridní připojení může také podporovat více HCMs. Výchozí chování je směrovat provoz mezi nakonfigurované HCMs pro libovolný dané koncový bod. Pokud chcete vysoké dostupnosti na hybridní připojení z vaší sítě, spusťte několik HCMs na samostatných počítačích. 

### <a name="manually-add-a-hybrid-connection"></a>Ručně přidat hybridní připojení ###

Pokud chcete povolit někdo mimo vaše předplatné pro hostování HCM instance pro danou hybridní připojení, sdílet připojovací řetězec brány pro hybridní připojení s nimi. Můžete to vidět ve vlastnostech pro hybridní připojení v [portál Azure][portal]. Chcete-li použít tento řetězec, vyberte **zadejte ručně** v HCM a vložte připojovací řetězec brány.


## <a name="troubleshooting"></a>Řešení potíží ##

Stav "Připojena" znamená, že je nakonfigurován pomocí této hybridní připojení alespoň jeden HCM a je schopný dosáhnout Azure. Pokud nebyl zobrazen stav pro hybridní připojení **připojeno**, hybridní připojení není nakonfigurováno na všechny HCM, který má přístup k Azure.

Hlavním důvodem, který klienti se nemůže připojit k jejich koncový bod je, protože koncový bod byl zadán pomocí IP adresy místo názvu DNS. Pokud vaše aplikace nelze dosáhnout požadovaného koncového bodu a použít IP adresu, přejdete k používání název DNS, který je na hostiteli, kde je spuštěna HCM. Také zkontrolujte, zda se název DNS na hostitele, na kterém je spuštěný HCM řeší správně. Ověřte, zda je připojení z hostitele se spuštěným HCM ke koncovému bodu hybridní připojení.  

Ve službě App Service může být nástroj tcpping vyvolat pomocí konzoly nástroje Rozšířené nástroje (Kudu). Tento nástroj vám sdělí, pokud máte přístup k koncový bod TCP, ale jeho nezjistíte Pokud máte přístup ke koncovému bodu hybridní připojení. Když použijete nástroj v konzole pro koncový bod hybridní připojení, jsou pouze potvrzení, že používá kombinaci hostitele a portu.  

## <a name="biztalk-hybrid-connections"></a>Hybridní připojení BizTalk ##

Starší schopností BizTalk hybridní připojení bylo ukončeno na nová BizTalk hybridní připojení. Můžete nadále používat stávající hybridní připojení BizTalk s vaší aplikací, ale měli byste migrovat na nové hybridní připojení, který používat předávání přes Azure. Mezi výhody v rámci nové služby BizTalk verze jsou:

- Vyžaduje se žádné další účet BizTalk.
- Protokol TLS je verze 1.2 místo verze 1.0.
- Komunikace je přes porty 80 a 443 a používá název DNS k dosažení Azure, a nikoli IP adresy a rozsah další porty.  

Chcete-li přidat stávající hybridní připojení BizTalk do vaší aplikace, přejděte na aplikace v [portál Azure][portal]a vyberte **sítě** > **konfigurace Koncové body hybridního připojení**. V tabulce Classic hybridní připojení, vyberte **přidat Classic hybridní připojení**. Pak uvidíte seznam BizTalk hybridní připojení.  


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

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
