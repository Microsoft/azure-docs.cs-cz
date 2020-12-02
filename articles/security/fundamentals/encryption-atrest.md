---
title: Šifrování dat Azure – on-REST – Azure Security
description: Tento článek poskytuje přehled o službě Azure Data Encryption v klidovém formátu, celkové možnosti a obecné informace.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 092320db9b7fe2b1f3fe142f84ad201d40dc6e2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492280"
---
# <a name="azure-data-encryption-at-rest"></a>Šifrování dat Azure v klidovém umístění

Microsoft Azure obsahuje nástroje pro ochranu dat v souladu s požadavky vaší společnosti na zabezpečení a dodržování předpisů. Tento dokument se zaměřuje na:

- Způsob ochrany dat v klidovém umístění napříč Microsoft Azure
- Popisuje různé komponenty, které se účastní implementace ochrany dat.
- Kontroluje odborníky a nevýhody různých přístupů k ochraně správy klíčů.

Šifrování v klidovém umístění je běžným požadavkem zabezpečení. V Azure můžou organizace šifrovat neaktivní neaktivní data bez rizika nebo nákladů na vlastní řešení správy klíčů. Organizace mají možnost nechat si Azure kompletně spravovat šifrování v klidovém umístění. Organizace navíc mají k dispozici různé možnosti pro správu šifrovacích a šifrovacích klíčů.

## <a name="what-is-encryption-at-rest"></a>Co je šifrování v klidovém umístění?

Šifrování v klidovém umístění je kódování (šifrování) dat, když je trvalá. Šifrování v návrzích REST v Azure používá symetrické šifrování k rychlému šifrování a dešifrování velkých objemů dat podle jednoduchého koncepčního modelu:

- Symetrický šifrovací klíč se používá k šifrování dat při zápisu do úložiště.
- Stejný šifrovací klíč se používá k dešifrování těchto dat, protože jsou připravená k použití v paměti.
- Data mohou být rozdělena na oddíly a pro každý oddíl lze použít různé klíče.
- Klíče musí být uloženy v zabezpečeném umístění s řízením přístupu na základě identity a zásadami auditování. Šifrovací klíče dat se často šifrují pomocí klíčového šifrovacího klíče v Azure Key Vault k dalšímu omezení přístupu.

V praxi jsou scénáře správy a řízení klíčů a také zajištění škálování a dostupnosti vyžadovat další konstrukce. Šifrování Microsoft Azure v konceptech a součástech REST jsou popsány níže.

## <a name="the-purpose-of-encryption-at-rest"></a>Účel šifrování v klidovém umístění

Šifrování v klidovém umístění poskytuje ochranu dat pro uložená data (v klidovém umístění). Útoky proti neaktivním datům obsahují pokusy o získání fyzického přístupu k hardwaru, na kterém jsou data uložená, a následnému zabezpečení obsažených dat. V takovém případě může být pevný disk serveru během údržby špatně zpracován, což umožňuje útočníkovi odebrat pevný disk. Později by útočník uvedl pevný disk do počítače pod svým ovládacím prvkem, aby se pokusil o přístup k datům.

Šifrování v klidovém stavu je navrženo tak, aby zabránilo útočníkovi v přístupu k nezašifrovaným datům tím, že zajistí, že se data budou šifrovat při použití disku. Pokud útočník získá pevný disk se zašifrovanými daty, ale ne šifrovacími klíči, útočník musí toto šifrování před tím, než bude data číst. Tento útok je mnohem složitější a spotřebovává prostředky než přístup k nešifrovaným datům na pevném disku. Z tohoto důvodu se důrazně doporučuje šifrování v klidovém umístění a u mnoha organizací je potřeba mít vysokou prioritu.

Šifrování v klidové době může být vyžadováno i organizací potřebnou pro řízení dat a dodržování předpisů. Předpisy pro odvětví a státní správu, jako jsou HIPAA, PCI a FedRAMP, rozvrhnout konkrétní ochranná opatření týkající se ochrany dat a požadavků na šifrování. Šifrování v klidovém umístění je povinná míra požadovaná pro splnění některých z těchto předpisů. Další informace o přístupu Microsoftu k ověřování FIPS 140-2 najdete v článku [publikace standardu FIPS (Federal Information Processing Standard) 140-2](/microsoft-365/compliance/offering-fips-140-2).

