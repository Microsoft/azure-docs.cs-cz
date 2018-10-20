---
title: Microsoft Azure Data šifrování neaktivních | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Microsoft Azure data šifrování v klidovém stavu, celkově a Obecné aspekty.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 40c69d996721b664fbea5cd539f60ecc6a521d24
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457642"
---
# <a name="azure-data-encryption-at-rest"></a>Azure Data šifrování neaktivních

Microsoft Azure zahrnuje nástroje na trhu při ochraně dat podle potřeb zabezpečení a dodržování předpisů vaší společnosti. Tento dokument se zaměřuje na:

- Způsob ochrany dat v klidovém stavu v Microsoft Azure
- Tento článek popisuje různé součásti účastní implementace ochrany dat
- Obsahuje přehled výhody a nevýhody ochrany přístupy různých správu klíčů. 

Šifrování v klidovém stavu je běžné požadavky zabezpečení. V Azure můžete dosáhnout organizace šifrování v klidovém stavu bez nutnosti náklady na implementaci a správu a riziko řešení pro správu vlastních klíčů. Organizace mají možnost umožňuje plně spravovat šifrování v klidovém stavu Azure. Kromě toho organizace mají různé možnosti, jak podrobně spravovat šifrování nebo šifrovací klíče.

## <a name="what-is-encryption-at-rest"></a>Co je šifrování v klidovém stavu?

Šifrování v klidovém stavu je kódování (šifrování) dat, když je trvalá. Šifrování v Rest návrhů v Azure pomocí šifrování se symetrickým šifrování a dešifrování velkého objemu dat rychle podle jednoduchých konceptuálního modelu:

- Symetrický šifrovací klíč se používá k šifrování dat při zápisu do úložiště. 
- Stejný šifrovací klíč se používá k dešifrování dat, jako je readied pro použití v paměti.
- Data mohou být rozdělit na oddíly a různé klíče mohou být použity pro každý oddíl.
- Klíče musí být uložen na bezpečném místě pomocí zásad řízení přístupu k omezení přístupu k určitým identity a protokolování použití klíče. Šifrovací klíče dat jsou často zašifrované pomocí asymetrického šifrování dá dál omezovat přístup.

V praxi klíčové scénáře správy a řízení, jakož i škálování a dostupnost záruky, vyžadují další konstrukce. Microsoft Azure šifrování v Rest konceptů a součástí jsou popsané níže.

## <a name="the-purpose-of-encryption-at-rest"></a>Účelem šifrování v klidovém stavu

Šifrování v klidovém stavu poskytuje ochranu dat pro uložená data (v klidovém stavu). Útoky na data v klidovém stavu zahrnout pokusy o získání fyzického přístupu na hardware, na kterém jsou data uložená a potom ohrozit zabezpečení dat omezením. Takový útok pevného disku serveru může mít byla manipulace při údržbě, což útočníkovi umožňuje odebrat pevného disku. Útočník by později vložit pevného disku do počítači v rámci pod kontrolou pokus o přístup k datům. 

Šifrování v klidovém stavu umožňuje útočníkovi zabránit v přístupu k nešifrovaná data zajištěním, data se šifrují na disku. Pokud útočník získat pevný disk s takovým šifrovaných dat a přístup k šifrovacím klíčům, by útočník ohrozit data bez obtíží skvělé. V takové situaci by musel pokusit útoků, šifrovaná data, která jsou mnohem složitější a využívání prostředků než přímý přístup k bez šifrování dat na pevném disku. Šifrování v klidovém stavu z tohoto důvodu se důrazně doporučuje a je s vysokou prioritou požadavkem pro mnoho společností. 

Šifrování v klidovém stavu můžou vyžadovat také vaší organizace potřeba data zásad správného řízení a dodržování předpisů úsilí. Oborové a vládní nařízení, jako je HIPAA, PCI a FedRAMP, rozložení konkrétní záruky týkající se požadavků na data protection a šifrování. Šifrování v klidovém stavu je povinné míra vyžadované z důvodu kompatibility s některými předpisů.

