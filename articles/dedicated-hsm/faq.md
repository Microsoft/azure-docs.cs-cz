---
title: Nejčastější dotazy – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Získejte odpovědi na běžné otázky týkající se modulu zabezpečení hardwaru v Azure, jako jsou základní informace, interoperabilita, vysoká dostupnost a podpora.
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: f6705b5f72aaa8f990aaf3b1daa33697b690d1f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608880"
---
# <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Získejte odpovědi na běžné otázky týkající se Microsoft Azure vyhrazeného modulu HSM.

## <a name="the-basics"></a>Základy

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Otázka: co je modul hardwarového zabezpečení (HSM)?

Modul hardwarového zabezpečení (HSM) je fyzické výpočetní zařízení, které slouží k ochraně a správě kryptografických klíčů. Klíče uložené v HSM lze použít pro kryptografické operace. Klíčový materiál zůstane bezpečně v neoprávněně odolném hardwarovém modulu. Modul hardwarového zabezpečení (HSM) povoluje použití klíčů pouze ověřeným a autorizovaným aplikacím. Materiál klíče nikdy neopouští hranici ochrany HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>Otázka: co je to vyhrazená nabídka HSM služby Azure?

Vyhrazený modul HARDWAROVÉho zabezpečení Azure je cloudová služba, která poskytuje HSM, která je hostovaná v datacentrech Azure, která jsou přímo připojená k virtuální síti zákazníka. Tyto HSM jsou vyhrazená síťová zařízení [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) . Nasazují se přímo do privátního adresního prostoru IP adres zákazníků a Microsoft nemá žádný přístup k kryptografické funkci HSM. Jenom zákazník má tato zařízení plnou správu a řízení kryptografie. Zákazníci zodpovídají za správu zařízení a můžou z jejich zařízení získat protokoly o všech činnostech. Vyhrazená HSM zákazníkům pomůžou splnit požadavky na dodržování předpisů, jako jsou FIPS 140-2 úrovně 3, HIPAA, PCI-DSS a eIDAS a spousta dalších.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>Otázka: Jaký hardware se používá pro vyhrazený modul HSM?

