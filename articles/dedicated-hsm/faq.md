---
title: Nejčastější dotazy – Azure Dedicated HSM | Dokumenty společnosti Microsoft
description: Nejčastější dotazy týkající se různých témat v azure dedicated hsm
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: a0cb7957008308425d91abb3e0f828cc40301736
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064933"
---
# <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Najděte odpovědi na časté otázky týkající se vyhrazeného modulu hardwarového zabezpečení Microsoft Azure.

## <a name="the-basics"></a>Základy

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Otázka: Co je modul hardwarového zabezpečení (HSM)?

Modul hardwarového zabezpečení (HSM) je fyzické výpočetní zařízení používané k ochraně a správě kryptografických klíčů. Klíče uložené v souborech hesm lze použít pro kryptografické operace. Klíčový materiál zůstává bezpečně v hardwarových modulech odolných proti neoprávněné manipulaci. HSM umožňuje pouze ověřené a autorizované aplikace používat klíče. Klíčový materiál nikdy neopouští hranici ochrany hsm.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>Otázka: Co je nabídka azure dedicated hsm?

Azure Dedicated HSM je cloudová služba, která poskytuje moduly hardwarového zabezpečení hostované v datových centrech Azure, které jsou přímo připojené k virtuální síti zákazníka. Tyto moduly hardwarového zabezpečení jsou vyhrazená síťová zařízení (Síť SafeNet HSM 7 Model A790 společnosti Gemalto). Jsou nasazeny přímo do privátního adresního prostoru zákazníků a společnost Microsoft nemá žádný přístup ke kryptografickým funkcím vzdálených připojení uživatelů. Pouze zákazník má plnou administrativní a kryptografickou kontrolu nad těmito zařízeními. Zákazníci jsou zodpovědní za správu zařízení a mohou získat úplné protokoly aktivit přímo ze svých zařízení. Vyhrazené moduly hardwarového zabezpečení pomáhají zákazníkům splnit požadavky na dodržování předpisů, jako jsou FIPS 140-2 Level 3, HIPAA, PCI-DSS a eIDAS a mnoho dalších.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>Otázka: Jaký hardware se používá pro vyhrazený modul hardwarového zabezpečení?

