---
title: Azure platebních zpracování plán, podle kterého – testování požadavky
description: PCI DSS požadavek 11
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 21cb854ba35adcf7576874e3c6c7149fbd33d998
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Testování požadavky pro kompatibilní se standardem PCI DSS prostředí 
## <a name="pci-dss-requirement-11"></a>PCI DSS požadavek 11

**Pravidelně zkoušet zabezpečení systémy a procesy**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Ohrožení zabezpečení jsou se zjistí průběžně jednotlivcům se zlými úmysly a výzkumných pracovníků a zavedena nový software. Součásti systému, procesy a vlastní software by měl být testována často zajistit, že ovládací prvky zabezpečení dále tak, aby odrážely změny prostředí.

## <a name="pci-dss-requirement-111"></a>PCI DSS požadavek 11.1

**11.1** implementace procesy testování přítomnost bezdrátových přístupových bodů (802.11) a zjišťování a identifikaci všech oprávnění a neoprávněným bezdrátové přístupové body čtvrtletně.

> [!NOTE]
> Metody, které mohou být použity v procesu zahrnovat ale nejsou kontroly omezená na bezdrátové sítě, fyzické nebo logické kontroly součásti systému a infrastruktury, řízení přístupu k síti (NAC) nebo bezdrátové ID nebo IP adresy.
Podle toho, která metod, musí být dostatek ke zjišťování a identifikaci ověřených i neověřených zařízení.


**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure nedovoluje ani povolit bezdrátové připojení v prostředí sítě Azure. Interní zabezpečení týmy pravidelně hledá podvodný bezdrátové signály čtvrtletně a podvodný signály jsou prozkoumat a odebrat. Zákazníci nemáte oprávnění k nasazení bezdrátové technologie v prostředí Azure. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Bezdrátové sítě a SNMP nejsou implementované v řešení.|



### <a name="pci-dss-requirement-1111"></a>PCI DSS požadavek 11.1.1

**11.1.1** udržujte inventář autorizovaný bezdrátové přístupové body, včetně zdokumentovaných obchodního oprávnění.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 11.1](#pci-dss-requirement-11-1). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Bezdrátové sítě a SNMP nejsou implementované v řešení.|



### <a name="pci-dss-requirement-1112"></a>PCI DSS požadavek 11.1.2

**11.1.2** implementace postupů reakcí na incidenty v případě zjištění neoprávněným bezdrátové přístupové body.


**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 11.1](#pci-dss-requirement-11-1). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Bezdrátové sítě a SNMP nejsou implementované v řešení.|



## <a name="pci-dss-requirement-112"></a>PCI DSS požadavek 11.2

**11.2** spustit interní a externí síť ohrožení zabezpečení kontroluje alespoň čtvrtletně a po všechny významné změny v síti (například nové instalace součásti systému, změny v síťové topologie, úpravy pravidlo brány firewall, produktu Upgrade). 

> [!NOTE]
> Více kontroly sestav lze spojovat pro proces čtvrtletně skenování k zobrazení, aby byly kontroloval všechny systémy a odstranily všechny použitelné ohrožení zabezpečení. Další dokumentaci může být nutné ověřit, zda probíhá adresovaném – opravené chyby zabezpečení.
> Pro počáteční PCI DSS dodržování předpisů, není potřeba dokončit čtyři čtvrtletí předat kontroly Pokud Hodnotitel ověřuje 1) byl poslední výsledek kontroly kontrolu předávání, 2) entita má popsané zásady a postupy nutnosti čtvrtletně kontrolu a 3) jak je znázorněno re-scan(s) bylo opraveno uvedeno ve výsledcích kontroly ohrožení zabezpečení. Pro další období po počáteční PCI DSS zkontrolovat, čtyři čtvrtletí předat kontroly musí mít došlo k chybě.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure provádí prověřování čtvrtletně interních a externích ohrožení zabezpečení. Kvalifikované pracovníky provádí kontroly. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore byla testována pera a ohrožení zabezpečení prohledávají ve snaze, jako je". Výsledky testů pera, se dají duplikovat pomocí běžných nástrojů, například nmap nebo pentest tools.com. Výsledky testu pera poskytne neprůkazné útok s žádné využitelných položky. Kromě toho [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations) poskytují informace o ohrožení zabezpečení a nápravu.|



### <a name="pci-dss-requirement-1121"></a>PCI DSS požadavek 11.2.1

