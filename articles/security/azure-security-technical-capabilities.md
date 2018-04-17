---
title: Technické možnosti zabezpečení Azure | Microsoft Docs
description: Další informace o cloudové výpočetní služby, které zahrnují široký výběr výpočetních instancích & služby, které je možné škálovat nahoru či dolů automaticky podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 489b2a66cadb77a338f9ae00443c072a4e19edfd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-technical-capabilities"></a>Technické možnosti zabezpečení Azure

Pomůže Azure aktuální a potenciální zákazníky, pochopit a využívat různé související se zabezpečením možnosti dostupné v a které obaluje platformě Azure, společnost Microsoft vyvinula řadu dokumenty White Paper, zabezpečení přehledy, osvědčené postupy a Kontrolní seznamy. Témata v rozsahu z hlediska spektra a hloubky a jsou pravidelně aktualizovány. Tento dokument je součástí této řady dle souhrnu v následující části abstraktní. Další informace o této série zabezpečení Azure naleznete na adrese (URL).

## <a name="azure-platform"></a>Platforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) cloudové platformy se skládá z infrastruktury a aplikační služby, s použitím integrovaného datové služby a pokročilou analýzu a nástroje pro vývojáře a služby hostované v rámci veřejného cloudu dat společnosti Microsoft centrech. Zákazníci používat Azure pro mnoho různých kapacity a scénáře, od základní výpočty, síť a úložiště, mobilní a webové aplikace služby úplné cloudu scénářů, jako je Internet věcí a lze použít s technologiích s otevřeným zdrojem a nasadit jako hybridní cloud nebo hostovaným v rámci datového centra zákazníka. Azure poskytuje technologie cloud jako stavební bloky, abyste ušetřili náklady, společností inovacemi. Zajistěte rychle a aktivně spravovat systémy. Pokud sestavení nebo migrovat IT prostředky do poskytovatele cloudových služeb, jsou spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky poskytují ke správě zabezpečení vaše cloudové prostředky.

Microsoft Azure je pouze výpočetní zprostředkovatele cloudu, který nabízí aplikace zabezpečené a konzistentní platformy a infrastruktury jako služba pro týmy pro práci v rámci své skillsets jiný cloud a úrovně složitost projektu, se službami integrované dat a analýz, které odkrýt intelligence z dat bez ohledu na existuje napříč společnosti Microsoft a jiných společností než Microsoft platforem, otevřete rozhraní a nástroje, poskytuje volba pro integraci s místním i nasazení cloudové služby Azure v místních datových center cloudu. V rámci důvěryhodné cloudu Microsoftu zákazníci spoléhají na Azure špičkový zabezpečení, spolehlivost, dodržování předpisů, ochrany osobních údajů a valná sítě osoby, partneři a procesy pro podporu organizace v cloudu.

Microsoft Azure můžete:

- Urychlit inovací s cloudem.

- & Aplikace se statistickými Power obchodních rozhodnutí.

- Volně sestavení a nasazení odkudkoli.

- Chraňte své firmy.

## <a name="scope"></a>Rozsah

