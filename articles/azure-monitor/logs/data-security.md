---
title: Zabezpečení dat Log Analytics | Microsoft Docs
description: Přečtěte si, jak Log Analytics chrání vaše soukromí a zabezpečuje vaše data.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: f8fa0344a6959025856f002e1de4eb8700c32da5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311307"
---
# <a name="log-analytics-data-security"></a>Zabezpečení dat Log Analytics
Účelem tohoto dokumentu je poskytnout informace, které jsou specifické pro Log Analytics, což je funkce Azure Monitor, která doplní informace na [Centrum zabezpečení Azure](https://www.microsoft.com/en-us/trust-center?rtc=1).  

Tento článek vysvětluje, jak Log Analytics shromažďuje, zpracovává a zabezpečuje data. Pomocí agentů se můžete připojit k webové službě, pomocí System Center Operations Manager shromažďovat provozní data nebo načítat data z diagnostiky Azure pro použití Log Analytics. 

Služba Log Analytics spravuje vaše cloudová data bezpečně pomocí následujících metod:

* Oddělení dat
* Uchovávání dat
* Fyzické zabezpečení
* Správa incidentů
* Dodržování předpisů
* Certifikace standardů zabezpečení

Můžete také použít další funkce zabezpečení integrované do Azure Monitor a Log Analytics. Tyto funkce vyžadují větší správu správců. 
* Klíče spravované zákazníkem (zabezpečení)
* Privátní úložiště Azure
* Síť s Private Linkem 
* Omezení přístupu podpory Azure nastavená pomocí bezpečnostního modulu Azure

Kontaktujte nás s případnými dotazy, návrhy nebo problémy, které se týkají těchto informací, včetně našich zásad zabezpečení v [možnostech podpory Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Bezpečné posílání dat pomocí TLS 1,2 

Aby se zajistilo zabezpečení dat při přenosu do Log Analytics, důrazně doporučujeme nakonfigurovat agenta tak, aby používal minimálně protokol TLS (Transport Layer Security) 1,2. Zjistili jsme, že starší verze TLS/SSL (Secure Sockets Layer) (SSL) jsou zranitelné a i když stále fungují k tomu, aby se zajistila zpětná kompatibilita, **nedoporučuje** se a odvětví se rychle přesouvá na zrušení podpory těchto starších protokolů. 

[Rada standardů zabezpečení PCI](https://www.pcisecuritystandards.org/) nastavila [konečný termín od 30. června 2018,](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) aby se zakázaly starší verze TLS/SSL a upgradoval na bezpečnější protokoly. Když Azure uvolní podporu starší verze, pokud agenti nemůžou komunikovat přes aspoň TLS 1,2, nebudete moct odesílat data Log Analytics. 

Nedoporučujeme explicitně nastavit vašeho agenta tak, aby používal jenom TLS 1,2, pokud to není nezbytně nutné, protože může přerušit funkce zabezpečení na úrovni platformy, které vám umožní automaticky zjišťovat a využívat novější bezpečnější protokoly, jako je třeba TLS 1,3. 

### <a name="platform-specific-guidance"></a>Doprovodné materiály pro konkrétní platformu

|Platforma/jazyk | Podpora | Další informace |
| --- | --- | --- |
|Linux | Distribuce systému Linux se obvykle spoléhají na [OpenSSL](https://www.openssl.org) pro podporu TLS 1,2.  | Zkontrolujte [OpenSSL protokolu změn](https://www.openssl.org/news/changelog.html) a potvrďte, že je podporovaná vaše verze OpenSSL.|
| Windows 8,0 – 10 | Podporované a povolené ve výchozím nastavení. | Potvrďte, že stále používáte [výchozí nastavení](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Podporované a povolené ve výchozím nastavení. | Potvrzení, že stále používáte [výchozí nastavení](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 a Windows Server 2008 R2 SP1 | Podporované, ale nejsou ve výchozím nastavení povolené. | Podrobnosti o tom, jak povolit, najdete na stránce [nastavení registru TLS (Transport Layer Security)](/windows-server/security/tls/tls-registry-settings) .  |

## <a name="data-segregation"></a>Oddělení dat
Jakmile služba Log Analytics ingestuje vaše data, budou se data v každé součásti v rámci služby uchovávat logicky odděleně. Všechna data jsou označena pro jednotlivé pracovní prostory. Toto značení přetrvává v průběhu celého životního cyklu dat a je vyžadováno na každé úrovni služby. Vaše data se ukládají do vyhrazené databáze v clusteru úložiště v oblasti, kterou jste vybrali.

## <a name="data-retention"></a>Uchovávání dat
Data prohledávání indexovaných protokolů se ukládají a uchovávají podle vašeho cenového plánu. Další informace najdete v tématu [Log Analytics ceny](https://azure.microsoft.com/pricing/details/log-analytics/).

V rámci smlouvy o [předplatném](https://azure.microsoft.com/support/legal/subscription-agreement/)Microsoft zachovává vaše data na základě podmínek smlouvy.  Při odebrání zákaznických dat se nezničí žádné fyzické jednotky.  

Následující tabulka uvádí některá z dostupných řešení a obsahuje příklady typu dat, která shromažďuje.

| **Řešení** | **Datové typy** |
| --- | --- |
| Capacity and Performance |Data a metadata výkonu |
| Update Management |Metadata a data stavu |
| Správa protokolů |Uživatelsky definované protokoly událostí, protokoly událostí systému Windows a/nebo protokoly služby IIS |
| Sledování změn |Soubory inventáře softwaru, Windows Service a Linux démon a metadata souborů Windows/Linux |
| SQL a Active Directory Assessment |Data služby WMI, data registru, data o výkonu a SQL Server dynamické správy zobrazení výsledků |

V následující tabulce jsou uvedeny příklady datových typů:

| **Datový typ** | **Pole** |
| --- | --- |
| Výstrahy |Název výstrahy, popis výstrahy, BaseManagedEntityId, ID problému, IsMonitorAlert, RuleId, elementu resolutionstate, priorita, závažnost, kategorie, vlastník, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfigurace |CustomerID, ID agenta, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Událost |ID události, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, vydavatel, FullNumber, číslo, kategorie, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Poznámka:** Když zapisujete události s vlastními poli do protokolu událostí systému Windows, Log Analytics je shromáždí. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, název sítě, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP adresa, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, Principaler, OffsetInMinuteFromGreenwichTime |
| Výkon |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stav |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, prostav, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fyzické zabezpečení
Služba Log Analytics je spravovaná pracovníky Microsoftu a všechny aktivity se zaznamenávají do protokolu a dají se auditovat. Log Analytics se provozuje jako služba Azure a splňuje všechny požadavky na dodržování předpisů a zabezpečení Azure. Podrobnosti o fyzickém zabezpečení prostředků Azure najdete na stránce 18 [přehledu zabezpečení Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fyzická přístupová práva k zabezpečeným oblastem se mění v rámci jednoho pracovního dne pro kohokoli, kdo už nemá zodpovědnost za službu Log Analytics, včetně přenosu a ukončení. Můžete si přečíst o globální fyzické infrastruktuře, kterou používáme v datových [centrech Microsoftu](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Správa incidentů
Log Analytics má proces správy incidentů, na které se dodržují všechny služby Microsoftu. Pro Shrnutí:

* Použijte sdílený model zodpovědnosti, kde část bezpečnostní zodpovědnost patří společnosti Microsoft a část patří zákazníkovi.
* Správa incidentů zabezpečení Azure:
  * Zahájit šetření při detekci incidentu
  * Vyhodnotit dopad a závažnost incidentu členem týmu reakce na incidenty při volání. Na základě legitimace může vyhodnocení nebo nemusí vést k dalšímu eskalaci týmu bezpečnostních odpovědí.
  * Diagnostikujte incidenty odborníky na reakci na zabezpečení, aby prováděli technické nebo forenzní šetření, a Identifikujte strategie omezení, zmírnění a řešení. Pokud se bezpečnostní tým domnívá, že zákaznická data mohla být vystavena nezákonné nebo neoprávněné osobě, je paralelní spuštění procesu oznamování incidentu zákazníků paralelně zahájeno.  
  * Stabilizace a obnovení z incidentu. Tým reakce na incident vytvoří plán obnovení pro zmírnění problému. K krizovým a paralelním krokům, jako je například umístění s použitím karantény, může dojít okamžitě a paralelně s diagnostikou. Dlouhodobá omezení se můžou naplánovat, ke kterému dochází po úspěšném bezprostředním riziku.  
  * Zavřete incident a proveďte po pojednání. Tým reakce na incidenty vytvoří po porážce, který obsahuje podrobnosti o incidentu, s úmyslem revidovat zásady, postupy a procesy, aby nedocházelo k opakování události.
* Upozorněte zákazníky na incidenty zabezpečení:
  * Určete rozsah ovlivněných zákazníků a poskytněte komukoli, kdo má dopad na to, co je to možné.
  * Vytvořte si oznámení, aby zákazníkům poskytovali podrobné informace, aby mohli provádět šetření na jejich konci a plnit veškeré závazky, které učinili koncovým uživatelům, a přitom nemuseli neoprávněně zpozdit proces oznámení.
  * V případě potřeby potvrďte a deklarujte incident.
  * Upozorněte zákazníky oznámení o incidentu bez nepřiměřeného zpoždění a v souladu s právním nebo smluvním závazkem. Oznámení o incidentech zabezpečení se doručují jednomu nebo několika správcům zákazníka jakýmkoli způsobem, který společnost Microsoft vybere, včetně e-mailu.
* Proveďte školení a školení pro tým:
  * Personál společnosti Microsoft je nutný k dokončení školení o zabezpečení a povědomí, které jim pomůže identifikovat a ohlásit podezřelé problémy se zabezpečením.  
  * Operátoři, kteří pracují na službě Microsoft Azure, mají k dispozici školicí povinnosti obklopující jejich přístup k citlivým systémům hostujícím zákaznická data.
  * Zaměstnanci Microsoft Security Response obdrží specializované školení pro své role.

I když je velmi zřídka, společnost Microsoft upozorní každého zákazníka během jednoho dne, pokud dojde k významné ztrátě dat zákazníků. 

Další informace o tom, jak Microsoft reaguje na incidenty zabezpečení, najdete v tématu [Microsoft Azure odezva zabezpečení v cloudu](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Dodržování předpisů
Program Log Analytics software pro vývoj softwaru a zabezpečení informací a zásad správného řízení v týmu podporuje své obchodní požadavky a dodržuje zákony a předpisy, jak je popsáno v tématu [Microsoft Azure Security Center](https://azure.microsoft.com/support/trust-center/) a [Microsoft Trust Center](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Jak Log Analytics vytváří požadavky na zabezpečení, jsou zde popsány také ovládací prvky zabezpečení, Správa a sledování rizik. Každoročně si projdeme zásady, standardy, postupy a pokyny.

Každý člen vývojového týmu obdrží formální školení zabezpečení aplikací. Interně používáme systém správy verzí pro vývoj softwaru. Každý softwarový projekt je chráněn systémem správy verzí.

Společnost Microsoft má tým zabezpečení a dodržování předpisů, který se dohlíží a vyhodnocuje všechny služby v Microsoftu. Členové zabezpečení informací tvoří tým a nejsou přidružení k technickým týmům, které vyvíjí Log Analytics. Bezpečnostní důstojníci mají svůj vlastní řetězec správy a provádějí nezávislé posouzení produktů a služeb, aby se zajistilo zabezpečení a dodržování předpisů.

Správní rada Microsoftu oznamuje výroční zprávu o všech programech zabezpečení informací v Microsoftu.

Log Analytics software pro vývoj a údržbu softwaru aktivně spolupracuje s týmy právního a dodržování předpisů Microsoftu a dalšími průmyslovými partnery k získání různých certifikátů.

## <a name="certifications-and-attestations"></a>Certifikace a osvědčení
Azure Log Analytics splňuje následující požadavky:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Standard zabezpečení dat (PCI DSS) v oboru platebních karet](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) Radou Security Standards (STANDARDem PCI).
* [Ovládací prvky organizace služeb (SOC) 1 typ 1 a SOC 2 typu 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) vyhovující
* [HIPAA a Hitech](/compliance/regulatory/offering-hipaa-hitech) pro společnosti, které mají smlouvu s organizací HIPAA pro obchodní přidružení
* Společná technická kritéria pro Windows
* Důvěryhodný výpočetní výkon Microsoftu
* Jako služba Azure Log Analytics komponenty, které používá, dodržují požadavky Azure na dodržování předpisů. Další informace najdete na [webu dodržování předpisů v centru zabezpečení Microsoftu](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> V některých certifikačních autoritách/potvrzeních jsou Log Analytics uvedeny pod jejím dřívějším názvem *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Tok dat zabezpečení cloud computingu
Následující diagram znázorňuje architekturu zabezpečení cloudu jako tok informací z vaší společnosti a jak je zabezpečená, protože je přesunutá do služby Log Analytics, kterou vám v Azure Portal uvidí. Další informace o jednotlivých krocích následují po diagramu.

![Obrázek shromažďování a zabezpečení dat Log Analytics](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Zaregistrujte se Log Analytics a Shromážděte data.
Aby mohla vaše organizace odesílat data Log Analytics, nakonfigurujete agenta pro Windows nebo Linux běžící na virtuálních počítačích Azure nebo na virtuálních nebo fyzických počítačích ve vašem prostředí nebo jiném poskytovateli cloudu.  Pokud používáte Operations Manager, ve skupině pro správu nakonfigurujte agenta Operations Manager. Uživatelé (kteří můžou být vy, další jednotliví uživatelé nebo skupina lidí) vytvořit jeden nebo více Log Analyticsch pracovních prostorů a registrovat agenty pomocí jednoho z následujících účtů:

* [ID organizace](../../active-directory/fundamentals/sign-up-organization.md)
* [Účet Microsoft – Outlook, Office Live, MSN](https://account.microsoft.com/account)

Log Analytics pracovní prostor je místo, kde se shromažďují, agregují, analyzují a prezentují data. Pracovní prostor se primárně používá jako prostředek k rozdělení dat a každý pracovní prostor je jedinečný. Můžete například chtít, aby se vaše produkční data spravovala s jedním pracovním prostorem a vaše testovací data byla spravovaná s jiným pracovním prostorem. Pracovní prostory také správcům umožňují řídit přístup uživatelů k datům. Ke každému pracovnímu prostoru může být přidruženo více uživatelských účtů a každý uživatelský účet může mít přístup k více Log Analyticsm pracovním prostorům. Pracovní prostory můžete vytvářet na základě oblasti Datacenter.

Pro Operations Manager skupina pro správu Operations Manager naváže spojení se službou Log Analytics. Pak můžete nakonfigurovat, které systémy spravované agentem ve skupině pro správu můžou shromažďovat a odesílat data do služby. V závislosti na řešení, které jste povolili, se data z těchto řešení odesílají přímo z Operations Manager management server do služby Log Analytics nebo z důvodu objemu dat shromažďovaných systémem spravovaným agentem se odesílají přímo od agenta ke službě. Pro systémy, které Operations Manager nemonitorují, se každý zabezpečeně přímo připojí k Log Analytics službě.

Veškerá komunikace mezi připojenými systémy a službou Log Analytics je zašifrovaná. Protokol TLS (HTTPS) se používá k šifrování.  Za tímto účelem se zaručí, že Log Analytics je aktuální s nejnovějšími zálohami v kryptografických protokolech.

Každý typ agenta shromažďuje data pro Log Analytics. Typ shromažďovaných dat závisí na typech používaných řešení. Souhrn shromažďování dat můžete zobrazit v tématu [přidání Log Analytics řešení z galerie řešení](../insights/solutions.md). Pro většinu řešení jsou k dispozici také podrobnější informace o kolekci. Řešení je sada předdefinovaných zobrazení, vyhledávací dotazy protokolů, pravidla shromažďování dat a logika zpracování. K importu řešení můžou použít Log Analytics jenom správci. Po importu řešení se přesune na Operations Manager servery pro správu (Pokud se používají) a potom na všechny vámi zvolené agenty. Následně agenti shromažďují data.

## <a name="2-send-data-from-agents"></a>2. odeslání dat z agentů
Všechny typy agentů zaregistrujete pomocí registračního klíče a mezi agentem a službou Log Analytics se vytvoří zabezpečené připojení pomocí ověřování založeného na certifikátech a TLS s portem 443. Log Analytics používá úložiště tajného kódu ke generování a údržbě klíčů. Privátní klíče se otočí každých 90 dní a ukládají se v Azure a spravují se pomocí operací Azure, které dodržují přísné zákonné postupy a postupy dodržování předpisů.

V Operations Manager skupina pro správu zaregistrovaná v pracovním prostoru Log Analytics vytváří zabezpečené připojení HTTPS s Operations Manager management server.

U agentů pro Windows nebo Linux běžících na virtuálních počítačích Azure se ke čtení diagnostických událostí v tabulkách Azure používá klíč úložiště jen pro čtení.  

S jakýmkoli agentem, který se hlásí do skupiny pro správu Operations Manager, která je integrovaná se službou Log Analytics, pokud management server z nějakého důvodu není možné komunikovat se službou, shromážděná data se ukládají místně do dočasné mezipaměti v management server.   Pokusí se data znovu odeslat každých osm minut po dobu dvou hodin.  V případě dat, která obcházejí management server a se odesílá přímo do Log Analytics, chování je konzistentní s agentem Windows.  

Data v mezipaměti agenta Windows nebo management server jsou chráněná úložištěm přihlašovacích údajů operačního systému. Pokud služba nemůže data zpracovat po dvou hodinách, agenti data zařadí do fronty. Pokud se fronta zaplní, agent začne odstraňovat datové typy počínaje daty o výkonu. Limit fronty agenta je klíč registru, takže ho můžete v případě potřeby upravit. Shromážděná data se komprimují a odesílají do služby, vybírají databáze Operations Manager skupiny pro správu, takže do nich nepřidá žádné zatížení. Až budou shromážděná data odeslána, odeberou se z mezipaměti.

Jak je popsáno výše, data z management server nebo přímo propojených agentů se odesílají přes TLS pro Microsoft Azure datová centra. Volitelně můžete použít ExpressRoute k zajištění dalšího zabezpečení dat. ExpressRoute je způsob, jak se přímo připojit k Azure z vaší stávající sítě WAN, jako je například přepínání mezi protokolem (MPLS) VPN, poskytované poskytovatelem síťové služby. Další informace najdete v tématu [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. služba Log Analytics přijímá a zpracovává data.
Služba Log Analytics zajišťuje, že příchozí data pochází z důvěryhodného zdroje tím, že ověřuje certifikáty a integritu dat pomocí ověřování Azure. Nezpracovaná nezpracovaná data se pak uloží do centra událostí Azure v oblasti, kde budou data nakonec uložená v klidovém formátu. Typ uložených dat závisí na typech řešení, které byly naimportovány a použity ke shromažďování dat. Potom služba Log Analytics zpracuje nezpracovaná data a ingestuje je do databáze.

Doba uchování shromážděných dat uložených v databázi závisí na zvoleném cenovém plánu. Pro úroveň *Free* je shromážděná data dostupná po dobu sedmi dnů. Pro *placenou* úroveň jsou shromážděná data dostupná po dobu 31 dnů ve výchozím nastavení, ale dají se prodloužit na 730 dní. Data jsou uložená v klidovém úložišti Azure Storage, aby se zajistila důvěrnost dat a data se replikují v rámci místní oblasti pomocí místně redundantního úložiště (LRS). Poslední dva týdny dat jsou také uloženy v mezipaměti založené na SSD a Tato mezipaměť je zašifrovaná.

Data v úložišti databáze nelze po ingestování měnit, ale je možné je odstranit pomocí [cesty rozhraní API pro *vyprázdnění*](personal-data-mgmt.md#delete). I když data nejde změnit, některé certifikace vyžadují, aby byla data zachovaná neměnná a v úložišti se nedají změnit ani odstranit. Data neměnnosti je možné dosáhnout pomocí [exportu dat](logs-data-export.md) do účtu úložiště, který je nakonfigurovaný jako [neproměnlivé úložiště](../../storage/blobs/storage-blob-immutability-policies-manage.md).

## <a name="4-use-log-analytics-to-access-the-data"></a>4. pro přístup k datům použijte Log Analytics
Pokud chcete získat přístup k pracovnímu prostoru Log Analytics, přihlaste se k Azure Portal pomocí účtu organizace nebo účet Microsoft, které jste dříve nastavili. Veškerý provoz mezi portálem a službou Log Analytics se odesílá přes zabezpečený kanál HTTPS. Při používání portálu se v klientském počítači (webovém prohlížeči) generuje ID relace a data se ukládají do místní mezipaměti, dokud se relace neukončí. Po ukončení se mezipaměť odstraní. Soubory cookie na straně klienta, které neobsahují identifikovatelné osobní údaje, se automaticky neodeberou. Soubory cookie relací jsou označeny jako HTTPOnly a jsou zabezpečené. Po předem určené době nečinnosti se Azure Portal relace ukončí.


## <a name="additional-security-features"></a>Další funkce zabezpečení
Pomocí těchto dalších funkcí zabezpečení můžete dále zabezpečit vaše prostředí Azure Monitor/Log Analytics. Tyto funkce vyžadují větší správu správců. 
- [Klíče spravované zákazníkem (zabezpečení)](../logs/customer-managed-keys.md) – pomocí klíčů spravovaných zákazníkem můžete šifrovat data odesílaná do vašich Log Analytics pracovních prostorů. Vyžaduje použití Azure Key Vault. 
- [Soukromé/zákazníkem spravované úložiště](./private-storage.md) – spravujte účet úložiště s vlastním šifrováním a sdělte Log Analytics, jak ho používat k ukládání dat monitorování. 
- [Soukromé odkazy sítě](./private-link-security.md) – privátní propojení Azure umožňuje zabezpečené propojení služeb Azure PaaS (včetně Azure monitor) do vaší virtuální sítě pomocí privátních koncových bodů. 
- [Bezpečnostní modul Azure pro zákazníky](../../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-preview) – Customer Lockbox pro Microsoft Azure poskytuje rozhraní pro zákazníky, kteří budou kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků. Používá se v případech, kdy technici Microsoftu potřebují získat přístup k datům zákazníků během zpracování žádostí o podporu.


## <a name="next-steps"></a>Další kroky
* Naučte se shromažďovat data pomocí Log Analytics pro virtuální počítače Azure po [rychlém startu virtuálního počítače Azure](../vm/quick-collect-azurevm.md).  

*  Pokud chcete shromažďovat data z fyzických nebo virtuálních počítačů se systémem Windows nebo Linux ve vašem prostředí, přečtěte si [rychlý Start pro počítače](../vm/quick-collect-linux-computer.md) se systémem Linux nebo [rychlý Start pro počítače se systémem Windows](../vm/quick-collect-windows-computer.md) .
