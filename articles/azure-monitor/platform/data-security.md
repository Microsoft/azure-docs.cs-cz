---
title: Protokolovat analýzy dat zabezpečení | Dokumentace Microsoftu
description: Další informace o tom, jak Log Analytics chrání vaše osobní údaje a chrání vaše data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 0ac169060f7ba0e58aeb3e36e3af1629b6453fc1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667359"
---
# <a name="log-analytics-data-security"></a>Zabezpečení dat analýzy protokolů
Účelem tohoto dokumentu je poskytnout informace, které jsou specifické pro Log Analytics, což je funkce Azure Monitor, která doplní informace na [Centrum zabezpečení Azure](../../security/fundamentals/trust-center.md).  

Tento článek vysvětluje, jak Log Analytics shromažďuje, zpracovává a zabezpečuje data. Agenty můžete použít pro připojení k webové službě, pomocí System Center Operations Manager ke shromažďování provozních dat nebo načíst data z diagnostiky Azure pro použití službou Log Analytics. 

Služba Log Analytics spravuje vaše data založené na cloudu bezpečně pomocí následujících metod:

* oddělení dat
* Uchovávání dat
* Fyzické zabezpečení
* Správa incidentů
* Dodržování předpisů
* certifikace standardů zabezpečení

Kontaktujte nás s případnými dotazy, návrhy nebo problémy, které se týkají těchto informací, včetně našich zásad zabezpečení v [možnostech podpory Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Odesílání dat pomocí protokolu TLS 1.2 

– Pomáhat zajistit zabezpečení dat při přenosu do služby Log Analytics, důrazně doporučujeme, abyste ke konfiguraci agenta pro použití s alespoň zabezpečení TLS (Transport Layer) 1.2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje**se a odvětví se rychle přesouvá na zrušení podpory těchto starších protokolů. 

[Rada standardů zabezpečení PCI](https://www.pcisecuritystandards.org/) nastavila [konečný termín od 30. června 2018,](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) aby se zakázaly starší verze TLS/SSL a upgradoval na bezpečnější protokoly. Jakmile Azure sníží stále podporuje starší verze, pokud vaši agenti nemohou komunikovat přes alespoň TLS 1.2, nemohli odesílat data do Log Analytics. 

Nedoporučujeme explicitním nastavením agenta na používat jenom TLS 1.2, pokud není nezbytně nutné, protože může dojít k narušení zabezpečení na úrovni funkce platformy, které umožňují automaticky rozpoznat a využijte výhod, jakmile budou k dispozici, například novější bezpečnější protokoly jako TLS 1.3. 

### <a name="platform-specific-guidance"></a>Pokyny pro konkrétní platformu

|Platformu nebo jazyk | Podpora | Další informace |
| --- | --- | --- |
|Linux | Distribuce systému Linux se obvykle spoléhají na [OpenSSL](https://www.openssl.org) pro podporu TLS 1,2.  | Zkontrolujte [OpenSSL protokolu změn](https://www.openssl.org/news/changelog.html) a potvrďte, že je podporovaná vaše verze OpenSSL.|
| Windows 8.0 10 | Podporované a ve výchozím nastavení povolená. | Potvrďte, že stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Podporované a ve výchozím nastavení povolená. | Potvrzení, že stále používáte [výchozí nastavení](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporované, ale není ve výchozím nastavení povolená. | Podrobnosti o tom, jak povolit, najdete na stránce [nastavení registru TLS (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) .  |

## <a name="data-segregation"></a>oddělení dat
Po ingestuje data služby Log Analytics, se ukládají data logicky oddělená pro jednotlivé komponenty v rámci služby. Všechna data jsou označená za jednotlivé pracovní prostory. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby. Vaše data se ukládají v databázi vyhrazené v clusteru úložiště v oblasti, které jste vybrali.

## <a name="data-retention"></a>Uchovávání dat
Indexovaná data vyhledávání protokolu je uložen a zachována podle cenového plánu. Další informace najdete v tématu [Log Analytics ceny](https://azure.microsoft.com/pricing/details/log-analytics/).

V rámci smlouvy o [předplatném](https://azure.microsoft.com/support/legal/subscription-agreement/)Microsoft zachovává vaše data na základě podmínek smlouvy.  Při odebrání zákaznická data nejsou zničeny žádné fyzické disky.  

V následující tabulce jsou uvedeny některé dostupná řešení a poskytuje příklady typů shromážděná data.

| **Řešení** | **Datové typy** |
| --- | --- |
| Kapacitu a výkon |Údaje o výkonu a metadat |
| Správa aktualizací |Metadata a stav dat |
| Správa protokolů |Uživatelem definované protokoly událostí, protokoly událostí Windows a/nebo protokoly IIS |
| Sledování změn |Inventář softwaru, služby Windows a Linux démon metadat a soubor metadat Windows/Linux |
| SQL a Active Directory Assessment |Data rozhraní WMI, registru data, údaje o výkonu a dynamické správy SQL Server zobrazení výsledků |

V následující tabulce jsou uvedeny příklady typů dat:

| **Datový typ** | **Pole** |
| --- | --- |
| Výstraha |Upozornění název, popis výstrahy, BaseManagedEntityId, ID problému, IsMonitorAlert, RuleId, elementu ResolutionState, prioritu, závažnost, kategorie, vlastník, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved RepeatCount TimeResolutionStateLastModified TimeResolutionStateLastModifiedInDB, |
| Konfigurace |ID zákazníka, ID agenta, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Událost |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Poznámka:** Když zapisujete události s vlastními poli do protokolu událostí systému Windows, Log Analytics je shromáždí. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP adresa, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Adresa, NetbiosDomainName LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Výkon |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stát |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fyzické zabezpečení
Služba Log Analytics je spravovaná zaměstnanců společnosti Microsoft a všechny aktivity jsou zaznamenávány a dají auditovat. Log Analytics je provozována jako služba Azure a splňuje všechny požadavky na zabezpečení a dodržování předpisů Azure. Podrobnosti o fyzickém zabezpečení prostředků Azure najdete na stránce 18 [přehledu zabezpečení Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fyzické přístupová práva k zabezpečení oblasti se mění v rámci jednoho pracovního dne pro každého, kdo není k dispozici odpovědnost za službu Log Analytics, včetně převodu a ukončení. Můžete si přečíst o globální fyzické infrastruktuře, kterou používáme v datových [centrech Microsoftu](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Správa incidentů
Log Analytics má správy incidentů proces, který dodržovat všechny služby společnosti Microsoft. Souhrnně řečeno, jsme:

* Použít sdílenou odpovědnost modelu, kde část zabezpečení odpovědnosti patří společnosti Microsoft a části patří k zákazníkovi
* Správa incidentů zabezpečení Azure:
  * Spustit šetření při zjištění incidentu
  * Posuzujete dopad a závažnost incidentu podle členů týmu volání na reakce na incidenty. Hodnocení založené na důkaz, může nebo nemusí mít za následek další eskalaci bezpečnostní tým odpovědi.
  * Diagnostika incidentu zabezpečení odpovědi expertů technické a forenzní zkoumání chování, určení strategií pro zadržení, zmírnění a alternativní řešení. Pokud bezpečnostní tým domnívá, že zákaznická data mají vystavení jednotlivec nezákonně nebo neoprávněně, paralelní provádění procesu odběratele oznámení Incident začne paralelně.  
  * Stabilizujte a obnovit z incidentu. Tým reakce na incidenty vytvoří plán obnovení tak, jak tyto problémy zmírnit. Kroky krize členství ve skupině jako je například umístění do karantény ovlivněné systémy může dojít okamžitě a paralelně při diagnóze. Zmírnění rizik delší období může plánované ke kterým dochází po uplynutí okamžitě riziko.  
  * Uzavřít incident a chování následné. Tým reakce na incidenty vytvoří následné, které jsou popsány podrobnosti o incidentu s úmyslem, pokud chcete upravit zásady, postupy a procesy, aby se zabránilo opakování události.
* Upozornění zákazníků na bezpečnostní incidenty v oblasti:
  * Určení rozsahu ovlivněných zákazníků a každý, kdo to má vliv na jako podrobné oznámení o nejdříve
  * Vytvořte upozornění poskytnout zákazníkům s podrobné dostatek informací, takže můžete provést šetření na své straně a splňovat žádné závazky, které se provedly pro své koncové uživatele při zpoždění není neoprávněně proces oznámení.
  * Potvrďte a deklarovat incidentu, podle potřeby.
  * Informujte zákazníky se oznámení o incidentu neprodleně způsobit nepřiměřený a v souladu s jakékoli právních nebo smluvních závazků. Upozornění na incidenty zabezpečení jsou doručovány na jeden nebo více správců zákazníka prostřednictvím jakýmkoli způsobem, který vybere Microsoft, včetně e-mailem.
* Připravenost týmu chování a vzdělávání:
  * Zaměstnanci Microsoftu nutných k dokončení zabezpečení a povědomí školení, která pomáhá identifikovat a hlášení problémů podezřelý zabezpečení.  
  * Operátory práci na službě Microsoft Azure mají povinnosti školení přidání obaluje přístup do citlivých systémů, který je hostitelem dat zákazníků.
  * Zaměstnanci Microsoftu zabezpečení odpovědi přijímat specializovaná školení pro jejich role

Pokud dojde ke ztrátě o všech datech zákazníků, upozorníme za jediný den každého zákazníka. Ale zákazníka nedošlo ke ztrátě dat nikdy se službou. 

Další informace o tom, jak Microsoft reaguje na incidenty zabezpečení, najdete v tématu [Microsoft Azure odezva zabezpečení v cloudu](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Dodržování předpisů
Program Log Analytics software pro vývoj softwaru a zabezpečení informací a zásad správného řízení v týmu podporuje své obchodní požadavky a dodržuje zákony a předpisy, jak je popsáno v tématu [Microsoft Azure Security Center](https://azure.microsoft.com/support/trust-center/) a [Microsoft Trust Center](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Jak stanovuje požadavky na zabezpečení Log Analytics, identifikuje kontrolní mechanismy zabezpečení, spravuje a monitoruje rizika jsou také popsány zde. Ročně, jsme revize zásad, standardů, postupy a pokyny.

Každý člen týmu vývoje obdrží školení o zabezpečení formální aplikace. Interně používáme systém správy verzí pro vývoj softwaru. Každý projekt software je chráněn systém správy verzí.

Microsoft má tým zabezpečení a dodržování předpisů, který dohlíží a vyhodnocuje všechny služby v Microsoftu. Informace o zabezpečení vedoucí pracovníci pověření ochranou tvoří tým a nejsou spojené s technické týmy, které vyvíjí Log Analytics. Pracovníci zabezpečení mají své vlastní řetězec správy a chování nezávislé hodnocení produktů a služeb k zajištění zabezpečení a dodržování předpisů.

Zprávy o všech programy zabezpečení informací v Microsoftu se dostávat oznámení od Microsoftu správní rady.

Vývoj softwaru Log Analytics a týmu service jsou aktivně pracuje s týmy Microsoft Legal a dodržování předpisů a další oborových partnerů získat různé certifikáty.

## <a name="certifications-and-attestations"></a>Certifikací a atestací
Azure Log Analytics splňuje následující požadavky:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Standard zabezpečení dat (PCI DSS) v oboru platebních karet](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) Radou Security Standards (STANDARDem PCI).
* [Ovládací prvky organizace služeb (SOC) 1 typ 1 a SOC 2 typu 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) vyhovující
* [HIPAA a Hitech](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) pro společnosti, které mají smlouvu s organizací HIPAA pro obchodní přidružení
* Obecná kritéria Engineering Windows
* Microsoft Trustworthy Computing
* Součásti, které používá Log Analytics jako služba Azure, proto zavázala dodržovat požadavky na dodržování předpisů Azure. Další informace najdete na [webu dodržování předpisů v centru zabezpečení Microsoftu](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> V některých certifikačních autoritách/potvrzeních jsou Log Analytics uvedeny pod jejím dřívějším názvem *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Cloud computingu tok dat zabezpečení
Následující diagram znázorňuje architekturu zabezpečení cloudu jako daný tok informací ve vaší společnosti a jak je zabezpečený, jako je přesune do služby Log Analytics, takže v konečném důsledku zjištěných můžete na webu Azure Portal. Další informace o jednotlivých kroků následuje diagramu.

![Obrázek shromažďování dat Log Analytics a zabezpečení](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zaregistrujte se Log Analytics a Shromážděte data.
Pro vaši organizaci k odesílání dat do Log Analytics konfiguraci agenta pro Windows nebo Linux spuštěný na virtuálních počítačích Azure nebo na virtuálních nebo fyzických počítačích ve vašem prostředí nebo jiný poskytovatel cloudu.  Pokud používáte nástroj Operations Manager, ze skupiny pro správu můžete nakonfigurovat agenta nástroje Operations Manager. Uživateli (která by se vám, jiné jednotlivé uživatele nebo skupinu osob) vytvořte jeden nebo více pracovních prostorů Log Analytics a zaregistrujte agenty pomocí jedné z těchto účtů:

* [ID organizace](../../active-directory/fundamentals/sign-up-organization.md)
* [Účet Microsoft – Outlook, Office Live, MSN](https://account.microsoft.com/account)

Pracovní prostor Log Analytics je se shromažďují data, agregují, analyzují a zobrazí. Pracovní prostor se používá především jako prostředek k dělení dat a každý pracovní prostor je jedinečný. Například můžete chtít mít produkční data spravovat pomocí jednoho pracovního prostoru a zkušebních dat spravované s jiným pracovním prostorem. Pracovní prostory také pomoci správce řízení uživatelského přístupu k datům. Každý pracovní prostor může mít více uživatelských účtů, které s ním spojená, a každý uživatelský účet dostanete víc pracovních prostorů Log Analytics. Můžete vytvořit pracovní prostory podle oblast datového centra.

Skupiny pro správu nástroje Operations Manager pro nástroj Operations Manager, naváže připojení se službou Log Analytics. Potom nakonfigurujete, které systémech spravovaných agentem ve skupině pro správu jsou povoleny pro shromažďování a odesílání dat do služby. V závislosti na řešení, které jste povolili, data z těchto řešení jsou buď odesílá přímo ze serveru pro správu Operations Manageru ke službě Log Analytics nebo kvůli objemu data shromážděná agentem řízený systém, odesílají přímo z agenta ke službě. Pro systémy nemonitorováno nástrojem Operations Manager každý spojuje bezpečně ke službě Log Analytics přímo.

Veškerá komunikace mezi systémy připojené a služba Log Analytics je zašifrovaná. Protokol TLS (HTTPS) se používá pro šifrování.  Procesu Microsoft SDL je následovat, ujistěte se, že je aktuální pomocí nejnovější zálohy v kryptografické protokoly Log Analytics.

Každý typ agent shromažďuje data pro Log Analytics. Typ dat, která se shromažďují je závisí na typech řešení použít. Souhrn shromažďování dat můžete zobrazit v tématu [přidání Log Analytics řešení z galerie řešení](../../azure-monitor/insights/solutions.md). Kromě toho je k dispozici pro většinu řešení podrobnější informace o kolekci. Řešení je sada předdefinovaných zobrazení, vyhledávací dotazy protokolů, pravidla shromažďování dat a zpracování logiky. Pouze správci Log Analytics můžete použít k importu řešení. Po importu řešení se přesune na servery pro správu nástroje Operations Manager (Pokud se používá) a potom na všechny agenty, které jste zvolili. Následně agenty shromažďovat data.

## <a name="2-send-data-from-agents"></a>2. odeslání dat z agentů
Zaregistrujte všechny typy agenta s klíčem registrace a navázat zabezpečené připojení mezi agentem a službou Log Analytics pomocí ověřování pomocí certifikátů a šifrování protokolu SSL s portem 443. Log Analytics používá úložiště tajných kódů pro generování a správy klíčů. Soukromé klíče jsou otočeny každých 90 dní a jsou uloženy v Azure a jsou spravované pomocí operací Azure, kteří striktní postupy zákonné požadavky a dodržování předpisů.

S nástrojem Operations Manager vytvoří skupiny pro správu zaregistrované s pracovním prostorem Log Analytics zabezpečeného připojení HTTPS pomocí serveru pro správu Operations Manageru.

Pro agenty Windows nebo Linux spuštěný na Azure virtual machines se používá klíč k úložišti jen pro čtení ke čtení diagnostické události do tabulek Azure.  

Pomocí libovolného agenta generování sestav pro skupinu pro správu nástroje Operations Manager, který je integrovaný s Log Analytics Pokud nemůže komunikovat se serverem pro správu služby z jakéhokoli důvodu, shromážděná data ukládají se místně v dočasné mezipaměti na správu Server.   Se pokusí znovu poslat data každých osm minut, další dvě hodiny.  Pro data, která obchází serveru pro správu a je odeslána přímo ke službě Log Analytics chování je konzistentní s agentem Windows.  

Windows nebo data agenta do mezipaměti serveru správy je chráněný službou úložiště přihlašovacích údajů operačního systému. Pokud služba nemůže zpracovat data za dvě hodiny, fronty agentů data. Pokud se fronta zaplní, agenta spustí vyřadit datové typy, počínaje údaje o výkonu. Limit fronty agenta je klíč registru, můžete upravit, v případě potřeby. Shromážděná data je komprimován a odešle do služby, bez použití nástroje Operations Manager management seskupit databáze, tak nepřidá žádné zatížení k nim. Po odeslání shromážděných dat, odebere se z mezipaměti.

Jak je popsáno výše, data ze serveru pro správu nebo přímo připojených agentů se odesílají prostřednictvím protokolu SSL do Datacenter Microsoft Azure. Volitelně můžete použít ExpressRoute pro zvýšení zabezpečení pro data. ExpressRoute je způsob, jak k Azure připojit přímo ze stávající sítě WAN, jako například více protokol popisek přepínání sítě VPN (MPLS) poskytované poskytovatelem síťové služby. Další informace najdete v tématu [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. služba Log Analytics přijímá a zpracovává data.
Služba Log Analytics zajišťuje, že příchozí data z důvěryhodného zdroje pomocí ověřování certifikátů a integritu dat s ověřováním Azure. Nezpracované nezpracovaná data se pak ukládá v Centru událostí Azure v oblasti, které nakonec se uloží data v klidovém stavu. Typ dat, která je uložena závisí na typech řešení, které byly naimportovány a používá ke shromažďování dat. Log Analytics pak služby procesy nezpracovaných dat a ingestuje do databáze.

Doba uchování shromážděná data uložená v databázi, závisí na vybrané cenového plánu. Pro úroveň *Free* je shromážděná data dostupná po dobu sedmi dnů. Pro *placenou* úroveň jsou shromážděná data dostupná po dobu 31 dnů ve výchozím nastavení, ale dají se prodloužit na 730 dní. Data jsou uložena v klidovém stavu ve službě Azure storage k zajištění důvěrnosti data zašifrovaná a data se replikují v rámci místní oblasti pomocí místně redundantního úložiště (LRS). Poslední dva týdny dat jsou také uloženy v mezipaměti založené na SSD a Tato mezipaměť je zašifrovaná.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. pro přístup k datům použijte Log Analytics
Pro přístup k pracovním prostoru Log Analytics, přihlášení k webu Azure portal pomocí účtu organizace nebo účtu Microsoft, který jste dřív nastavili. Všechny přenosy mezi portálem a službou Log Analytics se odesílají prostřednictvím zabezpečeného kanálu protokolu HTTPS. Během používání portálu, se vygeneruje ID relace na straně klienta uživatelské (webový prohlížeč) a data se ukládají do místní mezipaměti, dokud nebude ukončena relace. Když byla ukončena, odstraní se mezipaměť. Soubory cookie na straně klienta, které neobsahují identifikovatelné osobní údaje, se automaticky neodeberou. Soubory cookie relací jsou označeny HTTPOnly, která jsou zabezpečená. Po předem určené období nečinnosti je Azure portal relace ukončena.

## <a name="next-steps"></a>Další kroky
* Naučte se shromažďovat data pomocí Log Analytics pro virtuální počítače Azure po [rychlém startu virtuálního počítače Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Pokud chcete shromažďovat data z fyzických nebo virtuálních počítačů se systémem Windows nebo Linux ve vašem prostředí, přečtěte si [rychlý Start pro počítače](../../azure-monitor/learn/quick-collect-linux-computer.md) se systémem Linux nebo [rychlý Start pro počítače se systémem Windows](../../azure-monitor/learn/quick-collect-windows-computer.md) .

