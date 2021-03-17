---
title: Přehled šifrování Azure | Microsoft Docs
description: Přečtěte si o možnostech šifrování v Azure. Podívejte se na informace pro šifrování v klidovém umístění, šifrování v letu a správu klíčů pomocí Azure Key Vault.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879710"
---
# <a name="azure-encryption-overview"></a>Přehled šifrování v Azure

Tento článek poskytuje přehled o tom, jak se šifrování používá v Microsoft Azure. Pokrývá hlavní oblasti šifrování, včetně šifrování v klidovém umístění, šifrování v letu a správy klíčů pomocí Azure Key Vault. Každá část obsahuje odkazy na podrobnější informace.

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém umístění

Uložená data obsahují informace, které se nacházejí v trvalém úložišti na fyzických médiích v libovolném digitálním formátu. Médium může zahrnovat soubory na magnetických nebo optických médiích, Archivovaná data a zálohy dat. Microsoft Azure nabízí nejrůznější řešení pro ukládání dat, která vyhovují různým potřebám, včetně souborů, disků, objektů BLOB a tabulkového úložiště. Microsoft taky poskytuje šifrování pro ochranu [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md)a Azure Data Lake.

Šifrování dat v klidovém formátu je k dispozici pro služby v cloudových modelech SaaS (software jako služba), platforma jako služba (PaaS) a infrastruktura jako služba (IaaS). Tento článek shrnuje a poskytuje prostředky, které vám pomůžou s používáním možností šifrování Azure.

Podrobnější diskuzi o tom, jak se data v klidovém stavu šifrují v Azure, najdete v tématu [šifrování dat Azure v klidovém](encryption-atrest.md)stavu.

## <a name="azure-encryption-models"></a>Modely šifrování Azure

Azure podporuje různé modely šifrování, včetně šifrování na straně serveru, které používá klíče spravované službou, zákaznických klíčů v Key Vault nebo klíčů spravovaných zákazníkem na hardwaru ovládaném zákazníkem. Pomocí šifrování na straně klienta můžete spravovat a ukládat klíče místně nebo v jiném zabezpečeném umístění.

### <a name="client-side-encryption"></a>Šifrování na straně klienta

Šifrování na straně klienta se provádí mimo Azure. Obsahuje:

- Data zašifrovaná aplikací, která běží v datovém centru zákazníka nebo v aplikaci služby.
- Data, která jsou už při přijetí v Azure zašifrovaná.

Při šifrování na straně klienta nemají Poskytovatelé cloudových služeb přístup k šifrovacím klíčům a nemůžou tato data dešifrovat. Budete mít úplnou kontrolu nad klíči.

### <a name="server-side-encryption"></a>Šifrování na straně serveru

Tři modely šifrování na straně serveru nabízejí různé charakteristiky správy klíčů, které si můžete vybrat podle svých požadavků:

- **Klíče spravované službou**: poskytuje kombinaci řízení a pohodlí s nízkou režií.

- **Klíče spravované zákazníkem**: poskytuje kontrolu nad klíči, včetně podpory BYOK (Přineste si vlastní klíče), nebo umožňuje vygenerovat nové.

- **Klíče spravované službou v hardwaru ovládaném zákazníkem**: umožňuje spravovat klíče ve vašem vlastním úložišti, mimo kontrolu společnosti Microsoft. Tato vlastnost se nazývá hostitel vlastního klíče (HYOK). Konfigurace je ale komplexní a většina služeb Azure tento model nepodporuje.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Virtuální počítače s Windows a Linuxem můžete chránit pomocí služby [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md), která využívá technologii [Windows BitLocker](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) k ochraně disků a datových disků operačního systému pomocí úplného šifrování svazku.

Šifrovací klíče a tajné kódy jsou v [předplatném Azure Key Vault](../../key-vault/general/overview.md)chráněny. Pomocí služby Azure Backup můžete zálohovat a obnovovat šifrované virtuální počítače, které používají konfiguraci klíčového šifrovacího klíče (KEK).

