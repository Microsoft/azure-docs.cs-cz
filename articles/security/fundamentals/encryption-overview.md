---
title: Přehled šifrování Azure | Dokumenty společnosti Microsoft
description: Informace o různých možnostech šifrování v Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: mbaldwin
ms.openlocfilehash: ce78ade4df3c5bcea9e4e44750c430065cbfc5b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454641"
---
# <a name="azure-encryption-overview"></a>Přehled šifrování Azure

Tento článek obsahuje přehled použití šifrování v Microsoft Azure. Pokrývá hlavní oblasti šifrování, včetně šifrování v klidovém stavu, šifrování za letu a správy klíčů pomocí služby Azure Key Vault. Každá sekce obsahuje odkazy na podrobnější informace.

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém stavu

Data v klidovém stavu zahrnují informace, které jsou uloženy v trvalém úložišti na fyzickém médiu v libovolném digitálním formátu. Média mohou obsahovat soubory na magnetických nebo optických médiích, archivovaná data a zálohování dat. Microsoft Azure nabízí celou řadu řešení pro ukládání dat, která splňují různé potřeby, včetně ukládání souborů, disků, objektů blob a úložiště tabulek. Microsoft také poskytuje šifrování pro ochranu [Azure SQL Database](../../sql-database/sql-database-technical-overview.md), Azure [Cosmos DB](../../data-factory/introduction.md)a Azure Data Lake.

Šifrování dat v klidovém stavu je k dispozici pro služby v celém softwaru jako služba (SaaS), platforma jako služba (PaaS) a infrastruktura jako služba (IaaS) cloudové modely. Tento článek shrnuje a poskytuje prostředky, které vám pomůžou používat možnosti šifrování Azure.