**11.2.1** provádět kontroly čtvrtletně interní ohrožení zabezpečení. Řeší chyby zabezpečení a proveďte znovu kontroluje k ověření, že všechna ohrožení zabezpečení "vysoce rizikové" se vyřeší v souladu s ohrožení zabezpečení entity pořadí (podle požadavek 6.1). Je potřeba provést kontroly kvalifikované pracovníky.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure provádí kontroly pro chyby zabezpečení v podkladové infrastruktury ve oboru. Microsoft Azure implementuje ohrožení zabezpečení kontrolu serverové operační systémy, databáze a síťová zařízení s nástrojů pro vyhledávání odpovídající ohrožení zabezpečení. Azure webové aplikace jsou prohledávány s příslušnou odvětví kontrolu řešení. Čtvrtletně jsou prováděny prověřování ohrožení zabezpečení.<br /><br />Znovu prochází probíhají podle potřeby pro všechny systémy, dokud nebudou vyřešeny všechny "s vysokým rizikem" ohrožení zabezpečení (jak je určeno v požadavek 6.1). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore byla testována pera a ohrožení zabezpečení prohledávají ve snaze, jako je". Výsledky testů pera, se dají duplikovat pomocí běžných nástrojů, například nmap nebo pentest tools.com. Výsledky testu pera poskytne neprůkazné útok s žádné využitelných položky. Kromě toho [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations) poskytují informace o ohrožení zabezpečení a nápravu.|



### <a name="pci-dss-requirement-1122"></a>PCI DSS požadavek 11.2.2

**11.2.2** provádět kontroly čtvrtletně externí ohrožení zabezpečení, prostřednictvím schválení kontrolu dodavatele (ASV) schválila platební karty odvětví zabezpečení standardy Rada (PCI SSC). Proveďte znovu prochází podle potřeby, dokud se nedosáhne předávání kontroly. 

> [!NOTE]
> Prověřování čtvrtletně externí ohrožení zabezpečení, je nutné provést podle schválení kontrolu dodavatele (ASV), schválena platební karty odvětví zabezpečení standardy Radou (PCI SSC).
> Naleznete v Průvodci Program ASV publikovat na webu PCI Vědecký řídící výbor pro kontrolu odpovědnosti zákazníka, přípravy kontroly atd.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure provádí externí vyhledávání pro chyby zabezpečení v ve oboru podpůrné infrastruktuře, která je přístupná externě. Kontroly se provádějí pomocí schválení kontrolovat dodavatele (ASV).<br /><br />Microsoft Azure jako odběratel u střediska MSRC/OSSC měsíční oprava oznámení a hledá alespoň čtvrtletně ohrožení zabezpečení. Chyby zabezpečení identifikované vyhodnoceny a nápravy za zavedené časová osa založené na úrovni rizika.<br /><br />Chyba komplexní zabezpečení každé čtvrtletí cílové skenování seřazený podle priority komponenty prostředí Microsoft Azure se provádí k identifikaci ohrožení zabezpečení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Při nasazení Contoso Webstore, zákazníků ukázce jsou zodpovědní za provádění čtvrtletně externí ohrožení zabezpečení skenování a znovu prochází podle potřeby u všech instancí PaaS ve svém prostředí dat držitele karty (CDE) pomocí schválení kontrolu dodavatele (ASV) schválené standardy Radou platební karty oboru zabezpečení.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>PCI DSS požadavek 11.2.3

**11.2.3** provádět kontroly interních a externích a znovu prochází podle potřeby po veškeré významné změny. Je potřeba provést kontroly kvalifikované pracovníky.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Výsledky jsou uvedeny zúčastněným stranám a nápravy sledován tým zabezpečení Azure po uzavření. Výsledky testu Azure může sdílet s zákazníkům na základě smlouvy o. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za provádění kontrol čtvrtletně interních a externích ohrožení zabezpečení a znovu prochází podle potřeby u všech instancí PaaS v jejich CDE. Kontroly je třeba provést po významné změny v prostředí v oboru.<br /><br />Kontroly musí být provádí ASV nebo pracovníky organizační nezávisle.|



## <a name="pci-dss-requirement-113"></a>PCI DSS požadavek 11.3

