---
title: Časté otázky – vyhrazené modulu hardwarového zabezpečení Azure | Dokumentace Microsoftu
description: Nejčastější dotazy mezi probíraná témata různých na vyhrazené modulu hardwarového zabezpečení Azure
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/8/2019
ms.author: mbaldwin
ms.openlocfilehash: b79ed7ea3113f097f767ad7ff8bdc47b4e4916eb
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883562"
---
# <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Najděte odpovědi na běžné dotazy týkající se Microsoft Azure vyhrazené HSM.

## <a name="the-basics"></a>Základní informace

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Otázka: Co je modul hardwarového zabezpečení (HSM)?

Modul hardwarového zabezpečení (HSM) je fyzické výpočetní zařízení použít k ochraně kryptografických klíčů. Klíčů uložených v modulech hardwarového zabezpečení je možné pro kryptografické operace. Materiál klíče zůstanou bezpečně v modulech hardwarového odolné proti zfalšování, zapečetěných. Modul hardwarového zabezpečení umožňuje pouze ověří a autorizuje aplikací pro použití klíče. Obsah klíčů nikdy neopustí hranici ochrany HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>Otázka: Co je to vyhrazená nabídka HSM v Azure?

Azure vyhrazené modul hardwarového zabezpečení je Cloudová služba, která poskytuje modulů HSM služby hostované v datových centrech Azure, které jsou přímo připojené k virtuální síti zákazníka. Tyto moduly Hms jsou vyhrazené síťových zařízení (od Gemalto SafeNet sítě HSM 7 modelu A790). Se nasazuje přímo do strany zákazníků privátní adresní prostor IP adres a Microsoft nemá přístup k kryptografické funkce moduly hardwarového zabezpečení. Pouze zákazník má úplnou správu a kryptografické kontrolu nad tato zařízení. Zákazníci odpovídají za správu zařízení a dostanou úplná aktivita protokoly přímo ze svých zařízení. Dedikovaných modulů hardwarového zabezpečení pomáhá zákazníkům splňovat dodržování předpisů nebo zákonných požadavků, jako je například podle standardu FIPS 140-2 Level 3, HIPAA, PCI-DSS a eIDAS a mnoha dalších.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>Otázka: Jaký hardware se používá pro vyhrazené HSM?