Kromě dodržování předpisů a zákonné požadavky šifrování v klidovém stavu by měla být vnímané jako schopnost defense-in-depth platformy. I když společnost Microsoft poskytuje kompatibilní platformě pro služby, aplikace, a data, komplexní zařízení a fyzické zabezpečení, auditování a řízení přístupu k datům, je potřeba zajistit další bezpečnostní opatření "překrývající se" v případě jednoho z jiných bezpečnostní opatření selže. Šifrování v klidovém stavu poskytuje tyto další mechanismus ochrany před mobilními.

Společnost Microsoft se zavazuje poskytovat šifrování na zbývající možnosti napříč cloudovými službami a k dispozici zákazníkům vhodné možnosti správy šifrovacích klíčů a přístup k protokolům zobrazuje, když šifrovací klíče se používají. Kromě toho společnost Microsoft pracuje směrem k cíli zpřístupnění Veškerá zákaznická data v klidovém stavu zašifrovaná ve výchozím nastavení.

## <a name="azure-encryption-at-rest-components"></a>Šifrování Azure na zbývající součásti

Jak je popsáno výše, cílem šifrování v klidovém stavu je, že data se ukládají na disk se šifrují se šifrování s tajným klíčem. K dosažení tohoto cíle vytvoření zabezpečeného klíče, musí být zadaná úložiště, řízení přístupu a správě šifrovacích klíčů. I když podrobnosti se mohou lišit, můžete služby Azure šifrování v implementací Rest popsané v podmínky znázorněno v následujícím diagramu.

