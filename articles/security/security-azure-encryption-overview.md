---
title: Přehled Azure šifrování | Microsoft Docs
description: Další informace o různých možností šifrování v Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 00c8b30b5351b7a6e4388b186fab70e3a3357ef4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-encryption-overview"></a>Přehled Azure šifrování

Tento článek obsahuje přehled použití šifrování v Microsoft Azure. Pokrývá hlavními oblastmi šifrování, včetně šifrování v klidovém stavu, šifrování v cestě a správu klíčů s Azure Key Vault. Každá část obsahuje odkazy na podrobnější informace.

## <a name="encryption-of-data-at-rest"></a>Šifrování neaktivních uložených dat

Data v klidovém stavu zahrnují informace, které se nachází v trvalé úložiště na fyzickém médiu v libovolném digitální formátu. Médium může obsahovat soubory na magnetické nebo optické média, Archivovaná data a data zálohování. Microsoft Azure nabízí celou řadu řešení úložiště dat pro různé účely, včetně souborů, disku, objektů blob a úložiště table. Společnost Microsoft poskytuje také šifrování k ochraně [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [Azure Cosmos DB](../cosmos-db/introduction.md)a Azure Data Lake.

Šifrování dat v klidovém stavu k dispozici pro služby napříč software jako služba (SaaS), platformy jako služba (PaaS) a infrastruktury jako cloudovou modely služby (IaaS). Tento článek shrnuje a obsahuje materiály, které vám pomohou při použití možnosti Azure šifrování.

Podrobnější informace o tom, jak je šifrované data umístěná v Azure, najdete v části [šifrování na Rest Azure dat](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modely Azure šifrování

Azure podporuje různé modely šifrování, včetně šifrování na straně serveru, používající službu spravovat klíče, klíče spravovaného zákazníkem v Key Vault nebo klíče spravovaného zákazníkem na hardwaru řízenou zákazníka. Pomocí šifrování na straně klienta můžete spravovat a ukládání klíčů v místě nebo v jiném zabezpečené umístění.

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Šifrování na straně klienta se provádí mimo Azure. Obsahuje:

- Data šifrovat aplikace, která běží v datovém centru zákazníka nebo aplikace služby.
- Data, která už je šifrovaný při obdržení Azure.

Poskytovatele cloudových služeb pomocí šifrování na straně klienta, nebudete mít přístup k šifrovacím klíčům a nemůže data dešifrovat. Je-li zachovat úplnou kontrolu nad klíče.

### <a name="server-side-encryption"></a>Šifrování na straně serveru

Tři modely šifrování na straně serveru nabízejí různé správy klíčů charakteristiky, které můžete vybrat podle svých požadavků:

- **Službu spravovat klíče**: poskytuje kombinaci řízení a usnadnění práce s nízkou režii.

- **Spravované zákazníkem klíče**: poskytuje možnost ovládat klíčů, včetně podpory přineste si vlastní klíče (BYOK), nebo vám umožní generovat nové.

- **Službu spravovat klíče v řízené zákazníka hardwaru**: umožňuje spravovat klíče ve vaší vlastní úložiště, mimo dosah Microsoft. Tato vlastnost se nazývá hostitele si vlastní klíč (HYOK). Však konfigurace je složitá a nejvíce Azure services nepodporují tento model.

### <a name="azure-disk-encryption"></a>Azure disk encryption

Virtuální počítače Windows a Linux můžete chránit pomocí [Azure disk encryption](azure-security-disk-encryption.md), které používá [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technologie a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) k ochraně obě disky operačního systému a datové disky s šifrování celého svazku.

Šifrování klíče a tajné klíče jsou chráněné v vaše [předplatné Azure Key Vault](../key-vault/key-vault-whatis.md). Pomocí služby zálohování Azure můžete zálohování a obnovení šifrovaných virtuálních počítačů (VM), které používají konfiguraci klíč šifrovacích klíčů (KEK).

### <a name="azure-storage-service-encryption"></a>Šifrování služby Azure Storage

Data uložená v Azure Blob storage a sdílené složky Azure můžete šifrovat způsoby jak na straně serveru a na straně klienta.

[Šifrování služby Azure Storage (SSE)](../storage/common/storage-service-encryption.md) můžete automaticky šifrovat data předtím, než je uložena a automaticky dešifruje data po načtení. Proces je pro uživatele zcela transparentní. Šifrování služby úložiště používá 256 bitů [Advanced Encryption (Standard AES) šifrování](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedno z nejsilnější šifry bloku, která je k dispozici. AES transparentně zpracovává šifrování, dešifrování a správu klíčů.

### <a name="client-side-encryption-of-azure-blobs"></a>Šifrování na straně klienta objektů BLOB Azure

Můžete provádět klienta objektů BLOB šifrování Azure různými způsoby.

Klientská knihovna pro úložiště Azure pro balíček NuGet pro rozhraní .NET můžete použít k šifrování dat v rámci vaší klientské aplikace před jejich odesláním do úložiště Azure.

Další informace o a stáhnout Klientská knihovna pro úložiště Azure pro balíček NuGet pro rozhraní .NET najdete v tématu [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Při použití šifrování na straně klienta s Key Vault, vaše data se šifrují pomocí jednorázové symetrického obsahu šifrovací klíč (CEK) generovaný klientem úložiště Azure SDK. CEK se šifrují pomocí klíč šifrovacích klíčů (KEK), které mohou být buď symetrický klíč nebo pár asymetrických klíčů. Můžete ho spravovat místně nebo jej uložte v Key Vault. Šifrovaná data se pak nahrán do úložiště Azure.

Další informace o šifrování na straně klienta s Key Vault a začít pracovat s postupy najdete v tématu [kurz: šifrování a dešifrování objektů BLOB v Azure Storage pomocí Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Nakonec můžete taky Klientská knihovna pro úložiště Azure pro jazyk Java k provedení šifrování na straně klienta, před nahráním dat do služby Azure Storage a k dešifrování dat při stahování do klienta. Tato knihovna také podporuje integraci s [Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Šifrování dat v klidovém stavu s Azure SQL Database

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) je služba pro obecné účely relační databáze v Azure, který podporuje struktury například relačních dat, formát JSON, prostorových a XML. Databáze SQL podporuje šifrování na straně serveru pomocí funkce transparentní šifrování šifrování dat (TDE) i šifrování na straně klienta pomocí funkce Always Encrypted.

#### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

[Šifrování TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) se používá k šifrování [systému SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), a [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) datových souborů v reálném čase, pomocí databáze šifrovací klíč (DEK) , který je uložen v spouštěcí záznam databáze dostupnosti během obnovení.

Šifrování TDE chrání data a soubory protokolu, pomocí standardu AES a Triple Data Encryption Standard (3DES) šifrovacích algoritmů. Šifrování souboru databáze se provádí na úrovni stránky. Stránky v databázi chráněnou zašifrována předtím, než se zapisují na disk a jsou dešifrovat, pokud jste načtení do paměti. Ve výchozím nastavení na nově vytvořené databáze Azure SQL je teď povolené šifrování TDE.

#### <a name="always-encrypted-feature"></a>Vždy šifrovaný funkce

Pomocí [vždy šifrována](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkce v Azure SQL můžete šifrovat data v rámci klientské aplikace před ukládání do Azure SQL Database. Můžete také povolit delegování správy místní databáze třetím stranám a udržovat oddělení od uživatelů, kteří vlastní a můžete zobrazit data uživatelů, kteří ho spravovat, ale by neměl mít přístup k němu.

#### <a name="cell-level-or-column-level-encryption"></a>Šifrování na úrovni buněk nebo sloupce

S Azure SQL Database můžete použít symetrického šifrování pro sloupce dat pomocí jazyka Transact-SQL. Tato metoda se nazývá [úrovni buněk šifrování nebo šifrování na úrovni sloupce (Vymazat)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), protože slouží k šifrování i konkrétní buněk dat nebo konkrétní sloupce s různými šifrovacími klíči. Díky tomu získáte podrobnější možnosti šifrování než šifrování TDE, který šifruje data stránky.

VY obsahuje integrované funkce, které můžete použít k šifrování dat pomocí buď symetrický, nebo asymetrických klíčů a veřejný klíč certifikátu nebo přístupové heslo pomocí 3DES.

### <a name="cosmos-db-database-encryption"></a>Šifrování databáze cosmos DB

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) je globálně distribuované a více modelech databáze společnosti Microsoft. Ve výchozím nastavení je zašifrovaná data uživatele uložená v Cosmos DB ve stálé úložiště (jednotky SSD). Neexistují žádná opatření, můžete zapnout nebo vypnout. Šifrování v klidovém stavu je implementována pomocí několika technologiemi zabezpečení, včetně systémů zabezpečeného úložiště klíčů, šifrované sítí a kryptografické rozhraní API. Šifrovací klíče se spravují přes Microsoft a otáčejí na interní pokyny společnosti Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Šifrování na rest v Data Lake

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) je úložiště celopodnikové každý typ dat shromažďovaných na jednom místě před žádné formální definice požadavků nebo schéma. Data Lake Store podporuje "na standardně" transparentní šifrování dat v klidovém stavu, která se nastavuje během vytváření účtu. Ve výchozím nastavení Azure Data Lake Store spravuje klíče za vás, ale máte možnost spravovat sami.

Tři typy klíčů se používají v šifrování a dešifrování dat: hlavní šifrovací klíč (MEK), datový šifrovací klíč (DEK) a bloku šifrovací klíč (BEK). MEK se používá k šifrování klíče DEK, který je uložený na trvalé média, a BEK je odvozená od klíč DEK a datového bloku. Pokud spravujete vlastní klíče, můžete MEK otočit.

## <a name="encryption-of-data-in-transit"></a>Šifrování dat při přenosu

Azure nabízí mnoho mechanismy pro zachování dat privátní při jejich přesunu z jednoho umístění do druhého.

### <a name="tlsssl-encryption-in-azure"></a>Protokol TLS/SSL šifrování v Azure

Společnost Microsoft používá [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) protokol (TLS) k ochraně dat cestách mezi cloudové služby a zákazníků. Datových centrech společnosti Microsoft vyjednávání TLS připojení s klientské systémy, které se připojují ke službám Azure. Protokol TLS poskytuje silné ověřování, ochrana soukromí zpráv a integritu (povolení detekce zpráva manipulaci, zachycení a padělání), vzájemná funkční spolupráce, flexibilita algoritmu a snadné nasazení a používání.

[Ideální Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) chrání připojení mezi klientské systémy zákazníků a cloudové služby společnosti Microsoft podle jedinečné klíče. Připojení pomocí také délek klíčů pro šifrování RSA podle 2 048 bitů. Tato kombinace znesnadňuje někomu k zachycení a přístup k datům, která je při přenosu.

### <a name="azure-storage-transactions"></a>Azure transakcí úložiště

Pokud při práci s Azure Storage prostřednictvím portálu Azure, všechny transakce proběhnout přes protokol HTTPS. Také můžete rozhraní API REST úložiště přes protokol HTTPS pro interakci s Azure Storage. Při volání rozhraní REST API pro přístup k objektům v účtech úložiště, povolením bezpečnému přenosu, které je nutné pro účet úložiště, můžete vynutit používání protokolu HTTPS.

Sdílené přístupové podpisy ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), který slouží k delegovat přístup k objektům Azure Storage, zahrnují možnost určit, že při použití sdílené přístupové podpisy lze použít pouze protokol HTTPS. Tento přístup zajišťuje, že kdokoliv, kdo odešle propojení s tokeny SAS používá protokol správné.

[Protokol SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), který používá pro přístup k Azure Files sdílených složek, podporuje šifrování ale je k dispozici v systému Windows Server 2012 R2, Windows 8, Windows 8.1 a Windows 10. To umožňuje přístup mezi oblastmi a dokonce přejít na ploše.

Šifrování na straně klienta šifruje data před odesláním k vaší instanci Azure Storage, aby při přenosu v síti je zašifrovaná.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Šifrování paketů SMB přes virtuální sítě Azure 

Pomocí [protokolu SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) ve virtuálních počítačích se systémem Windows Server 2012 nebo novější, můžete provést datové přenosy zabezpečení tím, že šifrování dat při přenosu přes virtuálních sítí Azure. Šifrování dat, můžete ochránit proti manipulaci s obsahem a útokům odposlechu. Správci mohou povolit šifrování protokolu SMB pro celý server nebo jenom určité sdílené složky.

Po zapnutí šifrování protokolu SMB pro sdílenou složku nebo server, ve výchozím nastavení, mohou pouze klienti SMB 3.0 pro přístup ke složkám šifrované.

## <a name="in-transit-encryption-in-vms"></a>Šifrování během přenosu ve virtuálních počítačích

Data při přenosu do z a mezi virtuálními počítači se systémem Windows je zašifrovaná různými způsoby v závislosti na povaze připojení.

### <a name="rdp-sessions"></a>Relace RDP

Můžete se připojit a přihlásit se k virtuálnímu počítači pomocí [protokol RDP (Remote Desktop)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) z klientského počítače Windows nebo z Mac s klientem RDP nainstalována. Data při přenosu přes síť v relace RDP může být chráněna TLS.

Můžete taky vzdálené plochy pro připojení k virtuální počítač s Linuxem v Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Zabezpečený přístup k virtuální počítače s Linuxem pomocí protokolu SSH

Pro vzdálenou správu, můžete použít [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) pro připojení k virtuální počítače Linux spuštěné v Azure. SSH je šifrované připojení protokol, který umožňuje zabezpečené přihlašování přes nezabezpečený připojení. Je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. Pomocí klíče SSH pro ověřování eliminují nutnost použití hesel k přihlášení. Protokol SSH používá pár veřejného a privátního klíče (asymetrické šifrování) pro ověřování.

## <a name="azure-vpn-encryption"></a>Šifrování Azure VPN

Může se připojit k Azure přes virtuální privátní sítě, která vytvoří zabezpečené tunelové propojení k ochraně soukromí dat, které se odesílají přes síť.

### <a name="azure-vpn-gateways"></a>Azure VPN Gateway

Můžete použít [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) k odesílání šifrované přenosy mezi virtuální sítí a vaše místní umístění prostřednictvím veřejného připojení nebo odesílat provoz mezi virtuálními sítěmi.

Site-to-site VPN použijte [IPsec](https://en.wikipedia.org/wiki/IPsec) pro šifrování přenosu. Azure VPN Gateway pomocí sady výchozí návrhů. Můžete nakonfigurovat Azure VPN Gateway používat vlastní zásady protokolu IPsec/IKE službou konkrétní kryptografické algoritmy a klíče síly, nikoli nastaví Azure výchozí zásady.

### <a name="point-to-site-vpns"></a>Point-to-site VPN

Point-to-site VPN povolí jednotlivých klientských počítačů přístup k virtuální sítě Azure. [Nástroje zabezpečení SSTP Socket Tunneling Protocol ()](https://technet.microsoft.com/library/2007.06.cableguy.aspx) se používá k vytvoření tunelu VPN. Ho mohou procházet brány firewall (tunelové propojení se zobrazí jako připojení HTTPS). Můžete vytvořit vlastní infrastruktury interní veřejných klíčů (PKI) kořenové certifikační autority (CA) pro připojení point-to-site.

Připojení point-to-site VPN do virtuální sítě můžete nakonfigurovat pomocí portálu Azure pomocí ověření certifikátu nebo prostředí PowerShell.

Další informace o připojení point-to-site VPN do virtuální sítě Azure, najdete v části:

[Konfigurace připojení point-to-site k virtuální síti pomocí ověřování certifikace: portál Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurace připojení point-to-site k virtuální síti pomocí certifikátu ověřování: prostředí PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Site-to-site VPN 

Připojení k bráně VPN site-to-site slouží k připojení vaší místní sítě do virtuální sítě Azure přes tunelové propojení VPN protokolu IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje zařízení VPN s místními externího veřejnou IP adresu, přiřazen.

Připojení site-to-site VPN do virtuální sítě můžete nakonfigurovat pomocí portálu Azure, PowerShell nebo rozhraní příkazového řádku Azure.

Další informace naleznete v tématu:

[Na portálu Azure vytvořit připojení site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Umožňuje vytvořit připojení site-to-site v prostředí PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Vytvoření virtuální sítě pomocí připojení site-to-site VPN pomocí rozhraní příkazového řádku](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Šifrování během přenosu v Data Lake

Přenášená data se ve službě Data Lake Store také vždy šifrují. Kromě šifrování dat před ukládání trvalé média, data se také vždy zabezpečená přenosu pomocí protokolu HTTPS. HTTPS je jediný protokol, který se podporuje v rozhraních REST služby Data Lake Store.

Další informace o šifrování dat během přenosu v Data Lake najdete v tématu [šifrování dat v Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Správa klíčů s Key Vault

Bez správné ochrana a Správa klíčů je vykreslen šifrování nepoužitelné. Key Vault je doporučuje Microsoft řešení pro správu a řízení přístupu k šifrovací klíče používané cloudové služby. Oprávnění pro přístup k klíčů lze přiřadit ke službám nebo pro uživatele přes účty Azure Active Directory.

Key Vault zbavuje kterou přináší nutnost organizací potřeba konfigurovat, opravy a udržovat modulů hardwarového zabezpečení (HSM) a software pro správu klíčů. Pokud používáte Key Vault, můžete udržovat kontrolu. Microsoft nikdy vidí klíče a aplikace nemají přímý přístup k nim. Můžete také importovat nebo generovat klíče v modulech Hsm.

## <a name="next-steps"></a>Další postup

- [Přehled zabezpečení Azure](security-get-started-overview.md)
- [Přehled zabezpečení sítě Azure](security-network-overview.md)
- [Přehled zabezpečení databáze Azure](azure-database-security-overview.md)
- [Přehled zabezpečení virtuální počítače Azure](security-virtual-machines-overview.md)
- [Šifrování dat v klidovém stavu](azure-security-encryption-atrest.md)
- [Osvědčené postupy šifrování a zabezpečení dat](azure-security-data-encryption-best-practices.md)