Podrobnější informace o tom, jak se v Azure šifrují data v klidovém stavu, najdete [v tématu Šifrování dat v klidovém stavu Azure](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modely šifrování Azure

Azure podporuje různé modely šifrování, včetně šifrování na straně serveru, které používá klíče spravované službou, klíče spravované zákazníkem v trezoru klíčů nebo klíče spravované zákazníkem na hardwaru řízeném zákazníkem. Pomocí šifrování na straně klienta můžete spravovat a ukládat klíče místně nebo na jiném zabezpečeném místě.

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Šifrování na straně klienta se provádí mimo Azure. Obsahuje:

- Data zašifrovaná aplikací, která běží v datovém centru zákazníka nebo v aplikaci služby.
- Data, která je již šifrována, když je přijata Azure.

Díky šifrování na straně klienta nemají poskytovatelé cloudových služeb přístup k šifrovacím klíčům a nemohou tato data dešifrovat. Udržujete úplnou kontrolu nad klíči.

### <a name="server-side-encryption"></a>Šifrování na straně serveru

Tři modely šifrování na straně serveru nabízejí různé charakteristiky správy klíčů, které si můžete vybrat podle svých požadavků:

- **Klíče spravované službou**: Poskytuje kombinaci řízení a pohodlí s nízkou režií.

- **Klíče spravované zákazníkem**: Poskytuje kontrolu nad klíči, včetně podpory Bring Your Own Keys (BYOK), nebo umožňuje generovat nové.

- **Klíče spravované službou v hardwaru řízeném zákazníkem**: Umožňuje spravovat klíče v proprietárním úložišti mimo kontrolu společnosti Microsoft. Tato charakteristika se nazývá Host Your Own Key (HYOK). Konfigurace je však složitá a většina služeb Azure tento model nepodporuje.

### <a name="azure-disk-encryption"></a>Šifrování disku Azure

Virtuální počítače se systémem Windows a Linux můžete chránit pomocí [šifrování disku Azure](/azure/security/fundamentals/azure-disk-encryption-vms-vmss), které používá technologii Windows [BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) k ochraně disků operačního systému i datových disků pomocí šifrování celého svazku.

Šifrovací klíče a tajné klíče jsou zabezpečeny v [předplatném služby Azure Key Vault](../../key-vault/general/overview.md). Pomocí služby Azure Backup můžete zálohovat a obnovovat šifrované virtuální počítače , které používají konfiguraci kek (Key Encryption Key).

### <a name="azure-storage-service-encryption"></a>Šifrování služby Azure Storage Service

Data v klidovém stavu v úložišti Objektů blob Azure a sdílených složek Azure lze zašifrovat ve scénářích na straně serveru i na straně klienta.

[Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) může automaticky šifrovat data před uložením a automaticky dešifruje data při načtení. Tento proces je pro uživatele zcela transparentní. Šifrování služby Storage Service používá 256bitové [šifrování Advanced Encryption Standard (AES),](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)což je jedna z nejsilnějších blokových šifer, které jsou k dispozici. AES zpracovává šifrování, dešifrování a správu klíčů transparentně.

### <a name="client-side-encryption-of-azure-blobs"></a>Šifrování objektů BLOB Azure na straně klienta

Šifrování objektů BLOS Azure na straně klienta můžete provádět různými způsoby.

Balíček Klientského úložiště Azure pro .NET NuGet můžete použít k šifrování dat v klientských aplikacích před jejich odesláním do úložiště Azure.

Další informace o balíčku klienta úložiště Azure pro .NET NuGet a jeho stažení najdete v článku [Úložiště Windows Azure 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Při použití šifrování na straně klienta s trezoru klíčů, vaše data se šifruje pomocí jednorázovésymetrického šifrovacího klíče obsahu (CEK), který je generován sadou Azure Storage klienta SDK. CEK je šifrován pomocí šifrovacího klíče klíče (KEK), který může být buď symetrický klíč nebo asymetrický pár klíčů. Můžete ji spravovat místně nebo uložit do trezoru klíčů. Šifrovaná data se pak nahrají do Služby Azure Storage.

Další informace o šifrování na straně klienta pomocí trezoru klíčů a začít s pokyny s návody, [najdete v tématu Návod: Šifrování a dešifrování objektů BLOB ve službě Azure Storage pomocí trezoru klíčů](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Nakonec můžete také použít klientskou knihovnu úložiště Azure pro Javu k provedení šifrování na straně klienta před odesláním dat do služby Azure Storage a k dešifrování dat při jejich stažení do klienta. Tato knihovna také podporuje integraci s [trezorem klíčů](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Šifrování dat v klidovém stavu pomocí Azure SQL Database

[Azure SQL Database](../../sql-database/sql-database-technical-overview.md) je univerzální relační databázová služba v Azure, která podporuje struktury, jako jsou relační data, JSON, prostorové a XML. SQL Database podporuje šifrování na straně serveru prostřednictvím funkce Transparentní šifrování dat (TDE) a šifrování na straně klienta prostřednictvím funkce Vždy šifrované.

#### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) se používá k šifrování [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), Azure [SQL Database](../../sql-database/sql-database-technical-overview.md)a Azure SQL [Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) datové soubory v reálném čase, pomocí šifrovacího klíče databáze (DEK), který je uložen v databázi spouštěcí záznam pro dostupnost během obnovení.

TDE chrání data a soubory protokolu pomocí šifrovacích algoritmů AES a Triple Data Encryption Standard (3DES). Šifrování databázového souboru se provádí na úrovni stránky. Stránky v zašifrované databázi jsou před zápisem na disk zašifrovány a při čtení do paměti jsou dešifrovány. TDE je teď ve výchozím nastavení povolená v nově vytvořených databázích Azure SQL.

#### <a name="always-encrypted-feature"></a>Vždy šifrovaná funkce

Pomocí funkce [Vždy šifrované](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) v Azure SQL můžete šifrovat data v klientských aplikacích před jejich uložením do Azure SQL Database. Můžete také povolit delegování místní správy databáze na třetí strany a udržovat oddělení mezi těmi, kteří vlastní a mohou zobrazit data a ty, kteří je spravují, ale neměli by k nim mít přístup.

#### <a name="cell-level-or-column-level-encryption"></a>Šifrování na úrovni buněk nebo sloupců

Pomocí Azure SQL Database můžete použít symetrické šifrování na sloupec dat pomocí Transact-SQL. Tento přístup se nazývá [šifrování na úrovni buněk nebo šifrování na úrovni sloupců (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), protože jej můžete použít k šifrování určitých sloupců nebo dokonce konkrétních buněk dat pomocí různých šifrovacích klíčů. Získáte tak podrobnější možnost šifrování než TDE, které šifruje data na stránkách.

CLE má vestavěné funkce, které můžete použít k šifrování dat pomocí symetrických nebo asymetrických klíčů, veřejného klíče certifikátu nebo přístupové fráze pomocí 3DES.

### <a name="cosmos-db-database-encryption"></a>Šifrování databáze Cosmos DB

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) je globálně distribuovaná databáze více modelů společnosti Microsoft. Uživatelská data uložená v Cosmos DB v nevolatilním úložišti (jednotky SSD) jsou ve výchozím nastavení šifrována. Neexistují žádné ovládací prvky, které by jej zapínala nebo vypínala. Šifrování v klidovém stavu je implementováno pomocí řady technologií zabezpečení, včetně zabezpečených systémů úložiště klíčů, šifrovaných sítí a kryptografických rozhraní API. Šifrovací klíče spravuje společnost Microsoft a jsou otočeny podle interních pokynů společnosti Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Šifrování v klidovém stavu v datovém jezeře

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) je celopodnikové úložiště všech typů dat shromážděných na jednom místě před jakoukoli formální definicí požadavků nebo schématu. Úložiště data lake store podporuje "ve výchozím nastavení", transparentní šifrování dat v klidovém stavu, které se nastavuje při vytváření vašeho účtu. Ve výchozím nastavení Azure Data Lake Store spravuje klíče za vás, ale máte možnost spravovat sami.

Při šifrování a dešifrování dat se používají tři typy klíčů: hlavní šifrovací klíč (MEK), šifrovací klíč (DEK) a blokový šifrovací klíč (BEK). MEK se používá k šifrování DEK, který je uložen na trvalé médium a BEK je odvozen z DEK a datový blok. Pokud spravujete vlastní klíče, můžete otočit MEK.

## <a name="encryption-of-data-in-transit"></a>Šifrování dat při přenosu

Azure nabízí mnoho mechanismů pro zachování dat v soukromí, když se přesouvá z jednoho umístění do druhého.

### <a name="tlsssl-encryption-in-azure"></a>Šifrování TLS/SSL v Azure

Společnost Microsoft používá protokol TLS [(Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security) k ochraně dat při cestování mezi cloudovými službami a zákazníky. Datová centra Microsoftu vyjednají připojení TLS s klientskými systémy, které se připojují ke službám Azure. Protokol TLS poskytuje silné ověřování, ochranu osobních údajů a integritu zpráv (umožňuje detekci manipulace, zachycení a padělání zpráv), interoperabilitu, flexibilitu algoritmů a snadné nasazení a použití.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) chrání spojení mezi klientskými systémy zákazníků a cloudovými službami Microsoftu pomocí jedinečných klíčů. Připojení také používají 2 048bitové šifrovací klíče založené na RSA. Tato kombinace ztěžuje někomu zachytit a získat přístup k datům, která jsou v tranzitu.

### <a name="azure-storage-transactions"></a>Transakce azure úložiště

Když pracujete s Azure Storage prostřednictvím portálu Azure, všechny transakce se uskuteční přes HTTPS. K interakci s Azure Storage můžete taky použít rozhraní API úložiště REST přes protokol HTTPS. Můžete vynutit použití protokolu HTTPS při volání REST API pro přístup k objektům v účtech úložiště povolením zabezpečeného přenosu, který je vyžadován pro účet úložiště.

Sdílené přístupové podpisy[(SAS),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)které lze použít k delegování přístupu k objektům úložiště Azure, zahrnují možnost určit, že při použití sdílených přístupových podpisů lze použít jenom protokol HTTPS. Tento přístup zajišťuje, že každý, kdo odesílá odkazy s tokeny SAS používá správný protokol.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), který slouží k přístupu ke sdíleným složkám Azure Files, podporuje šifrování a je k dispozici ve Windows Server 2012 R2, Windows 8, Windows 8.1 a Windows 10. Umožňuje přístup napříč oblastmi a dokonce i přístup na ploše.

Šifrování na straně klienta šifruje data před odesláním do instance služby Azure Storage, takže jsou šifrovaná při jejich cestě po síti.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Šifrování SMB nad virtuálními sítěmi Azure 

Pomocí [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) ve virtuálních počítačích se systémem Windows Server 2012 nebo novějším můžete zajistit zabezpečení datových přenosů šifrováním dat při přenosu přes virtuální sítě Azure. Šifrováním dat pomáháte chránit před neoprávněnými zásahy a odposloucháváním útoků. Správci mohou povolit šifrování SMB pro celý server nebo pouze pro konkrétní sdílené složky.

Ve výchozím nastavení po zapnutí šifrování SMB pro sdílenou složku nebo server mohou k šifrovaným sdíleným položkám přistupovat pouze klienti SMB 3.0.

## <a name="in-transit-encryption-in-vms"></a>Šifrování na cestě ve virtuálních váska

Data při přenosu do, z a mezi virtuálními počítači, které jsou systémem Windows je šifrována v mnoha způsoby, v závislosti na povaze připojení.

### <a name="rdp-sessions"></a>Relace PRV

Virtuální počítač se můžete připojit a přihlásit pomocí [protokolu RDP (RdP) pomocí protokolu RDP (RdP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) z klientského počítače se systémem Windows nebo z počítače Mac s nainstalovaným klientem RDP. Data při přenosu přes síť v relacích PROTOKOLU RDP mohou být chráněna protokolem TLS.

Vzdálenou plochu můžete taky použít k připojení k virtuálnímu počítači s Linuxem v Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Zabezpečený přístup k virtuálním počítačům s Linuxem pomocí SSH

Pro vzdálenou správu můžete pomocí [zabezpečeného prostředí](../../virtual-machines/linux/ssh-from-windows.md) (SSH) připojit k virtuálním počítačům s Linuxem spuštěným v Azure. SSH je šifrovaný protokol připojení, který umožňuje zabezpečené přihlášení přes nezabezpečené připojení. Jedná se o výchozí protokol připojení pro virtuální počítače s Linuxem hostovaný v Azure. Pomocí klíčů SSH pro ověřování eliminujete potřebu hesla pro přihlášení. SSH používá pro ověřování dvojici veřejného a soukromého klíče (asymetrické šifrování).

## <a name="azure-vpn-encryption"></a>Šifrování Azure VPN

K Azure se můžete připojit prostřednictvím virtuální privátní sítě, která vytvoří zabezpečené tunelové propojení, které chrání soukromí dat odesílaných v síti.

### <a name="azure-vpn-gateways"></a>Brány VPN Azure

Pomocí brány [Azure VPN](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) můžete odesílat šifrovaný provoz mezi vaší virtuální sítí a místním umístěním přes veřejné připojení nebo odesílat přenosy mezi virtuálními sítěmi.

Sítě virtuálních sítí site-to-site používají k šifrování přenosu [síť IPsec.](https://en.wikipedia.org/wiki/IPsec) Brány Azure VPN používají sadu výchozích návrhů. Brány Azure VPN můžete nakonfigurovat tak, aby používaly vlastní zásady IPsec/IKE s konkrétními kryptografickými algoritmy a silnými stránkami klíčů, nikoli výchozími sadami zásad Azure.

### <a name="point-to-site-vpns"></a>Sítě VPN typu Point-to-Site

Virtuální sítě point-to-site umožňují jednotlivým klientským počítačům přístup k virtuální síti Azure. [Protokol SSTP (Secure Socket Tunneling Protocol)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) se používá k vytvoření tunelového propojení VPN. Může procházet firewally (tunel se zobrazí jako připojení HTTPS). Pro připojení typu Point-to-site můžete použít vlastní kořenovou certifikační autoritu infrastruktury veřejných klíčů (PKI).

Připojení VPN typu point-to-site k virtuální síti můžete nakonfigurovat pomocí portálu Azure s ověřováním certifikátů nebo PowerShellem.

Další informace o připojení VPN z bodu na web k virtuálním sítím Azure najdete v tématu:

[Konfigurace připojení z bodu na místo k virtuální síti pomocí certifikačního ověřování: Portál Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurace připojení typu point-to-site k virtuální síti pomocí ověřování certifikátu: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Sítě virtuálních sítí site-to-site 

Připojení brány VPN mezi lokalitami můžete použít k připojení místní sítě k virtuální síti Azure prostřednictvím tunelového propojení VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, ke kterému je přiřazena veřejná IP adresa směřující k externím adresám.

Připojení VPN mezi lokalitami k virtuální síti můžete nakonfigurovat pomocí portálu Azure, PowerShellu nebo rozhraní příkazového příkazu Konto Azure.

Další informace naleznete v tématu:

[Vytvoření připojení k webu na webu na webu Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Vytvoření připojení mezi lokalitami v Prostředí PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Vytvoření virtuální sítě s připojením VPN mezi lokalitami pomocí cli](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Šifrování během přenosu v datovém jezeře

Přenášená data se ve službě Data Lake Store také vždy šifrují. Kromě šifrování dat před jejich uložením na trvalé médium jsou data také vždy zabezpečena při přenosu pomocí protokolu HTTPS. HTTPS je jediný protokol, který se podporuje v rozhraních REST služby Data Lake Store.

Další informace o šifrování dat při přenosu v datovém jezeře najdete [v tématu Šifrování dat v úložišti Data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Správa klíčů pomocí trezoru klíčů

Bez řádné ochrany a správy klíčů je šifrování k ničemu. Trezor klíčů je řešení doporučené společností Microsoft pro správu a řízení přístupu k šifrovacím klíčům používaným cloudovými službami. Oprávnění k přístupovým klíčům lze přiřadit službám nebo uživatelům prostřednictvím účtů Azure Active Directory.

Key Vault zbavuje organizace nutnosti konfigurovat, opravovat a udržovat moduly hardwarového zabezpečení (HSM) a software pro správu klíčů. Při použití trezoru klíčů udržujete kontrolu. Společnost Microsoft nikdy neuvidí vaše klíče a aplikace k nim nemají přímý přístup. Klíče můžete také importovat nebo generovat v souborech hesm a hestely.

## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení Azure](get-started-overview.md)
- [Přehled zabezpečení sítě Azure](network-overview.md)
- [Přehled zabezpečení databáze Azure](database-security-overview.md)
- [Přehled zabezpečení virtuálních počítačů Azure](virtual-machines-overview.md)
- [Šifrování dat v klidovém stavu](encryption-atrest.md)
- [Osvědčené postupy šifrování a zabezpečení dat](data-encryption-best-practices.md)