![Komponenty](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Umístění úložiště šifrovacích klíčů a řízení přístupu k těmto klíčům je šifrování v modelu rest. Klíče musí být vysoce zabezpečené, ale zvládnou určenými uživateli a k dispozici ke konkrétním službám. Pro služby Azure Azure Key Vault je řešení doporučené úložiště klíčů a poskytuje společné prostředí pro správu napříč službami. Klíče jsou uloženy a spravovat trezory klíčů, a přístup k trezoru klíčů může dostat k uživatelům nebo službám. Azure Key Vault podporuje vytvoření odběratele klíčů nebo import klíče zákazníků pro použití ve scénářích klíče spravované zákazníkem šifrování.

### <a name="azure-active-directory"></a>Azure Active Directory

Oprávnění k použití klíčů uložených ve službě Azure Key Vault, Správa nebo k nim přistupovat k šifrování v Rest šifrování a dešifrování, mohou být zadány účtů služby Azure Active Directory. 

### <a name="key-hierarchy"></a>Klíč hierarchie

Více než jeden šifrovací klíč je používán šifrování v implementaci rest. Asymetrické šifrování je užitečné pro navázání vztahu důvěryhodnosti a ověřování, které jsou potřebné pro přístup ke klíčům a správu. Symetrické šifrování je mnohem efektivnější pro hromadné šifrování a dešifrování, umožňující silnější šifrování a vyšší výkon. Omezené použití jedné šifrovací klíč sníží riziko, že se klíč dojde k ohrožení bezpečnosti a náklady na znova šifrovat, když klíč musí být nahrazen. Azure bez na rest modelů použít klíč hierarchie skládá z následujících typů klíčů:

- **Datový šifrovací klíč (DEK)** – symetrický klíč AES256 použitý k šifrování oddílu nebo blok dat.  Na jeden prostředek může mít mnoho oddílů a mnoho šifrovací klíče. Šifrování každý blok dat jiným klíčem díky analýzy kryptografických útoků obtížnější. Je potřeba pro přístup k DEKs instance prostředku zprostředkovatele nebo aplikace, šifrování a dešifrování konkrétního bloku. Při klíč DEK je nahrazen nový klíč musí být pouze data v jeho přidružený blok šifrovaných znovu s novým klíčem.
- **Klíč šifrování klíčů (KEK)** – asymetrického šifrovací klíč použitý k šifrování dat šifrovací klíče. Použití klíčového šifrovacího klíče umožňuje šifrovací klíče dat, samy o sobě jde zašifrovaný a řídit. Entita, která má přístup k klíče KEK může být jiný než entity, která vyžaduje klíč DEK. Entita může zprostředkovat přístup k klíče DEK a omezit tak přístup každý klíč DEK do konkrétního oddílu. Protože se klíč KEK se vyžaduje k dešifrování DEKs, se klíč KEK je v podstatě jediný bod, podle kterého DEKs můžete efektivně odstranit, odstranění klíče kek.

Šifrovací klíče dat, šifrované pomocí klíče šifrovací klíče jsou uloženy odděleně a pouze entity s přístupem k šifrovací klíč klíče můžete získat nějaké šifrovací klíče dat šifrované pomocí klíče. Různé modely úložiště klíčů jsou podporovány. Každý model podrobněji dále v další části se budeme zabývat.

## <a name="data-encryption-models"></a>Modely šifrování dat

Pochopení různých modelů šifrování a jejich výhody a nevýhody je zásadní pro pochopení, jak implementovat šifrování v klidovém stavu různých zprostředkovatelů prostředků v Azure. Tyto definice jsou sdíleny napříč všechny poskytovatele prostředků v Azure tak, aby běžné language a taxonomie. 

Existují tři scénáře pro šifrování na straně serveru:

- Šifrování na straně serveru pomocí klíče spravované službou
    - Azure poskytovatelů prostředků provádět operace šifrování a dešifrování
    - Microsoft spravuje klíče
    - Úplné cloudové funkce

- Šifrování na straně serveru ve službě Azure Key Vault pomocí klíčů spravovaných zákazníkem
    - Azure poskytovatelů prostředků provádět operace šifrování a dešifrování
    - Zákazník řídí klíče účtů prostřednictvím Azure Key Vault
    - Úplné cloudové funkce

- Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem zákazníka řídí hardware
    - Azure poskytovatelů prostředků provádět operace šifrování a dešifrování
    - Zákazník řídí klíče na hardwaru řízené zákazníka
    - Úplné cloudové funkce

Šifrování na straně klienta zvažte následující:

- Služby Azure neuvidí dešifrovaná data
- Zákazníci spravovat a ukládat klíče v místním (nebo v jiných zabezpečené úložiště). Klíče nejsou k dispozici ke službám Azure
- Snížení cloudové funkce

Modely podporované šifrování v Azure rozdělit do dvou hlavních skupin: "Šifrování klienta" a "-šifrování na straně serveru" jako již bylo zmíněno dříve. Všimněte si, že, nezávisle na šifrování v modelu rest služby používané, Azure vždy doporučujeme používat zabezpečeného přenosu, jako je protokol TLS nebo HTTPS. Šifrování během přenosu, proto by měl řešit přenosový protokol a neměla by být hlavním faktorem při určování, které šifrování v modelu rest pro použití.

### <a name="client-encryption-model"></a>Model klientského šifrování

Model šifrování klienta odkazuje na šifrování, které se provádí mimo poskytovatele prostředků nebo Azure pomocí služby nebo volající aplikace. Šifrování lze provést aplikace služby v Azure, nebo aplikaci spuštěnou v datových centrech zákazníků. V obou případech se při využívání tohoto modelu šifrování obdrží poskytovatele prostředků Azure objektem blob šifrovaným dat bez možnosti dešifrovat data žádným způsobem nebo nebude mít přístup k šifrovacím klíčům. V tomto modelu správy klíčů se provádí volání aplikace nebo služby a je neprůhledný ke službě Azure.

![Klient](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model šifrování na straně serveru

Modely šifrování na straně serveru najdete šifrování, které se provádí pomocí služby Azure. V takovém modelu poskytovatel prostředků provádí operace šifrování a dešifrování. Například Azure Storage může přijímat data v operacích jako prostý text a interně slouží k šifrování a dešifrování. Poskytovatel prostředků může použít šifrovací klíče, které jsou spravované microsoftem nebo zákazník v závislosti na zadané konfigurace. 

![Server](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modely správy klíčů šifrování na straně serveru

Každý z šifrování na straně serveru v rest modely znamená odlišné charakteristiky správu klíčů. Jedná se o kde a jak šifrovací klíče jsou vytvořeny a uloženy a také přístup k modelům a postupy obměny klíče. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Šifrování na straně serveru pomocí klíče spravované službou

Základní požadavek je pro mnoho zákazníků, ujistěte se, že se data zašifrují pokaždé, když je v klidovém stavu. Šifrování na straně serveru pomocí klíčů spravovaných služeb umožňuje tento model umožňuje zákazníkům označit tento konkrétní prostředek (účet úložiště, databáze SQL atd.) pro šifrování a opuštění všechny aspekty správy klíčů, jako jsou klíče vystavování, otočení a zálohování do Microsoftu. Většina služeb Azure, která podporují šifrování v klidovém stavu obvykle podporovat model snižování zátěže správu šifrovacích klíčů do Azure. Poskytovatel prostředků Azure vytvoří klíče, umístí je do zabezpečeného úložiště a je v případě potřeby obnovuje. To znamená, že služba má úplný přístup ke klíčům a služby má plnou kontrolu nad správou životního cyklu přihlašovacích údajů.

![Spravované](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Šifrování na straně serveru pomocí klíče spravované službou proto rychle řeší potřeby mít šifrování v klidovém stavu s nízkou režii a zákazníka. Pokud je k dispozici zákazník obvykle otevře na webu Azure portal pro cílové předplatné a poskytovatele prostředků a zkontroluje pole, která udává, co by rádi data, která mají být šifrována. V někteří správci prostředků je ve výchozím nastavení šifrování na straně serveru s použitím klíčů spravovaných služeb.

Šifrování na straně serveru pomocí klíčů spravovaných microsoftem vyjadřuje služba má plný přístup k ukládání a spravuje klíče. Když někteří zákazníci chtít spravovat klíče, protože jejich pocit, že získají vyšší úroveň zabezpečení, náklady a riziko spojené s řešeními úložiště vlastních klíčů třeba zvážit při vyhodnocování tohoto modelu. V mnoha případech, může určit organizaci, může větší než riziko správy cloudu šifrování v klíčích rest omezení zdrojů nebo rizika místní řešení.  Ale tento model nemusí být dostatečné pro organizace, které mají požadavky na řízení vytváření nebo životního cyklu šifrovací klíče nebo mít jiný personál Spravovat šifrovací klíče služby než ty, které Správa služby (například oddělení správu klíčů z celkové správy modelu služby).

##### <a name="key-access"></a>Přístup ke klíčům

Když se používá k šifrování na straně serveru s použitím klíčů spravovaných služeb, vytváření klíčů, úložiště a přístup k službě jsou všechny spravované službou. Obvykle poskytovatelé základních prostředků Azure ukládat šifrovací klíče dat v úložišti, které je blízko dat a rychle k dispozici a přístupné při klíč šifrovací klíče jsou uloženy v zabezpečené interní úložiště.

**Výhody**

- Jednoduché nastavení
- Microsoft spravuje obměna klíčů, zálohování a redundance
- Zákazník nemá náklady spojené s implementací nebo rizika schéma vlastní správu klíčů.

**Nevýhody**

- Žádné kontrolu zákazníků nad šifrovací klíče (specifikace klíče, životní cyklus, odvolání, atd.)
- Schopnost oddělit správu klíčů z celkové správy modelu služby

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování na straně serveru pomocí klíčů zákazníka spravované ve službě Azure Key Vault 

Pro scénáře, kde je tento požadavek pro zašifrování dat na rest a ovládací prvek zákazníkům šifrovací klíče můžete použít šifrování na straně serveru pomocí klíčů spravovaných zákazníků ve službě Key Vault. Některé služby může ukládat jenom kořenový klíč šifrovacího klíče ve službě Azure Key Vault a uložení šifrovaného datového šifrovacího klíče v interní umístění blíž k datům. V tomto scénáři zákazníků přineste vlastní klíče do služby Key Vault (BYOK – přineste si vlastní klíč), nebo vygenerovat nové a používat zašifrování požadované prostředky. Když poskytovatele prostředků provádí šifrování a dešifrování operace použije nakonfigurovaný klíč jako kořenový klíč pro všechny operace šifrování. 

##### <a name="key-access"></a>Přístup ke klíčům

Šifrování na straně serveru modelu pomocí klíčů zákazníka spravované ve službě Azure Key Vault zahrnuje službu přístup ke klíčům k šifrování a dešifrování podle potřeby. Šifrování klíče rest jsou přístupné službě přes zásadu řízení přístupu. Tyto zásady udělí přístup identit služby získat klíč. Služba Azure spuštěné pro přidružené předplatné může mít nakonfigurovanou identitu v tomto předplatném. Službu můžete provádět ověřování pomocí Azure Active Directory a přijímat ověřovací token identifikaci sama jako jednají jménem předplatné služby. Tento token může být předloží Key Vault a získat klíč, který byl udělen přístup.

Pro operace pomocí šifrovacích klíčů, identitu služby může být udělen přístup k libovolnému z následujících operací: dešifrovat, encrypt, unwrapKey, wrapKey, ověřte, podepsat, získání, seznam, aktualizovat, vytvořit, importovat, odstranit, zálohování a obnovení.

Získat klíč pro použití při šifrování nebo dešifrování dat v klidovém stavu, který bude instance služby Správce prostředků se spustí jako identitu služby musí mít UnwrapKey (Chcete-li získat klíč pro dešifrování) a WrapKey (pro vložení klíče do trezoru klíčů při vytváření nového klíče).


>[!NOTE] 
>Podrobné informace o službě Key Vault autorizace najdete v článku zabezpečené stránky vaší služby key vault v [dokumentace ke službě Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Výhody**

- Použít plnou kontrolu nad klíči – šifrování, jakým se spravují klíče ve službě Key Vault zákazníka pod kontrolou zákazníka.
- Možnost šifrování více služeb, aby jeden hlavní server
- Můžete oddělit správu klíčů z celkové správy modelu služby
- Můžete definovat služby a klíče napříč oblastmi

**Nevýhody**

- Zákazník má plnou odpovědnost za správu přístupu ke klíčům
- Zákazník má plnou odpovědnost za správu životního cyklu u klíče
- Další konfigurace a nastavení režijní náklady

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Šifrování na straně serveru pomocí klíče spravované zákazníkem služby zákazníka řídí hardware

Některé služby Azure umožňují model správy klíčů hostitele Your Own Key (HYOK). Tento režim správy je užitečné v situacích, ve kterých je potřeba šifrovat data v klidovém stavu a Správa klíčů v úložišti proprietární mimo ovládací prvek od Microsoftu. V tomto modelu musí služba načíst klíč z externí web. Ovlivní to výkon a dostupnost záruky a konfigurace je složitější. Navíc vzhledem k tomu, že služba nemá přístup k klíč DEK během operace šifrování a dešifrování celkové záruky zabezpečení tohoto modelu jsou podobně jako když se klíče ve službě Azure Key Vault spravovaná zákazníkem.  V důsledku toho tento model není vhodný pro většinu organizací, pokud mají požadavky na konkrétní správy klíčů. Vzhledem k těmto omezením většina služeb Azure nepodporují šifrování na straně serveru pomocí klíče spravované zákazníkem serveru v řízené zákazníka hardwaru.

##### <a name="key-access"></a>Přístup ke klíčům

Při použití šifrování na straně serveru pomocí klíče spravované zákazníkem služby zákazníka řídí hardware klíče jsou zachovány v systému nakonfigurovaný příslušného zákazníka. Služby Azure, které podporují tento model poskytují úložiště klíčů zadaný prostředek pro zabezpečené připojení pro zákazníka.

**Výhody**

- Použít plnou kontrolu nad kořenový klíč – šifrování klíče spravuje úložiště zajišťované zákazníkem
- Možnost šifrování více služeb, aby jeden hlavní server
- Můžete oddělit správu klíčů z celkové správy modelu služby
- Můžete definovat služby a klíče napříč oblastmi

**Nevýhody**

- Plnou odpovědnost za úložiště klíčů, zabezpečení, výkonu a dostupnosti
- Plnou odpovědnost za správu přístupu ke klíčům
- Plnou odpovědnost za správu životního cyklu u klíče
- Významné instalace, konfigurace a náklady na průběžnou údržbu
- Zvýšená závislost na dostupnost sítě mezi datovým centrem zákazníka a datovými centry Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Šifrování v klidovém stavu uložených v cloudových službách Microsoftu

Cloudové služby společnosti Microsoft se používají ve všech třech cloudových modelech: IaaS, PaaS, SaaS. Dál budete mít příklady, jak se vešly na každý model:

- Softwarových služeb, které označují jako softwaru jako Server nebo platformám SaaS, které mají aplikace v cloudu, jako je Office 365 k dispozici.
- Služby platformy, které zákazníci využívat v cloudu ve svých aplikacích pomocí cloudu pro takové věci, jako jsou úložiště, analýzy a funkce služby Service bus.
- Služby infrastruktury nebo infrastruktura jako služba (IaaS) v které Zákazník nasazuje operační systémy a aplikace, které jsou hostované v cloudu a případně využívající jiných cloudových službách.

### <a name="encryption-at-rest-for-saas-customers"></a>Šifrování v klidovém stavu pro zákazníky, kteří SaaS

Software jako služba (SaaS) zákazníkům obvykle třeba šifrování v klidovém stavu povolený nebo k dispozici v jednotlivých služeb. Office 365 obsahuje celou řadu možností pro zákazníky, kteří k ověření nebo povolit šifrování v klidovém stavu. Informace o službách Office 365 najdete v článku technologií pro šifrování dat pro Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Šifrování v klidovém stavu pro zákazníky, kteří PaaS

Platforma jako služba (PaaS) zákaznická data se obvykle nachází v prostředí provádění aplikace a všechny poskytovatele prostředků Azure používá k ukládání dat zákazníka. Zobrazíte šifrování v klidovém stavu zkontrolujte dostupné možnosti v následující tabulce úložiště a aplikace platformy, které používáte. Pokud je podporováno, odkazy na pokyny k povolení šifrování v klidovém stavu jsou k dispozici pro každý poskytovatel prostředků. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Šifrování v klidovém stavu pro zákazníky IaaS

Infrastruktura jako služba (IaaS) zákazníkům může mít celou řadu služeb a aplikací používá. Služby IaaS můžete povolit šifrování v klidovém stavu uložených v Azure hostované virtuální počítače a virtuální pevné disky pomocí Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Šifrované úložiště

Jako u PaaS řešení IaaS mohou využívat další služby Azure, které ukládají data v klidovém stavu zašifrovaná. V těchto případech můžete povolit šifrování na webu podpory Rest podle jednotlivých spotřebované služby Azure. Následující tabulka uvádí hlavní úložiště, služby a aplikační platformy a model šifrování v klidovém stavu nepodporuje. Pokud je podporováno, jsou uvedeny odkazy na pokyny k povolení šifrování v klidovém stavu. 

#### <a name="encrypted-compute"></a>Šifrované výpočetní prostředky

Kompletní šifrování v Rest řešení vyžaduje, že je v nezašifrované podobě nikdy trvalá data. Při použití na serveru pro načítání dat v paměti, můžete se data ukládají místně různými způsoby, včetně stránkovací soubor Windows, výpisu a jakékoli protokolování, které může aplikace provádět. Aplikace IaaS Ujistěte se, že tato data se šifrují při nečinnosti může využít Azure Disk Encryption na virtuálním počítači Azure IaaS (Windows nebo Linux) a virtuální disk. 

#### <a name="custom-encryption-at-rest"></a>Vlastní šifrování v klidovém stavu

Doporučuje se, že je to možné, IaaS aplikace využívají Azure Disk Encryption a šifrování na zbývající možnosti spotřebované služby Azure. V některých případech, jako je nestandardní šifrování požadavky nebo úložiště na bázi mimo Azure, vývojář aplikace IaaS možná bude nutné k implementaci šifrování v rozhraní rest sami. Vývojáři lépe řešení IaaS integrovat s Azure očekávání správy a zákazníků s využitím některé komponenty Azure. Konkrétně vývojáři pomocí služby Azure Key Vault k zajištění zabezpečeného klíče úložiště také poskytovat svým zákazníkům konzistentní správu klíčů možností, většina platformy služeb Azure. Kromě toho měli použít vlastní řešení Azure-Managed služby identit, aby účty služby pro přístup k šifrovacím klíčům. Informace pro vývojáře v Azure Key Vault a identity spravované služby najdete v jejich příslušných sad SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Podpora modelu šifrování poskytovatele prostředků Azure

Microsoft Azure Services každý podporují jeden nebo více šifrování v rest modely. U některých služeb ale jeden nebo více modelů šifrování nemusí být použitelná. Pro služby, které podporují spravované zákazníkem klíčových scénářů může podporují pouze podmnožinu typy klíčů, které podporuje Azure Key Vault pro klíč šifrovací klíče. Kromě toho služby může uvolnit podporu pro tyto scénáře a typy klíčů na různé plány. Tato část popisuje šifrování prostřednictvím rest podpory v době psaní tohoto textu pro každou ze služeb úložiště Azure hlavní data.

### <a name="azure-disk-encryption"></a>Azure disk encryption

Každý zákazník se pomocí infrastruktury Azure jako služba (IaaS) funkce může dosáhnout šifrování v klidovém stavu pro své virtuální počítače IaaS a disků prostřednictvím Azure Disk Encryption. Další informace o Azure Disk encryption najdete v článku [dokumentace ke službě Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Úložiště Azure

Všechny služby Azure Storage (úložiště objektů Blob, Queue storage, Table storage a Azure Files) podporují šifrování na straně serveru v klidovém stavu, se některé služby, podpora klíčů spravovaných zákazníkem a šifrování na straně klienta.  

- Na serveru: Všechny služby Azure Storage povolit šifrování na straně serveru ve výchozím nastavení pomocí klíče spravované zákazníkem služby, které je transparentní pro aplikaci. Další informace najdete v tématu [šifrování služby Azure Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/storage-service-encryption). Azure Blob storage a službou soubory Azure také podporují klíče spravované zákazníkem RSA 2048 bitů ve službě Azure Key Vault. Další informace najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Na straně klienta: Objekty BLOB Azure, tabulky a fronty podporují šifrování na straně klienta. Při použití šifrování na straně klienta, zákazníci šifrování dat a nahrajte data jako zašifrovaný objekt blob. Zákazník se provádí správu klíčů. Další informace najdete v tématu [šifrování na straně klienta a služby Azure Key Vault pro Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database aktuálně podporuje šifrování v klidovém stavu pro scénáře šifrování na straně klienta a na straně služba spravovaná Microsoftem.

Podpora pro šifrování serveru aktuálně poskytuje prostřednictvím funkce SQL volat transparentního šifrování dat. Jakmile je zákazníkem v systému Azure SQL Database umožňuje transparentní šifrování dat klíč automaticky vytváří a spravovat pro ně. Na úrovni databáze a serveru lze povolit šifrování v klidovém stavu. Od června 2017 [transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) je povoleno standardně pro nově vytvořené databáze. Azure SQL Database podporuje klíče spravované zákazníkem RSA 2048 bitů ve službě Azure Key Vault. Další informace najdete v tématu [transparentního šifrování dat s podporou vlastního klíče pro Azure SQL Database a Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Šifrování na straně klienta dat Azure SQL Database je podporované prostřednictvím [s funkcí Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) funkce. Vždy používá šifrovaný klíč, který vytvoří a uloží klientem. Zákazníci můžou uložení hlavního klíče v úložišti certifikátů Windows, Azure Key Vault nebo místního modulu hardwarového zabezpečení. Pomocí SQL Server Management Studio, SQL uživatelé zvolit, jaké klíč by chtěli použít k šifrování sloupec.

|                                  |                    | **Model šifrování a správy klíčů** |                   |                    |
|----------------------------------|--------------------|--------------------|--------------------|--------------------|
|                                  | **Na serveru pomocí klíče spravované službou**     | **Na serveru pomocí spravované zákazníkem ve službě Key Vault**             |  **Na serveru pomocí spravované zákazníkem On-premises**                  | **Pomocí klienta spravovat klienta**      |
| **Úložiště a databází**        |                    |                    |                    |                    |                    |
| Disk (IaaS)                      | -                  | Ano, RSA 2048 bitů  | Ano               | -                  |
| SQL Server (IaaS)                | Ano                | Ano, RSA 2048 bitů  | Ano                | Ano                |
| Azure SQL (databáze/datový sklad) | Ano                | Ano, RSA 2048 bitů  | -                  | Ano                |
| Azure Storage (objekty BLOB bloku nebo stránky) | Ano                | Ano, RSA 2048 bitů  | -                  | Ano                |
| Azure Storage (soubory)            | Ano                | Ano, RSA 2048 bitů  | -                  | -                  |
| Azure Storage (tabulky, fronty)   | Ano                | -                  | -                  | Ano                |
| Cosmos DB (Documentdb)          | Ano                | -                  | -                  | -                  |
| StorSimple                       | Ano                | -                  | -                  | Ano                |
| Backup                           | -                  | -                  | -                  | Ano                |
| **Inteligentní funkce a analýzy**   |                    |                    |                    |                    |
| Azure Data Factory               | Ano                | -                  | -                  | -                  |
| Azure Machine Learning           | -                  | Ve verzi Preview, RSA 2048 bitů | -                  | -                  |
| Azure Stream Analytics           | Ano                | -                  | -                  | -                  |
| HDInsight (Azure Blob Storage)   | Ano                | -                  | -                  | -                  |
| HDInsight (Data Lake Storage)    | Ano                | -                  | -                  | -                  |
| Azure Data Lake Store            | Ano                | Ano, RSA 2048 bitů  | -                  | -                  |
| Katalog dat Azure               | Ano                | -                  | -                  | -                  |
| Power BI                         | Ano                | -                  | -                  | -                  |
| **Služby IoT**                 |                    |                    |                    |                    |
| IoT Hub                          | -                  | -                  | -                  | Ano                |
| Service Bus                      | Ano                | -                  | -                  | Ano                |
| Event Hubs                       | Ano                | -                  | -                  | -                  |
| Event Grid                       | Ano                | -                  | -                  | -                  |


## <a name="conclusion"></a>Závěr

Ochrana zákaznická data uložená v rámci služeb Azure je prvořadý význam společnosti Microsoft. Všechny Azure hostované služby se zavazuje poskytovat šifrování na zbývající možnosti. Základní služby, jako je Azure Storage, Azure SQL Database a důležité analytické a inteligentní služby již poskytují šifrování na zbývající možnosti. Některé z těchto služeb podporují klíčů řídit zákazníka a šifrování na straně klienta a klíče spravované zákazníkem služby a šifrování. Služby Microsoft Azure jsou široce vylepšování šifrování na dostupnost Rest a nové možnosti jsou plánovány pro verzi preview a obecně dostupná v nadcházejících měsících.