**11.3** implementovat metodologie pro testování průnikům, která zahrnuje následující:
- Je založena na odvětví přijata průnikům testování přístupy (například NIST SP800-115)
- Zahrnuje pokrytí pro celý CDE hraniční a důležité systémy
- Zahrnuje testování z prostředí uvnitř i mimo síť
- Zahrnuje testování pro ověření všechny segmentace a ovládací prvky oboru snížení
- Definuje aplikační vrstvě průnikům testy mají být zahrnuty, minimálně, ohrožení zabezpečení uvedené v 6.5 požadavek
- Definuje síťové vrstvy průnikům testy mají být zahrnuty komponent, které podporují funkce sítě, jakož i operační systémy
- Zahrnuje zkontrolujte a zvážení hrozby a ohrožení zabezpečení došlo v posledních 12 měsíců
- Určuje uchování průnikům testování výsledky a výsledky aktivity nápravy

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ověří služby třetích stran průnikům testování podle OWASP (otevřít projekt webové aplikace zabezpečení) top deset pomocí certifikované NEJVYŠŠÍHO testerům, sada. Výsledky testování jsou sledovány prostřednictvím rejstříku rizika, která je auditovat a zkontrolovat v pravidelných intervalech zajistit dodržování předpisů pro postupy zabezpečení. <br /><br />Microsoft také používá seskupování síťových adaptérů Red vůči infrastruktury spravovaný společností Microsoft, služeb a aplikací. Žádná data koncového zákazníka je úmyslně určené během síťových adaptérů Red a testování průnikům živý web. Testy jsou proti infrastruktury Microsoft Azure a platformy a také společnosti Microsoft vlastní aplikace a data. Nikdy cílí zákazníka klientům, aplikacím a datům hostované v Azure.<br /><br />Používala nezávislé Poradce vytvořte plán hodnocení systému a proveďte assessment ovládací prvky se Microsoft Azure. Ovládací prvky jsou prováděná ročně a výsledky jsou uvedeny na dotčené strany. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore byla testována pera a ohrožení zabezpečení prohledávají ve snaze, jako je". Výsledky testů pera, se dají duplikovat pomocí běžných nástrojů, například nmap nebo pentest tools.com. Výsledky testu pera poskytne neprůkazné útok s žádné využitelných položky. Kromě toho [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations) poskytují informace o ohrožení zabezpečení a nápravu.|



### <a name="pci-dss-requirement-1131"></a>PCI DSS požadavek 11.3.1

**11.3.1** provést *externí* průnikům testování alespoň každý rok po žádné významné infrastruktury nebo upgradu aplikace nebo úpravy (například upgrade operačního systému, síť dílčí přidán do prostředí, nebo webový server přidaný do prostředí).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 11.3](#pci-dss-requirement-11-3). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore byla testována pera a ohrožení zabezpečení prohledávají ve snaze, jako je". Výsledky testů pera, se dají duplikovat pomocí běžných nástrojů, například nmap nebo pentest tools.com. Výsledky testu pera poskytne neprůkazné útok s žádné využitelných položky. Kromě toho [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations) poskytují informace o ohrožení zabezpečení a nápravu.|



### <a name="pci-dss-requirement-1132"></a>PCI DSS požadavek 11.3.2

**11.3.2** provést *interní* průnikům testování alespoň každý rok po žádné významné infrastruktury nebo upgradu aplikace nebo úpravy (například upgrade operačního systému, síť dílčí přidán do prostředí, nebo webový server přidaný do prostředí).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Kontrakty Microsoft Azure s nezávislí posuzovatelé provést průnikům testování hranice Microsoft Azure. Cvičení Red týmu se také pravidelně provádí a výsledky používat k vylepšení zabezpečení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore byla testována pera a ohrožení zabezpečení prohledávají ve snaze, jako je". Výsledky testů pera, se dají duplikovat pomocí běžných nástrojů, například nmap nebo pentest tools.com. Výsledky testu pera poskytne neprůkazné útok s žádné využitelných položky. Kromě toho [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations) poskytují informace o ohrožení zabezpečení a nápravu.|



### <a name="pci-dss-requirement-1133"></a>PCI DSS požadavek verze 11.3.3

**Verze 11.3.3** opraveny využitelných ohrožení zabezpečení nalezena během testování, průnikům a testování se opakuje ověření opravy.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Postupy k monitorování komponenty platformy Microsoft Azure pro chyby zabezpečení známé byly vytvořeny. <br /><br /><br /><br />Chyba komplexní zabezpečení každé čtvrtletí cílové skenování seřazený podle priority součástí Azure produkčním prostředí provádí k identifikaci ohrožení zabezpečení. Výsledky jsou uvedeny zúčastněným stranám a nápravy sledován tým po uzavření. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations), které poskytují informace o ohrožení zabezpečení a opravy byly použity k zajištění, že pro ukázkové společnosti Contoso Webstore CDE provedla nápravu všechny zbývající potíže.|



### <a name="pci-dss-requirement-1134"></a>PCI DSS požadavek 11.3.4

**11.3.4** Pokud segmentace slouží k izolaci CDE z jiné sítě, provedení testů průnikům nejméně jednou ročně a jakmile budou všechny změny segmentace ovládací prvky nebo metod ověřte, zda jsou metody segmentace provozu a efektivní, a všechny systémy se na rozsah ze systémů v CDE izolujte.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Postupy k monitorování komponenty platformy Microsoft Azure pro chyby zabezpečení známé byly vytvořeny. <br /><br /><br /><br />Chyba komplexní zabezpečení každé čtvrtletí cílové skenování seřazený podle priority součástí Azure produkčním prostředí provádí k identifikaci ohrožení zabezpečení. Výsledky jsou uvedeny zúčastněným stranám a nápravy sledován tým po uzavření. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations), které poskytují informace o ohrožení zabezpečení a opravy byly použity k zajištění, že pro ukázkové společnosti Contoso Webstore CDE provedla nápravu všechny zbývající potíže.|