Kromě dodržování předpisů a zákonných požadavků šifrování v klidovém umístění zajišťuje ochranu před důkladnou ochranou. Microsoft Azure poskytuje odpovídající platformu pro služby, aplikace a data. Poskytuje také komplexní zařízení a fyzické zabezpečení, řízení přístupu k datům a auditování. Je ale důležité zajistit další "překrývající" se bezpečnostní opatření pro případ, že jedna z ostatních bezpečnostních opatření nebude úspěšná a šifrování v klidovém případě zajišťuje takové bezpečnostní opatření.

Společnost Microsoft se zavazuje šifrovat možnosti v klidovém provozu napříč Cloud Services a poskytnout zákazníkům kontrolu nad šifrovacími klíči a protokoly použití klíčů. Kromě toho společnost Microsoft pracuje na šifrování všech zákaznických dat, která jsou ve výchozím nastavení v klidovém stavu.

## <a name="azure-encryption-at-rest-components"></a>Šifrování Azure v součástech REST

Jak už bylo popsáno, cílem šifrování v klidovém stavu je, že data, která jsou trvalá na disku, se šifrují pomocí tajného šifrovacího klíče. K dosažení tohoto cíle se musí zadat zabezpečené vytváření klíčů, úložiště, řízení přístupu a Správa šifrovacích klíčů. I když se můžou podrobnosti lišit, šifrování služeb Azure při implementacích Rest se dá považovat za postupy znázorněné v následujícím diagramu.

