---
title: Microsoft Azure Data šifrování na Rest | Microsoft Docs
description: Tento článek obsahuje přehled Microsoft Azure data šifrování na rest, celkový možnosti a Obecné aspekty.
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
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: fa03d62a3125b3bf8f23a53903a733dbec8ea662
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839459"
---
# <a name="azure-data-encryption-at-rest"></a>Šifrování na Rest Azure dat

Microsoft Azure obsahuje nástroje pro ochranu dat podle potřeb zabezpečení a dodržování předpisů vaší společnosti. Tento dokument se zaměřuje na:

- Jak ochranu dat v klidovém stavu uložených v Microsoft Azure
- Popisuje různé součásti, kteří se k implementaci ochrany dat,
- Zkontroluje výhody a nevýhody ochrany přístupy různých správy klíčů. 

Šifrování v klidovém stavu je běžné požadavek na zabezpečení. Ve službě Azure organizace, můžete dosáhnout šifrování v klidovém stavu bez nutnosti náklady na implementaci a správu a riziko řešení pro správu vlastní klíče. Organizace mají možnost umožníte Azure, úplně spravovat šifrování v klidovém stavu. Kromě toho organizace mají různé možnosti, jak podrobně spravovat šifrování nebo šifrovací klíče.

## <a name="what-is-encryption-at-rest"></a>Co je šifrování v klidovém stavu?

Šifrování v klidovém stavu je kódování (šifrování) dat, když je nastavené jako trvalé. Šifrování v návrzích Rest v Azure pomocí symetrického šifrování šifrování a dešifrování velkých objemů dat rychle podle jednoduchého konceptuálního modelu:

- Symetrický šifrovací klíč se používá k šifrování dat, jako je zapsán do úložiště. 
- Stejný šifrovací klíč se používá k dešifrování dat, jako je readied pro použití v paměti.
- Data mohou být rozdělena na oddíly a může být pro každý oddíl používají různé klíče.
- Klíče musí být uložen v zabezpečeném umístění s zásady řízení přístupu omezení přístupu k určitým identit a protokolování použití klíče. Šifrovací klíče dat jsou často šifrován asymetrické šifrování dál omezit přístup.

V praxi klíčové scénáře pro správu a řízení a také škálování a dostupnosti záruky, vyžadují další konstrukce. Microsoft Azure šifrování v Rest konceptech a součástech jsou popsané níže.

## <a name="the-purpose-of-encryption-at-rest"></a>Účelem šifrování v klidovém stavu

Šifrování v klidovém stavu poskytuje ochranu dat uložených dat (v klidovém stavu). Útoky na data v rest zahrnout pokouší získat fyzický přístup k hardwaru, na kterém jsou data uložena a pak ohrozit zabezpečení dat omezením. Takový útok pevný disk server může mít byla manipulace během údržby, což útočníkovi umožňuje odebrat pevného disku. Útočník by později uvést pevného disku do počítače, řídí se pokusit o přístup k datům. 

Šifrování v klidovém stavu slouží k útočník zabránit v přístupu k nešifrovaná data zajištěním data se šifrují, pokud na disku. Pokud by útočník získat pevný disk s takovým zašifrovaná data a žádný přístup k šifrovacím klíčům, útočník by ohrožení dat bez velké problémy. V takové situaci by musel pokus útoky na šifrovaná data, která jsou mnohem složitější a využívání prostředků než přístup k bez šifrování dat na pevném disku. Z tohoto důvodu šifrování v klidovém stavu se důrazně doporučuje a je vyžadováno pro mnoho společností s vysokou prioritou. 

Šifrování v klidovém stavu můžou taky vyžadovat organizace potřebu úsilí data vedení a dodržování předpisů. Předpisy odvětví a government, jako je HIPAA, PCI a FedRAMP, rozvrhněte konkrétní bezpečnostní opatření týkající se požadavků na ochranu a šifrování dat. Šifrování v klidovém stavu je povinné míra vyžadované z důvodu kompatibility s některé z těchto předpisy.

Kromě dodržování předpisů a zákonné požadavky by měl být šifrování v klidovém stavu považována za schopností obrany do hloubky platformy. I když společnost Microsoft poskytuje kompatibilní platformu pro služby, aplikace a data, komplexní zařízením a fyzického zabezpečení dat řízení přístupu a auditování, je důležité zajistit další bezpečnostní opatření "překrývající se" v případě, jedním z zabezpečení měří selže. Šifrování v klidovém stavu poskytuje tyto další mechanismus obrany.