### <a name="pci-dss-requirement-11341"></a>PCI DSS požadavek 11.3.4.1

**11.3.4.1** *další požadavek pro poskytovatele služeb pouze:* Pokud se používá segmentace, potvrďte PCI DSS obor provedením průnikům testování v segmentace řídí minimálně každých šest měsíců a po provedení všech změn pro ovládací prvky segmentace nebo metody.

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 11.3.4](#pci-dss-requirement-11-3-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations), které poskytují informace o ohrožení zabezpečení a opravy byly použity k zajištění, že pro ukázkové společnosti Contoso Webstore CDE provedla nápravu všechny zbývající potíže.|



## <a name="pci-dss-requirement-114"></a>PCI DSS požadavek 11.4

**11.4** využít techniky zjišťování neoprávněných vniknutí nebo prevence vniknutí rozpoznat a zabránit vniknutí do sítě. Monitorovat všechny provoz v hraniční síti prostředí držitele karty data i v kritických bodů v prostředí datového držitele karty, a zobrazit výstrahu osob, které možného ohrožení.
Aktuálnost zjišťování neoprávněných vniknutí a moduly prevence, směrné plány a podpisy.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure provádí analýzu v reálném čase události v rámci jeho provozní prostředí a systémy ID generování téměř v reálném čase Výstrahy o událostech, které může potenciálně ohrozit zabezpečení systému. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore je služba PaaS a zjišťování neoprávněných vniknutí sítě a prevenci označují odpovědnost Azure. [Azure Security Center](https://azure.microsoft.com/services/security-center/) a [Azure Advisor](/azure/advisor/advisor-security-recommendations) zadejte neoprávněných vniknutí výstrahy a nápravu.|



## <a name="pci-dss-requirement-115"></a>PCI DSS požadavek 11.5

**11.5** nasazení mechanismus detekce změn (například integrity souborů nástrojů pro monitorování) do výstrahy osob, které neoprávněné změny (včetně změny, přidání a odstranění) důležitých systémových souborů, konfiguračních souborů nebo obsah soubory; a nakonfigurujte software, který chcete provést kritická souboru porovnání alespoň jednou za týden. 

> [!NOTE]
> Pro účely detekce změn nepostradatelné soubory jsou obvykle neměňte pravidelně, ale změny, které by mohly být známkou ohrožení zabezpečení systému nebo riziko ohrožení zabezpečení. Detekce změn mechanismy, jako je monitorování produkty integrity souborů obvykle předkonfigurovaných s kritické soubory související operačního systému. Další důležité soubory, jako jsou ty pro vlastní aplikace, musí být vyhodnocena a definované entity (to znamená, že zprostředkovatel obchodní nebo služby).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure udržuje a upozorní zákazníky potenciální změny a události, které může mít vliv na zabezpečení nebo dostupnost služeb prostřednictvím online řídicí panel služby. Změny závazky týkajícími se zabezpečení a povinnosti zabezpečení Microsoft Azure zákazníků jsou na webu Microsoft Azure aktualizovány v časovém limitu.<br /><br />Instalace nebo změny softwaru v Microsoft Azure je omezený na produkční prostředí oprávnění správy pracovníky a způsobem změňte postupy správy. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Ukázkové společnosti Contoso Webstore je služba PaaS a detekce změn nebyla implementovaná pomocí analýzy protokolů. Další informace najdete v tématu [pokyny PCI - řešení pro správu předinstalovaný](payment-processing-blueprint.md#management-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>PCI DSS požadavek 11.5.1

**11.5.1** implementujte proces reagovat na všechny výstrahy generované řešení detekce změn.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure pravidla monitorování událostí zajistit vyšší úroveň sledování pro operace vysoce rizikové a prostředky. Pro dodržování standardů zavedených zabezpečení jsou monitorovány Azure spravovaných síťových zařízení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore výstrahy pro změny jsou poskytovány implementace analýzy protokolů. Další informace najdete v tématu [pokyny PCI - řešení pro správu předinstalovaný](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>PCI DSS požadavek 11.6

**11.6** zajistěte, aby zásady zabezpečení a provozní postupy pro zabezpečení monitorování a testování jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore výstrahy pro změny jsou poskytovány implementace analýzy protokolů. Další informace najdete v tématu [pokyny PCI - řešení pro správu předinstalovaný](payment-processing-blueprint.md#management-solutions).<br /><br /><br /><br />|