Ústředním bodem tohoto dokumentu se vztahuje na funkce zabezpečení a funkce podporu Microsoft Azure základní komponenty, a to [Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction), [databází SQL Azure Microsoft](https://docs.microsoft.com/azure/sql-database/), [model virtuálního počítače Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/)a nástroje a infrastruktura, která ho všechny spravovat. Tento dokument white paper se zaměřují na Microsoft Azure technické možnosti k dispozici jako zákazníky ke splnění jejich role při ochraně zabezpečení a ochrana osobních údajů svých dat.

Význam vysvětlení tohoto modelu sdílenou odpovědnost je nezbytné pro zákazníky, kteří jsou přechodu do cloudu. Poskytovatelé cloudové nabízejí značné výhody pro zabezpečení a dodržování předpisů úsilí, ale tyto výhody nejsou zbaveny zákazník z ochranu svých uživatelů, aplikací a nabídek služeb.

Pro řešení IaaS zákazník zodpovídá nebo má sdílenou odpovědnost pro zabezpečení a správu operačního systému, konfiguraci sítě, aplikace, identity, klientů a data.  Sestavení řešení PaaS na IaaS nasazení, zákazník stále zodpovídá nebo má sdílenou odpovědnost pro zabezpečení a správu aplikací, identity, klienty a data. Pro řešení SaaS, Nonetheless, i nadále řízeni zákazníka. Musí zajistit správnou klasifikaci dat, a sdílejí zodpovědnost za správu svých uživatelů a zařízení koncový bod.

Tento dokument neposkytuje podrobné pokrytí všech souvisejících součástí platformy Microsoft Azure jako jsou weby Azure, Azure Active Directory, HDInsight, Media Services a dalším službám, které jsou na základě na základní komponenty. I když je k dispozici minimální úroveň obecné informace, se předpokládá čtečky obeznámeni s základní koncepty Azure, jak je popsáno v dalších odkazů na od společnosti Microsoft a součástí odkazů uvedených v tomto dokumentu.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>K dispozici zabezpečení technických možností plnit odpovědnost uživatele (zákazníka) - přehled

Microsoft Azure poskytuje služby, které pomáhají zákazníkům splňovaly potřeby zabezpečení, ochrany osobních údajů a dodržování předpisů. Následující obrázek pomůže popisují různé služby Azure k dispozici pro uživatele k vytvoření infrastruktury zabezpečení a dodržování aplikace na základě oborových standardů.

![K dispozici zabezpečení technické možnosti Big obrázek](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Spravovat a řídit, identity a přístup (chránit)

Azure pomáhá chránit firmy a osobní údaje tím, že vám ke správě identit uživatelů a přihlašovacích údajů a řízení přístupu.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft identit a přístupu řešení Nápověda pro správu IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu, povolení další úrovně ověřování, jako je vícefaktorové ověřování a zásady podmíněného přístupu. Monitorování podezřelé aktivity přes pokročilé zabezpečení vytváření sestav a auditování, výstrahy, pomáhá zmírnit potenciální potíže se zabezpečením. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) poskytuje jednotné přihlašování k tisícům cloudové aplikace (SaaS) a přístup k webové aplikace můžete spustit místně.

Výhody zabezpečení služby Azure Active Directory (Azure AD), zahrnují možnost:

- Vytvořit a spravovat jedinou identitu pro každého uživatele v rámci podniku hybridní udržování synchronizace uživatele, skupiny a zařízení.

- Zadejte jednoho přístupového přihlašování pro vaše aplikace, včetně tisícům předem integrovaných aplikací SaaS.

- Povolit aplikaci přístup k zabezpečení vynucením vícefaktorového ověřování založeného na pravidlech pro místní i cloudové aplikace.

- Poskytnout zabezpečený vzdálený přístup k místním webovým aplikacím prostřednictvím proxy aplikace služby Azure AD.

[Portál Azure Active Directory](http://aad.portal.azure.com/) je k dispozici součástí portálu Azure. Z tohoto řídicího panelu můžete získat přehled o stavu vaší organizace a snadno ponořte do Správa adresáře, uživatele nebo přístup k aplikaci.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Zde jsou základní možnosti správy identit Azure:

- Jednotné přihlašování

- Ověřování pomocí služby Multi-Factor Authentication

- Sledování zabezpečení, výstrahy a sestavy na základě learning počítače

- Správa identit a přístupu zákazníků

- Registrace zařízení

- Správa privilegovaných identit

- Ochrana identit

#### <a name="single-sign-on"></a>Jednotné přihlašování

[Jednotné přihlašování (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) znamená nebudete mít přístup k všechny aplikace a prostředky, které musíte udělat podnikání, po přihlášení pouze jednou pomocí jediného uživatelského účtu. Jakmile se přihlásíte, dostanete všechny aplikace, budete potřebovat, aniž by musel být vyžadovaný k ověření (zadejte například heslo) ještě jednou.

Mnoho organizací závisí software jako služba (SaaS) aplikace například Office 365, pole a Salesforce pro produktivita koncového uživatele. Historicky pracovníci IT potřebný jednotlivě vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé museli Zapamatovat heslo pro každou aplikaci SaaS.

[Azure AD rozšiřuje místní služby Active Directory do cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), povolení uživatelům používat jejich primární účet organizace, nejen Přihlaste se k jejich zařízení připojených k doméně a prostředkům společnosti, ale také všechny webu a aplikace SaaS, které jsou potřebné pro jejich úlohy.

Jenom uživatelé nemají ke správě více sad uživatelských jmen a hesel, přístup k aplikaci lze automaticky zřízeného nebo zrušte zřízené na základě organizační skupiny a jejich stav jako zaměstnanec. [Azure AD, představuje ovládací prvky zásad správného řízení zabezpečení a přístup](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) které umožňují centrálně spravovat přístup uživatelů v rámci aplikací SaaS.

#### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) je metoda ověřování, který vyžaduje použití víc než jednu metodu ověřování a přidá velmi důležitou druhou vrstvu zabezpečení uživatelská přihlášení a transakce. [MFA pomáhá chránit](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) přístup k datům a aplikacím při splnění požadavků uživatelů pro jednoduchý proces přihlášení. Zajišťuje silné ověřování přes celou řadu možností ověření – telefonní hovor, textová zpráva nebo mobilní aplikace oznámení nebo ověřovací kód a třetích stran tokeny OAuth.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Sledování zabezpečení, výstrahy a sestavy na základě learning počítače

Sledování zabezpečení a výstrah a na základě learning sestav počítače, které identifikují nekonzistentní přístupové vzorce vám umožní chránit vaši firmu. Přístup k Azure Active Directory a sestavy využití, které slouží k získat přehled o integrity a zabezpečení adresáři vaší organizace. Tyto informace a správce directory pomohou určit, kde může být bezpečnostním rizikům, tak, aby adekvátní můžete naplánovat zmírnění.

Na portálu Azure nebo prostřednictvím [portál Azure Active Directory](http://aad.portal.azure.com/), [sestavy](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) jsou rozdělené do následujících způsobů:

- Sestavy anomálií – obsahovat přihlášení události, které je neobvyklé. Naším cílem je mít budete vědět, tyto aktivity a díky kterému budete moci rozhodnout, zda je podezřelé události.

- Integrované sestavy aplikací – poskytují přehled o tom, jak cloudové aplikace jsou používány ve vaší organizaci. Azure Active Directory umožňuje integraci s tisíci cloudových aplikací.

- Zprávy o chybách – označení chyb, které mohou nastat při zřizování účtů k externím aplikacím.

- Uživatelská sestavy – zobrazí zařízení nebo přihlášení v data aktivit pro konkrétního uživatele.

- Protokoly aktivity – obsahovat záznam všech auditované události v posledních 24 hodin, posledních 7 dnů, nebo posledních 30 dnů a změny aktivity skupiny a aktivita resetování a registraci hesla.

#### <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu zákazníků

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je služba správy vysoce dostupný, globální, identity pro určených aplikace, která je škálovatelná pro stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Zákazníci se můžou ke všem vašim aplikacím přihlašovat přes přizpůsobitelné prostředí pomocí svých existujících účtů v sociálních sítích nebo pomocí nově vytvořených přihlašovacích údajů.

V minulosti, vývojáři aplikace, kteří chtěli [registrace a přihlašování uživatelů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) do svých aplikací mít by napsali vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure Active Directory B2C nabízí lepší způsob, jak integrovat správu identit uživatelů do aplikací s pomocí bezpečné, na standardech postavené platformy a velké sady rozšiřitelných zásad vaší organizace.

Pokud používáte Azure Active Directory B2C, vaši uživatelé mohou zaregistrovat do pro vaše aplikace pomocí svých účtů na sociálních (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nové přihlašovací údaje (e-mailovou adresu a heslo, nebo uživatelské jméno a heslo).

#### <a name="device-registration"></a>Registrace zařízení

[Registrace zařízení služby Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/device-management-introduction) je základem pro zařízení na základě [podmíněného přístupu](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-device-registration-on-premises-setup) scénáře. Když je zařízení registrováno, poskytne mu nástroj registrace zařízení Azure AD zařízení s identitou, která se používá k ověření zařízení při přihlášení uživatele. Ověřené zařízení a jeho atributy pak lze použít k vynucení zásad podmíněného přístupu u aplikací, které jsou hostovány na cloudu a v místním úložišti.

V kombinaci s [správu mobilních zařízení (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) řešení, jako je například Intune, budou atributy zařízení ve službě Azure Active Directory jsou aktualizovány o další informace o zařízení. To vám umožňuje vytvořit pravidla podmíněného přístupu, která vynucují, aby přístup měla pouze taková zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. 

#### <a name="privileged-identity-management"></a>Správa privilegovaných identit

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) umožňuje spravovat, řídit a monitorovat privilegované identity a přístup k prostředkům ve službě Azure AD a také další služeb Microsoft online services jako je Office 365 nebo Microsoft Intune.

Uživatelé někdy potřebovat provádět privilegované operace v Azure nebo Office 365 prostředků nebo jiných aplikací SaaS. To často znamená, že organizace mají jim dát trvalé privilegovaného přístupu ve službě Azure AD. Toto je rostoucí bezpečnostní riziko pro hostované cloudové prostředky, protože organizace nelze monitorovat dostatečně tyto činnosti uživatelů s jejich oprávněními správce. Kromě toho pokud uživatelský účet s privilegovaného přístupu je ohrožen, že jeden porušení zabezpečení by mohlo mít vliv jejich celkové zabezpečení cloudu. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko.

Azure AD Privileged Identity Management vám umožní:

- Uvidíte, kteří uživatelé jsou správci Azure AD

- Povolit na vyžádání "právě v čase" pro správu přístup k Online službám společnosti Microsoft, jako třeba Office 365 a Intune

- Získání sestavy o historii přístup správce a změny v přiřazení správců

- Získání výstrahy týkající se přístupu k privilegované role.

#### <a name="identity-protection"></a>Ochrana identit

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) je služba zabezpečení, která poskytuje ucelený přehled o rizikových událostech a potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Ochrana identity používá možností detekce anomálií existující Azure služby Active Directory (k dispozici prostřednictvím neobvyklé aktivity sestav Azure AD) a zavádí nové typy událostí rizik, které můžete zjišťovat anomálie v reálném čase.

## <a name="secured-resource-access-in-azure"></a>Přístup k zabezpečeným prostředkům v Azure

Spustí se z hlediska fakturační řízení přístupu v Azure. Vlastník účtu Azure, navštivte stránky přístup [centra účtů Azure](https://account.windowsazure.com/subscriptions), je správce účtu (AA). Předplatné je kontejner pro fakturaci, ale také slouží jako hranice zabezpečení: každého předplatného se službu správce (SA), který můžete přidat, odebrat a úpravy prostředků v tomto předplatném Azure pomocí portálu Azure. Výchozí SA nového předplatného je AA, ale AA změnit přidružení zabezpečení v centru účtů Azure.

![Přístup k zabezpečeným prostředkům v Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Odběry mají také přidružení s adresářem. Adresář definuje sadu uživatelů. Může jít o uživatele z práce nebo škola vytvořený adresář, nebo může se jednat o externí uživatele (to znamená, Accounts Microsoft). Odběry jsou přístupné pro podmnožinu těchto directory uživatelů, kteří mají přiřazený jako služba správce nebo Spolusprávce (CA); Jedinou výjimkou je, že starší verze důvodů Accounts Microsoft (dříve Windows Live ID) může být přiřazen jako SA nebo certifikační Autority bez se nachází v adresáři.

Zaměřené na zabezpečení společnosti by měla soustředit na poskytnutí zaměstnanci přesný oprávnění, které potřebují. Účet, který se útočníci můžou zpřístupnit příliš mnoho oprávnění. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) pomůže vyřešit tento problém tak, že nabídka vyladění správy přístupu pro Azure.

![Přístup k zabezpečeným prostředkům ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Pomocí RBAC, můžete v rámci týmu oddělit povinností a poskytnout pouze takovou úroveň přístupu pro uživatele, kteří potřebují k provádění svých úloh. Namísto udělení každý uživatel neomezený oprávnění v vašeho předplatného Azure nebo prostředky, můžete povolit jenom určité akce. Například použijte funkci RBAC umožníte jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiné můžete spravovat databází SQL v rámci stejného předplatného.

![Přístup k zabezpečeným prostředkům v Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Zabezpečení dat Azure a šifrování (Ochrana)

Jeden z klíčů do ochrany dat v cloudu je monitorování účtů pro možné stavy, která může nastat vaše data a jaké ovládací prvky jsou k dispozici pro tento stav. Azure data šifrování osvědčené postupy pro zabezpečení a doporučení být kolem stavy následující data.

- Na rest: To zahrnuje všechny informace, které kontejnerů, objektů úložiště a typy, které existují staticky na fyzickém médiu, být ho magnetické nebo optický disk.

- Na cestě: Když data přenášena mezi komponenty, umístění nebo programy, jako třeba přes síť, přes service bus (z lokálního prostředí do cloudu a naopak, včetně hybridních připojení, jako je například ExpressRoute), nebo během vstupu a výstupu, ho je představit jako za provozu.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Chcete-li dosáhnout šifrování v klidovém stavu, proveďte každou z následujících:

Podpora alespoň jeden z doporučené šifrování modely k šifrování dat popsané v následující tabulce.

| Modely šifrování |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Šifrování na serveru | Šifrování na serveru | Šifrování na serveru | Šifrování klienta
| Šifrování na straně serveru pomocí klíče spravované služby | Šifrování na straně serveru pomocí Customer-Managed klíčů v Azure Key Vault | Šifrování na straně serveru pomocí klíčů zákazníků spravovat místní |
| • Azure zprostředkovatelé prostředků provádět operace šifrování a dešifrování <br> • Microsoft spravuje klíče <br>• Úplné cloudu funkce | • Azure zprostředkovatelé prostředků provádět operace šifrování a dešifrování<br>• Zákazníka ovládací prvky klíče přes Azure Key Vault<br>• Úplné cloudu funkce | • Azure zprostředkovatelé prostředků provádět operace šifrování a dešifrování <br>• Zákazníka klíče místní ovládací prvky <br> • Úplné cloudu funkce| • Služeb azure nejde zobrazit dešifrovaná data <br>• Zákazníkům zachovat klíče místně (nebo v jiné zabezpečené úložiště). Klíče nejsou k dispozici ke službám Azure <br>• Snížit cloudu funkce|

### <a name="enabling-encryption-at-rest"></a>Povolení šifrování v klidovém stavu

**Identifikujte všechny umístění úložiště dat**

K šifrování všech dat je cílem šifrování v klidovém stavu. Díky tomu eliminuje možnost chybí důležitá data nebo všechny trvalou umístění. Výčet všech dat uložených v aplikaci. 

> [!Note] 
> Nejen "application data" nebo "PII, ale žádná data týkající se aplikace včetně účet metadata (předplatné mapování, informace o smlouvě, PII).

Zvažte, jaké úložiště, který používáte pro ukládání dat. Příklad:

- Externího úložiště (například SQL Azure Documentdb, HDInsights, Data Lake, atd.)

- Dočasné úložiště (žádné místní mezipaměť obsahující data klienta)

- Mezipaměť v paměti (možné zařadit do stránkovacího souboru.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Využít existující šifrování prostřednictvím rest podpory v Azure

Pro každý úložiště, které používáte využívejte existující šifrování prostřednictvím podpory Rest.

- Azure Storage: Viz [šifrování služby úložiště Azure pro Data v klidovém stavu](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Najdete v části [transparentní šifrování dat (šifrování TDE), vždycky šifrovaná SQL](https://msdn.microsoft.com/library/mt163865.aspx)

- Virtuální počítač & místní disk úložiště ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Pro virtuální počítač a místní úložiště disku použijte Azure Disk Encryption podporována:

#### <a name="iaas"></a>IaaS

Služby virtuálních počítačů IaaS (Windows nebo Linux) by měl používat [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) k šifrování svazků obsahující data zákazníků.

#### <a name="paas-v2"></a>PaaS v2

Služby provozované na PaaS v2 Service Fabric pomocí šifrování disku Azure pro sadu škálování virtuálního počítače [VMSS] k šifrování jejich virtuální počítače PaaS v2.

#### <a name="paas-v1"></a>PaaS v1

Azure Disk Encryption není aktuálně podporována u na PaaS v1. Proto musíte použít šifrování na úrovni aplikace k šifrování trvalých dat v klidovém stavu.  To zahrnuje, ale není omezeno na data aplikací, dočasné soubory, protokoly a výpisy stavu systému.

Většina služeb mají pokusit o využít šifrování zprostředkovatele prostředku úložiště. Některé služby mají udělat explicitní šifrování, například všechny trvalé materiál klíče (certifikáty, kořenové / hlavní klíče) musí být uložen v Key Vault.

Pokud podporujete šifrování na straně služby spravované zákazníkem klíče je potřeba způsob, jak zákazník k získání klíče do us. Podporované a doporučený způsob, jak to udělat integrací trezoru službou klíč s Azure (AZURE). V takovém případě zákazníků můžete přidávat a spravovat jejich klíče v Azure Key Vault. Zákazník může Naučte se používat službou AZURE prostřednictvím [Začínáme s Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Při integraci s Azure Key Vault, byste přidali kód pro klíč požádat službou AZURE v případě potřeby k dešifrování.

- V tématu [Azure Key Vault – krok za krokem](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) informace o tom, jak integrovat se službou AZURE.

Pokud podporujete zákazníka spravované klíče, potřebujete poskytovat UX pro zákazníka na zadejte Key Vault (nebo identifikátor URI trezoru klíčů) pro použití.

Jak šifrování v klidovém stavu zahrnuje šifrování hostitele, dat infrastruktury a klientů, ztrátě klíče z důvodu chyby systému nebo škodlivé aktivity by mohly znamenat šifrovaná data je ztraceny. Proto je důležité, aby šifrování v řešení Rest má scénáře obnovení po havárii komplexní odolné vůči selhání systému a podezřelé aktivity.

Služby, které implementují šifrování v klidovém stavu jsou obvykle přesto náchylné k šifrovacím klíčům nebo bez šifrování dat ponechána na hostitelské jednotce (například v souboru stránky hostitelský operační systém.) Služby musí proto zkontrolujte, že svazek hostitele pro jejich služby se šifrují. Pro usnadnění tohoto výpočetní team aktivoval nasazení šifrování hostitele, který používá [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP a rozšíření pro službu DCM a agenta pro šifrování svazku hostitele.

Většina služeb jsou implementovány na standardní virtuálních počítačích Azure. Tyto služby by měl získat [hostitele šifrování](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automaticky při výpočetní povolí ho. Pro služby spuštěné v výpočetní spravované clustery hostitelů je povolené šifrování automaticky jako je Windows Server 2016 nasazuje.

### <a name="encryption-in-transit"></a>Šifrování během přenosu

Základní součástí strategie ochrany dat by měly být ochrany dat během přenosu. Vzhledem k tomu, že se data přenášejí a zpět z mnoha umístění, obecné doporučení je vždy používat protokoly SSL/TLS pro výměnu dat v různých umístěních. V některých případech můžete chtít izolovat celý komunikační kanál mezi místní a cloudové infrastruktury pomocí virtuální privátní sítě (VPN).

Pro přesun mezi vaší místní infrastruktury a Azure data měli byste zvážit příslušná bezpečnostní opatření, například HTTPS nebo VPN.

Organizace, které je potřeba zabezpečit přístup z více pracovní stanice nachází v místě k Azure, použijte [Azure site-to-site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Organizace, které je potřeba zabezpečit přístup z jedné pracovní stanice nachází v místě k Azure, použijte [Point-to-Site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Větších datových sad se dají přesunout přes vyhrazené vysokorychlostní propojení WAN, jako [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pokud chcete použít ExpressRoute, můžete také šifrování dat na úrovni aplikace pomocí [SSL/TLS](https://support.microsoft.com/kb/257591) nebo jiné protokoly pro zvýšení ochrany.

Pokud jsou interakci s Azure Storage prostřednictvím portálu Azure, všechny transakce dojít přes HTTPS. [Rozhraní API REST úložiště](https://msdn.microsoft.com/library/azure/dd179355.aspx) přes HTTPS můžete použít také pro interakci s [Azure Storage](https://azure.microsoft.com/services/storage/) a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organizace, které se nepodařilo ochranu přenášených dat budou náchylnější pro [útokům man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx)a zneužití relace. Prvním krokem při přístupu k důvěrných dat může být tyto útoky.

Další informace o Azure VPN možnost přečíst v článku [plánování a návrhu pro bránu VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Vynutit šifrování dat na úrovni souborů

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) používá zásady šifrování, identity a autorizace, které pomáhají zabezpečit soubory a e-mailu. Azure RMS funguje napříč více zařízeními – telefony, tablety a počítače pomocí ochrany v rámci vaší organizace i mimo vaši organizaci. Tato možnost je možné, protože Azure RMS přidá úroveň ochrany, která zůstává s daty, i když opustí prostory vaší organizace.

Pokud používáte službu Azure RMS k ochraně souborů, používáte standardní kryptografie s plnou podporu systému [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Pokud využíváte Azure RMS pro ochranu dat, máte záruku, že zůstane ochrana se souborem, i v případě, že se zkopíruje do úložiště, které není pod kontrolou oddělení IT, třeba do cloudového úložiště. Stejné dojde souborů sdílených prostřednictvím e-mailu, je soubor chráněný jako příloha k e-mailovou zprávu s pokyny, jak chráněnou přílohu otevřít.
Při plánování pro Azure RMS přijetí doporučujeme následující:

- Nainstalujte [aplikace sdílení RMS](https://technet.microsoft.com/library/dn339006.aspx). Tato aplikace se integruje s Office aplikace nainstalováním Office doplňku tak, aby uživatelé mohli snadno chránit soubory přímo.

- Nakonfigurujte aplikace a služby pro podporu Azure RMS

- Vytvoření [vlastní šablony](https://technet.microsoft.com/library/dn642472.aspx) , podle vlastních podnikových požadavků. Příklad: šablonu pro horní tajných dat, která má být použita v všechny hlavní tajný klíč související s e-mailů.

Organizace, které jsou slabé na [klasifikace dat](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochranu souborů může být náchylnější k úniku dat. Bez ochrany správný soubor nebude možné získat obchodní údaje, sledovat zneužití a zabránit škodlivým přístupem k souborům organizace.

> [!Note]
> Další informace o Azure RMS přečíst v článku [Začínáme se službou Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Zabezpečení vaší aplikace (Ochrana)
Sice zodpovídají za zabezpečení infrastruktury a platformy, které vaše aplikace běží v Azure, je vaší povinností zabezpečit vaše vlastní aplikace. Jinými slovy musíte k vývoji, nasadit a spravovat kódu aplikace a obsah zabezpečené způsobem. Bez tohoto kódu aplikace nebo obsah může být zranitelný vůči hrozbám.

### <a name="web-application-firewall-waf"></a>Firewall webových aplikací (WAF)
[Brány firewall webových aplikací (firewall webových aplikací)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) je funkce [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) poskytuje centralizovanou ochrany webových aplikací z běžných zneužití a ohrožení zabezpečení.

Firewall webových aplikací je založený na základě [základní sady pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

Mezi běžné webové zranitelnosti, proti kterým firewall webových aplikací chrání, patří tyto:

- Ochrana před útoky prostřednictvím injektáže SQL.

- Ochrana před skriptováním mezi weby.

- Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

- Ochrana před narušením protokolu HTTP.

- Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

- Ochrana před roboty, prohledávacími moduly a skenery.

- Detekce časté nesprávné konfigurace aplikace (tedy Apache, IIS, atd.)

> [!Note]
> Podrobnější seznam pravidel a jejich ochrany naleznete v následujících [základní sady pravidel](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Azure také poskytuje několik snadno použitelné funkce, které pomáhají zabezpečit příchozí a odchozí přenosy pro vaši aplikaci. Azure pomáhá zákazníkům zabezpečit svůj kód aplikace poskytnutím externě k dispozici také funkce ke kontrole chyb zabezpečení webové aplikace.

- [Nastavení ověřování Azure Active Directory pro vaši aplikaci](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Zabezpečení přenosu do vaší aplikace povolením Transport Layer Security (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Vynutit veškerý příchozí provoz přes připojení HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Povolit zabezpečení striktní přenosu (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Omezit přístup k aplikaci tak, že IP adresa klienta](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Omezit přístup k aplikaci tak, že chování klienta - požadavek četnost a souběžnost](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Kontrola kódu webové aplikace pro chyby pomocí Tinfoil Security Scanning.](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurace vzájemné ověřování TLS vyžadovat klientské certifikáty pro připojení k vaší webové aplikace](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Konfigurovat certifikát klienta pro použití z vaší aplikace pro zabezpečené připojení k externím prostředkům](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Odebrat záhlaví standardní server předejdete nástroje z tímto způsobem vaší aplikace](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Bezpečně připojte vaše aplikace s prostředky v privátní síti prostřednictvím sítě VPN Point-To-Site](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Bezpečně připojte vaše aplikace s prostředky v privátní síti používáte hybridní připojení](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Aplikační služba Azure používá stejné Antimalwarové řešení používá Azure Cloud Services a virtuálních počítačů. Další informace o tom najdete v tématu naše [antimalwarových dokumentaci](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Zabezpečení sítě (Ochrana)
Microsoft Azure obsahuje robustní síťové infrastruktury pro podporu aplikací a služeb požadavky na připojení. Připojení k síti je možné mezi prostředky, které jsou umístěné v Azure, mezi místními a Azure hostovaným prostředkům a do a z Internetu a Azure.

[Azure síťové infrastruktury](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) umožňuje bezpečně připojit k dalším s prostředky Azure [virtuální sítě (virtuální sítě)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Virtuální síť je reprezentace vlastní sítě v cloudu. Virtuální síť je to logická izolace sítě cloudu Azure vyhrazeného pro vaše předplatné. Virtuální sítě může připojit k místní sítě.

![Zabezpečení sítě (Ochrana)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Pokud potřebujete řízení úrovně přístupu k základní síti (na základě IP adresy a protokoly TCP nebo UDP), pak můžete použít [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Skupina zabezpečení sítě (NSG) je základní stavových paketů filtrování brány firewall a umožňuje vám k řízení přístupu na základě [5 řazené kolekce členů](https://www.techopedia.com/definition/28190/5-tuple).

Azure sítě podporuje přizpůsobit chování směrování síťového provozu ve virtuálních sítí Azure. To provedete tak, že nakonfigurujete [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) v Azure.

[Vynuceného tunelování](https://www.petri.com/azure-forced-tunneling) mechanismus, můžete zajistit, že vaše služby nejsou povolené k navázání připojení k zařízení na Internetu.

Azure podporuje vyhrazené propojení WAN připojení k síti na pracovišti a virtuální síť Azure s [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Propojení mezi Azure a váš web používá vyhrazené připojení, které se nepřenášejí prostřednictvím veřejného Internetu. Pokud vaše aplikace Azure běží v několik datových center, můžete použít [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pro směrování požadavků od uživatelů inteligentně napříč instancí aplikace. Můžete také směrovat provoz do služby nejsou spuštěny v Azure, pokud jsou přístupné z Internetu.

## <a name="virtual-machine-security-protect"></a>Virtuální počítač zabezpečení (Ochrana)

[Virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/) umožňuje nasadit širokou škálu řešení výpočetní agilní způsobem. Díky podpoře systémů Windows, Linux, SQL Server, Oracle, IBM, SAP a Azure BizTalk Services můžete nasadit jakoukoli úlohu a jakýkoli jazyk skoro v každém operačním systému.

V Azure, můžete použít [antimalwarový software](https://docs.microsoft.com/azure/security/azure-security-antimalware) od dodavatelů zabezpečení, jako je Microsoft, Symantec, Trend Micro a Kaspersky ochrana virtuálních počítačů ze škodlivých souborů, adwaru a dalšími hrozbami.

Antimalware od Microsoftu pro Azure Cloud Services a virtuální počítače je funkce ochrany v reálném čase, který pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Antimalware od Microsoftu poskytuje konfigurovat výstrahy, když označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v Azure systémech.

[Zálohování Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) škálovatelné řešení, které chrání data aplikací s nulové kapitálovými investicemi a minimálními provozní náklady. Chyby aplikací můžou poškodit vaše data a lidské omyly zase můžou způsobit chyby v aplikacích. S Azure Backup jsou chráněné virtuální počítače s Windows a Linux.

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) pomáhá orchestraci replikace, převzetí služeb při selhání a obnovení úloh a aplikací, aby byly k dispozici ze sekundární lokality Pokud primární lokalita ocitne mimo provoz.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Zajištění dodržování předpisů: cloudových služeb z důvodu kontrolní seznam opatrností (Ochrana)

Microsoft vyvinul [cloudové služby kvůli opatrností kontrolního seznamu](https://aka.ms/cloudchecklist.download) umožňující organizacím uplatňovat kvůli opatrností podle svého uvážení přesunout do cloudu. Poskytuje strukturu pro organizaci všech velikost a typ – privátní podnikům a organizacím veřejných, včetně government na všech úrovních a neziskové organizace – k identifikaci svých vlastních výkonu, služby, Správa dat a cíle zásad správného řízení a požadavky. To umožňuje, aby porovnání nabídek poskytovatelů služeb jiný cloud, nakonec které tvoří základ pro smlouvy o cloudových služeb.

V seznamu úkolů poskytuje rozhraní, aby klauzule pomocí klauzule s novou standardní mezinárodní pro cloudové služby smlouvy, ISO/IEC 19086. Tento standard poskytuje sada důležité informace pro organizace, aby mohl snadněji rozhodnutí o přijetí cloudu a vytvořit běžné základů pro porovnání nabídek služeb cloudu.

V seznamu úkolů zvýší úroveň důkladně vetted přesunout do cloudu a poskytuje pokyny strukturovaných a konzistentní, opakovatelných přístup pro výběr poskytovatele cloudové služby.

Přijetí cloudu je již jednoduše technologie rozhodnutí. Protože touch kontrolní seznam požadavků na každý aspekt organizace, slouží o svolání všechny klíče interní-rozhodují – ředitel IT a ředitel zabezpečení informací, jakož i právní, riziko Odborníci v oblasti správy, nákup a dodržování předpisů. Tím se zvyšuje efektivita rozhodovací proces a základů rozhodnutí v zvukové reasoning, což snižuje pravděpodobnost nepředpokládaného roadblocks přijetí.

Kromě toho, v seznamu úkolů:

- Zpřístupní klíče diskusní témata pro vedoucí pracovníky na začátku procesu přijetí cloudu.

- Podporuje důkladné obchodní diskusí o předpisy a cíle organizace pro zabezpečení dat, ochrany osobních údajů a identifikovatelné osobní údaje (PII).

- Pomáhá organizacím identifikovat všechny potenciální problémy, které mohou ovlivnit projekt cloudové.

- Poskytuje sadu otázky, konzistentní s stejných podmínek, definice, metriky a výsledek pro každého zprostředkovatele zjednodušit proces porovnání nabídek z poskytovatele různých cloudových služeb.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure ověření zabezpečení infrastruktury a aplikací (zjistit)

[Zabezpečení provozu Azure](https://docs.microsoft.com/azure/security/azure-operational-security) odkazuje na službách, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svá data, aplikace a dalších prostředků ve službě Microsoft Azure.

![ověření zabezpečení (zjistit)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Zabezpečení provozu Azure je založený na rozhraní, které zahrnuje poznatky získané při různých možnostech, které jsou jedinečné pro společnosti Microsoft, včetně Microsoft SDL Security Development Lifecycle (), programu Microsoft zabezpečení odpovědi Centre a hloubkové povědomí o povahu hrozeb počítačové bezpečnosti.

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft operations management suite(OMS)

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je řešení pro správu IT pro hybridní cloud. Použitá samostatně nebo rozšířit existující nasazení produktu System Center, OMS vám dává flexibilní a řízení pro správu cloudové infrastruktury.

![Microsoft operations management suite(OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

S OMS můžete spravovat libovolnou instancí ve všech cloudu, včetně místní, Azure, AWS, Windows Server, Linux, VMware a OpenStack, při nižších nákladech, než konkurenční řešení. Vytvořené pro první cloudu world, OMS nabízí nové přístup ke správě vaší organizace tedy nejrychlejší a nákladově nejefektivnější způsob přizpůsobení nové úlohy, aplikace a cloudové prostředí a splňují nové obchodní příležitosti.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) poskytuje služby monitorování pro OMS získáváním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Tato metoda umožňuje konsolidovat data z různých zdrojů, takže můžete kombinovat data ze služeb Azure s vaší stávající místní prostředí. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Služba Security Center analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Seznam doporučení vás provede procesem konfigurace potřebných kontrol.

Příklady obsahují:

- Zřizování antimalwaru, aby se pomohl identifikovat a odebrat škodlivý software

- Konfigurace skupin zabezpečení sítě a pravidla pro řízení přenosu do virtuálních počítačů

- Zřizování firewallů webových aplikací, které pomáhají bránit útokům zaměřeným na vaše webové aplikace

- Nasazení chybějících aktualizací systému

- Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové programy a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

- Ohrožené virtuální počítače komunikaci s známé škodlivé IP adresy

- Pokročilý malware zjištěný pomocí zasílání zpráv o chybách systému Windows

- Útoky hrubou silou na virtuální počítače

- Výstrahy zabezpečení z integrovaných antimalwarových programů a bran firewall

### <a name="azure-monitor"></a>Azure monitorování

[Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) poskytuje odkazy na informace na konkrétní typy prostředků. Nabízí vizualizace, dotaz, směrování, výstrahy, automatické škálování a automatizace na datům umístěným jak infrastrukturu Azure (protokol aktivit) a každý jednotlivých prostředků Azure (diagnostických protokolů).

Cloudové aplikace jsou komplexní s mnoha přesunutí částmi. Monitorování poskytuje data a ujistěte se, že vaše aplikace zůstává nahoru a spuštěna v dobrém stavu. Také pomáhá stave vypnout potenciální problémy nebo vyřešit potíže s uplynulou těch, které jsou.

![Azure monitorování](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) kromě toho můžete data monitorování a získáte přehled o hloubkové o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

Auditování zabezpečení sítě je důležité pro zjišťování chyb zabezpečení sítě a zajištění dodržování zabezpečení IT a modelu regulačních zásad správného řízení. Pomocí zobrazení skupiny zabezpečení můžete načíst nakonfigurovaná skupina zabezpečení sítě a pravidel zabezpečení, jakož i pravidla efektivní zabezpečení. Seznam pravidel, použít můžete určit, že porty, které jsou otevřené a ss sítě ohrožení zabezpečení.

### <a name="network-watcher"></a>Sledovací proces sítě

[Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni sítě v, do a z Azure. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure. Tato služba obsahuje zachytáváním paketů, další směrování, IP tok ověření, zobrazení skupiny zabezpečení, tok protokolů NSG. Scénář úrovně monitorování poskytuje pohledu koncové síťových prostředků, na rozdíl od monitorování jednotlivých síťových prostředků.

### <a name="storage-analytics"></a>Analýza služby Storage

[Analytika úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) můžete ukládat metriky, které zahrnují agregované transakce statistiky a kapacity data o požadavcích služby úložiště. Transakce jsou hlášeny na úrovni operace rozhraní API, a také na úrovni služby úložiště a hlásí kapacity na úrovni služby úložiště. Metriky dat slouží k analýze využití služby úložiště, diagnostikovat problémy s požadavky na služby úložiště a zlepšit výkon aplikací, které používají službu.

### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelný služba Správa výkonu aplikace (APM) pro vývojáře, kteří ve více platformách. Slouží k monitorování živé webové aplikace. Automaticky zjišťuje anomálie ve výkonu. Obsahuje nástroje výkonné analytics při diagnostice problémů a zjistit, co uživatelé dělají s vaší aplikací. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Funguje u aplikací na široké škále platforem, jako jsou .NET, Node.js a J2EE, a to hostovaných lokálně i v cloudu. Se integruje s váš proces devOps a má spojovací body na různé nástroje pro vývoj.

Monitoruje tyto parametry:

- **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky.

- **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.

- **Výjimky** – analyzovat souhrnných statistik, nebo vyberte určité instance a přejít k podrobnostem trasování zásobníku a související požadavky. Hlásí se výjimky serveru i prohlížeče.

- **Zobrazení a načítání stránek** – Tyto informace hlásí prohlížeče uživatelů.

- **Volání AJAX z webové stránky** -sazby, doby odezvy a selhání sazby.

- **Počty uživatelů a relací.**

- **Čítače výkonu** ze serverových počítačů s Windows nebo Linuxem, jako je třeba CPU, paměť a využití sítě.

- **Diagnostika hostitele** z Dockeru nebo Azure.

- **Protokoly trasování diagnostiky** z vaší aplikace – umožňují zjistit korelaci mezi požadavky a událostmi trasování.

- **Vlastní události a metriky** napíšete sami v kód klienta nebo serveru ke sledování obchodní události, jako například položek prodaných, nebo hry won.

Infrastrukturu aplikace obvykle tvoří celá řada komponent, může to být třeba virtuální počítač, účet úložiště a virtuální síť nebo webová aplikace, databáze, databázový server a služby jiných výrobců. Tyto komponenty nevidíte jako samostatné entity, ale jako související a vzájemně provázané části jedné entity. Chcete je nasadit, spravovat a monitorovat jako skupinu. [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umožňuje pracovat s prostředky ve vašem řešení jako se skupinou.

Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

**Výhody použití Resource Manager**

Resource Manager poskytuje několik výhod:

- Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

- Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

- Můžete využít řízení přístupu pro všechny služby ve vaší skupině prostředků, protože do platformy pro správu je nativně integrováno řízení přístupu na základě role (RBAC).

- Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

- Můžete zpřehlednit fakturaci svojí organizace zobrazením nákladů na skupinu prostředků, které sdílejí stejnou značku.

> [!Note]
> Resource Manager poskytuje nový způsob nasazení a správy vašich řešení. Pokud jste použili dřívější model nasazení a chcete další informace o změnách, přečtěte si téma [nasazení Resource Manager principy a nasazení classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení načtením některá témata s našimi podrobné zabezpečení:

- [Auditování a protokolování](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Kybernetická](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Návrh a provozního zabezpečení](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Šifrování](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Správu identit a přístupu](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Zabezpečení sítě](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Řízení rizik](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
