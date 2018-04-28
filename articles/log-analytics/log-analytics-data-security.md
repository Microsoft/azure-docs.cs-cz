---
title: Protokolu zabezpečení dat Analytics | Microsoft Docs
description: Další informace o tom, jak analýzy protokolů chrání vaše osobní údaje a zabezpečuje data.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magoedte
ms.openlocfilehash: f14b96b88a96f4bef24602bb9338a77352fbf375
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="log-analytics-data-security"></a>Protokolu zabezpečení analýzy dat
Tento dokument je určený k poskytování Azure Log Analytics konkrétní informace, které doplňují informace na [Centrum zabezpečení Azure](../security/security-microsoft-trust-center.md).  

Tento článek vysvětluje, jak data jsou shromažďována, zpracování a zabezpečené analýzy protokolů. Agenti můžete použít pro připojení k webové službě, použít System Center Operations Manager ke shromažďování provozních dat nebo načtení dat z Azure diagnostics pro použití analýzy protokolů. 

Službu analýzy protokolů bezpečně spravuje vaše data založená na cloudu pomocí následujících metod:

* Oddělení dat
* Uchovávání dat
* Fyzické zabezpečení
* Správa incidentů
* Dodržování předpisů
* certifikace standardy zabezpečení

Kontaktujte nás s dotazy, návrhy nebo problémy k libovolnému následující informace, včetně naše zásady zabezpečení v [možnosti Azure podpory](http://azure.microsoft.com/support/options/).

## <a name="data-segregation"></a>Oddělení dat
Po službou analýzy protokolů je konzumována vaše data, se ukládají data logicky samostatné na jednotlivé komponenty v celé službě. Všechna data se označí za pracovního prostoru. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby. Data se ukládají vyhrazené databázi v clusteru úložiště, oblast, kterou jste vybrali.

## <a name="data-retention"></a>Uchovávání dat
Indexovaná data vyhledávání protokolu je uložena a zachována podle cenový plán. Další informace najdete v tématu [Log Analytics ceny](https://azure.microsoft.com/pricing/details/log-analytics/).

Jako součást vaší [předplatné smlouvy](https://azure.microsoft.com/support/legal/subscription-agreement/), společnost Microsoft bude uchovávat data dle podmínek smlouvy.  Při odstranění dat jsme také odstranit účet úložiště Azure, kde se nachází data.  Při odebrání data zákazníků žádné fyzické disky zničena.  

Následující tabulka uvádí některé z dostupných řešení a obsahuje příklady typu shromážděná data.

| **Řešení** | **Datové typy** |
| --- | --- |
| Kapacitu a výkon |Údaje o výkonu a metadata |
| Posouzení malwaru |Konfigurační data a metadata |
| Update Management |Stav metadata a data |
| Správa protokolů |Uživatelem definované protokoly událostí, protokoly událostí systému Windows nebo protokoly služby IIS |
| Sledování změn |Inventář softwaru, služba systému Windows a Linux démon metadata a metadata souboru Windows nebo Linuxem |
| SQL a hodnocení služby Active Directory |Data rozhraní WMI, registru dat, údaje o výkonu a dynamické správy SQL Server zobrazení výsledků |

V následující tabulce jsou uvedeny příklady typů dat:

| **Datový typ** | **Pole** |
| --- | --- |
| Výstrahy |Výstrahy název, popis výstrahy, BaseManagedEntityId, ID problému, IsMonitorAlert, RuleId, ResolutionState, Priority, závažnosti, kategorie, vlastníka, ResolvedBy, TimeRaised, TimeAdded, změněno, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved RepeatCount TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, |
| Konfigurace |CustomerID, ID agenta, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Událost |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Poznámka:** při zápisu událostí s vlastními poli v protokolu událostí systému Windows shromažďuje OMS je. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP adresa, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adresa, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Výkon |ObjectName, název_čítače, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stav |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fyzické zabezpečení
Službu analýzy protokolů je spravovat zaměstnanců společnosti Microsoft a všechny aktivity se zaznamenávají a je možné je auditovat. Analýzy protokolů je provozována jako služba Azure a splňuje všechny požadavky na zabezpečení a dodržování předpisů Azure. Podrobnosti o fyzické zabezpečení prostředků Azure lze zobrazit na stránce 18 [Přehled zabezpečení Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Pro každý, kdo již nemá odpovědnost za OMS služby, včetně přenosu a ukončení jsou fyzické přístupová práva k zabezpečení oblasti změnit do jednoho pracovního dne. Další informace o globální fyzické infrastruktuře používáme v [Microsoft Datacenters](https://www.microsoft.com/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Správa incidentů
OMS má proces správy incidentů, které splňovat všechny služby společnosti Microsoft. To Shrneme, jsme:

* Použít sdílené odpovědnost model, kde část zabezpečení odpovědnost patří společnosti Microsoft a část patří k zákazníkovi
* Spravovat incidenty zabezpečení Azure:
  * Spustit šetření při zjištění incidentu
  * Hodnotit dopad a závažnost incident podle reakcí na incidenty na volání člen týmu. Na základě na důkaz, hodnocení může nebo nemusí mít za následek další eskalaci zabezpečení týmu odpovědi.
  * Diagnostika incident odborníky zabezpečení odpovědi provede technické nebo forenzního vyšetřování, identifikovat strategie omezení, omezení rizik a alternativní řešení. Pokud týmem zabezpečení dochází k závěru, že data zákazníků může stát vystavené až k jednotlivcům nezákonné nebo neoprávněným, paralelní provádění proces oznámení Incident zákazník zahájí paralelně.  
  * Stabilizovat a obnovit z incidentu. Reakce na incidenty týmu vytvoří plán obnovení pro zmírnění problém. Krizové členství ve skupině kroky jako je například umístění do karantény ovlivněné systémy může dojít, okamžitě a souběžně s diagnostiku. Způsoby zmírnění rizik delší období může plánované kterých se provádějí až po uplynutí okamžitou riziko.  
  * Uzavřít incident a provedení po porážce. Reakce na incidenty týmu vytvoří postmortální, který popisuje podrobnosti o incidentu, s úmyslem zkontrolovat, jestli zásady, postupy a procesy, aby se zabránilo opakování události.
* Upozorněte na bezpečnostní incidenty v oblasti:
  * Určení oboru dopad odběratelů a pro poskytování kdokoliv, kdo je ovlivněno jako podrobné oznámení o nejdříve
  * Vytvoření oznámení zajistit, že zákazníkům podrobné dostatek informací, takže můžete provést šetření na jejich end a splňovat žádné závazky, která byla pro své koncové uživatele při zpozdit přílišný proces oznámení.
  * Potvrďte a deklarovat incidentu, podle potřeby.
  * Upozorněte zákazníkům incidentu oznámení bez prodlení nepřiměřený a v souladu s žádné právní či smluvními závazků. Oznámení bezpečnostních incidentů se doručují na jeden nebo více správců zákazníka a to jakýmkoli způsobem, který vybere Microsoft, včetně e-mailem.
* Chování team připravenosti a školení:
  * Microsoft pracovníky jsou nutné k dokončení zabezpečení a školení sledování, která pomáhá identifikovat a hlášení problémů možného zabezpečení.  
  * Operátory práce ve službě Microsoft Azure mají povinnosti školení přidání kolem svůj přístup k citlivé systémy hostování zákaznická data.
  * Microsoft security odpovědi pracovníky přijímat specializované školení pro své role

Pokud dojde ke ztrátě všech datech zákazníků, upozorníme každého zákazníka a to během jednoho dne. Ale zákazníka došlo ke ztrátě dat. nikdy se službou. 

Další informace o tom, jak Microsoft odpoví na bezpečnostní incidenty v najdete v tématu [odpověď zabezpečení společnosti Microsoft Azure v cloudu](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Dodržování předpisů
Analýzy protokolů softwaru vývoj a služby týmu informace o zabezpečení a zásad správného řízení programu podporuje požadavky na jeho firmy a dodržuje zákonů a nařízení, jak je popsáno v [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) a [ Centrum zabezpečení Microsoft dodržování předpisů](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Jak vytváří požadavky na zabezpečení analýzy protokolů, identifikuje ovládací prvky zabezpečení, spravuje a monitoruje rizika jsou také popsány existuje. Ročně, jsme zkontrolujte zásady, standardy, postupy a pokyny.

Každý člen týmu vývoj obdrží formální aplikace bezpečnostního školení. Interně používáme systém správy verzí pro vývoj softwaru. Každý projekt softwaru je chráněn systém správy verzí.

Společnost Microsoft nemá tým zabezpečení a dodržování předpisů, který dohlíží a vyhodnocuje všechny služby ve službě Microsoft. Informace o zabezpečení osob tvoří tým a nejsou přidruženy engineering oddělení, které sama vyvinula analýzy protokolů. Zabezpečení osoby mít vlastní řetězec správy a provedení nezávislé posuzování produktů a služeb k zajištění zabezpečení a dodržování předpisů.

Společnosti Microsoft správní rady je oznámení roční zprávu o všechny programy pro zabezpečení informací společnosti Microsoft.

Tým vývoj a služby softwaru analýzy protokolů aktivně ve spolupráci s týmy Microsoft Legal a dodržování předpisů a dalšími partnery odvětví získat různé certifikáty.

## <a name="certifications-and-attestations"></a>Certifikace a atestace podle
Azure Log Analytics splňuje následující požadavky:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Platebních karet (PCI kompatibilní) Data zabezpečení Standardní (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) Radou standardy PCI zabezpečení.
* [Typ služby organizace ovládacích prvků (SOC) 1 1 a 1 typ SOC 2](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) kompatibilní
* [HIPAA a HITECH](https://www.microsoft.com/TrustCenter/Compliance/HIPAA) pro společnosti, které mají HIPAA obchodní přidružit smlouvu
* Společná kritéria technikům Windows
* Microsoft Trustworthy Computing
* Jako služba Azure součásti, které využívá analýzy protokolů splňovat požadavky na Azure dodržování předpisů. Další informace v [dodržování předpisů Center důvěřovat Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> V některých osvědčení služby nebo atestace podle, analýzy protokolů je uveden v části jeho starší název *Statistika provozu*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Cloud computing tok dat zabezpečení
Následující diagram znázorňuje Architektura zabezpečení cloud jako toku informací z vaší společnosti a jak jsou zabezpečená, jako je přesune do služby analýzy protokolů, nakonec kontaktu s vámi v portálu Azure nebo klasického portálu OMS. Další informace o jednotlivých kroků následuje diagramu.

![Obrázek shromažďování dat analýzy protokolů a zabezpečení](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zaregistrujte si analýzy protokolů a shromažďování dat
Pro vaši organizaci k odesílání dat k analýze protokolů dokončení konfigurace agenta systému Windows nebo Linux spuštěné na virtuálních počítačích Azure nebo na virtuální nebo fyzické počítače v prostředí nebo ostatní poskytovatele cloudových služeb.  Pokud používáte nástroj Operations Manager, ze skupiny pro správu můžete nakonfigurovat agenta nástroje Operations Manager. Uživatelé (které mohou být můžete, ostatní jednotlivé uživatele nebo skupinu uživatelů) vytvořit jeden nebo více pracovních prostorů analýzy protokolů a zaregistrujte agenty pomocí jedné z těchto účtů:

* [ID organizace](../active-directory/sign-up-organization.md)
* [Účet Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Pracovní prostor analýzy protokolů je, kde se data shromažďují, agregován, analyzovat a zobrazovat. Pracovní prostor slouží především jako prostředek k oddílu dat a každý pracovního prostoru není jedinečný. Například můžete chtít mít vaše produkční data spravovaná pomocí jednoho pracovního prostoru a testovací data spravovaná pomocí jiného pracovního prostoru. Pracovní prostory také pomoci správci řídit uživatel přístup k datům. Každém pracovním prostoru může mít víc uživatelských účtů s ním spojená, a všechny uživatelské účty, můžete přístup k více analýzy protokolů pracovních prostorů. Můžete vytvořit na základě datacenter oblasti pracovních prostorů. Každý pracovní prostor se replikují do jiných datových centrech v oblasti, především pro dostupnost služeb analýzy protokolů.

Skupina pro správu nástroje Operations Manager pro nástroj Operations Manager, naváže připojení se služba analýzy protokolů. Pak je třeba nakonfigurovat, které systémy spravováno agentem ve skupině pro správu jsou povoleny pro shromažďování a odesílání dat do služby. V závislosti na řešení, které jste povolili, jsou data z těchto řešení buď odeslaný přímo serveru pro správu nástroje Operations Manager ke službě Analýza protokolů nebo z důvodu objem shromážděných spravovaný agent systému, dat jsou odesílány přímo z agenta ke službě. Pro systémy není sledovaných nástrojem Operations Manager každý připojuje bezpečně ke službě Analýza protokolů přímo.

Veškerá komunikace mezi systémy připojené a službu analýzy protokolů je zašifrovaná.  Protokol TLS (HTTPS) se používá pro šifrování.  V rámci procesu Microsoft SDL je následovaný zajistit, že je aktuální pomocí nejnovější pokroky v kryptografické protokoly analýzy protokolů.

Každý typ agent shromažďuje data pro analýzu protokolu. Typ dat, která se shromažďují je závisí na typech řešení použít. Zobrazí souhrn shromažďování dat v [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md). Kromě toho je k dispozici pro většinu řešení podrobnější informace o kolekci. Řešení je sady předdefinovaných zobrazení, protokolu vyhledávací dotazy, pravidla shromažďování dat a zpracování logiky. Analýzy protokolů pro import řešení mohou používat pouze správci. Po importu řešení, se přesune do serverů pro správu nástroje Operations Manager (Pokud se používá) a pro všechny agenty, které jste vybrali. Potom agenty shromažďovat data.

## <a name="2-send-data-from-agents"></a>2. Odesílání dat z agentů
Zaregistrujte všechny typy agenta s klíčem registrace a je navázat zabezpečené připojení mezi agentem a analýzy protokolů služby pomocí ověřování pomocí certifikátů a protokol SSL s portem 443. Analýzy protokolů používá tajný úložiště pro vygenerování a udržovat klíče. Privátní klíče otáčejí každých 90 dní a jsou uložené v Azure a spravuje činnosti Azure, kteří podle striktní regulačních a dodržování předpisů.

S nástrojem Operations Manager vytvoří skupiny pro správu zaregistrována pracovní prostor analýzy protokolů zabezpečeného připojení HTTPS se serverem pro správu nástroje Operations Manager.

U systému Windows nebo Linux agentů spuštěných na virtuálních počítačích Azure se použije klíč k úložišti jen pro čtení ke čtení diagnostických událostí do tabulek Azure.  

S každého agenta generování sestav do skupiny pro správu nástroje Operations Manager, která je integrovaná s analýzy protokolů Pokud nemůže komunikovat se serverem pro správu služby z jakéhokoli důvodu shromážděná data ukládají se místně v dočasné mezipaměti na správu Server.   Se pokusí znovu odeslat data každých 8 minut po dobu dvou hodin.  Pro data, která obchází serveru pro správu a je odeslána přímo k Log Analytics chování je konzistentní s agentem Windows.  

Windows nebo data agenta do mezipaměti serveru správy je chráněn úložiště přihlašovacích údajů operačního systému. Pokud služba nemůže zpracovat data po dvou hodin, budou data fronty agentů. Pokud zaplnění fronty, agent začne, vyřazení datové typy, počínaje údaje o výkonu. Limit fronty agenta je klíč registru, můžete upravit, v případě potřeby. Shromážděná data komprimovaná a odešle do služby, obcházení databáze skupiny správy nástroje Operations Manager, nepřidá žádné zatížení na ně. Po odeslání shromážděná data, odebere se z mezipaměti.

Jak je popsáno výše, data ze serveru pro správu nebo přímo připojené agentů se odešlou přes SSL datová centra služby Microsoft Azure. Volitelně můžete ExpressRoute pro další zabezpečení pro data. ExpressRoute je způsob, jak připojovat přímo k Azure z existující sítě WAN, například více protokol popisku přepínání sítě VPN (MPLS) poskytované poskytovatelem síťové služby. Další informace najdete v tématu [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Službu analýzy protokolů přijímá a zpracovává data
Službu analýzy protokolů zajišťuje, že příchozích dat z důvěryhodného zdroje pomocí ověřování certifikátů a integrity dat pomocí ověřování Azure. Nezpracované nezpracovaná data se pak uloženy v Centru událostí Azure v oblasti, které se data uloží nakonec v klidovém stavu. Typ dat uložených závisí na typech řešení, které byly naimportovány a používá ke shromažďování dat. Poté analýzy protokolů služby procesy nezpracovaná data a ingestuje do databáze.

Doba uchování shromážděných dat uložených v databázi, závisí na vybrané cenový plán. Pro *volné* vrstvy, shromážděná data je k dispozici 7 dní. Pro *zaplacenou* vrstvy, shromážděná data 31 dní ve výchozím nastavení je k dispozici, ale můžete rozšířit na 720 dnů. Data se ukládají zašifrovaná přinejmenším v úložišti Azure, šifrovací. Poslední dva týdny dat jsou také uloženy v mezipaměti založená na SSD a tato mezipaměť není aktuálně šifrována.  Plánujeme podporu takové šifrování v novější polovině 2018.  

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Pomocí analýzy protokolů pro přístup k datům
Pro přístup k pracovní prostor analýzy protokolů se přihlásíte do portálu Azure pomocí účtu organizace nebo účtu Microsoft, kterou jste vytvořili dříve. Všechny přenosy mezi portálem a službou analýzy protokolů se odesílají přes zabezpečený kanál protokolu HTTPS. Při použití portálu, se vygeneruje ID relace na straně klienta uživatele (webový prohlížeč) a data jsou uložena v místní mezipaměti, dokud relace je ukončena. Pokud byla ukončena, mezipaměti je odstranit. Soubory cookie na straně klienta, které neobsahují identifikovatelné osobní údaje, se automaticky neodeberou. Soubory cookie relací jsou označeny HTTPOnly, která jsou zabezpečená. Po předem určené době nečinnosti Azure portálu relace je ukončena.

## <a name="next-steps"></a>Další postup
* Postup shromažďování dat pomocí analýzy protokolů pro vaše virtuální počítače Azure následující [virtuálního počítače Azure rychlý Start](log-analytics-quick-collect-azurevm.md).  

*  Pokud chcete shromažďovat data z fyzické nebo virtuální počítače Windows nebo Linux ve vašem prostředí, najdete v článku [rychlý start pro počítače se systémem Linux](log-analytics-quick-collect-linux-computer.md) nebo [počítače rychlý start pro Windows](log-analytics-quick-collect-windows-computer.md)

