---
title: Časté otázky – vyhrazené modulu hardwarového zabezpečení Azure | Dokumentace Microsoftu
description: Nejčastější dotazy mezi probíraná témata různých na vyhrazené modulu hardwarového zabezpečení Azure
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: concepts
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 949281b1415e2098fb7bf0d3e9e9fbe0a5f5c835
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080992"
---
# <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Najděte odpovědi na běžné dotazy týkající se Microsoft Azure vyhrazené HSM.

## <a name="the-basics"></a>Základní informace

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Otázka: co je modulu hardwarového zabezpečení (HSM)?

Modul hardwarového zabezpečení (HSM) je fyzické výpočetní zařízení použít k ochraně kryptografických klíčů. Klíčů uložených v modulech hardwarového zabezpečení je možné pro kryptografické operace. Materiál klíče zůstanou bezpečně v modulech hardwarového odolné proti zfalšování, zapečetěných. Modul hardwarového zabezpečení umožňuje pouze ověří a autorizuje aplikací pro použití klíče. Obsah klíčů nikdy neopustí hranici ochrany HSM.

### <a name="q-what-is-azure-dedicated-hsm-offering"></a>Otázka: co je nabídka vyhrazené modulu hardwarového zabezpečení Azure?

Azure vyhrazené modul hardwarového zabezpečení je Cloudová služba, která poskytuje modulů HSM služby hostované v datových centrech Azure, které jsou přímo připojené k virtuální síti zákazníka. Tyto moduly Hms jsou vyhrazené síťových zařízení (od Gemalto SafeNet sítě HSM 7 modelu A790). Se nasazuje přímo do strany zákazníků privátní adresní prostor IP adres a Microsoft nemá přístup k kryptografické funkce moduly hardwarového zabezpečení. Pouze zákazník má úplnou správu a kryptografické kontrolu nad tato zařízení. Zákazníci odpovídají za správu zařízení a dostanou úplná aktivita protokoly přímo ze svých zařízení. Dedikovaných modulů hardwarového zabezpečení pomáhá zákazníkům splňovat dodržování předpisů nebo zákonných požadavků, jako je například podle standardu FIPS 140-2 Level 3, HIPAA, PCI-DSS a eIDAS a mnoha dalších.

## <a name="what-hardware-is-used-for-dedicated-hsm"></a>Jaký hardware se používá pro vyhrazené HSM?