### <a name="azure-storage-service-encryption"></a>Šifrování služby Azure Storage

Uložená data v úložišti objektů BLOB v Azure a sdílené složky Azure je možné šifrovat na straně serveru i na straně klienta.

[Šifrování služby Azure Storage (SSE)](../../storage/common/storage-service-encryption.md) může automaticky šifrovat data před jejich uložením a automaticky je dešifrovat při načtení dat. Tento proces je pro uživatele zcela transparentní. Šifrování služby Storage používá [šifrování Standard AES (Advanced Encryption Standard) AES (](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)256 bitů), což je jedno z nejsilnějších dostupných šifrovacích šifr. AES transparentně zpracovává šifrování, dešifrování a správu klíčů.

### <a name="client-side-encryption-of-azure-blobs"></a>Šifrování objektů blob Azure na straně klienta

Šifrování objektů BLOB v Azure můžete provádět různými způsoby pomocí šifrování na straně klienta.

K šifrování dat v klientských aplikacích před jejich odesláním do úložiště Azure můžete použít balíček klientské knihovny Azure Storage pro rozhraní .NET NuGet.

Další informace o a stažení balíčku klientské knihovny pro rozhraní .NET NuGet pro Azure Storage najdete v tématu [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Pokud používáte šifrování na straně klienta s Key Vault, vaše data se šifrují pomocí jednorázového šifrovacího klíče symetrického obsahu (CEK), který je vygenerován Azure Storage klientské sady SDK. CEK je zašifrovaný pomocí klíčového šifrovacího klíče (KEK), který může být buď symetrický klíč, nebo asymetrický klíčový pár. Můžete ji místně spravovat nebo ji uložit v Key Vault. Šifrovaná data se pak nahrají do Azure Storage.

Další informace o šifrování na straně klienta pomocí Key Vault a Začínáme s postupy najdete v tématu [kurz: šifrování a dešifrování objektů BLOB v Azure Storage pomocí Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Nakonec můžete použít také klientskou knihovnu Azure Storage pro jazyk Java k provedení šifrování na straně klienta před odesláním dat do Azure Storage a dešifrováním dat při jejich stažení do klienta. Tato knihovna také podporuje integraci s [Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Šifrování neaktivních dat pomocí Azure SQL Database

[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) je služba relační databáze pro obecné účely v Azure, která podporuje struktury, jako jsou relační data, JSON, prostor a XML. SQL Database podporuje šifrování na straně serveru prostřednictvím funkce transparentní šifrování dat (TDE) a šifrování na straně klienta prostřednictvím funkce Always Encrypted.

#### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

[TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) se používá k šifrování datových souborů služby [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)a [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) v reálném čase pomocí šifrovacího klíče databáze (klíč DEK), který je uložený v záznamu spuštění databáze pro dostupnost během obnovení.

TDE chrání data a soubory protokolů pomocí šifrovacích algoritmů AES a Triple Data Encryption Standard (3DES). Šifrování databázového souboru se provádí na úrovni stránky. Stránky v zašifrované databázi jsou před zápisem na disk zašifrované a při jejich čtení do paměti se dešifrují. TDE je teď ve výchozím nastavení povolená u nově vytvořených databází Azure SQL.

#### <a name="always-encrypted-feature"></a>Always Encrypted funkce

Díky funkci [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) v Azure SQL můžete šifrovat data v rámci klientských aplikací předtím, než je uložíte v Azure SQL Database. Můžete také povolit delegování místní správy databáze třetím stranám a zachovat oddělení mezi osobami, které vlastní, a mohou zobrazovat data a osoby, které ji spravují, ale nemají k nim přístup.

#### <a name="cell-level-or-column-level-encryption"></a>Šifrování na úrovni buňky nebo sloupce

Pomocí Azure SQL Database můžete použít symetrické šifrování pro sloupec dat pomocí jazyka Transact-SQL. Tento přístup se nazývá [šifrování na úrovni buňky nebo šifrování na úrovni sloupce (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), protože ho můžete použít k šifrování určitých sloupců nebo dokonce konkrétní buňky dat s různými šifrovacími klíči. Díky tomu získáte přesnější možnosti šifrování než TDE, které šifrují data na stránkách.

CLE má integrované funkce, které můžete použít k šifrování dat pomocí symetrického nebo asymetrického klíče, veřejného klíče certifikátu nebo hesla pomocí algoritmu 3DES.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB šifrování databáze

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) je globálně distribuovaná databáze Microsoftu pro více modelů. Uživatelská data uložená v Cosmos DB v nestálém úložišti (jednotky SSD) se standardně šifrují. Neexistují žádné ovládací prvky, které by bylo možné zapnout nebo vypnout. Šifrování v klidovém stavu se implementuje pomocí řady bezpečnostních technologií, včetně zabezpečených systémů úložiště klíčů, šifrovaných sítí a kryptografických rozhraní API. Šifrovací klíče spravuje Microsoft a jsou otočené podle interních pokynů Microsoftu.

### <a name="at-rest-encryption-in-data-lake"></a>Šifrování neaktivních umístění v Data Lake

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) je úložiště každého typu dat shromážděné na jednom místě před jakýmkoli formálním definováním požadavků nebo schématu. Data Lake Store podporuje ve výchozím nastavení "transparentní šifrování dat v klidovém stavu, které se nastavuje během vytváření vašeho účtu. Ve výchozím nastavení Azure Data Lake Store spravuje klíče, ale máte možnost je spravovat sami.

K šifrování a dešifrování dat se používají tři typy klíčů: hlavní šifrovací klíč (hlavní šifrovací klíč), šifrovací klíč dat (klíč DEK) a blokující šifrovací klíč (klíče bek). HLAVNÍ šifrovací klíč se používá k šifrování klíč DEK, který je uložen na trvalém médiu, a klíče bek je odvozen z klíč DEK a bloku dat. Pokud spravujete vlastní klíče, můžete hlavní šifrovací klíč otočit.

## <a name="encryption-of-data-in-transit"></a>Šifrování dat při přenosu

Azure nabízí řadu mechanismů pro zachování privátních dat při jejich přesunu z jednoho umístění do druhého.

### <a name="data-link-layer-encryption-in-azure"></a>Šifrování vrstvy pro propojení dat v Azure

Pokaždé, když se pohyb zákazníků Azure pohybuje mezi datovými centry – mimo fyzické hranice, která není řízená Microsoftem (nebo jménem Microsoftu) – šifrovací metoda vrstvy dat pomocí [standardů zabezpečení IEEE 802.1 AE Mac](https://1.ieee802.org/security/802-1ae/) (označované taky jako MACsec) se použije z Point-to-Point v rámci základního síťového hardwaru. Před odesláním se pakety zašifrují a dešifrují na zařízeních, aby se zabránilo fyzickým útokům typu "man-in-the-middle" nebo "Snoop/wiretapping". Vzhledem k tomu, že je tato technologie integrovaná na samotném síťovém hardwaru, zajišťuje na síťovém hardwaru přenosovou rychlost při nezvyšování latence spojení. Toto šifrování MACsec je ve výchozím nastavení zapnuté pro všechny přenosy dat Azure na cestách v rámci oblasti nebo mezi oblastmi a na straně zákazníků není potřeba žádná akce, kterou by bylo možné povolit. 

### <a name="tls-encryption-in-azure"></a>Šifrování TLS v Azure

Společnost Microsoft zákazníkům dává možnost používat protokol TLS ( [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) ) k ochraně dat při cestování mezi cloudovou službou a zákazníky. Datová centra Microsoftu vyjednávají připojení TLS k klientským systémům, které se připojují ke službám Azure. Protokol TLS zajišťuje silné ověřování, soukromí zpráv a integritu (povolení detekce manipulace, zachycení a padělání zpráv), interoperability, flexibility algoritmů a snadné nasazení a použití.

PFS ( [Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) ) chrání připojení mezi klientskými systémy zákazníků a cloudovou službou Microsoftu pomocí jedinečných klíčů. Připojení také používají délky šifrovacího klíče 2 048 založeného na šifrování RSA. Tato kombinace usnadňuje uživatelům zachycení a přístup k datům, která se přepravují.

### <a name="azure-storage-transactions"></a>Transakce Azure Storage

Když pracujete s Azure Storage přes Azure Portal, provedou se všechny transakce přes protokol HTTPS. K interakci s Azure Storage můžete použít taky REST API úložiště přes HTTPS. Pomocí protokolu HTTPS můžete vyhovět při volání rozhraní REST API pro přístup k objektům v účtech úložiště tím, že povolíte zabezpečený přenos, který je pro účet úložiště nutný.

Sdílené přístupové podpisy ([SAS](../../storage/common/storage-sas-overview.md)), které se dají použít k delegování přístupu k objektům Azure Storage, zahrnují možnost určit, že se při použití sdílených přístupových podpisů dá použít jenom protokol HTTPS. Tento přístup zajišťuje, že kdokoli, kdo posílá odkazy s tokeny SAS, používá správný protokol.

[SMB 3,0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), který se používá pro přístup ke sdíleným složkám souborů Azure, podporuje šifrování a je dostupný v systémech windows Server 2012 R2, Windows 8, Windows 8.1 a Windows 10. Umožňuje přístup mezi jednotlivými oblastmi a dokonce i přístup na plochu.

Šifrování na straně klienta šifruje data předtím, než se odešlou do instance Azure Storage, aby se při přenosu přes síť zašifroval.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Šifrování protokolu SMB přes virtuální sítě Azure 

Pomocí [protokolu SMB 3,0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) na virtuálních počítačích, na kterých běží Windows Server 2012 nebo novější, můžete zabezpečit přenos dat pomocí šifrování dat při přenosu přes virtuální sítě Azure. Šifrování dat vám umožní chránit před útoky na manipulaci a odposlouchávání. Správci můžou povolit šifrování protokolu SMB pro celý server nebo jenom určité sdílené složky.

Když je ve výchozím nastavení zapnuté šifrování SMB pro sdílenou složku nebo server, můžou přístup k šifrovaným sdíleným složkám povolit jenom klienti SMB 3,0.

## <a name="in-transit-encryption-in-vms"></a>Šifrování v přenosech na virtuálních počítačích

Data přenášená do, z a mezi virtuálními počítači, které používají systém Windows, lze v závislosti na povaze připojení šifrovat různými způsoby.

### <a name="rdp-sessions"></a>Relace protokolu RDP

K VIRTUÁLNÍmu počítači se můžete připojit a přihlásit se pomocí [protokol RDP (Remote Desktop Protocol) (RDP)](/windows/win32/termserv/remote-desktop-protocol) z klientského počítače se systémem Windows nebo z počítače Mac s nainstalovaným klientem RDP. Data přenášená přes síť v relacích RDP je možné chránit pomocí protokolu TLS.

Službu Vzdálená plocha můžete také použít k připojení k virtuálnímu počítači se systémem Linux v Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Zabezpečený přístup k virtuálním počítačům se systémem Linux pomocí protokolu SSH

Pro vzdálenou správu můžete použít [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) pro připojení k virtuálním počítačům se systémem Linux běžícím v Azure. SSH je zašifrovaný protokol připojení, který umožňuje zabezpečená přihlášení přes nezabezpečená připojení. Jedná se o výchozí protokol připojení pro virtuální počítače se systémem Linux hostované v Azure. Pomocí klíčů SSH pro ověřování Eliminujte nutnost přihlášení hesel. SSH používá pro ověřování pár veřejného a privátního klíče (asymetrické šifrování).

## <a name="azure-vpn-encryption"></a>Šifrování Azure VPN

K Azure se můžete připojit pomocí virtuální privátní sítě, která vytvoří zabezpečené tunelové propojení pro ochranu soukromí dat odesílaných přes síť.

### <a name="azure-vpn-gateways"></a>Brány VPN Azure

[Bránu Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) můžete použít k posílání šifrovaného provozu mezi vaší virtuální sítí a místním umístěním přes veřejné připojení nebo pro odesílání provozu mezi virtuálními sítěmi.

Sítě VPN typu Site-to-site používají k šifrování přenosu [protokol IPSec](https://en.wikipedia.org/wiki/IPsec) . Brány VPN Azure používají sadu výchozích návrhů. Brány VPN Azure můžete nakonfigurovat tak, aby používaly vlastní zásadu IPsec/IKE s konkrétními kryptografickými algoritmy a silnými klíči, a ne s výchozími nastaveními zásad Azure.

### <a name="point-to-site-vpns"></a>Sítě VPN typu Point-to-Site

Sítě VPN typu Point-to-site umožňují jednotlivým klientským počítačům přístup k virtuální síti Azure. [Protokol SSTP (Secure Socket Tunneling Protocol)](/previous-versions/technet-magazine/cc162322(v=msdn.10)) slouží k vytvoření tunelu VPN. Může procházet brány firewall (tunel se zobrazí jako připojení HTTPS). Pro připojení Point-to-site můžete použít vlastní kořenovou certifikační autoritu infrastruktury veřejných klíčů (PKI).

Můžete nakonfigurovat připojení VPN typu Point-to-site k virtuální síti pomocí Azure Portal s ověřováním certifikátů nebo PowerShellem.

Další informace o připojení VPN typu Point-to-site k virtuálním sítím Azure najdete v těchto tématech:

[Konfigurace připojení typu Point-to-site k virtuální síti pomocí ověřování certifikace: Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurace připojení typu Point-to-site k virtuální síti s použitím ověření certifikátu: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Sítě VPN typu Site-to-site 

Připojení brány VPN typu Site-to-site můžete použít k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou externí veřejnou IP adresu.

Připojení VPN typu Site-to-site k virtuální síti můžete nakonfigurovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI.

Další informace naleznete v tématu:

[Vytvoření připojení typu Site-to-site v Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)

[Vytvoření připojení typu Site-to-site v PowerShellu](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Vytvoření virtuální sítě s připojením VPN typu Site-to-site pomocí rozhraní příkazového řádku](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Šifrování při přenosu v Data Lake

Přenášená data se ve službě Data Lake Store také vždy šifrují. Kromě šifrování dat před jejich uložením v trvalém médiu se data vždycky během přenosu vždy zabezpečují pomocí protokolu HTTPS. HTTPS je jediný protokol, který se podporuje v rozhraních REST služby Data Lake Store.

Další informace o šifrování dat při přenosu v Data Lake najdete v tématu [šifrování dat v Data Lake Store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Správa klíčů pomocí Key Vault

Šifrování je bez správné ochrany a správy klíčů vygenerováno zbytečným. Key Vault je řešení, které se doporučuje od Microsoftu pro správu a řízení přístupu k šifrovacím klíčům používaným v cloudových službách. Oprávnění pro přístup k klíčům můžete přiřazovat službám nebo uživatelům prostřednictvím účtů Azure Active Directory.

Díky Key Vaultu organizace nemusí konfigurovat, opravovat a udržovat moduly hardwarového zabezpečení (HSM) a software pro správu klíčů. Při použití Key Vault zachováte řízení. Microsoft nikdy nevidí vaše klíče a aplikace k nim nemají přímý přístup. Můžete také importovat nebo generovat klíče v HSM.

## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení Azure](./overview.md)
- [Přehled zabezpečení sítě v Azure](network-overview.md)
- [Přehled zabezpečení Azure Database](../../azure-sql/database/security-overview.md)
- [Přehled zabezpečení virtuálních počítačů Azure](virtual-machines-overview.md)
- [Šifrování v klidovém stavu](encryption-atrest.md)
- [Osvědčené postupy šifrování a zabezpečení dat](data-encryption-best-practices.md)