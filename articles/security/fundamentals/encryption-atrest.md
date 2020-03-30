---
title: Šifrování dat Microsoft Azure v klidovém stavu | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled šifrování dat Microsoft Azure v klidovém stavu, celkové možnosti a obecné aspekty.
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
ms.date: 03/23/2020
ms.author: barclayn
ms.openlocfilehash: d8aa643dcf9734ac983c9c4c0d53bda24ce4688d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125071"
---
# <a name="azure-data-encryption-at-rest"></a>Šifrování dat Azure v klidovém stavu

Microsoft Azure obsahuje nástroje pro zabezpečení dat podle potřeb vaší společnosti v oblasti zabezpečení a dodržování předpisů. Tento článek se zaměřuje na:

- Jak jsou data chráněna v klidovém stavu v microsoft azure
- pojednává o různých složkách, které se podílejí na provádění ochrany údajů,
- Recenze klady a zápory různých přístupů klíčové ochrany řízení.

Šifrování v klidovém stavu je běžný požadavek na zabezpečení. V Azure můžou organizace šifrovat data v klidovém stavu bez rizika nebo nákladů na vlastní řešení pro správu klíčů. Organizace mají možnost nechat Azure zcela spravovat šifrování v klidovém stavu. Organizace mají navíc různé možnosti pro úzkou správu šifrovacích nebo šifrovacích klíčů.

## <a name="what-is-encryption-at-rest"></a>Co je šifrování v klidu?

Šifrování v klidovém stavu je kódování (šifrování) dat, když je trvalé. Návrhy Šifrování v klidovém stavu v Azure používají symetrické šifrování k rychlému šifrování a dešifrování velkého množství dat podle jednoduchého koncepčního modelu:

- Symetrický šifrovací klíč se používá k šifrování dat při zápisu do úložiště.
- Stejný šifrovací klíč se používá k dešifrování dat, jak je připraven pro použití v paměti.
- Data mohou být rozdělena na oddíly a pro každý oddíl mohou být použity různé klíče.
- Klíče musí být uloženy na bezpečném místě s kontrolou přístupu na základě identity a zásadami auditu. Šifrovací klíče dat jsou často šifrované pomocí šifrovacího klíče klíče v azure key vault u dalšího omezení přístupu.

V praxi vyžadují scénáře správy a řízení klíčů, stejně jako záruky škálování a dostupnosti, další konstrukce. Koncepty a součásti microsoft azure šifrování v klidovém stavu jsou popsány níže.

## <a name="the-purpose-of-encryption-at-rest"></a>Účel šifrování v klidovém stavu

Šifrování v klidovém stavu poskytuje ochranu dat pro uložená data (v klidovém stavu). Útoky proti datům v klidovém stavu zahrnují pokusy o získání fyzického přístupu k hardwaru, na kterém jsou data uložena, a následné ohrožení obsažených dat. Při takovém útoku mohl být pevný disk serveru během údržby nesprávně zpracován, což útočníkovi umožnilo odebrat pevný disk. Později by útočník dal pevný disk do počítače pod jejich kontrolou, aby se pokusil o přístup k datům.

Šifrování v klidovém stavu je navrženo tak, aby útočníkovi zabránilo v přístupu k nešifrovaným datům tím, že zajistí, že data budou šifrována na disku. Pokud útočník získá pevný disk se šifrovanými daty, ale nikoli šifrovacími klíči, musí šifrování porazit, aby data mohl číst. Tento útok je mnohem složitější a spotřebovává prostředky než přístup k nešifrovaným datům na pevném disku. Z tohoto důvodu šifrování v klidovém stavu je vysoce doporučeno a je požadavkem s vysokou prioritou pro mnoho organizací.

Šifrování v klidovém stavu může také vyžadovat potřeba organizace pro řízení dat a úsilí o dodržování předpisů. Průmyslové a vládní předpisy, jako jsou HIPAA, PCI a FedRAMP, stanoví konkrétní záruky týkající se ochrany dat a požadavků na šifrování. Šifrování v klidovém stavu je povinné opatření potřebné pro dodržování některých z těchto předpisů. Další informace o přístupu společnosti Microsoft k ověřování FIPS 140-2 naleznete v [publikaci FIPS (Federal Information Processing Standard) 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2). 