Společnost Microsoft se zavazuje poskytuje šifrování v možnosti rest napříč cloudové služby a k poskytování zákazníkům vhodný správy těchto šifrovacích klíčů a přístup k protokolům zobrazuje při použití šifrovacích klíčů. Kromě toho společnost Microsoft pracuje k dosažení cíle provedení všech zákaznická data zašifrovaná přinejmenším ve výchozím nastavení.

## <a name="azure-encryption-at-rest-components"></a>Azure šifrování v součásti Rest

Jak je popsáno dříve, cílem šifrování v klidovém stavu je, že data, která je trvalé na disku je šifrován tajný šifrovací klíč. K dosažení tohoto cíle vytvoření zabezpečeného klíče, je třeba zadat úložiště, řízení přístupu a správu šifrovacích klíčů. I když podrobnosti se může lišit, můžete služby Azure šifrování na Rest implementace popsané v podmínky znázorněné v následujícím diagramu.

![Komponenty](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Umístění úložiště šifrovacích klíčů a řízení přístupu na tyto klíče je důležitá pro šifrování v modelu rest. Klíče musí být vysoce zabezpečeným ale spravovat zadané uživatelem a k dispozici pro konkrétní služby. Pro služby Azure Azure Key Vault je řešení doporučené úložiště klíčů a poskytuje společné rozhraní pro správu v rámci služby. Klíče jsou uložené a spravovat v trezorů klíčů, a pro uživatele nebo služby je možné přidělit přístup k trezoru klíčů. Azure Key Vault podporuje vytvoření odběratele klíčů nebo import klíče zákazníků v situacích spravované zákazníkem šifrovací klíče.

### <a name="azure-active-directory"></a>Azure Active Directory

Oprávnění k použití klíče uložené v Azure Key Vault, Správa nebo k nim získat přístup k šifrování na Rest šifrování a dešifrování, je možné přidělit k účtům Azure Active Directory. 

### <a name="key-hierarchy"></a>Klíče hierarchie

Více než jeden šifrovací klíč se používá v šifrování v implementaci rest. Asymetrické šifrování je užitečné pro navázání vztahu důvěryhodnosti a ověřování, které jsou potřebné pro přístup ke klíčům a správu. Symetrické šifrování je efektivnější pro hromadné šifrování a dešifrování, povolení pro silnější šifrování a lepší výkon. Omezené použití jediný šifrovací klíč snižuje riziko ohrožení zabezpečení klíč a náklady znova šifrovat, když klíč se musí nahradit. Na modely rest Azure šifrování pomocí klíče hierarchie skládá z následujících typů klíčů:

- **Datový šifrovací klíč (DEK)** – symetrického klíče AES256 použitý k šifrování oddílu nebo datového bloku.  Jediný zdroj, může mít mnoho oddíly a mnoho šifrovací klíče dat. Šifrování každého bloku dat pomocí jiného klíče umožňuje analysis kryptografických útoků obtížnější. Přístup k DEKs je potřeba prostředků instanci zprostředkovatele nebo aplikace, která je šifrování a dešifrování konkrétní blok. Při klíč DEK se nahradí nový klíč pouze data v jeho přidružené bloku musí být znovu zašifrovat pomocí nového klíče.
- **Šifrovací klíč klíčů (KEK)** – asymetrické šifrovací klíč použitý k šifrování dat šifrovací klíče. Použití klíče šifrovací klíč umožňuje šifrovací klíče dat, se šifrovat a řídí. Typ entity, která má přístup k klíče KEK může být jiný než typ entity, která vyžaduje klíč DEK. Entita může zprostředkovatel přístup k klíč DEK omezit přístup každý klíč DEK na konkrétní oddíl. Vzhledem k tomu, že se klíč KEK je potřeba k dešifrování DEKs, je jediný bod, podle kterého DEKs můžete efektivně odstranit, odstranění klíče kek efektivně klíče KEK.

Šifrovací klíče dat šifrovaný klíč šifrovací klíče jsou uloženy odděleně a pouze entity s přístupem k šifrování klíče můžete získat žádné šifrovací klíče dat šifrované s tímto klíčem. Podporovány jsou různé modely úložiště klíčů. Každý model podrobněji později v další části se budeme zabývat.

## <a name="data-encryption-models"></a>Modely šifrování dat

K pochopení různých modelech šifrování a jejich výhody a nevýhody je důležité porozumět tomu, jak implementovat různých poskytovatelů prostředků v Azure šifrování v klidovém stavu. Tyto definice jsou sdíleny všech poskytovatelů prostředků v Azure k zajištění běžné jazyk a taxonomii. 

Existují tři scénáře pro šifrování na straně serveru:

- Šifrování na straně serveru pomocí služby spravovat klíče
    - Azure zprostředkovatelé prostředků provádět operace šifrování a dešifrování
    - Microsoft spravuje klíče
    - Funkce úplné cloudu

- Šifrování na straně serveru pomocí spravovaného zákazníkem klíčů v Azure Key Vault
    - Azure zprostředkovatelé prostředků provádět operace šifrování a dešifrování
    - Zákazník ovládací prvky klíče přes Azure Key Vault
    - Funkce úplné cloudu

- Šifrování na straně serveru pomocí klíče spravovaného zákazníkem na řízenou zákazníka hardwaru
    - Azure zprostředkovatelé prostředků provádět operace šifrování a dešifrování
    - Zákazník ovládací prvky klíče na řízenou zákazníka hardwaru
    - Funkce úplné cloudu

Pro šifrování na straně klienta zvažte následující:

- Služby Azure nemůžete zobrazit dešifrovaná data
- Zákazníci správě a ukládání klíčů v místě (nebo v jiné zabezpečené úložiště). Klíče nejsou k dispozici ke službám Azure
- Funkce snížené cloudu

Modely šifrování podporovaných v Azure rozdělit do dvou hlavních skupin: "Klienta šifrování" a "serverové šifrování", jako jsou již bylo zmíněno dříve. Všimněte si, že nezávislé šifrování v modelu rest použité, Azure služby vždy doporučujeme použití zabezpečeného přenosu například TLS nebo HTTPS. Šifrování v přenosu proto by měl být řešené pomocí přenosový protokol a by neměla být hlavní faktorem při určování, které šifrování ve model rest se má použít.

### <a name="client-encryption-model"></a>Model klientského šifrování

Modelu šifrování klienta odkazuje na šifrování, které se provádí mimo poskytovatele prostředků nebo Azure pomocí služby nebo volající aplikace. Šifrování lze provést podle aplikace služby v Azure nebo podle aplikace běžící v datovém centru zákazníka. V obou případech, při využití tohoto modelu šifrování poskytovatel prostředků Azure obdrží zašifrovaný objekt blob dat bez možnost dešifrování dat, jakýmkoli způsobem nebo mít přístup k šifrovacím klíčům. V tomto modelu správy klíčů se provádí volání aplikace nebo služby a je plné krytí ve službě Azure.

![Klient](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model šifrování na straně serveru

Modely šifrování na straně serveru naleznete šifrování, které se provádí pomocí služby Azure. Zprostředkovatel prostředků v modelu, provádí operace šifrování a dešifrování. Například Azure Storage data může zobrazit v prostém textu operacích a interně slouží k šifrování a dešifrování. Poskytovatel prostředku může použít šifrovací klíče, které jsou spravované společností Microsoft nebo zákazník v závislosti na Zadaná konfigurace. 

![Server](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modely šifrování na straně serveru správy klíčů

Každý z šifrování na straně serveru v rest modely znamená odlišné charakteristiky správy klíčů. To zahrnuje kde a jak šifrovací klíče jsou vytvořeny a uloženy a také přístup modely a postupy střídání klíče. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Šifrování na straně serveru pomocí klíče spravované služby

Pro mnoho zákazníků základní požadavek je potřeba zajistit, že data se šifrují vždy, když je v klidovém stavu. Šifrování na straně serveru pomocí klíče spravované služby tento model umožňuje umožňující zákazníkům označit konkrétní prostředek (účet úložiště, databáze SQL atd.) pro šifrování a ponechat všechny aspekty správy klíčů, jako je například klíče vystavování, otáčení a zálohování společnosti Microsoft. Většina služeb Azure, která podporují šifrování v klidovém stavu obvykle podporují tento model zpracování úloh správy šifrovací klíče do Azure. Poskytovatel prostředků Azure vytvoří klíče, umístí je do zabezpečeného úložiště a načte je podle potřeby. To znamená, že služba má úplný přístup k klíče a služba má plnou kontrolu nad Správa životního cyklu přihlašovacích údajů.

![Spravované](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Šifrování na straně serveru pomocí služby spravovat klíče proto rychle řeší nutné, abyste měli šifrování v klidovém stavu s nízkou režii zákazníka. Pokud je tato možnost je k dispozici, zákazník obvykle otevře portál Azure pro cílové předplatné a zprostředkovatele prostředků a zkontroluje pole, která udává, mu data chcete šifrovat. V některých správci prostředků je ve výchozím nastavení šifrování na straně serveru s klíči spravované služby.

Služba má úplný přístup k ukládání a spravuje klíče položku seznamu šifrování na straně serveru s klíči spravovaný společností Microsoft. Když někteří zákazníci chtít spravovat klíče, protože jejich pocit, že získají větší zabezpečení, náklady a riziko spojené s řešením vlastní úložiště klíčů měli byste zvážit při vyhodnocování tohoto modelu. V manIn mnoha případech ganization rozhodnutí omezení prostředků nebo rizika v případě místních řešení může větší než riziko správu cloudu šifrování na rest klíče.  Tento model ale nemusí být dostatečné pro organizace, které mají požadavky k řízení životního cyklu šifrovacích klíčů na vytvoření nebo mít různé pracovníky správu šifrovacích klíčů služby než ty, které Správa služby (tj, odloučení správu klíčů z celkové modelu správy pro službu).

##### <a name="key-access"></a>Přístup ke klíčům

Pokud se používá šifrování na straně serveru službu spravovat klíče, vytvoření klíče, úložiště a přístup k službě jsou všechny spravované službou. Obvykle zprostředkovatele základních prostředků Azure bude ukládat šifrovací klíče dat v úložišti, které je blízké data a rychle k dispozici a dostupné při klíč šifrovací klíče jsou uloženy v zabezpečené interní úložiště.

**Výhody**

- Jednoduché nastavení
- Spravuje Microsoft střídání klíče, zálohování a redundance
- Zákazník nemá náklady na implementaci nebo riziko schéma vlastní správy klíčů.

**Nevýhody**

- Žádné zákazníka kontrolu nad šifrovací klíče (specifikace klíče, životního cyklu, odvolání, atd.)
- Schopnost oddělit správu klíčů z celkové model správy pro službu

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování na straně serveru pomocí klíčů zákazníků spravované v Azure Key Vault 

Pro scénáře, kde je požadavek na šifrování dat na rest a řízení zákazníků šifrovací klíče můžete použít šifrování na straně serveru pomocí klíčů zákazníků spravované v Key Vault. Některé služby může uložit jenom kořenový klíč šifrovacího klíče v Azure Key Vault a uložte šifrované datového šifrovacího klíče z interních umístění blíže k datům. V tom, že zákazníci scénář přineste vlastní klíče do trezoru klíč (BYOK – přineste si vlastní klíč), nebo generovat nové a použít je k šifrování požadované prostředky. Během operace šifrování a dešifrování, provádí poskytovatele prostředků používá nakonfigurovaný klíč jako kořenový klíč pro všechny operace šifrování. 

##### <a name="key-access"></a>Přístup ke klíčům

Model šifrování na straně serveru s zákazníka spravované klíče v Azure Key Vault zahrnuje službu přístup ke klíčům k šifrování a dešifrování podle potřeby. Šifrování klíče rest dalo přistupovat ke službě přes zásadu řízení přístupu. Tato zásada uděluje přístup identity služby pro příjem klíč. Služba Azure systémem jménem přidružené předplatné se dá nakonfigurovat s identitou v tomto předplatném. Služba umožňuje provádět ověřování Azure Active Directory a přijímat ověřovací token identifikace sám sebe jako funguje jménem předplatné služby. Tento token mohou být poskytovány do Key Vault získat klíč, který byl udělen přístup.

Pro operace pomocí šifrovacích klíčů, identity služby je možné udělit přístup k některému z následujících operací: dešifrovat, šifrování, unwrapKey, wrapKey, ověřte, přihlášení, získání, seznam, aktualizace, vytvořit, importovat, odstranit, zálohování a obnovení.

Získat klíč pro použití v šifrování nebo dešifrování dat v klidovém stavu identitu služby, který v instanci služby Správce prostředků se spustí jako musí mít UnwrapKey (Chcete-li získat klíč pro dešifrování) a WrapKey (Chcete-li vložit klíče do trezoru klíčů při vytváření nového klíče).


>[!NOTE] 
>Další podrobnosti v Key Vault autorizace najdete zabezpečené stránku trezoru klíčů v [dokumentace Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Výhody**

- Použít plnou kontrolu nad klíče – šifrovací klíče se spravují v Key Vault zákazníka pod kontrolou zákazníka.
- Možnost šifrování více služeb jeden hlavní server
- Můžete oddělit správu klíčů z celkové model správy pro službu
- Můžete definovat služby a umístění klíče v oblastech

**Nevýhody**

- Zákazník má plnou odpovědnost za správu klíčů přístupu
- Zákazník má plnou odpovědnost za správu životního cyklu u klíče
- Další režii instalace a konfigurace

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Šifrování na straně serveru pomocí služby spravovat klíčů v řízené zákazníka hardwaru

Některé služby Azure povolit model správy klíčů hostitele si vlastní klíč (HYOK). Tento režim správy je užitečný ve scénářích, kde je potřeba šifrovat data umístěná a spravovat klíče ve vlastní úložiště mimo kontrolu společnosti Microsoft. V tomto modelu musíte službu načíst klíč z externího webu. Dopad na výkon a dostupnost záruky a konfigurace je složitější. Navíc vzhledem k tomu, že služba mít přístup k klíč DEK během operací šifrování a dešifrování celkové záruky zabezpečení tohoto modelu jsou podobná Pokud se klíče zákazníků spravované v Azure Key Vault.  Tento model v důsledku toho není vhodné pro většinu organizací, pokud mají požadavky na konkrétní správy klíčů. Většina služeb Azure z důvodu tato omezení nepodporují šifrování na straně serveru pomocí serveru spravované klíčů v řízené zákazníka hardwaru.

##### <a name="key-access"></a>Přístup ke klíčům

Pokud se používá šifrování na straně serveru pomocí služby spravovat klíčů v řídí hardwaru klíče udržované v systému nakonfigurovaný zákazník. Služby Azure, které podporují tento model poskytují prostředek pro zabezpečené připojení k zákazník zadaný úložiště klíčů.

**Výhody**

- Plnou kontrolu nad kořenový klíč používá – šifrovací klíče se spravují přes zákazníka poskytuje úložiště
- Možnost šifrování více služeb jeden hlavní server
- Můžete oddělit správu klíčů z celkové model správy pro službu
- Můžete definovat služby a umístění klíče v oblastech

**Nevýhody**

- Plnou odpovědnost za úložiště klíčů, zabezpečení, výkonu a dostupnosti
- Plnou odpovědnost za správu klíčů přístupu
- Plnou odpovědnost za správu životního cyklu u klíče
- Významné instalaci, konfiguraci a náklady na průběžnou údržbu
- Zvýšená závislost na dostupnost sítě mezi datového centra zákazníka a datových centrech Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Šifrování v klidovém stavu uložených v cloudové služby společnosti Microsoft

Cloudové služby společnosti Microsoft se používají v všechny tři cloudu modely: IaaS, PaaS, SaaS. Zde máte příklady, jak se vešly na každý model:

- Služby softwaru označuje jako softwaru jako Server nebo SaaS, které aplikace poskytované cloudu například Office 365.
- Služby platformy uvedeni zákazníci, kteří využívají cloud ve svých aplikacích pomocí cloudu pro třeba úložiště, analýzy a funkce service bus.
- Služby infrastruktury, nebo infrastruktura jako služba (IaaS) v které Zákazník nasazuje operační systémy a aplikace, které jsou hostované v cloudu a případně využití jiných cloudových služeb.

### <a name="encryption-at-rest-for-saas-customers"></a>Šifrování v klidovém stavu pro zákazníky SaaS

Software jako služba (SaaS) Zákazníci obvykle třeba šifrování v klidovém stavu povolený nebo k dispozici v jednotlivých služeb. Office 365 obsahuje celou řadu možností pro zákazníky a ověřte nebo povolit šifrování v klidovém stavu. Informace o službách Office 365 najdete v části technologií pro šifrování dat pro Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Šifrování v klidovém stavu pro zákazníky PaaS

Platforma jako služba (PaaS) zákazníka data se obvykle nachází v prostředí pro spuštění aplikace a všech poskytovatelů prostředků Azure používají k ukládání dat zákazníka. Dostupné možnosti zobrazíte šifrování v klidovém stavu zkontrolujte v následující tabulce úložiště a aplikace platformy, které používáte. Pokud je podporováno, jsou uvedené odkazy na pokyny k povolení šifrování v klidovém stavu pro každý poskytovatel prostředků. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Šifrování v klidovém stavu pro zákazníky IaaS

Infrastruktura jako služba (IaaS) zákazníků může mít celou řadu služeb a aplikací používá. Můžete povolit služby IaaS šifrování v klidovém stavu uložených v jejich Azure hostovaných virtuálních počítačů a virtuálních pevných disků pomocí Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Šifrované úložiště

Jako PaaS můžete využít řešení IaaS jinými službami Azure, které ukládají data zašifrovaná přinejmenším. V těchto případech můžete povolit šifrování na podporu Rest podle jednotlivých spotřebované Azure službou. Níže uvedená tabulka uvádí hlavní úložiště, služby a aplikace platformy a model šifrování v klidovém stavu podporovány. Pokud je podporováno, jsou uvedeny odkazy na pokyny k povolení šifrování v klidovém stavu. 

#### <a name="encrypted-compute"></a>Šifrované výpočetní

Dokončení šifrování v Rest řešení vyžaduje, aby nikdy jsou data uložena v nezašifrované podobě. Protože se používá na serveru načítání dat v paměti, můžete data místně trvalé různými způsoby, včetně Windows stránkovací soubor, stav systému a všechny protokolování, které může aplikace provádět. Chcete-li Ujistěte se, že tato data zašifrovaná přinejmenším IaaS aplikace použít Azure Disk Encryption na virtuálním počítači Azure IaaS (Windows nebo Linux) a virtuální disk. 

#### <a name="custom-encryption-at-rest"></a>Vlastní šifrování v klidovém stavu

Doporučuje se, že pokud je to možné, IaaS aplikace využívat Azure Disk Encryption a šifrování na Rest možnosti poskytované spotřebované služby Azure. V některých případech například požadavky na šifrování nestandardní nebo úložiště na bázi mimo Azure, vývojář aplikace IaaS muset implementaci šifrování na rest sami. Vývojáři mohou lepší řešení IaaS Azure správy a k zákaznickým očekávání integrovat s využitím určité Azure komponenty. Vývojáři konkrétně by měl používat službu Azure Key Vault k poskytování zabezpečeného úložiště klíčů a také svým zákazníkům poskytovali možnosti konzistentní správu klíčů, většina Azure služeb platformy. Kromě toho vlastní řešení by měl používat identity služby Azure-Managed povolit služby účtů pro přístup k šifrovacím klíčům. Informace pro vývojáře Azure Key Vault a identita spravované služby najdete v jejich příslušné sady SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Podpora modelu šifrování zprostředkovatelé prostředků Azure.

Služby Microsoft Azure každý podporují jeden nebo více šifrování v modelech rest. U některých služeb ale jeden nebo více modelů šifrování nemusí být použitelná. Kromě toho mohou služby uvolnit podporu pro tyto scénáře na různé plány. Tato část popisuje šifrování prostřednictvím podpory rest v době psaní tohoto textu pro jednotlivé služby hlavních dat Azure storage.

### <a name="azure-disk-encryption"></a>Azure disk encryption

Každý zákazník pomocí Azure infrastruktury jako služby (IaaS) funkce můžete dosáhnout šifrování v klidovém stavu pro své virtuální počítače IaaS a disky prostřednictvím Azure Disk Encryption. Další informace o Azure Disk encryption najdete v článku [dokumentace Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Úložiště Azure

Všechny služby Azure Storage (úložiště objektů Blob, úložiště Queue, Table storage a Azure Files) podporují šifrování na straně serveru v klidu, s některých služeb podporujících spravované zákazníkem klíčů a šifrování na straně klienta.  

- Serverové: Všechny služby úložiště Azure povolit šifrování na straně serveru ve výchozím nastavení pomocí klíče pro službu spravovat, což je transparentní pro aplikace. Další informace najdete v tématu [šifrování služby úložiště Azure pro Data v klidovém stavu](https://docs.microsoft.com/azure/storage/storage-service-encryption). Azure Blob storage a Azure Files také podporují spravované zákazníkem klíče v Azure Key Vault. Další informace najdete v tématu [šifrování služby úložiště v Azure Key Vault klíče spravovaného zákazníkem](https://docs.microsoft.com/en-us/azure/storage/common/storage-service-encryption-customer-managed-keys).
- Klienta: Azure BLOB, tabulek a podporují šifrování na straně klienta. Při použití šifrování na straně klienta, šifrování dat zákazníků a nahrajte data jako zašifrovaný objekt blob. Správu klíčů je potřeba zákazníka. Další informace najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-client-side-encryption).


#### <a name="azure-sql-database"></a>Azure SQL Database

Databáze SQL Azure aktuálně podporuje šifrování v klidovém stavu straně Microsoft spravované služby a scénáře šifrování na straně klienta.

Podpora pro šifrování na serveru je aktuálně k dispozici pomocí funkce SQL názvem transparentní šifrování dat. Jakmile zákazníka Azure SQL Database umožňuje šifrování TDE klíč se automaticky vytváří a spravují pro ně. Šifrování v klidovém stavu se dá nastavit na úrovni databáze nebo serveru. Od června 2017 [transparentní šifrování šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) bude povolená ve výchozím nastavení pro nově vytvořené databáze.

Šifrování na straně klienta Azure SQL Database dat je podporována prostřednictvím [vždy šifrována](https://msdn.microsoft.com/library/mt163865.aspx) funkce. Vždy používá šifrovaný klíč, který vytvoří a uloží klientem. Zákazníci může ukládat hlavní klíč do úložiště certifikátů systému Windows, Azure Key Vault nebo místního modulu hardwarového zabezpečení. Pomocí SQL Server Management Studio, SQL uživatelé zvolit, jaké klíče, které se mají použít k šifrování sloupec.

|                                  |                |                     | **Šifrování modelu**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Klienta** |
|                                  | **Správa klíčů** | **Spravovaný Service klíč** | **Zákazník spravované v trezoru klíčů** | **Spravované zákazníkem na místě** |        |
| **Úložiště a databáze**            |                |                     |                              |                              |        |
| Disk (IaaS)                      |                | -                   | Ano                          | Ano*                         | -      |
| Systému SQL Server (IaaS)                |                | Ano                 | Ano                          | Ano                          | Ano    |
| Databáze Azure SQL (PaaS)                 |                | Ano                 | Ano                          | -                            | Ano    |
| Úložiště Azure (objekty BLOB bloku nebo stránky) |                | Ano                 | Ano                          | -                            | Ano    |
| Úložiště Azure (soubory)            |                | Ano                 | Ano                          | -                            | -      |
| Úložiště Azure (tabulky, fronty)   |                | Ano                 | -                            | -                            | Ano    |
| Cosmos DB (dokument DB)          |                | Ano                 | -                            | -                            | -      |
| StorSimple                       |                | Ano                 | -                            | -                            | Ano    |
| Backup                           |                | -                   | -                            | -                            | Ano    |
| **Intelligence a analýzy**       |                |                     |                              |                              |        |
| Azure Data Factory               |                | Ano                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | Preview                      | -                            | -      |
| Azure Stream Analytics           |                | Ano                 | -                            | -                            | -      |
| HDInsight (úložiště objektů Blob v Azure)  |                | Ano                 | -                            | -                            | -      |
| HDInsight (Data Lake Storage)   |                | Ano                 | -                            | -                            | -      |
| Azure Data Lake Store            |                | Ano                 | Ano                          | -                            | -      |
| Katalog dat Azure               |                | Ano                 | -                            | -                            | -      |
| Power BI                         |                | Ano                 | -                            | -                            | -      |
| **Služby IoT**                     |                |                     |                              |                              |        |
| IoT Hub                          |                | -                   | -                            | -                            | Ano    |
| Service Bus                      |                | Ano              | -                            | -                            | Ano    |
| Event Hubs                       |                | Ano             | -                            | -                            | -      |


## <a name="conclusion"></a>Závěr

Ochrana dat zákazníků ukládaných v rámci služby Azure se především třeba Microsoft. Všechny Azure hostované služby jsou poskytovat šifrování v možnosti Rest. Základní služby, jako je například Azure Storage, Azure SQL Database a klíče analýzy a intelligence služby již zadat šifrování v možnosti Rest. Některé z těchto služeb podporují klíče zákazníků, které řídí, šifrování na straně klienta a také službu spravovat klíčů a šifrování. Služby Microsoft Azure jsou široce rozšíření šifrování v dostupnosti Rest a nové možnosti jsou plánované pro verzi preview a obecné dostupnosti v nadcházejících měsících.