Microsoft spolupracuje se službou identita Gemalto, aby mohla doručovat službu HSM vyhrazenou pro Azure. Konkrétní zařízení používaná [SafeNet Luna sítě HSM 7 modelu A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Toto zařízení nejen poskytuje FIPS 140-2 Level 3 ověřit firmwaru, ale také nabízí nízkou latenci, vysoký výkon a vysokou kapacitu přes 10 oddíly. 

### <a name="q-what-is-an-hsm-used-for"></a>Otázka: K čemu se HSM používá?

HSM slouží k ukládání kryptografických klíčů, které se používají pro kryptografické funkce, jako je SSL (Secure Sockets Layer), šifrování dat, PKI (infrastruktura veřejných klíčů), DRM (Správa digitálních práv) a podepisování dokumentů.

### <a name="q-how-does-dedicated-hsm-work"></a>Otázka: Jak služba Rezervované HSM funguje?

Zákazníci si můžou zřídit moduly hardwarového zabezpečení v různých oblastech pomocí Powershellu nebo rozhraní příkazového řádku. Zákazník Určuje, jaké virtuální sítě, moduly hardwarového zabezpečení připojené k se jednou zřízené moduly hardwarového zabezpečení bude k dispozici v určené podsítě na přiřazené IP adresy v zákazníka privátní adresní prostor IP adres. Potom zákazníci mohou připojit do modulů HSM služby pro správu zařízení pomocí protokolu SSH a správy nastavení připojení klienta modulu hardwarového zabezpečení, inicializace modulů hardwarového zabezpečení, vytvoření oddílů, definujte a přiřaďte role, například oddílu officer, kryptografických ředitel a kryptografických uživatele. Zákazník použije Gemalto HSM klienta nástroje/SDK/software k dispozici k provedení kryptografických operací ve svých aplikacích.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Otázka: Který software je součástí vyhrazené služby HSM?

Gemalto poskytuje veškerého softwaru pro zařízení HSM jednou zřídili microsoftem. Software je k dispozici na [Gemalto zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/). Zákazníci, kteří používají službu vyhrazené modulu hardwarového zabezpečení jsou požadovány k registraci pro Gemalto podporují a mají ID zákazníka, který umožňuje přístup a stáhnout si odpovídající softwaru. Verze 7.2, která je kompatibilní s FIPS 140-2 Level 3 ověřit verzi firmwaru 7.0.3 je podporované klientský software. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>Otázka: Nabízí vyhrazené HSM založené na službě Azure ověřování založené na heslech a PED?

V současnosti poskytuje Azure vyhrazené HSM jenom HSM s ověřováním pomocí hesla.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>Otázka: Bude se pro mě HSM vyhrazený hostitel HSM v Azure?

Microsoft nabízí jenom modul HARDWAROVÉho zabezpečení identita Gemalto SafeNet Luna jenom pomocí vyhrazené služby HSM a nemůže hostovat žádná zařízení poskytovaná zákazníky.

### <a name="q-does-azure-dedicated-hsm-support-payment-pinetf-features"></a>Otázka: Podporuje funkce HSM (připnutí/ETF) pro službu Azure vyhrazené součásti pro platby?

Vyhrazená služba HSM Azure používá SafeNet Luna Network HSM 7 (model A790). Tato zařízení nepodporují funkce pro platební modul HSM specifické pro modul HSM (například PIN nebo ETF) nebo certifikace. Pokud chcete, aby služba HSM vyhrazená službou Azure podporovala platební HSM v budoucnu, předejte prosím svůj názor na zástupce účtu Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>Otázka: Ve kterých oblastech Azure je dostupný vyhrazený modul HARDWAROVÉho zabezpečení?

Od 13. března 2019 je vyhrazený modul HSM dostupný ve 14 oblastech uvedených níže. Další oblasti jsou plánovány a mohou být popsány prostřednictvím zástupce účtu společnosti Microsoft.

* East US
* Východní USA 2
* USA – západ
* Střed USA – jih
* Jihovýchodní Asie
* Východní Asie
* Severní Evropa
* Západní Evropa
* Velká Británie – jih
* Spojené království – západ
* Kanada – střed
* Kanada – východ
* Austrálie – východ
* Austrálie – jihovýchod

## <a name="interoperability"></a>Vzájemná funkční spolupráce

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Otázka: Jak se moje aplikace připojí k vyhrazené HSM?

K provedení kryptografických operací z vašich aplikací používáte Gemalto HSM klienta nástroje/SDK/software k dispozici. Software je k dispozici na [Gemalto zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/). Zákazníci, kteří používají službu vyhrazené modulu hardwarového zabezpečení jsou požadovány k registraci pro Gemalto podporují a mají ID zákazníka, který umožňuje přístup a stáhnout si odpovídající softwaru.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Otázka: Může se aplikace připojit k vyhrazenému HSM z jiné virtuální sítě v nebo v různých oblastech?

Ano, budete muset použít [VNET peering](../virtual-network/virtual-network-peering-overview.md) v rámci oblasti k navázání připojení mezi virtuálními sítěmi. Pro připojení mezi různými oblastmi, je nutné použít [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Otázka: Můžu synchronizovat vyhrazený modul HARDWAROVÉho zabezpečení s místními HSM?

Ano, můžete synchronizovat v místním HSM pomocí modulu hardwarového zabezpečení vyhrazené. [Point-to-Point VPN typu point-to-site nebo](../vpn-gateway/vpn-gateway-about-vpngateways.md) připojení slouží k navázání připojení k místní síti.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Otázka: Můžu pomocí klíčů uložených v vyhrazeném modulu HSM šifrovat data používaná ostatními službami Azure?

Ne. Azure vyhrazené moduly Hms jsou pouze dostupné z uvnitř virtuální sítě.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Otázka: Můžu importovat klíče z existujícího místního HSM do vyhrazeného modulu HSM?

Ano, pokud máte místní Gemalto SafeNet moduly hardwarového zabezpečení. Existuje několik metod. V modulu hardwarového zabezpečení Gemalto dokumentaci.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Otázka: Jaké operační systémy podporuje vyhrazený klientský software HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* VM VMware, HyperV, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Otázka: Návody nakonfigurovat klientskou aplikaci, aby vytvořila konfiguraci s vysokou dostupností s více oddíly z více HSM?

Chcete-li mít vysokou dostupnost, nastavení konfigurace modulu hardwarového zabezpečení klientských aplikací pro použití oddíly z každého modulu HSM. V dokumentaci softwaru klienta Gemalto modulu hardwarového zabezpečení.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Otázka: Jaké mechanismy ověřování podporuje vyhrazený modul HSM?

Azure vyhrazené HSM používá 7 HSM SafeNet síťových zařízení (model A790) a podporují ověřování pomocí hesla.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Otázka: Jaké sady SDK, rozhraní API, klientský software je k dispozici pro použití s vyhrazeným modulem HSM?

PKCS č. 11, Java (JCA/balíčku JCE), Microsoft CAPI a CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Otázka: Můžu importovat/migrovat klíče z Luna 5/6 HSM do Azure vyhrazeného HSM?

Ano. Najdete v Průvodci migrací Gemalto. 

## <a name="using-your-hsm"></a>Pomocí vašeho HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Otázka: Návody rozhodnout, jestli chcete používat Azure Key Vault nebo vyhrazený modul HSM pro Azure?

Azure vyhrazené modul hardwarového zabezpečení je vhodnou volbou pro podniky, migrace do Azure u místních aplikací, které používají moduly hardwarového zabezpečení. Dedikovaných modulů hardwarového zabezpečení je k dispozici možnost migrace aplikace s minimálními změnami. Pokud kryptografické operace provádějí v kódu aplikace běží ve virtuálním počítači Azure, nebo webové aplikace, můžou použít vyhrazený modulu hardwarového zabezpečení. Obecně platí, pečlivě zabaleny softwaru spuštěného v modelech IaaS (infrastruktura jako služba), které podporují moduly hardwarového zabezpečení jako úložiště klíčů můžete vyhradit modulu hardwarového zabezpečení, jako je například aplikaci brány nebo nástrojem traffic manager pro bez klíčů SSL, ADCS (Active Directory Certificate Services), nebo podobné nástroje infrastruktury veřejných KLÍČŮ, nástroje nebo aplikace použít pro podepisování dokumentů, podepisování kódu nebo SQL Server (IaaS) nakonfigurovanou transparentní šifrování dat (databáze transparentní šifrování) pomocí hlavního klíče v modulu hardwarového zabezpečení pomocí zprostředkovateli EKM (extensible key management). Azure Key Vault je vhodné pro aplikace "vychází in-cloud" nebo šifrování v době rest scénáře, kdy zákaznická data zpracovává PaaS (platforma jako služba) nebo scénáře SaaS (Software jako služba), jako je klíč zákazníka Office 365, Azure Information Protection , Azure Disk Encryption šifrování Azure Data Lake Store s spravované zákazníkem, Azure Storage šifrování pomocí klíče se zákazníkem spravovaný klíč a SQL Azure s zákazníkem spravované klíče.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Otázka: Jaké scénáře použití nejlépe vyhovují vyhrazenému modulu HSM Azure?

Azure vyhrazené modul hardwarového zabezpečení je nejvhodnější pro scénáře migrace. To znamená, že při migraci místních aplikací do Azure, které už používáte moduly hardwarového zabezpečení. To poskytuje bezproblémové možnosti můžete migrovat do Azure s minimálními změnami aplikace. Pokud kryptografické operace provádějí v kódu aplikace běží ve virtuálním počítači Azure nebo webové aplikace, může se použít vyhrazený modulu hardwarového zabezpečení. Obecně platí pečlivě zabaleny software na modely IaaS (infrastruktura jako služba), které podporují moduly hardwarového zabezpečení, jak vyhradit HSM, můžete použít jako úložiště klíčů:

* Application gateway nebo nástrojem traffic manager pro protokol SSL bez kódu
* ADCS (Active Directory Certificate Services)
* Podobné nástroje infrastruktury veřejných KLÍČŮ
* Nástroje a aplikace použít pro podepisování dokumentů
* Podepisování kódu
* SQL Server (IaaS) nakonfigurovanou transparentní šifrování dat (databáze transparentní šifrování) pomocí hlavního klíče v modulu hardwarového zabezpečení pomocí zprostředkovateli EKM (extensible key management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Otázka: Je možné používat Rezervované HSM společně s klíčem zákazníka Office 365, službami Azure Information Protection, Azure Data Lake Store, Disk Encryption, šifrováním služby Azure Storage nebo transparentním šifrováním dat Azure SQL?

Ne. Vyhrazené HSM zřizován přímo do privátního IP adresního prostoru zákazníka proto nemá k dispozici v případě ostatních služeb Azure nebo Microsoft.

## <a name="administration-access-and-control"></a>Správa přístupu a ovládací prvek

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Otázka: Získá zákazník zcela výhradní kontrolu nad HSM pomocí vyhrazeného HSM?

Ano. Každé zařízení HSM je plně vyhrazená pro jednu jednoho zákazníka a nemá nikdo jiný správy po zřízení a změnit heslo správce.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Otázka: Jakou úroveň přístupu Microsoft potřebuje k mému HARDWAROVÉmu zabezpečení?

Microsoft nemá žádné administrativní nebo kryptografických kontrolu nad modul hardwarového zabezpečení. Microsoft má monitorování úrovně přístupu přes sériový port připojení k načtení základní telemetrii, třeba teplota a součást stavu. To umožňuje společnosti Microsoft poskytují proaktivní oznámení o problémy se stavem. V případě potřeby zákazníka můžete zakázat tento účet.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>Otázka: Jaký je účet "tenantAdmin", který Microsoft používá, používám uživatele s oprávněními správce na SafeNet HSM?

Zařízení HSM se dodává s výchozím uživatelem správce s obvyklým výchozím heslem. Microsoft nechce používat výchozí hesla, protože jakékoli zařízení je ve fondu, který čeká na zřízení zákazníky. To by nesplňovalo naše striktní požadavky na zabezpečení. Z tohoto důvodu jsme nastavili silné heslo, které se v době zřizování zahodí. V době zřizování vytvoříme také nového uživatele v roli správce s názvem "tenantAdmin". Tento uživatel má výchozí heslo a zákazníci ho při prvním přihlášení k nově zřízenému zařízení změní jako první akci. Tento proces zajišťuje vysoké stupně zabezpečení a udržuje pro naše zákazníky náš příslib pro správu s jediným řízením. Je třeba poznamenat, že uživatel "tenantAdmin" lze použít k resetování hesla uživatele správce, pokud zákazník upřednostňuje používání tohoto účtu. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Otázka: Může společnost Microsoft nebo kdokoli na klíčích služby Microsoft Access v mém vyhrazeném HSM?

Ne. Microsoft nemá přístup k klíčů uložených v zákazníka přidělené vyhrazené modulu hardwarového zabezpečení.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Otázka: Můžu upgradovat software/firmware na HSM přidělený mně?

Pokud chcete získat nejlepší podpora, společnost Microsoft důrazně doporučuje neupgradovat software a firmware na modul hardwarového zabezpečení. Zákazník však nemá úplné správy včetně upgradu software a firmware, pokud konkrétní součásti, které jsou zapotřebí ve směru z verzí firmwaru jiný. Před provedením změn si důsledky musí chápat, jak to může, například efekt FIPS ověřit stav. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>Otázka: Návody spravovat vyhrazený modul HSM?

Vyhrazené moduly hardwarového zabezpečení můžete spravovat přístup k nim pomocí protokolu SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Otázka: Návody spravovat oddíly ve vyhrazeném modulu HSM?

Klientský software Gemalto HSM se používá ke správě modulů hardwarového zabezpečení a oddíly.

### <a name="q-how-do-i-monitor-my-hsm"></a>Otázka: Návody monitorovat modul HARDWAROVÉho zabezpečení?

Zákazník má úplný přístup k protokolům aktivit modulu hardwarového zabezpečení přes syslog a SNMP. Zákazník bude muset nastavit syslog server nebo SNMP server pro příjem protokolů a událostí z moduly hardwarového zabezpečení.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Otázka: Můžu získat úplný přístup ke všem operacím HSM z vyhrazeného modulu HSM?

Ano. Odeslání protokolů z modulu hardwarového zabezpečení zařízení na syslog server

## <a name="high-availability"></a>Vysoká dostupnost

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Otázka: Je možné nakonfigurovat vysokou dostupnost ve stejné oblasti nebo ve více oblastech?

Ano. Nastavení a konfigurace vysoké dostupnosti se provádějí v poskytované Gemalto HSM klientský software. HSM ze stejné virtuální sítě nebo jiného virtuální sítě ve stejné oblasti nebo v různých oblastech nebo místní HSM připojená k virtuální síti pomocí VPN typu Site-to-site nebo Point-to-Point VPN je možné přidat do stejné konfigurace vysoké dostupnosti. Je potřeba poznamenat, že tato funkce synchronizuje jenom materiál klíče, a ne konkrétní položky konfigurace, jako třeba role.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Otázka: Mohu přidat moduly hardwarového zabezpečení z místní sítě pro skupinu vysokou dostupnost pomocí vyhrazené modulu hardwarového zabezpečení Azure?

Ano. Musí splňovat požadavky na vysokou dostupnost SafeNet Luna sítě HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Otázka: Je možné přidat moduly hardwarového zabezpečení Luna 5/6 z místních sítí pro skupinu vysokou dostupnost pomocí vyhrazené modulu hardwarového zabezpečení Azure?

Ne.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Otázka: Kolik HSM můžu přidat ke stejné konfiguraci s vysokou dostupností z jedné aplikace?

16 členů skupiny HA má zcela neomezený test s vynikajícími výsledky.

## <a name="support"></a>Podpora

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Otázka: Jaká je smlouva SLA pro vyhrazenou službu HSM?

Pro vyhrazenou službu HSM není poskytnuta žádná konkrétní záruka na provozní čas. Microsoft zajistí sítě úroveň přístupu k zařízení, a proto použít standardní síťové smlouvy Azure SLA.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Otázka: Jak se HSM používá ve vyhrazeném modulu HSM chráněným v Azure?

Datacentra Azure mají rozsáhlé fyzické a procedurální bezpečnostní mechanismy. Kromě toho vyhrazené moduly hardwarového zabezpečení jsou hostované v oblasti Další omezený přístup datového centra. Tyto oblasti mají další fyzické prvky řízení přístupu a sledování kamera pro zvýšení zabezpečení.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Otázka: Co se stane, pokud dojde k narušení zabezpečení nebo manipulaci s hardwarem?

Vyhrazená služba HSM používá 7 HSM SafeNet síťových zařízení. Tato zařízení podporují rozpoznání narušení fyzické a logické. Pokud by byl někdy proti události jsou automaticky zeroized moduly hardwarového zabezpečení.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Otázka: Návody zajistěte, aby se klíče v mých vyhrazených HSM ztratily kvůli chybě nebo škodlivému útoku Insider?

Důrazně doporučujeme použít zálohování zařízení v místním HSM provádět pravidelné pravidelné zálohy moduly hardwarového zabezpečení pro zotavení po havárii. Je potřeba použít připojení k síti VPN peer-to-peer nebo site-to-site k místní pracovní stanici připojené k zálohovací zařízení HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Otázka: Návody získat podporu pro vyhrazený modul HSM?

Podpora je poskytována společností Microsoft i identita Gemalto.  Pokud máte problém s hardwarem nebo síťovým přístupem, vyvolejte žádost o podporu od Microsoftu a pokud máte problém s konfigurací HSM, softwarem a aplikací, vyvolejte žádost o podporu pomocí identita Gemalto. Pokud máte neurčený problém, vyvolejte žádost o podporu od Microsoftu a pak identita Gemalto můžete podle potřeby. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>Otázka: Návody získat klientský software, dokumentaci a přístup k pokynům k integraci pro modul HARDWAROVÉho zabezpečení SafeNet Luna 7?

Po registraci služby se poskytne ID zákazníka identita Gemalto, které umožňuje registraci na portálu zákaznické podpory identita Gemalto. Tím povolíte přístup ke všem softwareům a dokumentaci a zároveň povolíte žádosti o podporu přímo v identita Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Otázka: V případě, že dojde k chybě zabezpečení a byla vydána oprava identita Gemalto, která je zodpovědná za upgrade/opravu operačního systému/firmwaru?

Microsoft nemá schopnost připojení do modulů HSM přidělené pro zákazníky. Zákazníci musí upgradů a oprav jejich moduly hardwarového zabezpečení.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>Otázka: Co když potřebuji restartovat modul HARDWAROVÉho zabezpečení?

Modul hardwarového zabezpečení (HSM) obsahuje možnost restartování z příkazového řádku, ale dochází k občasným chybám při restartu. z tohoto důvodu se doporučuje pro nejbezpečnější restart, který vyžádáte, aby se zařízení vyvolalo jako fyzicky restartováno. 

## <a name="cryptography-and-standards"></a>Šifrování a norem

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Otázka: Je bezpečné ukládat šifrovací klíče pro moje nejdůležitější data do vyhrazeného modulu HSM?

Ano, vyhrazené HSM zřídí 7 HSM SafeNet síťových zařízení, že ověřených modulů HSM s FIPS 140-2 úrovně 3 použití. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Otázka: Jaké kryptografické klíče a algoritmy podporuje vyhrazený modul HSM?

Vyhrazená zařízení ustanovení SafeNet sítě HSM 7 služby modulu hardwarového zabezpečení. Podporují široké spektrum typů kryptografických klíčů a algoritmů, mezi které patří: Úplná podpora Suite B

* Asymetrické:
  * RSA
  * DSA
  * Skupina Diffie-Hellman
  * Eliptické křivky
  * Kryptografie (ECDSA, ECDH, Ed25519, ECIES) s s názvem, definovaný uživatelem a Brainpool křivky, KCDSA
* Symetrický:
  * AES-GCM
  * (Triple DES)
  * DES
  * ARIA, POČÁTEČNÍ HODNOTY
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Odvození klíče: SP800-108 – režim čítače
  * Zabalení klíče: SP800-38F
  * Náhodné číslo generace: FIPS 140-2 schválil DRBG (SP 800-90. režim centra), který splňuje dokument BSI O DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Otázka: Je vyhrazena Standard HSM FIPS 140-2 úrovně 3?

Ano. Vyhrazená služba HSM zřídí 7 HSM SafeNet síťových zařízení, že ověřených modulů HSM s FIPS 140-2 úrovně 3 použití.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Otázka: Co potřebuji k tomu, abyste se ujistili, že provozujeme vyhrazený modul HSM v režimu ověření FIPS 140-2 Level 3?

Zařízení předpisy SafeNet Luna sítě HSM 7 Služba Dedicated modulu hardwarového zabezpečení. Použití těchto zařízení FIPS 140-2 úrovně 3 ověřených modulů Hsm. Nasadit výchozí konfiguraci, operační systém a firmware jsou také ověřené podle standardu FIPS. Není nutné provádět žádnou akci pro kompatibilita se standardem FIPS 140-2 Level 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Otázka: Jak zákazník zajišťují, že při zrušení modulu hardwarového zabezpečení všechny klíče dojde k vymazání?

Než požádáte o zrušení zřízení, musí mít zákazník zeroized modulu HSM pomocí modulu hardwarového zabezpečení klienta nástroje poskytuje Gemalto.

## <a name="performance-and-scale"></a>Výkon a škálování

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Otázka: Kolik kryptografických operací se podporuje za sekundu pomocí vyhrazeného modulu HSM?

Vyhrazená zařízení ustanovení SafeNet sítě HSM 7 modulu hardwarového zabezpečení (model A790). Zde je uveden seznam maximální výkon pro některé operace: 

* RSA-2048: 10 000 transakcí za sekundu
* ECC P256: 20 000 transakcí za sekundu
* AES-GCM: 17 000 transakcí za sekundu

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Otázka: Kolik oddílů je možné vytvořit v vyhrazeném modulu HSM?

SafeNet Luna HSM 7 model A790 zahrnuje licenci na 10 oddílů za cenu služby. Zařízení má omezení 100 oddílů a přidávání oddílů do tohoto limitu by znamenalo dodatečné licenční náklady a vyžadovalo instalaci nového licenčního souboru na zařízení.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Otázka: Kolik klíčů je možné podporovat v vyhrazeném modulu HSM?

Maximální počet klíčů je funkce dostupné paměti. SafeNet Luna 7 modelu A790 používá má 32MB paměti. Následující čísla se vztahují také na páry klíčů, pokud používáte asymetrické klíče.

* RSA 2048 - 19,000
* ECC-P256 - 91,000

Kapacitu se liší v závislosti na konkrétní klíčové atributy, nastavte v šabloně generování klíčů a počet oddílů.