Kromě splnění požadavků na dodržování předpisů a regulačních požadavků poskytuje šifrování v klidovém stavu ochranu. Microsoft Azure poskytuje kompatibilní platformu pro služby, aplikace a data. Poskytuje také komplexní zabezpečení a fyzické zabezpečení, řízení přístupu k datům a auditování. Je však důležité poskytnout další "překrývající se" bezpečnostní opatření v případě, že jedno z dalších bezpečnostních opatření selže a šifrování v klidovém stavu poskytuje takové bezpečnostní opatření.

Společnost Microsoft se zavazuje k šifrování v možnostech klidového stavu napříč cloudovými službami a poskytuje zákazníkům kontrolu nad šifrovacími klíči a protokoly použití klíče. Společnost Microsoft navíc ve výchozím nastavení pracuje na šifrování všech zákaznických dat v klidovém stavu.

## <a name="azure-encryption-at-rest-components"></a>Azure Encryption at Rest Components

Jak již bylo popsáno výše, cílem šifrování v klidovém stavu je, že data, která jsou na disku uložena, jsou šifrována tajným šifrovacím klíčem. K dosažení tohoto cíle musí být zajištěno vytvoření zabezpečeného klíče, ukládání, řízení přístupu a správa šifrovacích klíčů. I když podrobnosti se mohou lišit, Azure služby šifrování v době implementace lze popsat v termínech znázorněno v následujícím diagramu.