Společnost Microsoft spolupracuje se společností Gemalto na poskytování služby Azure Dedicated HSM. Konkrétním použitým zařízením je [SafeNet Luna Network HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Toto zařízení poskytuje nejen fips 140-2 Level 3 ověřený firmware, ale také nabízí nízkou latenci, vysoký výkon a vysokou kapacitu prostřednictvím 10 oddílů. 

### <a name="q-what-is-an-hsm-used-for"></a>Otázka: K čemu se používá objekt hsm?

Soubory hestlačítek se používají pro ukládání kryptografických klíčů, které se používají pro kryptografické funkce, jako je ssl (vrstva zabezpečeného soketu), šifrování dat, infrastruktura infrastruktury veřejných klíčů (infrastruktura veřejných klíčů), drioda (správa digitálních práv) a podepisování dokumentů.

### <a name="q-how-does-dedicated-hsm-work"></a>Otázka: Jak funguje vyhrazený modul hardwarového zabezpečení?

Zákazníci mohou zřazovat moduly zabezpečení v určitých oblastech pomocí prostředí PowerShell nebo rozhraní příkazového řádku. Zákazník určí, k jaké virtuální síti budou servery hsm připojeny, a po zřízení budou servery hsm k dispozici v určené podsíti na přiřazených ADRESÁCH IP v privátním adresním prostoru zákazníka. Zákazníci se pak mohou připojit k hsm s pomocí SSH pro správu a správu zařízení, nastavit připojení klientů hsm, inicializovat soubory hesmatele, vytvářet oddíly, definovat a přiřazovat role, jako je například úředník pro oddíly, kontrolor kryptografických zařízení a uživatel kryptografických zařízení. Pak zákazník bude používat Gemalto za předpokladu, HSM klientské nástroje / SDK / software provádět kryptografické operace ze svých aplikací.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Otázka: Jaký software je dodáván se službou Vyhrazené moduly hardwarového zabezpečení?

Gemalto dodává veškerý software pro zařízení HSM po zřízení společností Microsoft. Software je k dispozici na [portálu zákaznické podpory Gemalto](https://supportportal.gemalto.com/csm/). Zákazníci, kteří používají vyhrazenou službu hardwarového zabezpečení, musí být zaregistrováni pro podporu Gemalto a mít ID zákazníka, které umožňuje přístup a stahování příslušného softwaru. Podporovaný klientský software je verze 7.2, která je kompatibilní s fips 140-2 Level 3 ověřený firmware verze 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>Otázka: Nabízí vyhrazený modul hardwarového zabezpečení Azure ověřování založené na heslech a ped?

V tuto chvíli azure dedicated hsm poskytuje pouze moduly hardwarového zabezpečení s ověřováním na základě hesla.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>Otázka: Bude Azure Vyhrazené Modul hardwarového zabezpečení hostitele mými moduly hardwarového zabezpečení pro mě?

Společnost Microsoft nabízí modul hardwarového zabezpečení gemalto SafeNet Luna network pouze prostřednictvím vyhrazené služby hardwarového zabezpečení a nemůže hostovat žádná zařízení poskytnutá zákazníkem.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>Otázka: Podporuje azure dedicated hsm funkce platby (PIN/EFT)?

Služba Azure Dedicated HSM používá zařízení SafeNet Luna Network HSM 7 (model A790). Tato zařízení nepodporují platební funkce specifické pro hsm (například PIN nebo EFT) nebo certifikace. Pokud chcete, aby služba Azure Dedicated HSM v budoucnu podporovala moduly hardwarového zabezpečení pro platby, předejte zpětnou vazbu zástupci účtu Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>Otázka: Ve kterých oblastech Azure je vyhrazený modul hardwarového zabezpečení k dispozici?

Od konce března 2019 je vyhrazený modul hardwarového zabezpečení k dispozici ve 14 regionech uvedených níže. Další oblasti jsou plánovány a lze je projednat prostřednictvím zástupce účtu Microsoft.

* USA – východ
* USA – východ 2
* USA – západ
* USA – středojih
* Jihovýchodní Asie
* Východní Asie
* Indie – střed
* Indie – jih
* Japonsko – východ
* Japonsko – západ
* Severní Evropa
* Západní Evropa
* Spojené království – jih
* Spojené království – západ
* Střední Kanada
* Kanada – východ
* Austrálie – východ
* Austrálie – jihovýchod

## <a name="interoperability"></a>Interoperabilita

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Otázka: Jak se aplikace připojuje k vyhrazenému modulu hardwarového zabezpečení?

Ke provádění kryptografických operací z vašich aplikací používáte klientské nástroje Gemalto za předpokladu, že je k dispozici pomocí sady HSM.You use Gemalto provided HSM client tools/SDK/software to perform cryptographic operations from your applications. Software je k dispozici na [portálu zákaznické podpory Gemalto](https://supportportal.gemalto.com/csm/). Zákazníci, kteří používají vyhrazenou službu hardwarového zabezpečení, musí být zaregistrováni pro podporu Gemalto a mít ID zákazníka, které umožňuje přístup a stahování příslušného softwaru.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Otázka: Může se aplikace připojit k vyhrazenému modulu hardwarového zabezpečení z jiné virtuální sítě v oblastech nebo napříč oblastmi?

Ano, budete muset použít [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) v rámci oblasti k navázání připojení mezi virtuálními sítěmi. Pro připojení mezi oblastmi je nutné použít [bránu VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Otázka: Lze synchronizovat vyhrazený modul hardwarového zabezpečení s místními moduly hardwarového zabezpečení?

Ano, můžete synchronizovat místní moduly hardwarového zabezpečení pomocí vyhrazeného modulu hardwarového zabezpečení. [Připojení k síti POINT-TO-point vpn nebo připojení bodu k síti](../vpn-gateway/vpn-gateway-about-vpngateways.md) lze použít k navázání připojení k místní síti.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Otázka: Můžu šifrovat data používaná jinými službami Azure pomocí klíčů uložených v vyhrazeném modulu hardwarového zabezpečení?

Ne. Azure Dedicated HSMs jsou přístupné jenom z uvnitř vaší virtuální sítě.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Otázka: Lze importovat klíče z existujícího místního modulu hardwarového zabezpečení do vyhrazeného modulu hardwarového zabezpečení?

Ano, pokud máte místní skryté servery hesmazívky Gemalto SafeNet. Existuje několik metod. Viz dokumentace Gemalto HSM.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Otázka: Jaké operační systémy jsou podporovány vyhrazeným klientským softwarem modulu hardwarového zabezpečení?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuální: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Otázka: Jak nakonfiguruji klientskou aplikaci tak, aby vytvořila konfiguraci s vysokou dostupností s více oddíly z více objektových počítačů s připojením k selhání?

Chcete-li mít vysokou dostupnost, je třeba nastavit konfiguraci klientské aplikace hsm pro použití oddílů z každého serveru zabezpečení zabezpečení. Podívejte se na dokumentaci ke klientnímu softwaru Gemalto HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Otázka: Jaké mechanismy ověřování jsou podporovány vyhrazeným modulu hardwarového zabezpečení?

Azure Dedicated HSM používá safenet sítě HSM 7 zařízení (model A790) a podporují ověřování na základě hesla.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Otázka: Jaké sady SDK, rozhraní API, klientský software je k dispozici pro použití s vyhrazeným modulem hardwarového zabezpečení?

PKCS#11, Java (JCA/JCE), Microsoft CAPI a CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Otázka: Můžu importovat nebo migrovat klíče z modulů hardwarového zabezpečení Luna 5/6 do modulů hardwarového zabezpečení Azure?

Ano. Podívejte se na průvodce migrací Gemalto. 

## <a name="using-your-hsm"></a>Používání hsm

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Otázka: Jak se rozhodnu, jestli použít Azure Key Vault nebo Azure Dedicated HSM?

Azure Dedicated HSM je vhodnou volbou pro podniky, které migrují do místních aplikací Azure, které používají moduly hardwarového zabezpečení. Vyhrazené moduly hardwarového zabezpečení představují možnost migrovat aplikaci s minimálními změnami. Pokud se kryptografické operace provádějí v kódu aplikace spuštěném ve virtuálním počítači Azure nebo webové aplikaci, můžou používat vyhrazený modul hardwarového zabezpečení. Obecně platí, že software zabalený do smršťování běžící v modelech IaaS (infrastruktura jako služba), které podporují moduly hardwarového zabezpečení jako úložiště klíčů, můžete použít modul y DSM, například bránu aplikace nebo správce provozu pro bezklíčový protokol SSL, ADCS (Služba Active Directory Certificate Services) nebo podobné nástroje Infrastruktury veřejných klíčů, nástroje nebo aplikace používané pro podepisování dokumentů, podepisování kódu nebo SQL Server (IaaS) nakonfigurovaný pomocí tde (transparentní šifrování databáze) s hlavním klíčem v modulu hardwarového zabezpečení pomocí zprostředkovatele EKM (rozšiřitelného klíče). Azure Key Vault je vhodný pro aplikace "born-in-cloud" nebo pro šifrování v klidovém stavu, kde jsou zákaznická data zpracovávána scénáři PaaS (platforma jako služba) nebo SaaS (Software jako služba), jako je klíč zákazníků Office 365, Azure Information Protection , Azure Disk Encryption, Azure Data Lake Store šifrování s klíčem spravovaným zákazníkem, šifrování Azure Storage s klíčem spravovaným zákazníkem a Azure SQL s klíčem spravovaným zákazníkem.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Otázka: Jaké scénáře použití nejlépe vyhovují vyhrazenému modulu hardwarového zabezpečení Azure?

Azure Dedicated HSM je nejvhodnější pro scénáře migrace. To znamená, že pokud migrujete místní aplikace do Azure, které už používají servery zabezpečení zabezpečení. To poskytuje možnost nízké tření pro migraci do Azure s minimálními změnami v aplikaci. Pokud se kryptografické operace provádějí v kódu aplikace spuštěném ve virtuálním počítači Azure nebo webové aplikaci, lze použít vyhrazený modul hardwarového zabezpečení. Obecně platí, že software zabalený do zmenšení spuštěný v modelech IaaS (infrastruktura jako služba), který podporuje servery hsm jako úložiště klíčů, může používat vyskladnění hsm, například:

* Aplikační brána nebo správce provozu pro bezklíčové SSL
* ADCS (služba ADCCertificate Services)
* Podobné nástroje PKI
* Nástroje/aplikace používané pro podepisování dokumentů
* Podepisování kódu
* SQL Server (IaaS) nakonfigurovaný s TDE (transparentní šifrování databáze) s hlavním klíčem v modulu hardwarového zabezpečení pomocí zprostředkovatele EKM (rozšiřitelné správy klíčů)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Otázka: Lze vyhrazený modul hardwarového zabezpečení používat s klíčem zákazníků Office 365, ochranou informací Azure, úložištěm datových jezer Azure, šifrováním disku, šifrováním úložiště Azure, Azure SQL TDE?

Ne. Vyhrazený modul hardwarového zabezpečení se zřápává přímo do privátního ip adresového prostoru zákazníka, takže není přístupný jiným službám Azure nebo Microsoftu.

## <a name="administration-access-and-control"></a>Správa, přístup a řízení

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Otázka: Má zákazník získat plnou výhradní kontrolu nad moduly hardwarového zabezpečení s vyhrazené moduly hardwarového zabezpečení?

Ano. Každé zařízení modulu hardwarového zabezpečení je plně vyhrazeno pro jednoho zákazníka a nikdo jiný nemá administrativní kontrolu po zřízení a změně hesla správce.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Otázka: Jakou úroveň přístupu má společnost Microsoft k mému hsm?

Společnost Microsoft nemá žádnou administrativní nebo kryptografickou kontrolu nad hsm. Společnost Microsoft má přístup na úrovni monitorování prostřednictvím připojení sériového portu k načtení základní telemetrie, jako je teplota a stav komponent. To společnosti Microsoft umožňuje poskytovat proaktivní oznámení o zdravotních problémech. V případě potřeby může zákazník tento účet deaktivovat.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>Otázka: Co je účet "tenantadmin" Microsoft používá, jsem zvyklý na to, že uživatel správce je "správce" na programových serverech hsm SafeNet?

Zařízení HSM je dodáváno s výchozím uživatelem správce s obvyklým výchozím heslem. Společnost Microsoft nechtěla mít výchozí hesla v provozu, zatímco jakékoli zařízení je ve fondu čeká na zřízení zákazníky. To by nesplňovalo naše přísné bezpečnostní požadavky. Z tohoto důvodu jsme nastavili silné heslo, které je zahozeno v době zřizování. Také v době zřizování vytvoříme nového uživatele v roli správce s názvem "tenantadmin". Tento uživatel má výchozí heslo a zákazníci to změnit jako první akce při prvním přihlášení do nově zřízeného zařízení. Tento proces zajišťuje vysoký stupeň bezpečnosti a zachovává náš slib výhradní administrativní kontroly pro naše zákazníky. Je třeba poznamenat, že "tenantadmin" uživatel lze použít k resetování hesla uživatele správce, pokud zákazník dává přednost použití tohoto účtu. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Otázka: Může Microsoft nebo kdokoli na přístupové klíče společnosti Microsoft v mém vyhrazené msm?

Ne. Společnost Microsoft nemá žádný přístup ke klíčům uloženým v vyhrazeném modulu hardwarového zabezpečení přiděleném zákazníkem.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Otázka: Mohu upgradovat software/firmware na softwarových softwarových omezeních?

Chcete-li získat nejlepší podporu, Společnost Microsoft důrazně doporučuje, aby upgrade softwaru nebo firmwaru na HSM. Zákazník však má plnou administrativní kontrolu včetně upgradu softwaru / firmwaru, pokud jsou vyžadovány specifické funkce z různých verzí firmwaru. Před provedením změn je třeba chápat důsledky, protože by to mohlo například ovlivnit stav potvrzený FIPS. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>Otázka: Jak mohu spravovat vyhrazený modul hardwarového zabezpečení?

Vyhrazené moduly hardwarového zabezpečení můžete spravovat tak, že k nim budete přistupovat pomocí ssh.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Otázka: Jak lze spravovat oddíly na vyhrazeném modulu hardwarového zabezpečení?

Klientský software Gemalto HSM se používá ke správě hmenea a oddílů.

### <a name="q-how-do-i-monitor-my-hsm"></a>Otázka: Jak lze sledovat svůj hsm?

Zákazník má plný přístup k protokolům aktivit hsm prostřednictvím syslog a SNMP. Zákazník bude muset nastavit server syslog nebo Server SNMP pro příjem protokolů nebo událostí z hsm.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Otázka: Lze získat úplný protokol přístupu všech operací modulu hardwarového zabezpečení z vyhrazeného modulu hardwarového zabezpečení?

Ano. Protokoly ze zařízení HSM můžete odesílat na server syslogu

## <a name="high-availability"></a>Vysoká dostupnost

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Otázka: Je možné nakonfigurovat vysokou dostupnost ve stejné oblasti nebo ve více oblastech?

Ano. Konfigurace a nastavení s vysokou dostupností jsou prováděny v klientském softwaru HSM poskytovaném společností Gemalto. Moduly zabezpečení zabezpečení ze stejné virtuální sítě nebo jiných virtuálních počítačů ve stejné oblasti nebo napříč oblastmi nebo v místních moduly zabezpečení připojení k síti připojení k virtuální síti pomocí sítě VPN nebo vpn z bodu do bodu lze přidat do stejné konfigurace s vysokou dostupností. Je třeba poznamenat, že to synchronizuje pouze materiál klíče a ne konkrétní položky konfigurace, jako jsou role.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Otázka: Můžu přidat moduly hardwarového zabezpečení z místní sítě do skupiny s vysokou dostupností pomocí vyhrazeného modulu hardwarového zabezpečení Azure?

Ano. Musí splňovat vysoké požadavky na dostupnost pro SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Otázka: Můžu přidat moduly hardwarového zabezpečení Luna 5/6 z místních sítí do skupiny s vysokou dostupností pomocí vyhrazeného modulu hardwarového zabezpečení Azure?

Ne.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Otázka: Kolik objekty zabezpečení lze přidat do stejné konfigurace s vysokou dostupností z jedné aplikace?

16 členů skupiny HA má nedostatečně pryč, full-throttle testování s vynikajícími výsledky.

## <a name="support"></a>Podpora

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Otázka: Co je sla pro vyhrazené služby modulu hardwarového zabezpečení?

Pro vyhrazené služby hardwarového zabezpečení není k dispozici žádná zvláštní záruka provozu. Společnost Microsoft zajistí přístup k zařízení na úrovni sítě, a proto platí standardní síťové licence Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Otázka: Jak se moduly hardwarového zabezpečení používané v azure dedicated hsm chráněné?

Datová centra Azure mají rozsáhlé fyzické a procedurální ovládací prvky zabezpečení. Kromě toho vyhrazené moduly hardwarového zabezpečení jsou hostovány v oblasti s přístupem k dalšímu omezenému přístupu v datovém centru. Tyto oblasti mají další fyzické kontroly přístupu a kamerový dohled pro větší bezpečnost.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Otázka: Co se stane, pokud dojde k narušení zabezpečení nebo události manipulace s hardwarem?

Vyhrazená služba hardwarového zabezpečení používá zařízení SafeNet Network HSM 7. Tato zařízení podporují fyzickou a logickou detekci manipulace. Pokud je někdy tamper událost hsm s automaticky nulovány.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Otázka: Jak zajistím, aby klíče v vyhrazených modulech hardwarového zabezpečení nebyly ztraceny kvůli chybě nebo škodlivému útoku zasvěcených osob?

Důrazně doporučujeme používat místní zařízení pro zálohování hsm k provádění pravidelné pravidelné zálohování hsm pro zotavení po havárii. Budete muset použít připojení VPN typu peer-to-peer nebo site-to-site k místní pracovní stanici připojené k zálohovacímu zařízení modulu hesm.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Otázka: Jak získám podporu pro vyhrazený modul hardwarového zabezpečení?

Podporu poskytuje společnost Microsoft i Společnost Gemalto.  Pokud máte problém s přístupem k hardwaru nebo síti, vznesete žádost o podporu u společnosti Microsoft a pokud máte problém s konfigurací modulu hardwarového zabezpečení, software mandatorní software a vývoj aplikací vyvoláte žádost o podporu s Gemalto. Pokud máte neurčený problém, požádejte o podporu společnost Microsoft a gemalto pak může být zapojeno podle potřeby. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>Otázka: Jak získám klientský software, dokumentaci a přístup k integračním pokynům pro bezpečnostní zdroj SafeNet Luna 7?

Po registraci ke službě bude poskytnuto zákaznické id Společnosti Gemalto, které umožňuje registraci na portálu zákaznické podpory Gemalto. To umožní přístup ke všem softwarem a dokumentaci, stejně jako povolení požadavků na podporu přímo s Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Otázka: Pokud byla nalezena chyba zabezpečení a společnost Gemalto vydala opravu, kdo je zodpovědný za upgrade/opravu operačního systému/firmwaru?

Společnost Microsoft nemá možnost připojit se k hmerám ům, které jsou zákazníkům přiděleny. Zákazníci musí upgradovat a opravovat své soubory hesm.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>Otázka: Co když potřebuji restartovat svůj hsm?

HSM má možnost restartování příkazového řádku, nicméně dochází k problémům s restartováním a z tohoto důvodu se doporučuje pro nejbezpečnější restartování, které vyvolá žádost o podporu se společností Microsoft, aby zařízení bylo fyzicky restartováno. 

## <a name="cryptography-and-standards"></a>Kryptografie a standardy

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Otázka: Je bezpečné ukládat šifrovací klíče pro nejdůležitější data ve vyhrazeném modulu hardwarového zabezpečení?

Ano, vyhrazená ustanovení modulu hardwarového zabezpečení zařízení SafeNet Network HSM 7, která používají moduly hardwarového zabezpečení fips 140-2 úrovně 3. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Otázka: Jaké kryptografické klíče a algoritmy jsou podporovány vyhrazeným modulem hardwarového zabezpečení?

Vyhrazená služba hardwarového zabezpečení zřazená zařízení SafeNet Network HSM 7. Podporují širokou škálu typů kryptografických klíčů a algoritmů, včetně: Full Suite B podpora

* Asymetrické:
  * RSA
  * Dsa
  * Diffie-Hellman
  * Eliptická křivka
  * Kryptografie (ECDSA, ECDH, Ed25519, ECIES) s pojmenovanými, uživatelem definovanými a brainpoolovými křivkami, KCDSA
* Symetrický:
  * AES-GCM
  * Trojitý DES
  * DES
  * ÁRIE, SEMENO
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Derivace kláves: Režim čítače SP800-108
  * Zabalení klíče: SP800-38F
  * Generování náhodných čísel: FIPS 140-2 schválený DRBG (režim CTR SP 800-90), vyhovující BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Otázka: Je vyhrazený HSM FIPS 140-2 Úroveň 3 ověřen?

Ano. Vyhrazená služba hardwarového zabezpečení zřazená zařízení SafeNet Network HSM 7, která používají moduly hardwarového zabezpečení fips 140-2 úrovně 3.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Otázka: Co musím udělat, abych se ujistil, že ovládám vyhrazené moduly hardwarového zabezpečení v ověřeném režimu FIPS 140-2 level 3?

Vyhrazené služby HSM zřazují zařízení SafeNet Luna Network HSM 7. Tato zařízení používají rsm s validovanými 140-2 úrovní 3. Výchozí nasazená konfigurace, operační systém a firmware jsou také ověřeny FIPS. Není nutné provádět žádné akce pro fips 140-2 úroveň 3 dodržování.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Otázka: Jak zákazník zajistí, že při zrušení zřízení objektu zabezpečení je vymazán veškerý materiál klíče?

Před vyžádáním zrušení zřízení musí mít zákazník vynulování hsm pomocí Gemalto za předpokladu, že nástroje klienta HSM.

## <a name="performance-and-scale"></a>Výkon a škálování

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Otázka: Kolik kryptografických operací je podporováno za sekundu pomocí vyhrazeného modulu hardwarového zabezpečení?

Vyhrazená ustanovení modulu hardwarového zabezpečení zařízení SafeNet Network HSM 7 (model A790). Zde je přehled maximálního výkonu pro některé operace: 

* RSA-2048: 10 000 transakcí za sekundu
* ECC P256: 20 000 transakcí za sekundu
* AES-GCM: 17 000 transakcí za sekundu

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Otázka: Kolik oddílů lze vytvořit v vyhrazeném modulu hardwarového zabezpečení?

Použitý model A790 SafeNet Luna HSM 7 obsahuje licenci na 10 oddílů v ceně služby. Zařízení má limit 100 oddílů a přidání oddílů až do tohoto limitu by mělo za následek dodatečné licenční náklady a vyžadovalo instalaci nového licenčního souboru v zařízení.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Otázka: Kolik klíčů může být podporováno v vyhrazeném modulu hardwarového zabezpečení?

Maximální počet tlačítek je funkce dostupné paměti. SafeNet Luna 7 model A790 v provozu má 32MB paměti. Následující čísla jsou také použitelná pro dvojice klíčů, pokud používáte asymetrické klíče.

* RSA-2048 - 19 000
* ECC-P256 - 91 000

Kapacita se bude lišit v závislosti na konkrétní chod klíčových atributů nastavených v šabloně generování klíče a počtu oddílů.
