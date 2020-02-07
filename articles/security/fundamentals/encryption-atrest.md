---
title: Šifrování dat Microsoft Azure v klidovém případě | Microsoft Docs
description: Tento článek poskytuje přehled o Microsoft Azure šifrování dat v klidovém formátu, celkové možnosti a obecné informace.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2019
ms.author: barclayn
ms.openlocfilehash: d814058866991b3d94363125870c27ae170b3ae8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064355"
---
# <a name="azure-data-encryption-at-rest"></a>Šifrování dat Azure – v klidovém případě

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

Šifrování v klidové době může být vyžadováno i organizací potřebnou pro řízení dat a dodržování předpisů. Předpisy pro odvětví a státní správu, jako jsou HIPAA, PCI a FedRAMP, rozvrhnout konkrétní ochranná opatření týkající se ochrany dat a požadavků na šifrování. Šifrování v klidovém umístění je povinná míra požadovaná pro splnění některých z těchto předpisů.

Kromě dodržování předpisů a zákonných požadavků šifrování v klidovém umístění zajišťuje ochranu před důkladnou ochranou. Microsoft Azure poskytuje odpovídající platformu pro služby, aplikace a data. Poskytuje také komplexní zařízení a fyzické zabezpečení, řízení přístupu k datům a auditování. Je ale důležité zajistit další "překrývající" se bezpečnostní opatření pro případ, že jedna z dalších bezpečnostních opatření neproběhne úspěšně a šifrování v klidovém případě zajišťuje bezpečnostní opatření.

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

Šifrovací klíče dat šifrované pomocí klíčového šifrovacího klíče se ukládají samostatně a k dešifrování těchto šifrovacích klíčů může použít jenom entita s přístupem ke klíčovým šifrovacím klíčem. Podporují se různé modely úložiště klíčů. Každý model se podrobněji probere dále v další části.

## <a name="data-encryption-models"></a>Modely šifrování dat

Porozumění různým modelům šifrování a jejich specialistům a nevýhodám je nezbytné pro porozumění způsobu, jakým různé poskytovatele prostředků v Azure implementují šifrování v klidovém formátu. Tyto definice se sdílejí napříč všemi poskytovateli prostředků v Azure, aby se zajistil společný jazyk a taxonomie.

Pro šifrování na straně serveru existují tři scénáře:

- Šifrování na straně serveru pomocí klíčů spravovaných službou
  - Poskytovatelé prostředků Azure provádějí operace šifrování a dešifrování.
  - Microsoft spravuje klíče
  - Plná funkčnost cloudu

- Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v Azure Key Vault
  - Poskytovatelé prostředků Azure provádějí operace šifrování a dešifrování.
  - Klíče řízení zákazníka prostřednictvím Azure Key Vault
  - Plná funkčnost cloudu

- Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem na hardwaru ovládaném zákazníkem
  - Poskytovatelé prostředků Azure provádějí operace šifrování a dešifrování.
  - Klávesy řízení zákazníka na hardwaru ovládaném zákazníkem
  - Plná funkčnost cloudu

Pro šifrování na straně klienta Vezměte v úvahu následující skutečnosti:

- Služby Azure nevidí dešifrovaná data
- Zákazníci spravují a ukládají klíče místně (nebo v jiných zabezpečených úložištích). Pro služby Azure nejsou klíče k dispozici.
- Omezené funkce cloudu

Podporované modely šifrování v Azure se rozdělí do dvou hlavních skupin: "šifrování klienta" a "šifrování na straně serveru", jak je uvedeno výše. Nezávisle na šifrování používaného modelu REST služby Azure vždycky doporučují používat zabezpečený přenos, jako je TLS nebo HTTPS. Šifrování v přenosu by proto mělo být adresováno transportním protokolem a nemělo by být hlavním faktorem při určování šifrování používaného modelu REST.

### <a name="client-encryption-model"></a>Model šifrování klienta