![Komponenty](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

Umístění úložiště šifrovacích klíčů a řízení přístupu k těmto klíčům je centrální pro model šifrování v klidovém stavu. Klíče musí být vysoce zabezpečené, ale spravovatelné určenými uživateli a dostupné pro konkrétní služby. Pro služby Azure je Azure Key Vault doporučeným řešením úložiště klíčů a poskytuje běžné možnosti správy napříč službami. Klíče jsou uloženy a spravovány v trezorech klíčů a uživatelům nebo službám lze získat přístup k trezoru klíčů. Azure Key Vault podporuje vytváření klíčů zákazníkem nebo import klíčů zákazníků pro použití ve scénářích šifrovacího klíče spravovaném zákazníkem.

### <a name="azure-active-directory"></a>Azure Active Directory

Oprávnění k použití klíčů uložených v úložišti klíčů Azure, buď ke správě nebo přístupu k nim pro šifrování v klidovém stavu šifrování a dešifrování, lze udělit účtům Azure Active Directory.

### <a name="key-hierarchy"></a>Hierarchie klíčů

Více než jeden šifrovací klíč se používá v šifrování v klidovém provádění. Uložení šifrovacího klíče v azure key vault zajišťuje zabezpečený přístup ke klíčům a centrální správu klíčů. Místní přístup k šifrovacím klíčům služby je však efektivnější pro hromadné šifrování a dešifrování než interakce s trezorem klíčů pro každou operaci dat, což umožňuje silnější šifrování a lepší výkon. Omezení použití jednoho šifrovacího klíče snižuje riziko, že klíč bude ohrožen a náklady na opětovné šifrování při nutnosti nahradit klíč. Azure šifrování v klidovém stavu modely používají hierarchii klíčů skládá z následujících typů klíčů k řešení všech těchto potřeb:

- **Datový šifrovací klíč (DEK)** – symetrický klíč AES256 používaný k šifrování oddílu nebo bloku dat.  Jeden prostředek může mít mnoho oddílů a mnoho klíčů šifrování dat. Šifrování každého bloku dat pomocí jiného klíče ztěžuje útoky kryptoanalýzy. Přístup k DEKs je potřeba poskytovatele prostředků nebo instance aplikace, která je šifrování a dešifrování konkrétní blok. Při nahrazení dek s novým klíčem pouze data v jeho přidružené bloku musí být znovu zašifrována s novým klíčem.
- **Šifrovací klíč (KEK)** – šifrovací klíč používaný k šifrování šifrovacích klíčů dat. Použití šifrovacího klíče klíče, který nikdy neopustí trezor klíčů, umožňuje šifrování datových klíčů samotných šifrovat a ovládat. Entita, která má přístup k KEK může být jiný než entita, která vyžaduje DEK. Entita může zprostředkovat přístup k DEK omezit přístup každého DEK na konkrétní oddíl. Vzhledem k tomu, KEK je nutné dešifrovat DEKs, KEK je skutečně jediný bod, kterým DEKs lze účinně odstranit odstraněním KEK.

Šifrovací klíče dat, šifrované pomocí šifrovacích klíčů klíčů, jsou uloženy samostatně a pouze entita s přístupem k šifrovacímu klíči klíče klíče může tyto šifrovací klíče dat dešifrovat. Podporovány jsou různé modely úložiště klíčů. Každý model budeme podrobněji projednávat podrobněji dále v další části.

## <a name="data-encryption-models"></a>Modely šifrování dat

Pochopení různých modelů šifrování a jejich výhody a nevýhody je nezbytné pro pochopení, jak různí poskytovatelé prostředků v Azure implementovat šifrování v klidovém stavu. Tyto definice jsou sdíleny mezi všemi poskytovateli prostředků v Azure, aby byl zajištěn společný jazyk a taxonomie.

Existují tři scénáře pro šifrování na straně serveru:

- Šifrování na straně serveru pomocí klíčů spravovaných službou
  - Zprostředkovatelé prostředků Azure provádějí operace šifrování a dešifrování
  - Microsoft spravuje klíče
  - Plná funkce cloudu

- Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v úložišti Azure Key Vault
  - Zprostředkovatelé prostředků Azure provádějí operace šifrování a dešifrování
  - Zákazník řídí klíče prostřednictvím azure key vaultu
  - Plná funkce cloudu

- Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem na hardwaru řízeném zákazníkem
  - Zprostředkovatelé prostředků Azure provádějí operace šifrování a dešifrování
  - Zákazník řídí klíče na hardwaru řízeném zákazníkem
  - Plná funkce cloudu

Šifrování na straně klienta zvažte následující:

- Služby Azure nevidí dešifrovaná data
- Zákazníci spravují a ukládají klíče místně (nebo v jiných zabezpečených obchodech). Klíče nejsou dostupné pro služby Azure
- Snížená funkce cloudu

Podporované modely šifrování v Azure jsou rozděleny do dvou hlavních skupin: "Šifrování klienta" a "Šifrování na straně serveru", jak již bylo zmíněno dříve. Nezávisle na použitém modelu šifrování v klidovém stavu služby Azure vždy doporučují použití zabezpečeného přenosu, jako je TLS nebo HTTPS. Šifrování v přenosu by proto mělo být řešeno přenosovým protokolem a nemělo by být hlavním faktorem při určování, který model šifrování v klidovém stavu má být používán.

### <a name="client-encryption-model"></a>Model šifrování klienta

Model šifrování klienta odkazuje na šifrování, které se provádí mimo poskytovatele prostředků nebo Azure službou nebo volající aplikací. Šifrování může provádět aplikace služby v Azure nebo aplikace spuštěná v datovém centru zákazníka. V obou případech při využití tohoto modelu šifrování Zprostředkovatel prostředků Azure obdrží šifrovaný objekt blob dat bez možnosti dešifrovat data jakýmkoli způsobem nebo mít přístup k šifrovacím klíčům. V tomto modelu se správa klíčů provádí volající službou nebo aplikací a je neprůhledná pro službu Azure.

![Klient](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model šifrování na straně serveru

Modely šifrování na straně serveru odkazují na šifrování, které provádí služba Azure. V tomto modelu zprostředkovatel prostředků provádí operace šifrování a dešifrování. Azure Storage může například přijímat data v operacích ve formátu prostého textu a interně provede šifrování a dešifrování. Poskytovatel prostředků může používat šifrovací klíče spravované společností Microsoft nebo zákazníkem v závislosti na zařízené konfiguraci.

![Server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modely správy šifrovacího klíče na straně serveru

Každý z modelů šifrování na straně serveru v klidovém stavu znamená charakteristické charakteristiky správy klíčů. To zahrnuje, kde a jak jsou vytvořeny a uloženy šifrovací klíče, stejně jako přístupové modely a postupy střídání klíčů.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Šifrování na straně serveru pomocí klíčů spravovaných službou

Pro mnoho zákazníků je základním požadavkem zajistit, aby data byla šifrována vždy, když jsou v klidovém stavu. Šifrování na straně serveru pomocí klíčů spravovaných službou umožňuje tento model tím, že umožňuje zákazníkům označit konkrétní prostředek (účet úložiště, SQL DB atd.) pro šifrování a ponechat všechny aspekty správy klíčů, jako je vydávání klíčů, otočení a zálohování společnosti Microsoft . Většina služeb Azure, které podporují šifrování v klidovém stavu, obvykle podporuje tento model snižování zátěže správy šifrovacích klíčů do Azure. Poskytovatel prostředků Azure vytvoří klíče, umístí je do zabezpečeného úložiště a v případě potřeby je načte. To znamená, že služba má úplný přístup ke klíčům a služba má plnou kontrolu nad správou životního cyklu pověření.

![Spravované](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Šifrování na straně serveru pomocí klíčů spravovaných službou proto rychle řeší potřebu mít šifrování v klidovém stavu s nízkou režií vůči zákazníkovi. Když je zákazník k dispozici, obvykle otevře portál Azure pro cílové předplatné a poskytovatele prostředků a zakontroluje políčko s uvedením, že by chtěl, aby byla data zašifrována. V některých správci prostředků je ve výchozím nastavení zapnuto šifrování na straně serveru pomocí klíčů spravovaných službou.

Šifrování na straně serveru pomocí klíčů spravovaných společností Microsoft znamená, že služba má plný přístup k ukládání a správě klíčů. Zatímco někteří zákazníci mohou chtít spravovat klíče, protože mají pocit, že získají větší zabezpečení, náklady a rizika spojená s vlastním řešením úložiště klíčů by měly být vzaty v úvahu při hodnocení tohoto modelu. V mnoha případech může organizace určit, že omezení prostředků nebo rizika místního řešení může být větší než riziko správy cloudu šifrování v klidovéklíče.  Tento model však nemusí být dostačující pro organizace, které mají požadavky na řízení vytváření nebo životního cyklu šifrovacích klíčů nebo na to, aby šifrovací klíče služby spravovali různí pracovníci než ti, kteří službu spravují (to znamená oddělení z celkového modelu řízení služby).

##### <a name="key-access"></a>Přístup ke klíči

Při použití šifrování na straně serveru pomocí klíčů spravovaných službou je vytváření klíčů, úložiště a přístup ke službě spravováno službou. Zprostředkovatelé prostředků Azure obvykle uloží klíče šifrování dat v úložišti, které je blízko dat a je rychle dostupné a přístupné, zatímco šifrovací klíče klíče klíče jsou uloženy v zabezpečeném interním úložišti.

**Výhody**

- Jednoduché nastavení
- Microsoft spravuje střídání klíčů, zálohování a redundanci
- Zákazník nemá náklady spojené s implementací nebo riziko vlastního schématu správy klíčů.

**Nevýhody**

- Žádná kontrola zákazníka nad šifrovacími klíči (specifikace klíče, životní cyklus, odvolání atd.)
- Žádná možnost oddělit správu klíčů od celkového modelu správy služby

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v úložišti Azure Key Vault

Pro scénáře, kde je požadavek na šifrování dat v klidovém stavu a řízení šifrovacích klíčů, mohou zákazníci použít šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v trezoru klíčů. Některé služby mohou ukládat pouze kořenový klíč šifrování klíče v trezoru klíčů Azure a ukládat šifrovaný klíč šifrování dat v interním umístění blíže k datům. V tomto scénáři mohou zákazníci přinést své vlastní klíče do trezoru klíčů (BYOK – Přineste si vlastní klíč) nebo vygenerovat nové a použít je k šifrování požadovaných prostředků. Zatímco zprostředkovatel prostředků provádí operace šifrování a dešifrování, používá nakonfigurovaný klíč šifrování jako kořenový klíč pro všechny operace šifrování.

Ztráta šifrovacích klíčů znamená ztrátu dat. Z tohoto důvodu by neměly být odstraněny klíče. Klíče by měly být zálohovány vždy, když jsou vytvořeny nebo otočeny. [Obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) by mělo být povoleno v každém trezoru, který ukládá šifrovací klíče klíče. Namísto odstranění klíče nastavte povolenou hodnotu false nebo nastavte datum vypršení platnosti.

##### <a name="key-access"></a>Přístup ke klíči

Model šifrování na straně serveru s klíči spravovanými zákazníky v azure key vault zahrnuje službu přístup ke klíčům k šifrování a dešifrování podle potřeby. Šifrování v klidovém klíči jsou zpřístupněny službě prostřednictvím zásad řízení přístupu. Tato zásada uděluje identitě služby přístup k přijetí klíče. Službu Azure spuštěnou jménem přidruženého předplatného lze nakonfigurovat s identitou v tomto předplatném. Služba může provádět ověřování služby Azure Active Directory a přijímat ověřovací token, který se identifikuje jako tato služba jednající jménem předplatného. Tento token pak může být předložen key vault získat klíč, ke kterému byl udělen přístup.

Pro operace pomocí šifrovacích klíčů může být identitě služby udělen přístup k některé z následujících operací: dešifrovat, šifrovat, rozbalitklíč, wrapKey, ověřit, podepsat, získat, seznam, aktualizovat, vytvářet, importovat, odstraňovat, zálohovat a obnovovat.

Chcete-li získat klíč pro použití při šifrování nebo dešifrování dat v klidovém stavu, identita služby, kterou bude spuštěna instance služby Resource Manager, protože musí mít klávesu UnwrapKey (chcete-li získat klíč pro dešifrování) a WrapKey (chcete-li vložit klíč do trezoru klíčů při vytváření nového klíče).

>[!NOTE]
>Další podrobnosti o autorizaci trezoru klíčů najdete na stránce zabezpečeného trezoru klíčů v [dokumentaci k úložišti klíčů Azure](../../key-vault/key-vault-secure-your-key-vault.md).

**Výhody**

- Plná kontrola nad použitými klíči – šifrovací klíče jsou spravovány v trezoru klíčů zákazníka pod kontrolou zákazníka.
- Možnost šifrování více služeb do jednoho hlavního serveru
- Může oddělit správu klíčů od celkového modelu správy služby
- Může definovat službu a klíčové umístění napříč regiony

**Nevýhody**

- Zákazník má plnou odpovědnost za správu přístupu ke klíčům
- Zákazník má plnou odpovědnost za správu životního cyklu klíčů
- Další režie konfigurace & instalaci

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem v hardwaru řízeném zákazníkem

Některé služby Azure umožňují model správy klíčů hostovat vlastní klíč (HYOK). Tento režim správy je užitečný ve scénářích, kde je potřeba šifrovat data v klidovém stavu a spravovat klíče v proprietárním úložišti mimo kontrolu společnosti Microsoft. V tomto modelu musí služba načíst klíč z externího webu. Jsou ovlivněny záruky výkonu a dostupnosti a konfigurace je složitější. Navíc vzhledem k tomu, že služba má přístup k DEK během operací šifrování a dešifrování celkové záruky zabezpečení tohoto modelu jsou podobné, když klíče jsou spravovány zákazníkem v Azure Key Vault.  V důsledku toho tento model není vhodný pro většinu organizací, pokud nemají specifické požadavky na správu klíčů. Vzhledem k těmto omezením většina služeb Azure nepodporuje šifrování na straně serveru pomocí klíčů spravovaných serverem v hardwaru řízeném zákazníkem.

##### <a name="key-access"></a>Přístup ke klíči

Při šifrování na straně serveru pomocí klíčů spravovaných službou v hardwaru řízeném zákazníkem jsou klíče udržovány v systému nakonfigurovaném zákazníkem. Služby Azure, které podporují tento model, poskytují způsob navázání zabezpečeného připojení k úložišti klíčů dodanému zákazníkem.

**Výhody**

- Plná kontrola nad použitým kořenovým klíčem – šifrovací klíče spravuje obchod poskytovaný zákazníkem
- Možnost šifrování více služeb do jednoho hlavního serveru
- Může oddělit správu klíčů od celkového modelu správy služby
- Může definovat službu a klíčové umístění napříč regiony

**Nevýhody**

- Plná odpovědnost za úložiště klíčů, zabezpečení, výkon a dostupnost
- Plná odpovědnost za správu přístupu ke klíčům
- Plná odpovědnost za správu životního cyklu klíčů
- Významné náklady na nastavení, konfiguraci a průběžnou údržbu
- Zvýšená závislost na dostupnosti sítě mezi zákaznickým datovým centrem a datovými centry Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Šifrování v klidovém stavu v cloudových službách Microsoftu

Služby Microsoft Cloud se používají ve všech třech cloudových modelech: IaaS, PaaS, SaaS. Níže máte příklady toho, jak se vejdou na každý model:

- Softwarové služby označované jako Software jako server nebo SaaS, které mají aplikace poskytované cloudem, jako je Office 365.
- Služby platformy, které zákazníci využívají cloud ve svých aplikacích, pomocí cloudu pro věci, jako je úložiště, analýzy a funkce service bus.
- Infrastruktura služby nebo infrastruktura jako služba (IaaS), ve kterém zákazník nasadí operační systémy a aplikace, které jsou hostované v cloudu a případně využití jiných cloudových služeb.

### <a name="encryption-at-rest-for-saas-customers"></a>Šifrování v klidovém stavu pro zákazníky SaaS

Zákazníci softwaru jako služby (SaaS) mají obvykle šifrování v klidovém stavu povolené nebo dostupné v každé službě. Office 365 má několik možností, jak zákazníkům ověřit nebo povolit šifrování v klidovém stavu. Informace o službách Office 365 najdete v tématu [Šifrování v Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Šifrování v klidovém stavu pro zákazníky PaaS

Data zákazníka platformy jako služby (PaaS) se obvykle nacházejí ve službě úložiště, jako je úložiště objektů blob, ale mohou být také uložena do mezipaměti nebo uložena v prostředí pro spouštění aplikací, jako je například virtuální počítač. Chcete-li zobrazit možnosti šifrování v klidovém stavu, které máte k dispozici, prověřte následující tabulku pro platformy úložiště a aplikací, které používáte.

### <a name="encryption-at-rest-for-iaas-customers"></a>Šifrování v klidovém stavu pro zákazníky IaaS

Infrastruktura jako služba (IaaS) zákazníci mohou mít celou řadu služeb a aplikací v provozu. Služby IaaS můžou povolit šifrování v klidovém stavu ve svých virtuálních počítačích a virtuálních počítačích hostovaných v Azure pomocí Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Šifrované úložiště

Stejně jako PaaS, iaas řešení můžete využít další služby Azure, které ukládají data šifrované v klidovém stavu. V těchto případech můžete povolit šifrování v klidovém stavu podpory poskytované každou spotřebovanou službou Azure. Níže uvedená tabulka vyjmenovává hlavní platformy pro úložiště, služby a aplikace a model šifrování v klidovém stavu. 

#### <a name="encrypted-compute"></a>Šifrovaný výpočetní výkon

Všechny spravované disky, snímky a bitové kopie jsou šifrovány pomocí šifrování služby Úložiště pomocí klíče spravovaného službou. Úplnější řešení Šifrování v klidovém stavu zajišťuje, že data nikdy nezůstanou v nezašifrované podobě. Při zpracování dat ve virtuálním počítači mohou být data zachována do stránkovacího souboru Windows nebo odkládacího souboru Linuxu, výpisu stavu systému nebo do protokolu aplikace. Chcete-li zajistit, aby tato data byla šifrována v klidovém stavu, aplikace IaaS můžou používat Azure Disk Encryption na virtuálním počítači Azure IaaS (Windows nebo Linux) a na virtuálním disku.

#### <a name="custom-encryption-at-rest"></a>Vlastní šifrování v klidovém stavu

Pokud je to možné, aplikace IaaS využívají možnosti Azure Disk Encryption and Encryption at Rest poskytované všemi spotřebovanými službami Azure, kdykoli je to možné. V některých případech, jako jsou požadavky na nepravidelné šifrování nebo úložiště založené na Azure, může být nutné, aby vývojář aplikace IaaS implementoval šifrování v klidovém stavu. Vývojáři řešení IaaS se můžou lépe integrovat se správou Azure a očekáváními zákazníků využitím určitých komponent Azure. Konkrétně vývojáři by měli používat službu Azure Key Vault k poskytování zabezpečeného úložiště klíčů a také poskytovat svým zákazníkům konzistentní možnosti správy klíčů s možností většiny služeb platformy Azure. Kromě toho vlastní řešení by měla používat Azure spravované služby identity povolit účty služeb pro přístup k šifrovacím klíčům. Informace o vývojáři o Azure Key Vault a identity spravované služby, najdete v tématu jejich příslušných sad SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Podpora modelu šifrování poskytovatelů prostředků Azure

Služby Microsoft Azure podporují jeden nebo více modelů šifrování v klidovém stavu. U některých služeb však nemusí být použitelný jeden nebo více modelů šifrování. Pro služby, které podporují scénáře klíčů spravované zákazníkem, mohou podporovat pouze podmnožinu typů klíčů, které azure key vault podporuje pro šifrovací klíče klíčů klíčů. Kromě toho služby může uvolnit podporu pro tyto scénáře a typy klíčů v různých plánech. Tato část popisuje podporu šifrování v klidovém stavu v době tohoto psaní pro každou z hlavních služeb úložiště dat Azure.

### <a name="azure-disk-encryption"></a>Šifrování disku Azure

Každý zákazník, který používá Azure Infrastructure as a Service (IaaS) funkce můžete dosáhnout šifrování v klidovém stavu pro své virtuální počítače IaaS a disky prostřednictvím šifrování disku Azure. Další informace o šifrování disku Azure najdete v [dokumentaci k šifrování disku Azure](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Úložiště Azure

Všechny služby Azure Storage (úložiště objektů blob, úložiště front, úložiště tabulek a soubory Azure) podporují šifrování na straně serveru v klidovém stavu; některé služby navíc podporují klíče spravované zákazníkem a šifrování na straně klienta. 

- Na straně serveru: Všechny služby Azure Storage Services umožňují šifrování na straně serveru ve výchozím nastavení pomocí klíčů spravovaných službou, které jsou pro aplikaci transparentní. Další informace najdete v tématu [Šifrování služby Úložiště Azure pro data v klidovém stavu](../../storage/common/storage-service-encryption.md). Úložiště objektů blob Azure a soubory Azure také podporují rsa 2048bitové klíče spravované zákazníky v Azure Key Vault. Další informace najdete [v tématu Šifrování služby úložiště pomocí klíčů spravovaných zákazníkem v trezoru klíčů Azure](../../storage/common/storage-encryption-keys-portal.md).
- Na straně klienta: Azure Objekty blob, tabulky a fronty podporují šifrování na straně klienta. Při použití šifrování na straně klienta zákazníci šifrují data a nahrávají data jako šifrovaný objekt blob. Správa klíčů provádí zákazník. Další informace najdete [v tématu Šifrování na straně klienta a Trezor klíčů Azure pro úložiště Microsoft Azure](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database aktuálně podporuje šifrování v klidovém stavu pro scénáře šifrování na straně služby spravované microsoftem a na straně klienta.

Podpora šifrování serveru je aktuálně poskytována prostřednictvím funkce SQL s názvem Transparentní šifrování dat. Jakmile zákazník Azure SQL Database povolí klíč TDE, automaticky se pro ně vytvoří a spravová. Šifrování v klidovém stavu lze povolit na úrovni databáze a serveru. Od června 2017 je [transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) ve výchozím nastavení povoleno v nově vytvořených databázích. Azure SQL Database podporuje rsa 2048bitové klíče spravované zákazníky v Azure Key Vault. Další informace najdete [v tématu Transparentní šifrování dat s podporou přineste si vlastní klíč pro Azure SQL Database a datový sklad](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Šifrování dat Azure SQL Database na straně klienta je podporované pomocí funkce [Vždy šifrované.](https://msdn.microsoft.com/library/mt163865.aspx) Vždy šifrované používá klíč, který vytvořil a uložil klient. Zákazníci mohou hlavní klíč uložit do úložiště certifikátů Windows, trezoru klíčů Azure nebo v místním modulu hardwarového zabezpečení. Pomocí sql server Management Studio, sql uživatelé zvolit, jaký klíč, který chcete použít k šifrování, který sloupec.

#### <a name="encryption-model-and-key-management-table"></a>Model šifrování a tabulka správy klíčů

|                                  |                    | **Model šifrování a správa klíčů** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Pomocí klíče spravovaného službou na straně serveru**     | **Na straně serveru pomocí klíče spravovaného zákazníkem**             | **Na straně klienta pomocí klientského klíče**      |
| **Umělá a počítačová výuka**      |                    |                    |                    |
| Azure Cognitive Search           | Ano                | Ano                | -                  |
| Azure Machine Learning           | Ano                | Ano                | -                  |
| Azure Machine Learning Studio    | Ano                | Náhled, RSA 2048-bit | -               |
| Power BI                         | Ano                | Náhled, RSA 2048-bit | -                  |
| **Analýza**                    |                    |                    |                    |
| Azure Stream Analytics           | Ano                | -                  | -                  |
| Event Hubs                       | Ano                | Ano, všechny délky RSA. | -                  |
| Funkce                        | Ano                | Ano, všechny délky RSA. | -                  |
| Azure Analysis Services          | Ano                | -                  | -                  |
| Azure Data Catalog               | Ano                | -                  | -                  |
| Apache Kafka ve službě Azure HDInsight  | Ano                | Všechny délky RSA.   | -                  |
| Přehledy aplikací Azure Monitor | Ano                | Ano                | -                  |
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
| Škálovací sada virtuálních strojů        | Ano                | Ano, RSA 2048-bit  | -                  |
| SAP HANA                         | Ano                | Ano, RSA 2048-bit  | -                  |
| App Service                      | Ano                | Ano                | -                  |
| Automatizace                       | Ano                | Ano                | -                  |
| Azure Portal                     | Ano                | Ano                | -                  |
| Logic Apps                       | Ano                | Ano                | -                  |
| Azure Managed Applications       | Ano                | Ano                | -                  |
| Service Bus                      | Ano                | Ano                | -                  |
| Site Recovery                    | Ano                | Ano                | -                  |
| **Databáze**                    |                    |                    |                    |
| SQL Server na virtuálních počítačích   | Ano                | Ano, RSA 2048-bit  | Ano                |
| Azure SQL Database               | Ano                | Ano, RSA 2048-bit  | Ano                |
| Databáze Azure SQL pro MariaDB   | Ano                | -                  | -                  |
| Azure SQL Database pro MySQL     | Ano                | Ano                | -                  |
| Azure SQL Database pro PostgreSQL | Ano               | Ano                | -                  |
| Azure Synapse Analytics          | Ano                | Ano, RSA 2048-bit  | -                  |
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
| **Integrace**                  |                    |                    |                    |
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
| Diskové úložiště                     | Ano                | Ano                | -                  |
| Spravované diskové úložiště             | Ano                | Ano                | -                  |
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

Ochrana zákaznických dat uložených ve službách Azure má pro Microsoft prvořadý význam. Všechny hostované služby Azure se zavázaly k poskytování šifrování v klidovém stavu. Základní služby, jako je Azure Storage, Azure SQL Database a klíčové analýzy a zpravodajské služby, už už poskytují možnosti šifrování v klidovém stavu. Některé z těchto služeb podporují klíče řízené zákazníkem a šifrování na straně klienta, stejně jako klíče spravované službou a šifrování. Služby Microsoft Azure obecně zvyšují dostupnost šifrování v klidovém stavu a v nadcházejících měsících se plánují nové možnosti pro předběžnou verzi a obecnou dostupnost.