Microsoft uzavřel partnerství s Gemalto k zajištění služeb vyhrazené modulu hardwarového zabezpečení Azure. Konkrétní zařízení používaná [SafeNet Luna sítě HSM 7 modelu A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Toto zařízení nejen poskytuje FIPS 140-2 Level 3 ověřit firmwaru, ale také nabízí nízkou latenci, vysoký výkon a vysokou kapacitu přes 10 oddíly. 

### <a name="q-what-is-an-hsm-used-for"></a>Otázka: co se nepoužívá modul HSM?

HSM slouží k ukládání kryptografických klíčů, které se používají pro kryptografické funkce, jako je SSL (Secure Sockets Layer), šifrování dat, PKI (infrastruktura veřejných klíčů), DRM (Správa digitálních práv) a podepisování dokumentů.

### <a name="q-how-does-dedicated-hsm-work"></a>Otázka: Jak funguje vyhrazené HSM?

Zákazníci si můžou zřídit moduly hardwarového zabezpečení v různých oblastech pomocí Powershellu nebo rozhraní příkazového řádku. Zákazník Určuje, jaké virtuální sítě, moduly hardwarového zabezpečení připojené k se jednou zřízené moduly hardwarového zabezpečení bude k dispozici v určené podsítě na přiřazené IP adresy v zákazníka privátní adresní prostor IP adres. Potom zákazníci mohou připojit do modulů HSM služby pro správu zařízení pomocí protokolu SSH a správy nastavení připojení klienta modulu hardwarového zabezpečení, inicializace modulů hardwarového zabezpečení, vytvoření oddílů, definujte a přiřaďte role, například oddílu officer, kryptografických ředitel a kryptografických uživatele. Zákazník použije Gemalto HSM klienta nástroje/SDK/software k dispozici k provedení kryptografických operací ve svých aplikacích.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Dotaz: jaký software je poskytován službou vyhrazené HSM?

Gemalto poskytuje veškerého softwaru pro zařízení HSM jednou zřídili microsoftem. Software je k dispozici na [Gemalto zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/). Zákazníci, kteří používají službu vyhrazené modulu hardwarového zabezpečení jsou požadovány k registraci pro Gemalto podporují a mají ID zákazníka, který umožňuje přístup a stáhnout si odpovídající softwaru. Verze 7.2, která je kompatibilní s FIPS 140-2 Level 3 ověřit verzi firmwaru 7.0.3 je podporované klientský software. 

## <a name="interoperability"></a>Vzájemná funkční spolupráce

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Otázka: jak aplikaci připojit k modulu hardwarového zabezpečení vyhrazené?

K provedení kryptografických operací z vašich aplikací používáte Gemalto HSM klienta nástroje/SDK/software k dispozici. Software je k dispozici na [Gemalto zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/). Zákazníci, kteří používají službu vyhrazené modulu hardwarového zabezpečení jsou požadovány k registraci pro Gemalto podporují a mají ID zákazníka, který umožňuje přístup a stáhnout si odpovídající softwaru.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Dotaz: lze aplikaci připojit k vyhrazené HSM z jiné virtuální síti v nebo víc oblastech?

Ano, budete muset použít [VNET peering](../virtual-network/virtual-network-peering-overview.md) v rámci oblasti k navázání připojení mezi virtuálními sítěmi. Pro připojení mezi různými oblastmi, je nutné použít [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Dotaz: lze synchronizovat vyhrazené HSM s moduly hardwarového zabezpečení s místními?

Ano, můžete synchronizovat v místním HSM pomocí modulu hardwarového zabezpečení vyhrazené. [Point-to-Point VPN typu point-to-site nebo](../vpn-gateway/vpn-gateway-about-vpngateways.md) připojení slouží k navázání připojení k místní síti.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Dotaz: lze zašifrovat data používaná dalšími službami Azure, pomocí klíčů uložených v HSM vyhrazený?

Ne. Azure vyhrazené moduly Hms jsou pouze dostupné z uvnitř virtuální sítě.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Dotaz: lze importovat klíče z existujícího místního modulu HSM do HSM vyhrazený?

Ano, pokud máte místní Gemalto SafeNet moduly hardwarového zabezpečení. Existuje několik metod. V modulu hardwarového zabezpečení Gemalto dokumentaci.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Otázka: Jaké operační systémy podporují HSM vyhrazený klientský software?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuální: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Otázka: jak nakonfigurovat Moje klientská aplikace pro vytvoření konfigurace vysoké dostupnosti s více oddílů z více modulů hardwarového zabezpečení?

Chcete-li mít vysokou dostupnost, nastavení konfigurace modulu hardwarového zabezpečení klientských aplikací pro použití oddíly z každého modulu HSM. V dokumentaci softwaru klienta Gemalto modulu hardwarového zabezpečení.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Otázka: jaké ověřovací mechanismy, které podporují vyhrazené HSM?

Azure vyhrazené HSM používá 7 HSM SafeNet síťových zařízení (model A790) a podporují ověřování pomocí hesla.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Otázka: jaké sad SDK, rozhraní API, klientský software je k dispozici pro použití s vyhrazené HSM?

PKCS č. 11, Java (JCA/balíčku JCE), Microsoft CAPI a CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Dotaz: lze importovat nebo migrovat klíče z Luna 5/6 HSM do modulů HSM služby Azure vyhrazený?

Ano. Najdete v Průvodci migrací Gemalto. 

## <a name="using-your-hsm"></a>Pomocí vašeho HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Otázka: jak se rozhodnout, jestli chcete používat Azure Key Vault nebo vyhrazené modulu hardwarového zabezpečení Azure?

Azure vyhrazené modul hardwarového zabezpečení je vhodnou volbou pro podniky, migrace do Azure u místních aplikací, které používají moduly hardwarového zabezpečení. Dedikovaných modulů hardwarového zabezpečení je k dispozici možnost migrace aplikace s minimálními změnami. Pokud kryptografické operace provádějí v kódu aplikace běží ve virtuálním počítači Azure, nebo webové aplikace, můžou použít vyhrazený modulu hardwarového zabezpečení. Obecně platí, pečlivě zabaleny softwaru spuštěného v modelech IaaS (infrastruktura jako služba), které podporují moduly hardwarového zabezpečení jako úložiště klíčů můžete vyhradit modulu hardwarového zabezpečení, jako je například aplikaci brány nebo nástrojem traffic manager pro bez klíčů SSL, ADCS (Active Directory Certificate Services), nebo podobné nástroje infrastruktury veřejných KLÍČŮ, nástroje nebo aplikace použít pro podepisování dokumentů, podepisování kódu nebo SQL Server (IaaS) nakonfigurovanou transparentní šifrování dat (databáze transparentní šifrování) pomocí hlavního klíče v modulu hardwarového zabezpečení pomocí zprostředkovateli EKM (extensible key management). Azure Key Vault je vhodné pro aplikace "vychází in-cloud" nebo šifrování v době rest scénáře, kdy zákaznická data zpracovává PaaS (platforma jako služba) nebo scénáře SaaS (Software jako služba), jako je klíč zákazníka Office 365, Azure Information Protection , Azure Disk Encryption šifrování Azure Data Lake Store s spravované zákazníkem, Azure Storage šifrování pomocí klíče se zákazníkem spravovaný klíč a SQL Azure s zákazníkem spravované klíče.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Otázka: jaké Doporučené scénáře použití podle vyhrazených modulu hardwarového zabezpečení Azure?

Azure vyhrazené modul hardwarového zabezpečení je nejvhodnější pro scénáře migrace. To znamená, že při migraci místních aplikací do Azure, které už používáte moduly hardwarového zabezpečení. To poskytuje bezproblémové možnosti můžete migrovat do Azure s minimálními změnami aplikace. Pokud kryptografické operace provádějí v kódu aplikace běží ve virtuálním počítači Azure nebo webové aplikace, může se použít vyhrazený modulu hardwarového zabezpečení. Obecně platí pečlivě zabaleny software na modely IaaS (infrastruktura jako služba), které podporují moduly hardwarového zabezpečení, jak vyhradit HSM, můžete použít jako úložiště klíčů:

* Application gateway nebo nástrojem traffic manager pro protokol SSL bez kódu
* ADCS (Active Directory Certificate Services)
* Podobné nástroje infrastruktury veřejných KLÍČŮ
* Nástroje a aplikace použít pro podepisování dokumentů
* Podepisování kódu
* SQL Server (IaaS) nakonfigurovanou transparentní šifrování dat (databáze transparentní šifrování) pomocí hlavního klíče v modulu hardwarového zabezpečení pomocí zprostředkovateli EKM (extensible key management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Dotaz: lze Dedicated HSM použije klíč zákazníka Office 365, Azure Information Protection, Azure Data Lake Store, šifrování disku, šifrování služby Azure Storage, transparentní šifrování dat SQL Azure?

Ne. Vyhrazené HSM zřizován přímo do privátního IP adresního prostoru zákazníka proto nemá k dispozici v případě ostatních služeb Azure nebo Microsoft.

## <a name="administration-access-and-control"></a>Správa přístupu a ovládací prvek

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Otázka: zákazník získat úplné výhradní kontrolu nad moduly hardwarového zabezpečení pomocí vyhrazené HSM?

Ano. Každé zařízení HSM je plně vyhrazená pro jednu jednoho zákazníka a nemá nikdo jiný správy po zřízení a změnit heslo správce.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Otázka: jaké úroveň přístupu má Microsoft nějaké Moje HSM?

Microsoft nemá žádné administrativní nebo kryptografických kontrolu nad modul hardwarového zabezpečení. Microsoft má monitorování úrovně přístupu přes sériový port připojení k načtení základní telemetrii, třeba teplota a součást stavu. To umožňuje společnosti Microsoft poskytují proaktivní oznámení o problémy se stavem. V případě potřeby zákazníka můžete zakázat tento účet.

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Otázka: je Microsoft ani nikomu v Microsoft přístupových klíčů v mé vyhrazené HSM?

Ne. Microsoft nemá přístup k klíčů uložených v zákazníka přidělené vyhrazené modulu hardwarového zabezpečení.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Dotaz: lze upgradovat software a firmware na moduly hardwarového zabezpečení přidělené pro mě?

Pokud chcete získat nejlepší podpora, společnost Microsoft důrazně doporučuje neupgradovat software a firmware na modul hardwarového zabezpečení. Zákazník však nemá úplné správy včetně upgradu software a firmware, pokud konkrétní součásti, které jsou zapotřebí ve směru z verzí firmwaru jiný. Před provedením změn si důsledky musí chápat, jak to může, například efekt FIPS ověřit stav. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>Otázka: Jak můžu spravovat vyhrazené HSM?

Vyhrazené moduly hardwarového zabezpečení můžete spravovat přístup k nim pomocí protokolu SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Otázka: Jak můžu spravovat oddíly na vyhrazené HSM?

Klientský software Gemalto HSM se používá ke správě modulů hardwarového zabezpečení a oddíly.

### <a name="q-how-do-i-monitor-my-hsm"></a>Otázka: Jak můžu monitorovat Moje HSM?

Zákazník má úplný přístup k protokolům aktivit modulu hardwarového zabezpečení přes syslog a SNMP. Zákazník bude muset nastavit syslog server nebo SNMP server pro příjem protokolů a událostí z moduly hardwarového zabezpečení.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Otázka: mohu získat úplný přístup protokolu všech operací modulu hardwarového zabezpečení z vyhrazené modulu HSM?

Ano. Odeslání protokolů z modulu hardwarového zabezpečení zařízení na syslog server

## <a name="high-availability"></a>Vysoká dostupnost

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Otázka: je možné nakonfigurovat vysoké dostupnosti ve stejné oblasti nebo v několika oblastech?

Ano. Nastavení a konfigurace vysoké dostupnosti se provádějí v poskytované Gemalto HSM klientský software. Moduly hardwarového zabezpečení ze stejné virtuální síti nebo jiných virtuálních sítí ve stejné oblasti nebo víc oblastech nebo v místním HSM připojené k virtuální síti s použitím site-to-site nebo VPN typu point-to-point lze přidat do stejné konfigurace vysoké dostupnosti.

### <a name="can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Mohu přidat moduly hardwarového zabezpečení z místní sítě pro skupinu vysokou dostupnost pomocí vyhrazené modulu hardwarového zabezpečení Azure?

Ano. Musí splňovat požadavky na vysokou dostupnost SafeNet Luna sítě HSM 7.

### <a name="can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Je možné přidat moduly hardwarového zabezpečení Luna 5/6 z místních sítí pro skupinu vysokou dostupnost pomocí vyhrazené modulu hardwarového zabezpečení Azure?

Ne.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Otázka: kolik modulů hardwarového zabezpečení je možné přidat do stejné konfigurace vysoké dostupnosti z jednoho jednu aplikaci?

16.

## <a name="support"></a>Podpora

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Otázka: co je smlouva SLA pro službu vyhrazené HSM?

V současné době neexistuje žádná smlouva SLA k dispozici pro službu vyhrazené HSM. Microsoft zajistí sítě úroveň přístupu k zařízení, a proto použít standardní síťové smlouvy Azure SLA.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Otázka: jak se používají moduly hardwarového zabezpečení v Azure vyhrazená chráněné HSM?

Datacentra Azure mají rozsáhlé fyzické a procedurální bezpečnostní mechanismy. Kromě toho vyhrazené moduly hardwarového zabezpečení jsou hostované v oblasti Další omezený přístup datového centra. Tyto oblasti mají další fyzické prvky řízení přístupu a sledování kamera pro zvýšení zabezpečení.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Otázka: co se stane, pokud dojde k porušení zabezpečení nebo hardware manipulaci události?

Vyhrazená služba HSM používá 7 HSM SafeNet síťových zařízení. Tato zařízení podporují rozpoznání narušení fyzické a logické. Pokud by byl někdy proti události jsou automaticky zeroized moduly hardwarového zabezpečení.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Otázka: jak zajistit, že klíče v modulech HSM Moje vyhrazené neztratily z důvodu chyby nebo útok zlými úmysly?

Důrazně doporučujeme použít zálohování zařízení v místním HSM provádět pravidelné pravidelné zálohy moduly hardwarového zabezpečení pro zotavení po havárii. Je potřeba použít připojení k síti VPN peer-to-peer nebo site-to-site k místní pracovní stanici připojené k zálohovací zařízení HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Otázka: Jak můžu získat podporu pro vyhrazené HSM?

Stejným způsobem získejte podporu pro všechny ostatní služby Azure. Tým podpory Azure bude eskalovat Gemalto podpoře podle potřeby v závislosti na tento případ.

### <a name="q-how-do-i-get-access-to-dedicated-hsm-client-software-documentation-firmware-images"></a>Otázka: Jak můžu získat přístup k modulu hardwarového zabezpečení vyhrazené klientský software, dokumentaci, imagích firmwaru?

Zákazník musí spolupracovat přímo s Gemalto získat přístup k modulu hardwarového zabezpečení klientský software, dokumentaci, Image operačního systému a firmware.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Otázka: Pokud je nalezena ohrožení zabezpečení a opravy vydání podle Gemalto, který je zodpovědný za upgradu nebo opravy operačního systému/firmwaru?

Microsoft nemá schopnost připojení do modulů HSM přidělené pro zákazníky. Zákazníci musí upgradů a oprav jejich moduly hardwarového zabezpečení.

## <a name="cryptography-and-standards"></a>Šifrování a norem

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Otázka: je bezpečné ukládání šifrovacích klíčů pro nejdůležitější data ve vyhrazené HSM?

Ano, vyhrazené HSM zřídí 7 HSM SafeNet síťových zařízení, že ověřených modulů HSM s FIPS 140-2 úrovně 3 použití. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Otázka: jaké kryptografických klíčů a algoritmy podporuje vyhrazené HSM?

Vyhrazená zařízení ustanovení SafeNet sítě HSM 7 služby modulu hardwarového zabezpečení. Podporuje širokou škálu typy kryptografických klíčů a algoritmy včetně: podpora úplné Suite B

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
  * Metoda HMAC/hash/Message Digest: SHA-1, SHA-2, SM3
  * Odvození klíče: Režim SP800 108 čítač
  * Zabalení klíče: SP800-38F
  * Náhodné generování čísel: FIPS 140-2 schválené DRBG (SP 800-90 PEV.cenu režimu), v souladu s BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Otázka: je vyhrazené modulu hardwarového zabezpečení FIPS, které ověří 140-2 Level 3?

Ano. Vyhrazená služba HSM zřídí 7 HSM SafeNet síťových zařízení, že ověřených modulů HSM s FIPS 140-2 úrovně 3 použití.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Otázka: co musím udělat pro Ujistěte se, že můžu pracovat vyhrazené modulu hardwarového zabezpečení v FIPS 140-2 Level 3 ověření režimu?

Zařízení předpisy SafeNet Luna sítě HSM 7 Služba Dedicated modulu hardwarového zabezpečení. Použití těchto zařízení FIPS 140-2 úrovně 3 ověřených modulů Hsm. Nasadit výchozí konfiguraci, operační systém a firmware jsou také ověřené podle standardu FIPS. Není nutné provádět žádnou akci pro kompatibilita se standardem FIPS 140-2 Level 3.

### <a name="how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Jak zákazník zajišťují, že při zrušení modulu hardwarového zabezpečení všechny klíče dojde k vymazání?

Než požádáte o zrušení zřízení, musí mít zákazník zeroized modulu HSM pomocí modulu hardwarového zabezpečení klienta nástroje poskytuje Gemalto.

## <a name="performance-and-scale"></a>Výkon a škálování

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Otázka: kolik kryptografické operace jsou podporovány za sekundu s vyhrazené HSM?

Vyhrazená zařízení ustanovení SafeNet sítě HSM 7 modulu hardwarového zabezpečení (model A790). Zde je uveden seznam maximální výkon pro některé operace: 

* RSA 2048: 10 000 transakcí za sekundu
* ECC P256: 20 000 transakcí za sekundu
* AES-GCM: 17,000 transakcí za sekundu

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Otázka: kolik oddíly je možné vytvořit v vyhrazené HSM?

Podle konkrétní model HSM používá, existují 10 oddíly, které jsou k dispozici.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Otázka: kolik klíčů může být podporovaný ve vyhrazené HSM?

Maximální počet klíčů. Tato čísla platí také pro páry klíčů Pokud se používá asymetrické klíče.

* RSA 2048 - 19,000
* ECC-P256 - 91,000
* AES-256 - 218,000

Kapacitu se liší v závislosti na konkrétní klíčové atributy, nastavte v šabloně generování klíčů a počet oddílů.