Model šifrování klienta odkazuje na šifrování, které se provádí mimo poskytovatele prostředků nebo Azure pomocí služby nebo volání aplikace. Šifrování může provádět aplikace služby v Azure nebo aplikace spuštěná v zákaznickém datovém centru. Pokud se v obou případech využívá tento model šifrování, poskytovatel prostředků Azure obdrží zašifrovaný objekt BLOB dat bez možnosti dešifrovat data jakýmkoli způsobem nebo mít přístup k šifrovacím klíčům. V tomto modelu se Správa klíčů provádí pomocí volající služby nebo aplikace a je neprůhledná pro službu Azure.

![Klient](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model šifrování na straně serveru

Modely šifrování na straně serveru odkazují na šifrování, které provádí služba Azure. V tomto modelu poskytovatel prostředků provádí operace šifrování a dešifrování. Azure Storage například může přijímat data v operacích s prostým textem a provede šifrování a dešifrování interně. Poskytovatel prostředků může používat šifrovací klíče spravované společností Microsoft nebo zákazníkem v závislosti na zadané konfiguraci.

![Server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modely správy šifrovacích klíčů na straně serveru

Každé šifrování na straně serveru v modelech REST zahrnuje různé charakteristiky správy klíčů. To zahrnuje místo, kde a jak se vytvářejí šifrovací klíče a ukládají se i modely přístupu a postupy střídání klíčů.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Šifrování na straně serveru pomocí klíčů spravovaných službou

U mnoha zákazníků je základním požadavkem zajistit, aby data byla zašifrována, kdykoli jsou v klidovém stavu. Šifrování na straně serveru pomocí klíčů spravovaných službou umožňuje tomuto modelu povolit zákazníkům označit konkrétní prostředek (účet úložiště, databázi SQL atd.) pro šifrování a ponechat všechny aspekty správy klíčů, jako je vystavování klíčů, rotace a zálohování do Microsoftu. . Většina služeb Azure, které podporují šifrování v klidovém umístění, obvykle podporuje tento model přesměrování správy šifrovacích klíčů do Azure. Poskytovatel prostředků Azure vytvoří klíče, umístí je do zabezpečeného úložiště a v případě potřeby je načte. To znamená, že služba má úplný přístup ke klíčům a služba má plnou kontrolu nad správou životního cyklu přihlašovacích údajů.

![spravovaná](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Šifrování na straně serveru pomocí klíčů spravovaných službou proto rychle řeší nutnost šifrování v klidovém provozu s nízkou režií zákazníkovi. Pokud je k dispozici zákazník, obvykle otevře Azure Portal pro cílového předplatného a poskytovatele prostředků a zkontroluje box s označením, že chcete data šifrovat. V některých správců prostředků je šifrování na straně serveru s klíči spravovanými službami ve výchozím nastavení zapnuté.

Šifrování na straně serveru pomocí klíčů spravovaných Microsoftem předpokládá, že služba má úplný přístup k ukládání a správě klíčů. I když někteří zákazníci můžou chtít klíče spravovat, protože mají větší zabezpečení, měli byste při vyhodnocování tohoto modelu vzít v úvahu náklady a rizika spojené s vlastním řešením úložiště klíčů. V mnoha případech může organizace určit, že omezení prostředků nebo rizika místního řešení můžou být větší než riziko správy cloudu šifrování v klávesách REST.  Tento model ale nemusí být dostačující pro organizace, které mají požadavky na řízení vytváření nebo životního cyklu šifrovacích klíčů nebo mají různé pracovníky spravovat šifrovací klíče služby, než je Správa služby (tj. oddělení). správy klíčů z celkového modelu správy pro službu).

##### <a name="key-access"></a>Přístup ke klíči

Pokud se používá šifrování na straně serveru pomocí klíčů spravovaných službou, je vytvoření klíče, úložiště a přístup ke službě spravováno službou. Obecně platí, že základní poskytovatelé prostředků Azure budou ukládat šifrovací klíče dat do úložiště, které je blízko dat a budou rychle dostupné a přístupné, když jsou šifrovací klíče uložené v zabezpečeném interním úložišti.

**Výhody**

- Jednoduché nastavení
- Microsoft spravuje střídání klíčů, zálohování a redundanci.
- Zákazník nemá náklady spojené s implementací nebo rizikem vlastního schématu správy klíčů.

**Nevýhody**

- Žádné řízení od zákazníka u šifrovacích klíčů (specifikace klíče, životní cyklus, odvolání atd.)
- Není možné oddělit správu klíčů od celkového modelu správy pro službu.

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v Azure Key Vault

U scénářů, kde požadavek slouží k šifrování uložených dat a řízení uživatelů šifrovacích klíčů, můžou používat šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v Key Vault. Některé služby můžou ukládat jenom šifrovací klíč kořenového klíče v Azure Key Vault a uložit šifrovaný šifrovací klíč dat do interního umístění blíže k datům. V takovém scénáři můžou zákazníci přinášet vlastní klíče Key Vault (BYOK – Bring Your Own Key) nebo generovat nové a použít je k zašifrování požadovaných prostředků. I když poskytovatel prostředků provádí operace šifrování a dešifrování, používá nakonfigurovaný klíč šifrování klíče jako kořenový klíč pro všechny operace šifrování.

Ztráta klíčového šifrovacího klíče znamená ztrátu dat. Z tohoto důvodu by klíče neměly být odstraněny. Klíče by se měly zálohovat vždy, když se vytvoří nebo otočí. [Obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) by mělo být povolené v jakémkoli trezoru, který ukládá šifrovací klíče klíčů. Místo odstranění klíče nastavte možnost povoleno na hodnotu NEPRAVDA nebo nastavte datum vypršení platnosti.

##### <a name="key-access"></a>Přístup ke klíči

Model šifrování na straně serveru s klíčem spravovaným zákazníkem v Azure Key Vault zahrnuje službu, která při přístupu k klíčům zašifruje a dešifruje podle potřeby. Šifrování v klíčích REST zpřístupňuje služba prostřednictvím zásad řízení přístupu. Tato zásada uděluje přístup k identitě služby pro získání klíče. Služba Azure spuštěná v rámci přidruženého předplatného se dá nakonfigurovat s identitou v tomto předplatném. Služba může provést Azure Active Directory ověřování a získat ověřovací token, který identifikuje sám sebe jako tuto službu jednající jménem předplatného. Pomocí tohoto tokenu je pak možné Key Vault získat klíč, kterému byl udělen přístup k.

U operací využívajících šifrovací klíče může být identitě služby udělen přístup k některým z následujících postupů: dešifrovat, šifrovat, unwrapKey, wrapKey, ověřit, podepsat, získat, vypsat, aktualizovat, vytvořit, importovat, odstranit, zálohovat a obnovit.

Chcete-li získat klíč pro použití při šifrování nebo dešifrování dat v případě, že je spuštěna instance služby Správce prostředků, musí mít UnwrapKey (k získání klíče pro dešifrování) a WrapKey (pro vložení klíče do trezoru klíčů při vytváření nového klíče).

>[!NOTE]
>Další podrobnosti o autorizaci Key Vault najdete na stránce zabezpečení trezoru klíčů v [dokumentaci k Azure Key Vault](../../key-vault/key-vault-secure-your-key-vault.md).

**Výhody**

- Úplná kontrola nad použitými klíči – šifrovací klíče se spravují v Key Vault zákazníka pod řízením zákazníka.
- Možnost šifrovat více služeb do jedné hlavní větve
- Může oddělit správu klíčů od celkového modelu správy pro službu.
- Může definovat službu a umístění klíčů v různých oblastech.

**Nevýhody**

- Zákazník má plnou zodpovědnost za správu přístupu ke klíčům.
- Zákazník má plnou zodpovědnost za správu životního cyklu klíčů.
- Další nastavení & režijních nákladů na konfiguraci

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v hardwaru ovládaném zákazníkem

Některé služby Azure umožňují model správy klíčů hostitele vlastní klíč (HYOK). Tento režim správy je užitečný ve scénářích, kdy je potřeba zašifrovat neaktivní data a spravovat klíče ve speciálním úložišti mimo kontrolu Microsoftu. V tomto modelu musí služba získat klíč z externího webu. Jsou ovlivněny záruky výkonu a dostupnosti a konfigurace je složitější. Vzhledem k tomu, že služba má přístup k klíč DEK během operace šifrování a dešifrování, jsou celkové záruky zabezpečení tohoto modelu podobné tomu, když jsou klíče spravované zákazníkem v Azure Key Vault.  V důsledku toho tento model není vhodný pro většinu organizací, pokud nemá specifické požadavky na správu klíčů. V důsledku těchto omezení většina služeb Azure nepodporuje šifrování na straně serveru pomocí klíčů spravovaných serverem v hardwaru ovládaném zákazníkem.

##### <a name="key-access"></a>Přístup ke klíči

Když se používá šifrování na straně serveru pomocí klíčů spravovaných službou v hardwaru ovládaném zákazníkem, klíče se udržují v systému nakonfigurovaném zákazníkem. Služby Azure, které podporují tento model, poskytují prostředky pro vytvoření zabezpečeného připojení k úložišti klíčů poskytovanému zákazníkem.

**Výhody**

- Úplná kontrola nad použitým kořenovým klíčem – šifrovací klíče se spravují pomocí zákaznického úložiště.
- Možnost šifrovat více služeb do jedné hlavní větve
- Může oddělit správu klíčů od celkového modelu správy pro službu.
- Může definovat službu a umístění klíčů v různých oblastech.

**Nevýhody**

- Plná zodpovědnost za úložiště klíčů, zabezpečení, výkon a dostupnost
- Plná zodpovědnost za správu přístupu ke klíčům
- Plná zodpovědnost za správu životního cyklu klíčů
- Významné náklady na instalaci, konfiguraci a průběžnou údržbu
- Zvýšená závislost na dostupnosti sítě mezi zákaznickým datovým centrem a datacentry Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Šifrování v klidovém umístění v cloudových službách Microsoftu

Služby Microsoft Cloud Services se používají ve všech třech cloudových modelech: IaaS, PaaS, SaaS. Níže najdete příklady toho, jak se vejdou na jednotlivé modely:

- Softwarové služby označované jako software jako server nebo SaaS, které obsahují aplikace poskytované cloudem, jako je například Office 365.
- Služby platformy, které zákazníci využívají cloud ve svých aplikacích, a to s využitím cloudu pro věci, jako jsou úložiště, analýza a funkce služby Service Bus.
- Služby infrastruktury, neboli infrastruktura jako služba (IaaS), ve které zákazník nasazuje operační systémy a aplikace hostované v cloudu a případně využívá jiné cloudové služby.

### <a name="encryption-at-rest-for-saas-customers"></a>Šifrování v klidovém případě pro zákazníky SaaS

Zákazníci SaaS (software jako služba) obvykle mají povolené šifrování v klidovém provozu nebo dostupné v každé službě. Sada Office 365 má několik možností, jak zákazníkům ověřit nebo povolit šifrování v klidovém umístění. Informace o službách Office 365 najdete v tématu [šifrování v sadě office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Šifrování v klidovém případě pro zákazníky PaaS

Data zákazníka typu platforma jako služba (PaaS) se obvykle nacházejí ve službě úložiště, jako je například Blob Storage, ale mohou být také uložena do mezipaměti nebo ukládána do prostředí pro spuštění aplikace, jako je například virtuální počítač. Pokud chcete zobrazit možnosti šifrování v klidovém provozu, které máte k dispozici, Prohlédněte si níže uvedenou tabulku pro platformy úložiště a aplikací, které používáte.

### <a name="encryption-at-rest-for-iaas-customers"></a>Šifrování v klidovém případě pro zákazníky IaaS

Zákazníci infrastruktury jako služby (IaaS) můžou mít různé služby a aplikace, které se používají. Služba IaaS Services může povolit šifrování v klidovém formátu virtuálních počítačů a virtuálních pevných disků Azure pomocí Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Šifrované úložiště

Podobně jako PaaS můžou řešení IaaS využívat i další služby Azure, které ukládají zašifrovaná data v klidovém stavu. V těchto případech můžete povolit šifrování v rámci podpory REST, které poskytuje každá spotřebovaná služba Azure. Níže uvedená tabulka shrnuje hlavní platformy úložiště, služeb a aplikací a model podporovaného šifrování v klidovém úložišti. 

#### <a name="encrypted-compute"></a>Šifrované výpočetní prostředky

Všechny Managed Disks, snímky a image se šifrují pomocí Šifrování služby Storage pomocí klíče spravovaného službou. Úplnější šifrování v řešení REST zajišťuje, aby se data nikdy nezachovala v nezašifrované podobě. Při zpracování dat na virtuálním počítači může být data trvalá v souboru stránky Windows nebo v systému souborů odkládacího souboru Linux, ve výpisu stavu systému nebo do protokolu aplikace. Aby byla tato data zašifrovaná v klidovém stavu, můžou aplikace IaaS používat Azure Disk Encryption na virtuálním počítači Azure IaaS (Windows nebo Linux) a na virtuálním disku.

#### <a name="custom-encryption-at-rest"></a>Vlastní šifrování v klidovém umístění

Doporučuje se, aby aplikace IaaS využily Azure Disk Encryption a šifrování při možnostech REST, které poskytují všechny spotřebované služby Azure. V některých případech, například v případě neplatných požadavků na šifrování nebo úložiště založeného na neazure, může vývojář aplikace IaaS potřebovat implementovat šifrování v klidovém umístění. Vývojáři řešení IaaS se můžou lépe integrovat se správou Azure a očekáváními zákazníků díky využití určitých komponent Azure. Vývojáři by konkrétně měli používat službu Azure Key Vault k poskytování zabezpečeného úložiště klíčů a poskytovat zákazníkům s konzistentními možnostmi správy klíčů u většiny služeb platformy Azure. Vlastní řešení navíc by měla používat identity služby spravované v Azure k povolení účtů služeb pro přístup k šifrovacím klíčům. Informace o vývojářích Azure Key Vault a identitách spravované služby najdete v příslušných sadách SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Podpora modelu šifrování pro poskytovatele prostředků Azure

Služba Microsoft Azure Services každý podporuje jedno nebo více šifrování v modelech REST. U některých služeb se ale nemusí použít jeden nebo více modelů šifrování. Pro služby, které podporují klíčové scénáře spravované zákazníky, můžou podporovat jenom podmnožinu typů klíčů, které Azure Key Vault podporuje pro klíče šifrování klíčů. Kromě toho mohou služby vydávat podporu pro tyto scénáře a typy klíčů v různých plánech. Tato část popisuje šifrování při podpoře REST v době psaní pro každou hlavní službu Azure Data Storage.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Každý zákazník, který používá funkce infrastruktury jako služby (IaaS) Azure, může pro své virtuální počítače s IaaS a disky prostřednictvím Azure Disk Encryption dosáhnout šifrování v klidovém provozu. Další informace o službě Azure Disk Encryption najdete v [dokumentaci k Azure Disk Encryption](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Úložiště Azure

Všechny Azure Storage služby (úložiště objektů blob, úložiště front, úložiště tabulek a soubory Azure) podporují šifrování na straně serveru v klidovém umístění. Některé služby dále podporují klíče spravované zákazníkem a šifrování na straně klienta. 

- Na straně serveru: všechny Azure Storage služby umožňují ve výchozím nastavení šifrování na straně serveru pomocí klíčů spravovaných službou, které jsou pro aplikaci transparentní. Další informace najdete v tématu [šifrování služby Azure Storage pro](../../storage/common/storage-service-encryption.md)neaktivní neaktivní data. Azure Blob Storage a soubory Azure podporují také RSA 2048 klíče spravované zákazníkem v Azure Key Vault. Další informace najdete v tématu [šifrování služby Storage používání klíčů spravovaných zákazníkem v Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Klientská strana: objekty blob, tabulky a fronty Azure podporují šifrování na straně klienta. Při použití šifrování na straně klienta zašifrují zákazníci data a odesílají data jako zašifrovaný objekt BLOB. Správu klíčů provádí zákazník. Další informace najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database aktuálně podporuje šifrování v klidovém provozu pro scénáře pro šifrování na straně služby spravované Microsoftem a na straně klienta.

Podpora pro šifrování serveru se v současnosti poskytuje prostřednictvím funkce SQL s názvem transparentní šifrování dat. Jakmile zákazník Azure SQL Database povolí, automaticky se vytvoří a nařídí klíč TDE. Šifrování v klidovém umístění lze povolit na úrovni databáze a serveru. Od června 2017 je [transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) ve výchozím nastavení povolená u nově vytvořených databází. Azure SQL Database podporuje RSA 2048 klíčů spravovaných zákazníkem v Azure Key Vault. Další informace najdete v tématu [transparentní šifrování dat s podporou Bring Your Own Key pro Azure SQL Database a datový sklad](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Šifrování Azure SQL Databasech dat na straně klienta je podporováno prostřednictvím funkce [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) . Always Encrypted používá klíč, který vytvořil a uložil klient. Zákazníci můžou hlavní klíč Uložit do úložiště certifikátů Windows, Azure Key Vault nebo místního modulu hardwarového zabezpečení. Pomocí SQL Server Management Studio uživatelé SQL zvolí, který klíč mají použít k zašifrování, který sloupec.

#### <a name="encryption-model-and-key-management-table"></a>Model šifrování a tabulka správy klíčů

|                                  |                    | **Model šifrování a Správa klíčů** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Na straně serveru pomocí klíče spravovaného službou**     | **Na straně serveru pomocí klíče spravovaného zákazníkem**             | **Klientská strana s použitím klíče spravovaného klientem**      |
| **AI a Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Ano                | Ano                | -                  |
| Azure Machine Learning           | Ano                | Ano                | -                  |
| Azure Machine Learning Studio    | Ano                | Verze Preview, RSA 2048-bit | -               |
| Power BI                         | Ano                | Verze Preview, RSA 2048-bit | -                  |
| **Analýzy**                    |                    |                    |                    |
| Azure Stream Analytics           | Ano                | -                  | -                  |
| Event Hubs                       | Ano                | Ano, všechny délky RSA. | -                  |
| Funkce                        | Ano                | Ano, všechny délky RSA. | -                  |
| Azure Analysis Services          | Ano                | -                  | -                  |
| Katalog dat Azure               | Ano                | -                  | -                  |
| Apache Kafka ve službě Azure HDInsight  | Ano                | Všechny délky RSA.   | -                  |
| Azure Monitor Application Insights | Ano                | Ano                | -                  |
| Azure Monitor Log Analytics | Ano                | Ano                | -                  |
| Průzkumník dat Azure              | Ano                | Ano                | -                  |
| Azure Data Factory               | Ano                | Ano                | -                  |
| Azure Data Lake Store            | Ano                | Ano, RSA 2048-bit  | -                  |
| **Kontejnery**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ano                | Ano                | -                  |
| Container Instances              | Ano                | Ano                | -                  |
| Container Registry               | Ano                | Ano                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtuální počítače                 | Ano                | Ano, RSA 2048-bit  | -                  |
| Sada škálování virtuálních počítačů        | Ano                | Ano, RSA 2048-bit  | -                  |
| SAP HANA                         | Ano                | Ano, RSA 2048-bit  | -                  |
| App Service                      | Ano                | Ano                | -                  |
| Automation                       | Ano                | Ano                | -                  |
| Azure Portal                     | Ano                | Ano                | -                  |
| Logic Apps                       | Ano                | Ano                | -                  |
| Azure Managed Applications       | Ano                | Ano                | -                  |
| Service Bus                      | Ano                | Ano                | -                  |
| Site Recovery                    | Ano                | Ano                | -                  |
| **Databáze**                    |                    |                    |                    |
| SQL Server na virtuálních počítačích   | Ano                | Ano, RSA 2048-bit  | Ano                |
| Azure SQL Database               | Ano                | Ano, RSA 2048-bit  | Ano                |
| Azure SQL Database pro MariaDB   | Ano                | -                  | -                  |
| Azure SQL Database pro MySQL     | Ano                | Ano                | -                  |
| Azure SQL Database pro PostgreSQL | Ano               | Ano                | -                  |
| Azure Synapse Analytics          | Ano                | Ano, RSA 2048-bit  | Ano                |
| SQL Server Stretch Database      | Ano                | Ano, RSA 2048-bit  | Ano                |
| Table Storage                    | Ano                | Ano                | Ano                |
| Azure Cosmos DB                  | Ano                | Ano                | -                  |
| Azure Databricks                 | Ano                | Ano                | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Ano                | -                  | Ano                |
| Azure Repos                      | Ano                | -                  | Ano                |
| **Identita**                     |                    |                    |                    |
| Azure Active Directory           | Ano                | -                  | -                  |
| Azure Active Directory Domain Services | Ano          | Ano, RSA 2048-bit  | -                  |
| **Spolupráci**                  |                    |                    |                    |
| Service Bus                      | Ano                | Ano                | Ano                |
| Event Grid                       | Ano                | -                  | -                  |
| API Management                   | Ano                | -                  | -                  |
| **Služby IoT**                 |                    |                    |                    |
| IoT Hub                          | Ano                | Ano                | Ano                |
| **Správa a zásady správného řízení**    |                    |                    |                    |
| Azure Site Recovery              | Ano                | -                  | -                  |
| **Média**                        |                    |                    |                    |
| Media Services                   | Ano                | -                  | Ano                |
| **Úložiště**                      |                    |                    |                    |
| Blob Storage                     | Ano                | Ano, RSA 2048-bit  | Ano                |
| Disk Storage                     | Ano                | Ano                | -                  |
| Spravované Disk Storage             | Ano                | Ano                | -                  |
| File Storage                     | Ano                | Ano, RSA 2048-bit  | -                  |
| Queue Storage                    | Ano                | Ano                | Ano                |
| Avere vFXT                       | Ano                | -                  | -                  |
| Azure NetApp Files               | Ano                | -                  | -                  |
| Archiv služby Storage                  | Ano                | Ano, RSA 2048-bit  | -                  |
| StorSimple                       | Ano                | Ano, RSA 2048-bit  | Ano                |
| Azure Backup                     | Ano                | Ano                | Ano                |
| Data Box                         | Ano                | -                  | Ano                |
| Data Box Edge                    | Ano                | Ano                | -                  |

## <a name="conclusion"></a>Závěr

Ochrana zákaznických dat uložených v rámci služeb Azure má pro společnost Microsoft prvořadý význam. Všechny služby hostované v Azure se zavázaly zajistit možnosti šifrování v klidovém prostředí. Základní služby, jako jsou Azure Storage, Azure SQL Database a Key Analytics a služba Intelligence, již poskytují šifrování v možnostech REST. Některé z těchto služeb podporují klíče řízené zákazníky a šifrování na straně klienta i klíče a šifrování spravované službou. Služby Microsoft Azure Services v současné době zvyšují šifrování v klidové dostupnosti a v nadcházejících měsících jsou plánovány nové možnosti pro verzi Preview a obecnou dostupnost.