Microsoft spolupracuje se službou Thales, aby mohla doručovat službu HSM vyhrazenou pro Azure. Konkrétní zařízení, které používáte, je [Thales modelu HSM Luna 7 pro A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Toto zařízení neposkytuje pouze ověřený firmware [FIPS 140-2 úrovně 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) , ale nabízí nízkou latenci, vysoký výkon a vysokou kapacitu prostřednictvím 10 oddílů. 

### <a name="q-what-is-an-hsm-used-for"></a>Otázka: co je modul HARDWAROVÉho zabezpečení, který se používá pro?

HSM se používají k ukládání kryptografických klíčů, které se používají pro kryptografické funkce, jako je TLS (Transport Layer Security), šifrování dat, PKI (infrastruktura veřejných klíčů), DRM (Správa digitálních práv) a podepisování dokumentů.

### <a name="q-how-does-dedicated-hsm-work"></a>Otázka: jak vyhrazený modul HSM funguje?

Zákazníci mohou zřídit HSM v konkrétních oblastech pomocí prostředí PowerShell nebo rozhraní příkazového řádku. Zákazník určuje, ke které virtuální síti bude HSM připojen a jakmile bude HSM, bude k dispozici v určené podsíti v přiřazených IP adresách v adresním prostoru privátních IP adres zákazníka. Pak se zákazníci mohou připojit k HSM pomocí SSH pro správu a správu zařízení, nastavit připojení klientů HSM, inicializovat HSM, vytvářet oddíly, definovat a přiřazovat role, jako je například úředník oddělení, kryptografický pracovník a kryptografický uživatel. Zákazník pak bude k provádění kryptografických operací ze svých aplikací používat Thales klientské nástroje/sada SDK/software modulu HSM.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Otázka: Jaký software je součástí vyhrazené služby HSM?

Thales poskytuje veškerý software pro zařízení HSM po zřízení Microsoftu. Software je k dispozici na [portálu zákaznické podpory Thales](https://supportportal.thalesgroup.com/csm). Zákazníci, kteří používají vyhrazenou službu HSM, se musí zaregistrovat pro podporu Thales a mít ID zákazníka, které umožňuje přístup a stažení relevantního softwaru. Podporovaný klientský software je verze 7,2, která je kompatibilní se ověřeným firmwarem verze 7.0.3 standardu FIPS 140-2 úrovně 3. 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>Otázka: Jaké dodatečné náklady mohou být účtovány pomocí vyhrazené služby HSM?

Při používání vyhrazené služby HSM se budou účtovat tyto položky navíc. 
* Použití vyhrazeného místního zálohovacího zařízení je možné použít u vyhrazené služby HSM, ale bude to mít za následek další náklady a mělo by být přímo navázáno z Thales.
* Vyhrazený modul HARDWAROVÉho zabezpečení je k dispozici s licencí 10 partition. Pokud zákazník vyžaduje více oddílů, bude se za další licence účtovat další poplatky přímo ze Thales.
* Vyhrazený modul HARDWAROVÉho zabezpečení vyžaduje síťovou infrastrukturu (virtuální síť, VPN Gateway atd.) a prostředky, jako jsou virtuální počítače pro konfiguraci zařízení. Tyto další zdroje se účtují za mimořádné náklady a nejsou zahrnuté do vyhrazených cen služby HSM.

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>Otázka: nabízí vyhrazený modul HSM založený na heslech a ověřování založené na PED?

V současnosti poskytuje Azure vyhrazené HSM jenom HSM s ověřováním pomocí hesla.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>Otázka: bude pro mě HSM vyhrazený hostitel modulu HSM pro Azure?

Microsoft nabízí jenom Thales model HSM Luna 7 s použitím vyhrazené služby HSM a nemůže hostovat zařízení poskytovaná zákazníky.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>Otázka: podporuje funkce služby pro platby ve službě HSM vyhrazené modulem pro zabezpečení (PIN/EFT) Azure.

Vyhrazená služba HSM Azure používá Thales Luna 7 HSM. Tato zařízení nepodporují funkce pro platební modul HSM specifické pro modul HSM (například PIN nebo EFT) nebo certifikace. Pokud chcete, aby služba HSM vyhrazená službou Azure podporovala platební HSM v budoucnu, předejte zpětnou vazbu vašemu zástupci účtu Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>Otázka: u kterých oblastí Azure je dostupný vyhrazený modul HARDWAROVÉho zabezpečení?

Od 13. března 2019 je vyhrazený modul HSM dostupný ve 14 oblastech uvedených níže. Další oblasti jsou plánovány a mohou být popsány prostřednictvím zástupce účtu společnosti Microsoft.

* East US
* USA – východ 2
* USA – západ
* Západní USA 2
* Středojižní USA
* Southeast Asia
* Východní Asie
* Indie – střed
* Indie – jih
* Japonsko – východ
* Japonsko – západ
* Severní Evropa
* West Europe
* Spojené království – jih
* Spojené království – západ
* Střední Kanada
* Kanada – východ
* Austrálie – východ
* Austrálie – jihovýchod
* Švýcarsko – sever
* Švýcarsko – západ
* USA (Gov) – Virginia
* USA (Gov) – Texas

## <a name="interoperability"></a>Vzájemná funkční spolupráce

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Otázka: jak se moje aplikace připojí k vyhrazené HSM?

K provádění kryptografických operací z vašich aplikací se používají Thales klientské nástroje/sada SDK/software modulu HSM. Software je k dispozici na [portálu zákaznické podpory Thales](https://supportportal.thalesgroup.com/csm). Zákazníci, kteří používají vyhrazenou službu HSM, se musí zaregistrovat pro podporu Thales a mít ID zákazníka, které umožňuje přístup a stažení relevantního softwaru.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Otázka: může se aplikace připojit k vyhrazenému HSM z jiné virtuální sítě v rámci nebo napříč oblastmi?

Ano, budete muset v rámci jedné oblasti použít [partnerský vztah](../virtual-network/virtual-network-peering-overview.md) virtuálních sítí, aby bylo možné navázat připojení mezi virtuálními sítěmi. Pro připojení mezi jednotlivými oblastmi je nutné použít [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Otázka: mohu synchronizovat vyhrazený modul HARDWAROVÉho zabezpečení s místními HSM?

Ano, místní HSM můžete synchronizovat s vyhrazeným HSM. Připojení [typu Point-to-Point VPN nebo Point-to-site](../vpn-gateway/vpn-gateway-about-vpngateways.md) lze použít k navázání připojení k místní síti.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Otázka: můžu šifrovat data používaná jinými službami Azure pomocí klíčů uložených v vyhrazeném modulu HSM?

No. Vyhrazené HSM Azure jsou dostupné jenom v rámci vaší virtuální sítě.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Otázka: mohu importovat klíče z existujícího místního modulu HARDWAROVÉho zabezpečení do vyhrazeného modulu HSM?

Ano, pokud máte místní Thales Luna 7 HSM. Existuje více metod. Informace najdete v [dokumentaci modulu HSM pro Thales](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Otázka: Jaké operační systémy podporuje vyhrazený klientský software HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuální: VMware, Hyper-V, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Otázka: Návody nakonfigurovat klientskou aplikaci, aby vytvořila konfiguraci s vysokou dostupností s více oddíly z více HSM?

Pokud chcete mít vysokou dostupnost, musíte nastavit konfiguraci klientské aplikace HSM tak, aby používala oddíly z každého HSM. Informace najdete v dokumentaci k klientskému softwaru Thales HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Otázka: Jaké mechanismy ověřování podporuje vyhrazený modul HSM?

Vyhrazený modul HARDWAROVÉho zabezpečení Azure používá zařízení [A790 modelu Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) a podporuje ověřování pomocí hesla.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Otázka: Jaké sady SDK, rozhraní API a klientský software jsou k dispozici pro použití s vyhrazeným modulem HSM?

PKCS # 11, Java (JCA/JCE), Microsoft CAPI a CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Otázka: mohu importovat/migrovat klíče z Luna 5/6 HSM do Azure vyhrazené HSM?

Ano. Obraťte se na příslušného zástupce Thales a sdělte příslušné příručce k migraci Thales. 

## <a name="using-your-hsm"></a>Použití modulu HARDWAROVÉho zabezpečení

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Otázka: Návody rozhodnout, jestli chcete používat Azure Key Vault nebo vyhrazený modul HSM pro Azure?

Vyhrazený modul HARDWAROVÉho zabezpečení Azure je vhodný volbou pro podniky, které migrují na místní aplikace Azure, které používají HSM. Vyhrazená HSM prezentují možnost Migrace aplikace s minimálními změnami. Pokud se kryptografické operace provádějí v kódu aplikace běžícím na virtuálním počítači Azure nebo ve webové aplikaci, můžou použít vyhrazený HSM. Obecně zúžený software, který běží v modelech IaaS (infrastruktura jako služba), který podporuje HSM jako úložiště klíčů, může použít vyhradit modul hardwarového zabezpečení (HSM), například služba Application Gateway nebo Traffic Manager pro bez klíčů TLS, ASSD (Active Directory Certificate Services) nebo podobné nástroje PKI, nástroje/aplikace používané k podepisování dokumentů, podepisování kódu nebo SQL Server (IaaS) nakonfigurovaná s TDE (transparentní šifrování databáze) pomocí hlavního klíče v modulu HARDWAROVÉho zabezpečení s použitím poskytovatele EKM (Extensible Key Management). Azure Key Vault je vhodný pro aplikace typu "v rámci cloudu" nebo pro šifrování ve scénářích REST, kde jsou zákaznická data zpracovávána scénáři PaaS (platforma jako služba) nebo SaaS (software jako služba), jako je například klíč zákazníka Office 365, Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store šifrování pomocí klíče spravovaného zákazníkem, Azure Storage šifrování pomocí spravovaného klíče zákazníka a Azure SQL se spravovaným klíčem zákazníka.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Otázka: Jaké scénáře použití nejlépe vyhovují vyhrazenému modulu HSM Azure?

Vyhrazený modul HARDWAROVÉho zabezpečení Azure je vhodný pro scénáře migrace. To znamená, že pokud migrujete místní aplikace do Azure, které už používají HSM. Díky tomu je k dispozici bezproblémová možnost migrace do Azure s minimálními změnami aplikace. Pokud se kryptografické operace provádějí v kódu aplikace běžícím na virtuálním počítači Azure nebo ve webové aplikaci, může se použít vyhrazený modul HSM. Obecně zúžený software běžící v IaaS (infrastruktura jako služba), který podporuje HSM jako úložiště klíčů, může použít vyhradit modul hardwarového zabezpečení (HSM), například:

* Application Gateway nebo Traffic Manager pro bez klíčů TLS
* ASSD (Active Directory Certificate Services)
* Podobné nástroje PKI
* Nástroje/aplikace používané pro podepisování dokumentů
* Podepisování kódu
* SQL Server (IaaS) nakonfigurované s TDE (transparentní šifrování databáze) pomocí hlavního klíče v modulu HARDWAROVÉho zabezpečení s použitím poskytovatele EKM (Extensible Key Management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Otázka: je možné použít vyhrazený modul HARDWAROVÉho zabezpečení s klíčem zákazníka Office 365, Azure Information Protection, Azure Data Lake Store, šifrování disku, Azure Storage šifrování, Azure SQL TDE?

No. Vyhrazený modul HARDWAROVÉho zabezpečení se zřizuje přímo v adresním prostoru privátních IP adres zákazníka, takže není přístupný pro jiné služby Azure nebo Microsoft.

## <a name="administration-access-and-control"></a>Správa, přístup a řízení

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>Otázka: provede zákazníka úplnou kontrolu nad HSM a vyhrazeným '?

Ano. Každé zařízení HSM je plně vyhrazené jednomu zákazníkovi a nikdo jiný nemá administrativní řízení, když se zřídí a heslo správce se změnilo.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Otázka: jakou úroveň přístupu Microsoft potřebuje k mému HARDWAROVÉmu zabezpečení?

Společnost Microsoft nemá žádné administrativní ani kryptografické kontroly nad modulem HSM. K načtení základní telemetrie, jako je třeba teplota a stav komponenty, má společnost Microsoft přístup na úrovni monitoru prostřednictvím připojení sériového portu. Díky tomu může společnost Microsoft poskytovat proaktivní oznámení o problémech se stavem. V případě potřeby může zákazník tento účet zakázat.

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>Otázka: co je účet správce tenanta, který Microsoft používá, používám uživatele s oprávněními správce na Thales Luna HSM?

Zařízení HSM se dodává s výchozím uživatelem správce s obvyklým výchozím heslem. Microsoft nechce používat výchozí hesla, protože jakékoli zařízení je ve fondu, který čeká na zřízení zákazníky. To by nesplňovalo naše striktní požadavky na zabezpečení. Z tohoto důvodu jsme nastavili silné heslo, které se v době zřizování zahodí. V době zřizování vytvoříme také nového uživatele v roli správce s názvem "správce tenanta". Tento uživatel má výchozí heslo a zákazníci ho při prvním přihlášení k nově zřízenému zařízení změní jako první akci. Tento proces zajišťuje vysoké stupně zabezpečení a udržuje pro naše zákazníky náš příslib pro správu s jediným řízením. Je třeba poznamenat, že uživatel s oprávněními správce klienta může použít k resetování hesla uživatele správce, pokud zákazník upřednostňuje použití tohoto účtu. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Otázka: v mém vyhrazeném modulu HSM může společnost Microsoft nebo kdokoli na klíčích Microsoft Access.

No. Společnost Microsoft nemá žádný přístup k klíčům uloženým ve vyhrazeném modulu HARDWAROVÉho zabezpečení přiděleného zákazníky.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Otázka: můžu upgradovat software/firmware na HSM přidělený mně?

Pokud se vyžadují konkrétní funkce z různých verzí firmwaru, má zákazník úplný ovládací prvek pro správu včetně upgradu softwaru/firmwaru. Než provedete změny, kontaktujte prosím Microsoft o upgradu kontaktováním HSMRequest@microsoft.com  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>Otázka: Návody Správa vyhrazeného modulu HARDWAROVÉho zabezpečení?

Vyhrazenou HSM můžete spravovat tak, že k nim přistupujete pomocí SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Otázka: Návody Správa oddílů ve vyhrazeném modulu HSM?

Klientský software Thales HSM se používá ke správě HSM a oddílů.

### <a name="q-how-do-i-monitor-my-hsm"></a>Otázka: Návody monitorování modulu HARDWAROVÉho zabezpečení?

Zákazník má úplný přístup k protokolům aktivit HSM prostřednictvím protokolu syslog a SNMP. Zákazník bude muset nastavit server syslog nebo server SNMP pro příjem protokolů nebo událostí z HSM.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Otázka: mohu získat úplný přístup ke všem operacím HSM z vyhrazeného modulu HSM?

Ano. Protokoly ze zařízení HSM můžete odeslat na server syslog.

## <a name="high-availability"></a>Vysoká dostupnost

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Otázka: je možné nakonfigurovat vysokou dostupnost ve stejné oblasti nebo ve více oblastech?

Ano. Konfigurace a nastavení vysoké dostupnosti se provádí v klientském softwaru HSM, který poskytuje Thales. HSM ze stejné virtuální sítě nebo jiného virtuální sítě ve stejné oblasti nebo v různých oblastech nebo místní HSM připojená k virtuální síti pomocí VPN typu Site-to-site nebo Point-to-Point VPN je možné přidat do stejné konfigurace vysoké dostupnosti. Je potřeba poznamenat, že tato funkce synchronizuje jenom materiál klíče, a ne konkrétní položky konfigurace, jako třeba role.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Otázka: mohu přidat HSM z místní sítě do skupiny vysoké dostupnosti pomocí vyhrazeného modulu HARDWAROVÉho zabezpečení Azure?

Ano. Musí splňovat požadavky vysoké dostupnosti pro [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Otázka: mohu přidat Luna 5/6 HSM z místních sítí do skupiny vysoké dostupnosti pomocí vyhrazeného modulu HARDWAROVÉho zabezpečení Azure?

No.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Otázka: kolik HSM můžu přidat ke stejné konfiguraci vysoké dostupnosti z jedné aplikace?

16 členů skupiny HA má zcela neomezený test s vynikajícími výsledky.

## <a name="support"></a>Podpora

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Otázka: jaká je smlouva SLA pro vyhrazenou službu HSM?

Pro vyhrazenou službu HSM není poskytnuta žádná konkrétní záruka na provozní čas. Microsoft zajistí přístup k zařízení na úrovni sítě, a proto se použijí standardní síťové Slay Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Otázka: jak se HSM používá ve vyhrazeném modulu HSM chráněným v Azure?

Datacentra Azure mají rozsáhlou fyzickou a procedurální kontrolu zabezpečení. Kromě toho, že vyhrazené HSM jsou hostované v oblasti s omezeným přístupem v datacentru. Tyto oblasti mají další ovládací prvky fyzického přístupu a monitorování videokamery pro zvýšení zabezpečení.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Otázka: co se stane, pokud dojde k narušení zabezpečení nebo události manipulace s hardwarem?

Vyhrazená služba HSM používá zařízení [HSM Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) . Tato zařízení podporují detekci fyzických a logických zásahů. Pokud dojde k nějaké neoprávněné události, HSM se automaticky vyhodnotí jako nuly.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Otázka: Návody zajistěte, aby se klíče v mých vyhrazených HSM ztratily kvůli chybě nebo škodlivému útoku Insider?

Pro účely pravidelného periodického zálohování HSM pro zotavení po havárii se důrazně doporučuje použít místní zálohovací zařízení HSM. K místní pracovní stanici připojené k zálohovacímu zařízení HSM budete muset použít připojení VPN typu peer-to-peer nebo site-to-site.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Otázka: Návody získat podporu pro vyhrazený modul HSM?

Podpora je poskytována společností Microsoft i Thales.  Pokud máte problém s hardwarem nebo přístupem k síti, vyvolejte žádost o podporu od Microsoftu a pokud máte problém s konfigurací HSM, softwarem a vývojem aplikací, vyvolejte žádost o podporu s Thales. Pokud máte neurčený problém, vyvolejte žádost o podporu od Microsoftu a pak Thales můžete podle potřeby. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-luna-7-hsm"></a>Otázka: Návody získat klientský software, dokumentaci a přístup k pokynům k integraci pro modul HSM pro Thales Luna 7?

Po registraci služby se poskytne ID zákazníka Thales, které umožňuje registraci na portálu zákaznické podpory Thales. Tím povolíte přístup ke všem softwareům a dokumentaci a zároveň povolíte žádosti o podporu přímo v Thales.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>Otázka: v případě, že dojde k chybě zabezpečení a byla vydána oprava Thales, která zodpovídá za upgrade/opravy operačního systému/firmwaru?

Microsoft není schopen se připojit k HSM přidělenému zákazníkům. Zákazníci musí upgradovat a opravovat své HSM.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>Otázka: Co když potřebuji restartovat modul HARDWAROVÉho zabezpečení?

Modul hardwarového zabezpečení (HSM) obsahuje možnost restartování z příkazového řádku, ale došlo k potížím, když restart přestane reagovat občas a z tohoto důvodu se doporučuje pro nejbezpečnější restart, který vyžádáte, aby se zařízení fyzicky restartovalo. 

## <a name="cryptography-and-standards"></a>Kryptografie a standardy

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Otázka: je bezpečné ukládat šifrovací klíče pro moje nejdůležitější data do vyhrazeného modulu HSM?

Ano, vyhrazená ustanovení HSM Thales Luna 7 HSM, která jsou ověřená [na úrovni FIPS 140-2 úrovně 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) . 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Otázka: Jaké kryptografické klíče a algoritmy podporuje vyhrazený modul HSM?

Vyhrazená služba HSM zřídí zařízení Thales Luna 7 HSM. Podporují celou škálu typů kryptografických klíčů a algoritmy, mezi které patří: plná podpora Suite B.

* Symetrick
  * RSA
  * DSA
  * Diffie-Hellman
  * Eliptická křivka
  * Kryptografie (ECDSA, ECDH, Ed25519, ECIES) s pojmenovanými, uživatelsky definovanými a Brainpoolmi křivkami, KCDSA
* Pás
  * AES – GCM
  * Triple DES
  * DES
  * ARIA, SEMENA
  * RC2
  * ŠIFR
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Odvození klíče: režim čítače SP 800-108
  * Zabalení klíče: SP 800 – 38F
  * Generování náhodného čísla: DRBG schválené pro FIPS 140-2 (SP 800-90. režim), vyhovuje dokument BSI O DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Otázka: je ověřena rezervovaná úroveň Standard HSM FIPS 140-2 úrovně 3?

Ano. Vyhrazená služba HSM zřídí [Thales Luna 7 modelu HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) , která jsou ověřená [na úrovni FIPS 140-2 úrovně 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) .

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Otázka: co potřebuji k tomu, abyste se ujistili, že provozovat vyhrazený HSM v režimu ověřování FIPS 140-2 Level 3?

Vyhrazená služba HSM zřídí zařízení Thales Luna 7 HSM. Tato zařízení mají HSM ověřený Standard FIPS 140-2 úrovně 3. Výchozí nasazená konfigurace, operační systém a firmware jsou taky ověřené standardem FIPS. Pro dodržování předpisů FIPS 140-2 úrovně 3 není nutné provádět žádnou akci.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Otázka: jak zákazník zaručí, že když dojde ke zrušení zřízení modulu HARDWAROVÉho zabezpečení, dojde k vymazání všech klíčových materiálů?

Než si vyžádáte zrušení zřízení, zákazník musí vyhodnotit modul HARDWAROVÉho zabezpečení s použitím Thales poskytovaných klientskými nástroji HSM.

## <a name="performance-and-scale"></a>Výkon a škálování

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Otázka: kolik kryptografických operací se podporuje za sekundu pomocí vyhrazeného modulu HSM?

Vyhrazená ustanovení HSM Thales Luna 7 HSM. Zde je souhrn maximálního výkonu pro některé operace: 

* RSA-2048:10 000 transakcí za sekundu
* ECC P256:20 000 transakcí za sekundu
* AES-GCM: 17 000 transakcí za sekundu

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Otázka: kolik oddílů lze vytvořit v vyhrazeném modulu HSM?

[Thales Luna 7 modelu HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) zahrnuje licenci na 10 oddílů za cenu služby. Zařízení má omezení 100 oddílů a přidávání oddílů do tohoto limitu by znamenalo dodatečné licenční náklady a vyžadovalo instalaci nového licenčního souboru na zařízení.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Otázka: kolik klíčů je možné podporovat v vyhrazeném modulu HSM?

Maximální počet klíčů je funkce dostupné paměti. SafeNet Luna 7 modelu A790 používá má 32MB paměti. Následující čísla se vztahují také na páry klíčů, pokud používáte asymetrické klíče.

* RSA-2048 – 19 000
* ECC-P256-91 000

Kapacita se bude lišit v závislosti na konkrétních atributech klíče nastavených v šabloně generování klíčů a na počtu oddílů.
