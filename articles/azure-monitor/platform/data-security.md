---
title: Zabezpečení dat služby Log Analytics | Dokumenty společnosti Microsoft
description: Přečtěte si, jak Služba Log Analytics chrání vaše soukromí a zabezpečuje vaše data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 63d8d8d3701a9adca4bd01e6e061877f5d0bd245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333359"
---
# <a name="log-analytics-data-security"></a>Zabezpečení dat služby Log Analytics
Tento dokument je určen k poskytování informací specifických pro Log Analytics, což je funkce Azure Monitor, k doplnění informací o [Azure Trust Center](../../security/fundamentals/trust-center.md).  

Tento článek vysvětluje, jak Log Analytics shromažďuje, zpracovává a zabezpečuje data. Agenty můžete použít k připojení k webové službě, ke shromažďování provozních dat pomocí nástroje System Center Operations Manager nebo k načtení dat z diagnostiky Azure pro použití službou Log Analytics. 

Služba Log Analytics bezpečně spravuje vaše cloudová data pomocí následujících metod:

* Oddělení dat
* Uchovávání dat
* Fyzické zabezpečení
* Správa incidentů
* Dodržování předpisů
* Certifikace bezpečnostních standardů

Kontaktujte nás s případnými dotazy, návrhy nebo problémy týkajícími se některé z následujících informací, včetně našich zásad zabezpečení v [možnostech podpory Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Bezpečné odesílání dat pomocí TLS 1.2 

Chcete-li zajistit zabezpečení dat při přenosu do Log Analytics, důrazně doporučujeme nakonfigurovat agenta tak, aby používal alespoň zabezpečení transportní vrstvy (TLS) 1.2. Starší verze vrstvy SSL (TLS/Secure Sockets Layer) byly shledány zranitelnými a zatímco v současné době stále pracují na povolení zpětné kompatibility, **nejsou doporučeny**a odvětví se rychle přesouvá k opuštění podpory pro tyto starší protokoly. 

[Rada pro bezpečnostní standardy PCI](https://www.pcisecuritystandards.org/) stanovila [lhůtu 30.](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) Jakmile Azure klesne starší podporu, pokud vaši agenti nemohou komunikovat přes alespoň TLS 1.2, nebudete moct odesílat data do Log Analytics. 

Nedoporučujeme explicitně nastavit agenta tak, aby používal pouze protokol TLS 1.2, pokud to není nezbytně nutné, protože může přerušit funkce zabezpečení na úrovni platformy, které vám umožní automaticky detekovat a využívat novější bezpečnější protokoly, jakmile budou k dispozici, například jako TLS 1.3. 

### <a name="platform-specific-guidance"></a>Pokyny pro konkrétní platformu

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
|Linux | Linuxové distribuce mají tendenci spoléhat se na [OpenSSL](https://www.openssl.org) pro podporu TLS 1.2.  | Zkontrolujte [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) a potvrďte, že je vaše verze OpenSSL podporována.|
| Windows 8.0 - 10 | Podporováno a ve výchozím nastavení povoleno. | Chcete-li ověřit, zda stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Podporováno a ve výchozím nastavení povoleno. | Ověření, zda stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Aktualizace Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporováno, ale ve výchozím nastavení není povoleno. | Podrobnosti o povolení najdete na stránce [nastavení registru TLS (Transport Layer Security) (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |

## <a name="data-segregation"></a>Oddělení dat
Po požití dat službou Log Analytics jsou data logicky oddělena pro každou komponentu v rámci služby. Všechna data jsou označena podle pracovního prostoru. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby. Data jsou uložena ve vyhrazené databázi v clusteru úložiště v oblasti, kterou jste vybrali.

## <a name="data-retention"></a>Uchovávání dat
Data vyhledávání v indexovaném protokolu jsou uložena a uchovávána podle vašeho cenového plánu. Další informace naleznete v [tématu Log Analytics Pricing](https://azure.microsoft.com/pricing/details/log-analytics/).

V rámci vaší [smlouvy o předplatném](https://azure.microsoft.com/support/legal/subscription-agreement/)bude společnost Microsoft uchovávat vaše údaje podle podmínek smlouvy.  Po odebrání dat zákazníka nebudou zničeny žádné fyzické jednotky.  

V následující tabulce jsou uvedena některá dostupná řešení a příklady typu shromažďovacích dat.

| **Řešení** | **Typy dat** |
| --- | --- |
| Kapacita a výkon |Údaje o výkonu a metadata |
| Update Management |Metadata a data stavu |
| Správa protokolů |Uživatelem definované protokoly událostí, protokoly událostí systému Windows nebo protokoly služby IIS |
| Sledování změn |Inventář softwaru, služba Windows a metadata daemonu Linuxu a metadata souborů Windows/Linux |
| Vyhodnocení SQL a služby Active Directory |Výsledky zobrazení dat služby WMI, dat registru, výkonu a zobrazení dynamické správy serveru SQL Server |

V následující tabulce jsou uvedeny příklady datových typů:

| **Datový typ** | **Pole** |
| --- | --- |
| Výstrahy |Název výstrahy, Popis výstrahy, BaseManagedEntityId, ID problému, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExcept, LastModifiedExceptCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfigurace |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Událost |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Poznámka:** Při zápisu událostí s vlastními poli do protokolu událostí systému Windows je služba Log Analytics shromažďuje. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adresa, NetbiosDomainName, Logické procesory, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Výkon |Název_objektu,_název_čítače, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stav |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fyzické zabezpečení
Služba Log Analytics je spravována pracovníky společnosti Microsoft a všechny aktivity jsou protokolovány a lze je auditovat. Log Analytics se provozuje jako služba Azure a splňuje všechny požadavky na dodržování předpisů a zabezpečení Azure. Podrobnosti o fyzickém zabezpečení prostředků Azure si můžete zobrazit na stránce 18 [přehledu zabezpečení Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fyzická přístupová práva do zabezpečených oblastí se změní během jednoho pracovního dne pro každého, kdo již nemá odpovědnost za službu Log Analytics, včetně přenosu a ukončení. O globální fyzické infrastruktuře, kterou používáme v [datových centrech společnosti Microsoft](https://azure.microsoft.com/global-infrastructure/), si můžete přečíst.

## <a name="incident-management"></a>Správa incidentů
Log Analytics má proces správy incidentů, který dodržují všechny služby společnosti Microsoft. Abychom to shrnuli, jsme:

* Používejte model sdílené odpovědnosti, pokud část odpovědnosti za zabezpečení patří společnosti Microsoft a část patří zákazníkovi.
* Správa incidentů zabezpečení Azure:
  * Zahájit vyšetřování po zjištění incidentu
  * Vyhodnoťte dopad a závažnost incidentu členem týmu reakce na incidenty na zavolání. Na základě důkazů může, ale nemusí mít za následek další eskalaci týmu pro reakci na zabezpečení.
  * Diagnostikujte incident odborníky na reakci na zabezpečení, abyste provedli technické nebo forenzní vyšetřování, identifikovali strategie zadržování, zmírnění a řešení. Pokud se tým zabezpečení domnívá, že zákaznická data mohla být vystavena nezákonné nebo neoprávněné osobě, souběžné spuštění procesu oznámení o incidentu zákazníka začíná souběžně.  
  * Stabilizovat a zotavit se z incidentu. Tým pro reakci na incident vytvoří plán obnovení ke zmírnění problému. Krizové kontejnmentové kroky, jako je umístění do karantény ovlivněné systémy, mohou nastat okamžitě a souběžně s diagnózou. Mohou být plánována dlouhodobější zmírnění rizika, ke kterým dojde po uplynutí bezprostředního rizika.  
  * Uzavřete incident a proveďte pitvu. Tým pro reakci na incident vytvoří postmortem, který popisuje podrobnosti incidentu, s úmyslem revidovat zásady, postupy a procesy, aby se zabránilo opakování události.
* Upozorněte zákazníky na bezpečnostní incidenty:
  * Určit rozsah ovlivněných zákazníků a poskytnout každému, kdo je ovlivněn, co nejpodrobnější oznámení
  * Vytvořte oznámení, které zákazníkům poskytne dostatek podrobných informací, aby mohli provést šetření na jejich konci a splnit veškeré závazky, které učinili vůči svým koncovým uživatelům, aniž by nepřiměřeně zpozdili proces oznámení.
  * Potvrďte a oznamte incident podle potřeby.
  * Informujte zákazníky oznámením o incidentu bez zbytečného odkladu a v souladu s jakýmkoli právním nebo smluvním závazkem. Oznámení o bezpečnostních incidentech jsou doručována jednomu nebo více správcům zákazníka jakýmkoli způsobem, který společnost Microsoft vybere, a to i prostřednictvím e-mailu.
* Proveďte připravenost týmu a školení:
  * Pracovníci společnosti Microsoft musí dokončit školení o zabezpečení a zvyšování povědomí, což jim pomáhá identifikovat a hlásit podezřelé problémy se zabezpečením.  
  * Operátoři pracující na službě Microsoft Azure mají další povinnosti školení týkající se jejich přístupu k citlivým systémům hostujícím zákaznická data.
  * Pracovníci společnosti Microsoft pro reakci na zabezpečení absolvují specializovaná školení pro své role

Dojde-li ke ztrátě zákaznických dat, upozorníme každého zákazníka do jednoho dne. Ke ztrátě dat zákazníků však u služby nikdy nedošlo. 

Další informace o tom, jak Microsoft reaguje na bezpečnostní incidenty, najdete [v tématu Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Dodržování předpisů
Program pro vývoj softwaru Log Analytics a program pro zabezpečení informací a zásadsprávné řízení týmu služby podporuje jeho obchodní požadavky a dodržuje zákony a předpisy, jak je popsáno v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/) a dodržování předpisů centra zabezpečení microsoft [trustů](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Jak Log Analytics stanoví požadavky na zabezpečení, identifikuje ovládací prvky zabezpečení, spravuje a monitoruje rizika jsou také popsány zde. Každoročně kontrolujeme zásady, standardy, postupy a pokyny.

Každý člen vývojového týmu obdrží formální školení zabezpečení aplikace. Interně používáme systém správy verzí pro vývoj softwaru. Každý softwarový projekt je chráněn systémem správy verzí.

Společnost Microsoft má tým pro zabezpečení a dodržování předpisů, který dohlíží a vyhodnocuje všechny služby v Microsoftu. Pracovníci zabezpečení informací tvoří tým a nejsou spojeny s inženýrskými týmy, které vyvíjí Log Analytics. Bezpečnostní pracovníci mají svůj vlastní řídící řetězec a provádějí nezávislé hodnocení produktů a služeb, aby zajistili bezpečnost a dodržování předpisů.

Představenstvo společnosti Microsoft je oznámeno ve výroční zprávě o všech programech zabezpečení informací společnosti Microsoft.

Tým pro vývoj softwaru log Analytics a servisní tým aktivně spolupracuje s týmy Microsoft Legal and Compliance a dalšími průmyslovými partnery na získání různých certifikací.

## <a name="certifications-and-attestations"></a>Certifikace a osvědčení
Azure Log Analytics splňuje následující požadavky:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Standard zabezpečení dat (PCI DSS) (PCI DSS) (PCI Compliant) (PCI Industry) od](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) Rady pro bezpečnostní standardy PCI.
* [Ovládací prvky organizace služeb (SOC) 1 typ 1 a SOC 2 typ 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) kompatibilní
* [HIPAA a HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) pro společnosti, které mají smlouvu hipaa business associate
* Společná technická kritéria systému Windows
* Microsoft Trusted Computing
* Jako služba Azure součásti, které Log Analytics používá, splňují požadavky na dodržování předpisů Azure. Další informace naleznete v centru [zabezpečení společnosti Microsoft Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> V některých certifikacích/atetacích je Služba Log Analytics uvedena pod svým dřívějším názvem *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Tok dat o zabezpečení cloud computingu
Následující diagram znázorňuje architekturu zabezpečení cloudu jako tok informací z vaší společnosti a jak je zabezpečený, jak se přesune do služby Log Analytics, nakonec vidět na portálu Azure. Další informace o jednotlivých krocích následují podle diagramu.

![Obrázek shromažďování dat a zabezpečení služby Log Analytics](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zaregistrujte se do služby Log Analytics a shromažďujte data
Aby vaše organizace odesílala data do Log Analytics, nakonfigurujete agenta Windows nebo Linuxu spuštěného na virtuálních počítačích Azure nebo na virtuálních nebo fyzických počítačích ve vašem prostředí nebo jiném poskytovateli cloudu.  Pokud používáte Nástroj Operations Manager, nakonfigurujete ze skupiny pro správu agenta nástroje Operations Manager. Uživatelé (což můžete být vy, jiní jednotliví uživatelé nebo skupina lidí) vytvářejí jeden nebo více pracovních prostorů Analýzy protokolů a registrují agenty pomocí jednoho z následujících účtů:

* [ID organizace](../../active-directory/fundamentals/sign-up-organization.md)
* [Účet Microsoft – Outlook, Office Live, MSN](https://account.microsoft.com/account)

Pracovní prostor Analýzy protokolů je místo, kde jsou data shromažďována, agregována, analyzována a prezentována. Pracovní prostor se používá především jako prostředek k rozdělení dat a každý pracovní prostor je jedinečný. Můžete například chtít, aby byla produkční data spravována pomocí jednoho pracovního prostoru a testovací data spravována s jiným pracovním prostorem. Pracovní prostory také pomáhají správci řídit přístup uživatelů k datům. Ke každému pracovnímu prostoru může být přidruženo více uživatelských účtů a každý uživatelský účet může přistupovat k více pracovním prostorům Analýzy protokolů. Pracovní prostory se vytvářejí na základě oblasti datového centra.

Pro Operations Manager vytvoří skupina pro správu operations manageru připojení ke službě Log Analytics. Potom nakonfigurujete, které systémy spravované agenty ve skupině pro správu mohou shromažďovat a odesílat data do služby. V závislosti na povoleném řešení jsou data z těchto řešení odesílána přímo ze serveru pro správu nástroje Operations Manager do služby Log Analytics nebo z důvodu objemu dat shromážděných systémem spravovaným agentem. zástupce služby. U systémů, které nejsou sledovány nástrojoperations managerem, se každý z nich bezpečně připojuje přímo ke službě Log Analytics.

Veškerá komunikace mezi připojenými systémy a službou Log Analytics je šifrována. Protokol TLS (HTTPS) se používá pro šifrování.  Proces Microsoft SDL je sledován, aby bylo zajištěno, že služba Log Analytics je aktuální s nejnovějšími pokroky v kryptografických protokolech.

Každý typ agenta shromažďuje data pro analýzu protokolů. Typ shromažďovaných dat závisí na typech použitých řešení. Souhrn shromažďování dat si můžete prohlédnout na [adrese Add Log Analytics řešení z Galerie řešení](../../azure-monitor/insights/solutions.md). Další podrobnější informace o kolekci jsou k dispozici pro většinu řešení. Řešení je sada předdefinovaných zobrazení, dotazů pro vyhledávání protokolů, pravidel shromažďování dat a logiky zpracování. K importu řešení mohou používat pouze správci. Po importu je řešení přesunuto na servery pro správu nástroje Operations Manager (pokud je použito) a potom na všechny agenty, které jste zvolili. Poté agenti shromažďují data.

## <a name="2-send-data-from-agents"></a>2. Odesílání dat od agentů
Zaregistrujete všechny typy agentů pomocí klíče pro zápis a je navázáno zabezpečené připojení mezi agentem a službou Log Analytics pomocí ověřování na základě certifikátů a TLS s portem 443. Log Analytics používá úložiště tajných klíčů ke generování a údržbě klíčů. Privátní klíče se střídají každých 90 dní a jsou uložené v Azure a jsou spravované operacemi Azure, které dodržují přísné předpisy a postupy dodržování předpisů.

S Operations Manager, skupina pro správu registrovaná v pracovním prostoru Log Analytics vytvoří zabezpečené připojení HTTPS se serverem pro správu Operations Manager.

Pro agenty Windows nebo Linux u virtuálních počítačů Azure se klíč úložiště jen pro čtení používá ke čtení diagnostických událostí v tabulkách Azure.  

S jakýmkoli agentem, který se hlásí skupině správy nástroje Operations Manager, která je integrována s protokolovou analýzou, pokud server pro správu není z jakéhokoli důvodu schopen komunikovat se službou, shromážděná data jsou uložena místně v dočasné mezipaměti ve správě Server.   Snaží se znovu odeslat data každých osm minut po dobu dvou hodin.  U dat, která obchází server pro správu a jsou odesílána přímo do analýzy protokolů, je chování konzistentní s agentem systému Windows.  

Data uložená v mezipaměti systému Windows nebo agenta serveru pro správu jsou chráněna úložištěm pověření operačního systému. Pokud služba nemůže zpracovat data po dvou hodinách, agenti budou data zařadit do fronty. Pokud se fronta zaplní, agent začne přepouštět datové typy, počínaje daty o výkonu. Limit fronty agenta je klíč registru, takže jej můžete v případě potřeby upravit. Shromážděná data jsou komprimována a odeslána do služby, čímž se obchází databáze skupiny správy nástroje Operations Manager, takže do nich nepřidají žádné zatížení. Po odeslání shromážděná data je odebrána z mezipaměti.

Jak je popsáno výše, data ze serveru pro správu nebo agentů s přímým připojením se odesílají přes TLS do datových center Microsoft Azure. Volitelně můžete použít ExpressRoute poskytnout další zabezpečení pro data. ExpressRoute je způsob, jak se přímo připojit k Azure z vaší stávající sítě WAN, jako je například síť VPN pro přepínání popisků (MPLS) s více protokoly, kterou poskytuje poskytovatel síťových služeb. Další informace naleznete v tématu [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Služba Log Analytics přijímá a zpracovává data
Služba Log Analytics zajišťuje, že příchozí data pocházejí z důvěryhodného zdroje ověřením certifikátů a integrity dat pomocí ověřování Azure. Nezpracovaná nezpracovaná data se pak uloží do centra událostí Azure v oblasti, data se nakonec uloží do klidového stavu. Typ dat, která je uložena, závisí na typech řešení, která byla importována a použita ke shromažďování dat. Potom služba Log Analytics zpracuje nezpracovaná data a ingestuje do databáze.

Doba uchovávání shromážděných dat uložených v databázi závisí na zvoleném cenovém plánu. Pro úroveň *Free* jsou shromážděná data k dispozici po dobu sedmi dnů. U *placené* úrovně jsou shromážděná data ve výchozím nastavení k dispozici po dobu 31 dnů, ale lze je prodloužit na 730 dní. Data se ukládají šifrované v klidovém stavu v úložišti Azure, aby byla zajištěna důvěrnost dat a data se replikují v rámci místní oblasti pomocí místně redundantního úložiště (LRS). Poslední dva týdny dat jsou také uloženy v mezipaměti založené na SSD a tato mezipaměť je šifrována.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Pomocí log analytics pro přístup k datům
Chcete-li získat přístup k pracovnímu prostoru Log Analytics, přihlaste se k portálu Azure pomocí účtu organizace nebo účtu Microsoft, který jste nastavili dříve. Veškerý provoz mezi portálem a službou Log Analytics je odesílán prostředkem zabezpečeného kanálu HTTPS. Při použití portálu je v uživatelském klientovi (webovém prohlížeči) generováno ID relace a data jsou uložena v místní mezipaměti, dokud není relace ukončena. Po ukončení je mezipaměť odstraněna. Soubory cookie na straně klienta, které neobsahují osobně identifikovatelné informace, nejsou automaticky odstraněny. Relační soubory cookie jsou označeny jako HTTPOnly a jsou zabezpečeny. Po předem určené dobu nečinnosti je ukončena relace portálu Azure.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak shromažďovat data pomocí Log Analytics pro vaše virtuální počítače Azure po [rychlém startu virtuálního počítače Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Pokud chcete shromažďovat data z fyzických nebo virtuálních počítačů se systémem Windows nebo Linux ve vašem prostředí, přečtěte [si úvodní příručku pro počítače s Linuxem](../../azure-monitor/learn/quick-collect-linux-computer.md) nebo [Úvodní příručku pro počítače se systémem Windows.](../../azure-monitor/learn/quick-collect-windows-computer.md)