![Komponenty](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Umístění úložiště šifrovacích klíčů a řízení přístupu k těmto klíčům je centrální pro šifrování v modelu REST. Klíče je nutné vysoce zabezpečit, ale lze je spravovat pomocí zadaných uživatelů a dostupných pro konkrétní služby. Pro služby Azure je Azure Key Vault doporučené řešení úložiště klíčů a nabízí běžné prostředí pro správu napříč službami. Klíče se ukládají a spravují v trezorech klíčů a přístup k trezoru klíčů je možné udělit uživatelům nebo službám. Azure Key Vault podporuje vytváření klíčů nebo import klíčů zákazníka pro použití ve scénářích šifrovacích klíčů spravovaných zákazníkem.

### <a name="azure-active-directory"></a>Azure Active Directory

Oprávnění k použití klíčů uložených v Azure Key Vault, a to buď pro správu, nebo pro přístup k šifrování v klidovém šifrování a dešifrování, se dá předávat účtům Azure Active Directory.

### <a name="key-hierarchy"></a>Hierarchie klíčů

Při implementaci neaktivního šifrování se používá více než jeden šifrovací klíč. Uložení šifrovacího klíče v Azure Key Vault zajišťuje zabezpečený přístup k klíčům a centrální správu klíčů. Místní přístup k šifrovacím klíčům je ale efektivnější pro hromadné šifrování a dešifrování než při práci s Key Vault pro každou datovou operaci, což umožňuje silnější šifrování a lepší výkon. Omezení použití jediného šifrovacího klíče snižuje riziko ohrožení bezpečnosti klíče a náklady na opakované šifrování, když je nutné klíč nahradit. Šifrování Azure v modelech REST používá klíčovou hierarchii, která se skládá z následujících typů klíčů za účelem vyřešení všech těchto potřeb:

- **Šifrovací klíč dat (klíč DEK)** – symetrický AES256 klíč, který slouží k šifrování oddílu nebo bloku dat.  Jeden prostředek může mít mnoho oddílů a mnoho datových šifrovacích klíčů. Šifrování každého bloku dat jiným klíčem usnadňuje útokům na kryptografickou analýzu. Poskytovatel prostředků nebo instance aplikace, která šifruje a šifruje konkrétní blok, vyžaduje přístup k DEKs. Pokud je klíč DEK nahrazen novým klíčem, je nutné znovu zašifrovat pomocí nového klíče pouze data v jeho přidruženém bloku.
- Klíč **šifrovacího klíče (KEK)** – šifrovací klíč používaný k šifrování šifrovacích klíčů dat. Použití klíčového šifrovacího klíče, který nikdy neopouští Key Vault umožňuje šifrování a řízení šifrovacích klíčů dat. Entita, která má přístup k KEK, může být jiná než entita, která vyžaduje klíč dek. Entita může zprostředkovatelům přístup k klíč DEK omezit přístup ke každému klíč dek na konkrétní oddíl. Vzhledem k tomu, že KEK je vyžadován k dešifrování DEKs, je KEK v podstatě jediným bodem, pomocí kterého je DEKs možné efektivně odstranit odstraněním KEK.

Šifrovací klíče dat šifrované pomocí klíčového šifrovacího klíče se ukládají samostatně a k dešifrování těchto šifrovacích klíčů může použít jenom entita s přístupem ke klíčovým šifrovacím klíčem. Podporují se různé modely úložiště klíčů. Další informace najdete v tématu [modely šifrování dat](encryption-models.md) .

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Šifrování v klidovém umístění v cloudových službách Microsoftu

Služby Microsoft Cloud Services se používají ve všech třech cloudových modelech: IaaS, PaaS, SaaS. Níže najdete příklady toho, jak se vejdou na jednotlivé modely:

- Softwarové služby označované jako software jako server nebo SaaS, které obsahují aplikace poskytované cloudem, jako je například Microsoft 365.
- Služby platformy, které zákazníci využívají cloud ve svých aplikacích, a to s využitím cloudu pro věci, jako jsou úložiště, analýza a funkce služby Service Bus.
- Služby infrastruktury, neboli infrastruktura jako služba (IaaS), ve které zákazník nasazuje operační systémy a aplikace hostované v cloudu a případně využívá jiné cloudové služby.

### <a name="encryption-at-rest-for-saas-customers"></a>Šifrování v klidovém případě pro zákazníky SaaS

Zákazníci SaaS (software jako služba) obvykle mají povolené šifrování v klidovém provozu nebo dostupné v každé službě. Microsoft 365 má několik možností, jak zákazníkům ověřit nebo povolit šifrování v klidovém umístění. Informace o Microsoft 365 Services najdete v tématu [šifrování v Microsoft 365](/microsoft-365/compliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Šifrování v klidovém případě pro zákazníky PaaS

Data zákazníka typu platforma jako služba (PaaS) se obvykle nacházejí ve službě úložiště, jako je například Blob Storage, ale mohou být také uložena do mezipaměti nebo ukládána do prostředí pro spuštění aplikace, jako je například virtuální počítač. Pokud chcete zobrazit možnosti šifrování v klidovém provozu, které máte k dispozici, Prohlédněte si níže uvedenou tabulku pro platformy úložiště a aplikací, které používáte.

### <a name="encryption-at-rest-for-iaas-customers"></a>Šifrování v klidovém případě pro zákazníky IaaS

Zákazníci infrastruktury jako služby (IaaS) můžou mít různé služby a aplikace, které se používají. Služba IaaS Services může povolit šifrování v klidovém formátu virtuálních počítačů a virtuálních pevných disků Azure pomocí Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Šifrované úložiště

Podobně jako PaaS můžou řešení IaaS využívat i další služby Azure, které ukládají zašifrovaná data v klidovém stavu. V těchto případech můžete povolit šifrování v rámci podpory REST, které poskytuje každá spotřebovaná služba Azure. Níže uvedená tabulka shrnuje hlavní platformy úložiště, služeb a aplikací a model podporovaného šifrování v klidovém úložišti.

#### <a name="encrypted-compute"></a>Šifrované výpočetní prostředky

Všechny Managed Disks, snímky a image se šifrují pomocí Šifrování služby Storage pomocí klíče spravovaného službou. Úplnější šifrování v řešení REST zajišťuje, aby se data nikdy nezachovala v nezašifrované podobě. Při zpracování dat na virtuálním počítači může být data trvalá v souboru stránky Windows nebo v systému souborů odkládacího souboru Linux, ve výpisu stavu systému nebo do protokolu aplikace. Aby byla tato data zašifrovaná v klidovém stavu, můžou aplikace IaaS používat Azure Disk Encryption na virtuálním počítači Azure IaaS (Windows nebo Linux) a na virtuálním disku.

#### <a name="custom-encryption-at-rest"></a>Vlastní šifrování v klidovém umístění

Doporučuje se, aby aplikace IaaS využily Azure Disk Encryption a šifrování při možnostech REST, které poskytují všechny spotřebované služby Azure. V některých případech, například v případě neplatných požadavků na šifrování nebo úložiště založeného na neazure, může vývojář aplikace IaaS potřebovat implementovat šifrování v klidovém umístění. Vývojáři řešení IaaS se můžou lépe integrovat se správou Azure a očekáváními zákazníků díky využití určitých komponent Azure. Vývojáři by konkrétně měli používat službu Azure Key Vault k poskytování zabezpečeného úložiště klíčů a poskytovat zákazníkům s konzistentními možnostmi správy klíčů u většiny služeb platformy Azure. Vlastní řešení navíc by měla používat Azure-Managed identity služby k povolení účtů služeb pro přístup k šifrovacím klíčům. Informace o vývojářích Azure Key Vault a identitách spravované služby najdete v příslušných sadách SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Podpora modelu šifrování pro poskytovatele prostředků Azure

Služba Microsoft Azure Services každý podporuje jedno nebo více šifrování v modelech REST. U některých služeb se ale nemusí použít jeden nebo více modelů šifrování. Pro služby, které podporují klíčové scénáře spravované zákazníky, můžou podporovat jenom podmnožinu typů klíčů, které Azure Key Vault podporuje pro klíče šifrování klíčů. Kromě toho mohou služby vydávat podporu pro tyto scénáře a typy klíčů v různých plánech. Tato část popisuje šifrování při podpoře REST v době psaní pro každou hlavní službu Azure Data Storage.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Každý zákazník, který používá funkce infrastruktury jako služby (IaaS) Azure, může pro své virtuální počítače s IaaS a disky prostřednictvím Azure Disk Encryption dosáhnout šifrování v klidovém provozu. Další informace o službě Azure Disk Encryption najdete v [dokumentaci k Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md).

#### <a name="azure-storage"></a>Azure Storage

Všechny Azure Storage služby (úložiště objektů blob, úložiště front, úložiště tabulek a soubory Azure) podporují šifrování na straně serveru v klidovém umístění. Některé služby dále podporují klíče spravované zákazníkem a šifrování na straně klienta.

- Na straně serveru: všechny Azure Storage služby umožňují ve výchozím nastavení šifrování na straně serveru pomocí klíčů spravovaných službou, které jsou pro aplikaci transparentní. Další informace najdete v tématu [šifrování služby Azure Storage pro](../../storage/common/storage-service-encryption.md)neaktivní neaktivní data. Azure Blob Storage a soubory Azure podporují také RSA 2048 klíče spravované zákazníkem v Azure Key Vault. Další informace najdete v tématu [šifrování služby Storage používání klíčů spravovaných zákazníkem v Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).
- Klientská strana: objekty blob, tabulky a fronty Azure podporují šifrování na straně klienta. Při použití šifrování na straně klienta zašifrují zákazníci data a odesílají data jako zašifrovaný objekt BLOB. Správu klíčů provádí zákazník. Další informace najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database aktuálně podporuje šifrování v klidovém provozu pro scénáře pro šifrování na straně služby spravované Microsoftem a na straně klienta.

Podpora pro šifrování serveru se v současnosti poskytuje prostřednictvím funkce SQL s názvem transparentní šifrování dat. Jakmile zákazník Azure SQL Database povolí, automaticky se vytvoří a nařídí klíč TDE. Šifrování v klidovém umístění lze povolit na úrovni databáze a serveru. Od června 2017 je [transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) ve výchozím nastavení povolená u nově vytvořených databází. Azure SQL Database podporuje RSA 2048 klíčů spravovaných zákazníkem v Azure Key Vault. Další informace najdete v tématu [transparentní šifrování dat s podporou Bring Your Own Key pro Azure SQL Database a datový sklad](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql).

Šifrování Azure SQL Databasech dat na straně klienta je podporováno prostřednictvím funkce [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) . Always Encrypted používá klíč, který vytvořil a uložil klient. Zákazníci můžou hlavní klíč Uložit do úložiště certifikátů Windows, Azure Key Vault nebo místního modulu hardwarového zabezpečení. Pomocí SQL Server Management Studio uživatelé SQL zvolí, který klíč mají použít k zašifrování, který sloupec.

## <a name="conclusion"></a>Závěr

Ochrana zákaznických dat uložených v rámci služeb Azure má pro společnost Microsoft prvořadý význam. Všechny služby hostované v Azure se zavázaly zajistit možnosti šifrování v klidovém prostředí. Služba Azure podporuje buď klíče spravované službou, klíče spravované zákazníkem nebo šifrování na straně klienta. Služby Azure jsou široce zaměřené na šifrování v klidové dostupnosti a nové možnosti se plánují pro náhled a obecnou dostupnost v nadcházejících měsících.

## <a name="next-steps"></a>Další kroky

- Další informace o klíčích spravovaných službou a klíčích spravovaných zákazníkem najdete v tématu [modely šifrování dat](encryption-models.md) .
- Přečtěte si, jak Azure používá [dvojité šifrování](double-encryption.md) ke zmírnění hrozeb, které jsou součástí šifrování dat.
- Zjistěte, co Microsoft zajišťuje [integritu platformy a zabezpečení](platform.md) hostitelů, kteří přecházejí mezi kanály pro vytváření a vytváření, integraci, provozování a opravy firmwaru.
