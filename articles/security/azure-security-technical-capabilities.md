---
title: Technické možnosti zabezpečení Azure | Dokumentace Microsoftu
description: Přečtěte si o cloudové výpočetní služby, které zahrnují široké škály výpočetních instancí a služby, které je možné škálovat směrem nahoru a dolů automaticky podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: c94a078e77f1e3e5b70d2d95016b8417fe2172d5
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118475"
---
# <a name="azure-security-technical-capabilities"></a>Technické možnosti zabezpečení Azure

Pomáhat aktuální a budoucí Azure zákazníky, pochopit a využívat různé související se zabezpečením možnosti dostupné v, a obaluje platformy Azure, společnost Microsoft vyvinula řadu dokumenty White Paper, přehledy o zabezpečení, osvědčené postupy a Kontrolní seznamy. Témata z hlediska rozebírají v rozsahu a jsou pravidelně aktualizovány. Tento dokument je části této série dle souhrnu v následující části abstraktní. Další informace o této sérii zabezpečení Azure najdete na adrese (URL).

## <a name="azure-platform"></a>Platforma Azure

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) cloudovou platformu se skládá z infrastruktury a aplikační služby, pomocí integrované datové služby a pokročilých analýz a vývojářské nástroje a služby, hostované v datech veřejného cloudu Microsoftu centra. Zákazníci pomocí Azure pro mnoho různých kapacity a scénářů, od základní výpočetní, síťové a úložiště, na mobilní a webové aplikace služby na plně cloudovou scénáře, jako je Internet of Things a je možné použít s využitím opensourcových technologií a nasadit jako hybridního cloudu nebo hostované v rámci datového centra zákazníka. Azure poskytuje cloudové technologie jako stavební bloky, které pomůže podnikům šetřit náklady, vám umožní inovovat rychle a proaktivně správě systémů. Při sestavení nebo migraci prostředků IT k poskytovateli cloudu jste závislí na schopnosti této organizace k ochraně vašich aplikací a dat s ovládacími prvky poskytují ke správě zabezpečení vašich cloudových prostředků a služeb.

Microsoft Azure je jediný poskytovatel cloudu výpočetní, který nabízí zabezpečené a konzistentní aplikační platformy a infrastruktury as-a-service pro týmy, které pro práci v rámci jejich různé cloudové dovednosti a úrovně složitosti projektu, se službami data integrovaná v a analýzy, které odhalení hlubších souvislostí data bez ohledu na to existuje Microsoftu a jiné platformy než Microsoft, otevřete architekturami a nástroji, poskytování volbou pro integraci s místním nasazení i cloudových služeb Azure v rámci cloudu místní datacentra. Jako součást důvěryhodného cloudu Microsoft zákazníci využívají Azure pro špičkové zabezpečení, spolehlivost, dodržování předpisů, ochrany osobních údajů a rozsáhlé sítě lidi, partnery a procesy pro podporu organizace v cloudu.

Microsoft Azure můžete:

- Zrychlete inovace s využitím cloudu.

- Aplikace a aplikace s přehledy Power obchodní rozhodnutí.

- Tvorba bez omezení a nasazovat kdekoli.

- Chraňte svoje podnikání.

## <a name="scope"></a>Rozsah

Funkce zabezpečení a funkce Podpora základní komponenty Microsoft Azure, a to se týká ústředním bodem tento dokument White Paper [Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction), [Microsoft Azure SQL Database](https://docs.microsoft.com/azure/sql-database/), [ Model virtuálního počítače Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/)a nástroje a infrastruktura, která správu. Tento dokument white paper se soustředí na Microsoft Azure technické možnosti pro vás dostupný jako zákazníky pro splnění jejich role při ochraně zabezpečení a ochrana osobních údajů svých dat.

Důležitost vysvětlení tohoto modelu sdílená odpovědnost je zásadní pro zákazníky, kteří se přesouvají do cloudu. Poskytovatelé cloudových služeb přináší značné výhody pro účely zabezpečení a dodržování předpisů, ale tyto výhody nejsou zbaveny zákazníka k ochraně jejich uživatelé, aplikace a nabídky služeb.

Pro řešení IaaS zákazník zodpovídá nebo má sdílenou odpovědnost za zabezpečení a správu operačního systému, konfigurace sítě, aplikace, identitu, klientů a data.  Sestavení řešení PaaS nasazení IaaS, zákazník stále zodpovídá nebo má sdílenou odpovědnost za zabezpečení a správu aplikací, identit, klientů a data. Pro řešení SaaS, Nonetheless, i nadále odpovídat zákazníka. Musíte zajistit, že správnou klasifikaci dat a sdílejí zodpovědnost za správu svých uživatelů a zařízení koncový bod.

Tento dokument neobsahuje podrobné pokrytí všech souvisejících součástí platformy Microsoft Azure jako jsou weby Azure, Azure Active Directory, HDInsight, Media Services a dalším službám, které jsou rozloženy do vrstev imitovaná základních komponent. I když je k dispozici minimální úroveň obecné informace, čtenářům se předpokládá, že znáte základní koncepty Azure popsaný v jiných odkazů na poskytované společností Microsoft a součástí odkazů uvedených v tomto dokumentu white paper.

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Technické možnosti zabezpečení dostupné ke splnění odpovědnost uživatele (zákazník) – přehled

Microsoft Azure poskytuje služby, které zákazníkům pomáhají při splnění požadavků zabezpečení, ochrany osobních údajů a dodržování předpisů. Na následujícím obrázku pomáhá popisují různé služby Azure k dispozici pro uživatele vytvořit infrastrukturu zabezpečení a dodržování aplikace na základě oborových standardů.

![Zabezpečení k dispozici technické možnosti velký obrázek](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Správa a řízení identit a přístupu uživatelů (chránit)

Azure vám pomůže chránit firmy a osobní údaje tím, že vám ke správě identit uživatelů a přihlašovacích údajů a řízení přístupu.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft identity a přístupu k řešení Nápověda pro funkci správy IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu, povolení další úrovně ověřování, jako je například vícefaktorové ověřování a podmíněného přístupu zásady. Monitorování podezřelých aktivit prostřednictvím pokročilého generování sestav zabezpečení, auditování a upozorňování pomáhá zmírňovat potenciální problémy se zabezpečením. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) poskytuje jednotné přihlašování k tisícům cloudových (SaaS) aplikací a přístup k webovým aplikacím, které běží místně.

Výhody zabezpečení Azure Active Directory (Azure AD) zahrnují možnost:

- Vytvářejte a spravujte jedinou identitu pro každého uživatele ve vaší organizaci hybridní udržování synchronizace uživatele, skupiny a zařízení.

- Poskytnutí přístupu jednotné přihlašování pro vaše aplikace, včetně tisíce předem integrovaných aplikací SaaS.

- Povolit zabezpečení přístupu aplikací tím, že vynucuje založeného na pravidlech ověřování službou Multi-Factor Authentication v místním i cloudovým aplikacím.

- Zřízení zabezpečený vzdálený přístup k místním webovým aplikacím prostřednictvím Proxy aplikací Azure AD.

[Portálu Azure Active Directory](http://aad.portal.azure.com/) je k dispozici část na webu Azure portal. Na tomto řídicím panelu můžete získat přehled o stavu vaší organizace a snadno podrobně spravovat adresář, uživatele nebo přístupu k aplikacím.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Tady jsou základní možnosti správy identit Azure:

- Jednotné přihlašování

- Ověřování pomocí služby Multi-Factor Authentication

- Monitorování zabezpečení, oznámení a sestavy založené na učení počítače

- Správa identit a přístupu zákazníků

- Registrace zařízení

- Privileged Identity Management

- Ochrana identit

#### <a name="single-sign-on"></a>Jednotné přihlašování

[Jednotné přihlašování (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) znamená, že schopnost přistupovat ke všem aplikacím a prostředkům, které je třeba uzavírat obchody, přihlášením jen jednou pomocí jednoho uživatelského účtu. Jakmile se přihlásíte, můžete můžete přístup ke všem aplikacím, aniž by musel pracovat a nemusí opakovat ověření (třeba zadávat heslo) podruhé.

Řada organizací používá software jako služba (SaaS) aplikací jako je Office 365, Box nebo Salesforce pro produktivity koncových uživatelů. V minulosti pracovníky technické potřeby jednotlivě vytvářet a aktualizovat uživatelské účty v jednotlivých aplikací SaaS a uživatelé museli Zapamatovat heslo pro každou aplikaci SaaS.

[Azure AD rozšiřuje místní služby Active Directory do cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), umožňuje uživatelům použít svůj primární účet organizace, nejen přihlaste do jejich zařízení připojených k doméně a firemním prostředkům, ale také všechny webu a aplikace SaaS, které jsou potřebné pro své práce.

Nejen uživatele není potřeba spravovat víc kopií uživatelských jmen a hesel, přístup k aplikaci může být automaticky zřízeného nebo zruší podle organizační skupiny a jejich stav jako zaměstnanec. [Azure AD přináší zabezpečení a zásad správného řízení řízení přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) , díky kterým můžete centrálně spravovat přístup uživatelů v aplikacích SaaS.

#### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication

[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) je metoda ověřování, který vyžaduje použití víc ověřovacích metod a přidá do uživatelská přihlášení a transakce velmi důležitou druhou vrstvu zabezpečení. [MFA pomáhá chránit](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) přístup k datům a aplikace a současně plní požadavky uživatelů na jednoduchý přihlašovací proces. Nabízí silné ověřování přes celou řadu možností ověřování – telefonní hovor, textová zpráva nebo mobilní aplikace oznámení nebo ověřovací kód a třetích stran tokenů OAuth.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorování zabezpečení, oznámení a sestavy založené na učení počítače

Monitorování zabezpečení a upozornění a machine learning sestav založených na, které rozpoznávají nekonzistentní vzorce přístupu můžete pomoct chránit vaši firmu. Přístup k Azure Active Directory a sestavy využití můžete získat náhled do integrity a zabezpečení adresáři vaší organizace. Pomocí těchto informací adresáře správce můžete zjistit, kde můžou být potenciální bezpečnostní rizika, mohli odpovídajícím způsobem plánovat pro zmírnění rizika.

Na webu Azure Portal nebo prostřednictvím [portálu Azure Active Directory](http://aad.portal.azure.com/), [sestavy](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) jsou rozdělené do následujících způsobů:

- Sestavy anomálií – obsahovat události, které jsme zjištěna neobvyklá přihlášení. Naším cílem je informovat vás tyto aktivity a umožní vám moct rozhodnout o tom, zda je podezřelé události.

- Sestavy integrovaných aplikací – poskytuje přehled o používání cloudových aplikací ve vaší organizaci. Azure Active Directory nabízí integraci s tisíci cloudových aplikací.

- Zprávy o chybách – označení chyb, které mohou nastat při zřizování účtů do externí aplikace.

- Sestavy pro konkrétní uživatele – zobrazení zařízení/přihlašovací aktivita dat pro konkrétního uživatele.

- Protokoly aktivit – obsahovat záznam všechny auditované události v posledních 24 hodin, posledních 7 dnů, nebo posledních 30 dní a aktivitu změny skupiny a aktivita resetování a registraci hesla.

#### <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu zákazníků

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) se vysoce dostupnou a globální služba pro správu identit pro zákaznické aplikace s možností škálování na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Zákazníci se můžou ke všem vašim aplikacím přihlašovat přes přizpůsobitelné prostředí pomocí svých existujících účtů v sociálních sítích nebo pomocí nově vytvořených přihlašovacích údajů.

V minulosti, vývojářům aplikací, kteří chtěli [registrace a přihlašování uživatelů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) do svých aplikací by jste napsali vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure Active Directory B2C nabízí lepší způsob, jak integrovat správu identit uživatelů do aplikací pomocí zabezpečené, na standardech postavené platformy a velké škály rozšiřitelných zásad vaší organizace.

Při použití Azure Active Directory B2C uživatele můžete zaregistrovat pro vaše aplikace pomocí jejich účtů na sociálních sítích (Facebook, Google, Amazon, LinkedIn) nebo pomocí nově vytvořených přihlašovacích údajů (e-mailovou adresu a heslo, nebo uživatelské jméno a heslo).

#### <a name="device-registration"></a>Registrace zařízení

[Registrace zařízení služby Azure AD](https://docs.microsoft.com/azure/active-directory/device-management-introduction) je základem pro na základě zařízení [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup) scénáře. Když je zařízení registrováno, registrace zařízení služby Azure AD nabízí zařízení s identitou, která se používá k ověření zařízení při přihlášení uživatele. Ověřené zařízení a jeho atributy pak lze použít k vynucení zásad podmíněného přístupu u aplikací, které jsou hostovány na cloudu a v místním úložišti.

V kombinaci s [správu mobilních zařízení (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) řešení, třeba Intune, budou atributy zařízení ve službě Azure Active Directory aktualizovány o další informace o zařízení. To vám umožňuje vytvořit pravidla podmíněného přístupu, která vynucují, aby přístup měla pouze taková zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. 

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) umožňuje spravovat, řídit a monitorovat privilegované identity a přístup k prostředkům ve službě Azure AD a dalších online službách Microsoftu jako Office 365 nebo Microsoft Intune.

Někdy uživatelé budou muset provádět privilegované operace v prostředků Azure nebo Office 365 nebo jiným aplikacím SaaS. To často znamená, že organizace mají jim dát trvalé privilegovaný přístup v Azure AD. To je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůže monitorovat dostatečně co uživatelé dělají s jejich oprávněními správce. Navíc pokud dojde k ohrožení uživatelský účet s privilegovaným přístupem, tento jeden porušení zabezpečení může mít vliv jejich celkové zabezpečení cloudu. Azure AD Privileged Identity Management pomáhá řešit rizika.

Azure AD Privileged Identity Management vám umožní:

- Podívejte se, kteří jsou správci Azure AD

- Povolit na vyžádání "just in time" pro správu přístup k Microsoft Online Services, jako jsou Office 365 a Intune

- Získejte sestavy obsahující historii přístupů správce a změny v přiřazení správce

- Dostávejte upozornění týkající se přístupu k privilegovanou roli

#### <a name="identity-protection"></a>Ochrana identit

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) je služba zabezpečení, která poskytuje umožní získat přehled o rizikových událostech a potenciálních chybách zabezpečení dopadem na identity vaší organizace. Identity Protection používá funkce detekce anomálií stávající Azure Active Directory (k dispozici prostřednictvím sestav neobvyklých aktivit Azure AD) a zavádí nové typy rizikových událostí, které dokáží detekovat anomálie v reálném čase.

## <a name="secured-resource-access-in-azure"></a>Přístup k zabezpečeným prostředkům v Azure

Řízení přístupu v Azure spustí z hlediska fakturace. Vlastník účtu Azure, poslednímu [centra účtů Azure](https://account.windowsazure.com/subscriptions), je správce účtu (AA). Předplatné je kontejner pro fakturaci, ale působí také jako hranice zabezpečení: má každé předplatné služby správce (SA), můžete přidat, odebrat a upravit prostředky Azure v tomto předplatném pomocí webu Azure portal. Výchozí přidružení zabezpečení nového předplatného je AA, ale AA můžete změnit přidružení zabezpečení v centru účtů Azure.

![Přístup k zabezpečeným prostředkům v Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

Předplatná také mít přidružený k adresáři. Adresář definuje sadu uživatelů. Může jít o uživatele z pracovní nebo školní účet, který vytvoří adresář, nebo mohou být externím uživatelům (to znamená, Accounts Microsoft). Předplatná jsou přístupné pro podmnožinu těchto adresáře uživatelů, kteří mají přiřazený jako službu správce nebo Spolusprávce (CA); Jedinou výjimkou je, že kvůli starším verzím Accounts Microsoft (dřív Windows Live ID) může být přiřazen jako SA nebo certifikační Autorita není k dispozici v adresáři.

Týkající se zabezpečení společnosti byste se zaměřit na poskytuje zaměstnancům konkrétní oprávnění, které potřebují. Příliš mnoho oprávnění, mohou vystavit účet tak, aby útočníci. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) pomáhá tento problém vyřešit tím, že nabízí propracovanou správu přístupu pro Azure.

![Přístup k zabezpečeným prostředkům ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Pomocí řízení přístupu na základě role můžete povinnosti v rámci týmu oddělit a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce. Ne všichni poskytuje neomezená oprávnění v předplatném Azure nebo prostředky, můžete povolit pouze určité akce. Například pomocí RBAC nechat jednoho zaměstnance spravovat virtuální počítače v předplatném, zatímco jiné můžete spravovat databáze SQL v rámci stejného předplatného.

![Přístup k zabezpečeným prostředkům v Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Zabezpečení dat v Azure a šifrování (Ochrana)

Jeden z klíčů na ochranu dat v cloudu je připadajících na možné stavy, které mohou nastat vaše data a jaké ovládací prvky jsou k dispozici pro tento stav. Pro Azure data šifrování osvědčené postupy pro zabezpečení a doporučení se kolem stavy následující data.

- V klidovém stavu: To zahrnuje všechny informace, které objekty úložiště, kontejnery a typy, které existují staticky na fyzickém médiu, být magnetické nebo optické disku.

- V cestě: Když data přenášejí mezi komponentami, umístění nebo programy, jako například v síti přes service bus (z místních do cloudu a naopak, včetně hybridních připojení, jako je například ExpressRoute), nebo během vstupně výstupní, je myšlenky o jako za provozu.

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Pokud chcete dosáhnout šifrování v klidovém stavu, proveďte všechny následující:

Alespoň jeden z doporučených šifrování modely podrobně popsané v následující tabulce k šifrování dat podporují.

| Modely šifrování |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Šifrování serveru | Šifrování serveru | Šifrování serveru | Šifrování klienta
| Šifrování na straně serveru pomocí klíče spravované služby | Šifrování na straně serveru pomocí Customer-Managed klíčů ve službě Azure Key Vault | Šifrování na straně serveru pomocí klíčů spravovaná zákazníkem v místním prostředí |
| • Azure poskytovatelů prostředků provádět operace šifrování a dešifrování <br> • Microsoft spravuje klíče <br>• Úplné cloudové funkce | • Azure poskytovatelů prostředků provádět operace šifrování a dešifrování<br>• Zákazník řídí klíče účtů prostřednictvím Azure Key Vault<br>• Úplné cloudové funkce | • Azure poskytovatelů prostředků provádět operace šifrování a dešifrování <br>• Zákazník řídí klíče v místním prostředí <br> • Úplné cloudové funkce| • Služeb azure neuvidí dešifrovaná data <br>• Zákazníky zachovat klíče v místním (nebo v jiných zabezpečené úložiště). Klíče nejsou k dispozici ke službám Azure <br>• Snižuje cloudové funkce|

### <a name="enabling-encryption-at-rest"></a>Povolení šifrování v klidovém stavu

**Identifikujte všechny umístění úložiště dat**

K šifrování všech dat je cílem šifrování v klidovém stavu. Tím se eliminuje možnost chybí důležitá data nebo všechny trvalého umístění. Zobrazení výčtu všechna data uložená v aplikaci. 

> [!Note] 
> Nejen "application data" nebo "PII", ale všechna data týkající se na aplikace, včetně účtu metadata (předplatné mapování, informace o smlouvě identifikovatelné osobní údaje).

Zvažte, jaké úložiště k ukládání dat používáte. Příklad:

- Externí úložiště (například SQL Azure Document DB, HDInsights, Data Lake, atd.)

- Dočasné úložiště (všechny místní mezipaměti, která obsahuje data tenanta)

- Mezipaměť v paměti (možné zařadit do stránkovacího souboru.)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Využijte stávající šifrování prostřednictvím rest podpory v Azure

Pro každé úložiště, které používáte využijte stávající šifrování na webu podpory Rest.

- Azure Storage: Zobrazit [šifrování služby Azure Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: Zobrazit [transparentní šifrování dat (TDE) SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- VM & místní diskové úložiště ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Úložiště virtuálního počítače a místní disk pomocí Azure Disk Encryption tam, kde se nepodporuje:

#### <a name="iaas"></a>IaaS

Služby se virtuální počítače IaaS (Windows nebo Linuxem) by měl používat [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) pro šifrování svazků obsahující data zákazníků.

#### <a name="paas-v2"></a>PaaS v2

Služby spuštěné na PaaS v2 Service Fabric pomocí Azure disk encryption pro sadu škálování virtuálního počítače [VMSS] k šifrování svých virtuálních počítačů v2 PaaS.

#### <a name="paas-v1"></a>PaaS v1

Azure Disk Encryption aktuálně nepodporuje PaaS v1. Proto musíte použít šifrování na úrovni aplikace k šifrování trvalých dat v klidovém stavu.  To zahrnuje, ale není omezena pouze na data aplikací, dočasných souborů, protokoly a výpisy stavu systému.

Většina služeb má pokusit o využívat šifrování zprostředkovatele prostředku úložiště. Některé služby je nutné provést explicitní šifrování, například všechny trvalé materiál klíče (certifikáty, kořenový / hlavního klíče) musí být uložen ve službě Key Vault.

Pokud vyvíjíte šifrování na straně služby pomocí klíčů spravovaných zákazníkem existuje musí být způsob, jak zákazník získat klíč pro nás. Podporované a doporučený způsob, jak to udělat díky integraci s Azure Key Vaultu (AKV). V tomto případě Zákazníci můžete přidat a spravovat své klíče ve službě Azure Key Vault. Zákazník může Další informace o použití přes AKV [Začínáme se službou Key Vault](https://go.microsoft.com/fwlink/?linkid=521402).

Integrace se službou Azure Key Vault, by přidáte kód požádat o klíč z službou AZURE v případě potřeby pro dešifrování.

- Zobrazit [Azure Key Vault – krok za krokem](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) informace o tom, jak integrovat se službou AZURE.

Pokud podporujete spravovaná zákazníkem klíčů, budete muset poskytnout uživatelského prostředí pro zákazníky k určení, které služby Key Vault (nebo identifikátor URI služby Key Vault) používat.

Šifrování v klidovém stavu zahrnuje šifrování hostitele, infrastruktury a klientského data, ke ztrátě klíče z důvodu selhání systému nebo může znamenat škodlivou aktivitu, šifrovaná data je ztraceny. Proto je důležité, že má vaše šifrování v řešení Rest scénáře obnovení po havárii komplexní odolné vůči selhání systému a škodlivé aktivity.

Služby, které implementují šifrování v klidovém stavu jsou obvykle přesto náchylné k šifrovacím klíčům nebo data ponechána nešifrované podobě na jednotce hostitele (například v souboru stránky hostitelský operační systém.) Proto služby musíte zajistit, že svazek hostitele pro jejich služby se šifrují. Pro usnadnění tohoto výpočetní povolila týmu nasazení šifrování hostitele, který používá [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP a rozšíření pro službu DCM a agenta pro šifrování svazku hostitele.

Většina služeb jsou implementovány ve standardní virtuální počítače Azure. Tyto služby by měl získat [hostitele šifrování](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automaticky při výpočetní povolí ho. Pro služby spuštěné na výpočty spravované clustery hostitelů je povolené šifrování automaticky při nasazení Windows serveru 2016.

### <a name="encryption-in-transit"></a>Šifrování během přenosu

Ochrana dat při přenosu by měl být nedílnou součást vaší strategie ochrany dat. Protože data je přesun vpřed a zpět z mnoha umístění, obecné doporučení je vždy používají protokoly SSL/TLS pro výměnu dat mezi různými umístěními. V některých případech můžete chtít izolovat celý komunikační kanál mezi místní a cloudové infrastruktury pomocí virtuální privátní sítě (VPN).

Pro data přesouvaná mezi vaší místní infrastruktuře i v Azure měli byste zvážit odpovídající opatření, jako je HTTPS nebo VPN.

Pro organizace, které je potřeba zabezpečit přístup z více pracovních stanic, které jsou umístěny v místním do Azure, použijte [Azure VPN typu site-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Pro organizace, které potřebují k zabezpečení přístupu k jedné pracovní stanice, které jsou umístěny v místním do Azure, použijte [Point-to-Site VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Větší datové sady je možné přesunout nad vyhrazenou vysokorychlostní propojení WAN, jako [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Pokud se rozhodnete používat ExpressRoute, můžete taky k šifrování dat na úrovni aplikace pomocí [SSL/TLS](https://support.microsoft.com/kb/257591) nebo jiné protokoly pro zvýšení ochrany.

Pokud jsou při interakci s Azure Storage na webu Azure Portal, všechny transakce probíhat přes protokol HTTPS. [Rozhraní REST API pro Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) přes HTTPS můžete také použít k interakci s [služby Azure Storage](https://azure.microsoft.com/services/storage/) a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organizace, které se nepodařilo ochranu přenášených dat budou náchylnější pro [útoky man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [odposlouchávání](https://technet.microsoft.com/library/gg195641.aspx)a napadení relace. Tyto útoky může být prvním krokem v získání přístupu k důvěrným datům.

Další informace o možnosti Azure VPN najdete v článku [plánování a návrh pro VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Vynutit šifrování dat na úrovni souborů

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) využívá zásady šifrování, identity a autorizace, aby napomohla zabezpečení souborů a e-mailu. Azure RMS funguje napříč více zařízeními – telefony, tablety a počítače s ochranou v rámci vaší organizace i mimo vaši organizaci. Tato funkce je možné, protože Azure RMS přidá úroveň ochrany, která zůstává s daty, i když opustí hranice vaší organizace.

Při použití služby Azure RMS k ochraně souborů použijete standardní kryptografie s plnou podporou [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Při využití Azure RMS pro ochranu dat, máte jistotu, že zůstane ochrana se souborem, i v případě, že je zkopírován do úložiště, které není pod kontrolou oddělení IT, třeba do cloudového úložiště. Stejné dojde k souborů sdílených prostřednictvím e-mailu, je soubor chráněný jako příloha e-mailovou zprávu s pokyny, jak chráněnou přílohu otevřít.
Při plánování přechodu na službu Azure RMS, doporučujeme následující:

- Nainstalujte [aplikace pro sdílení obsahu RMS](https://technet.microsoft.com/library/dn339006.aspx). Tato aplikace integruje Office aplikace nainstalováním Office doplňku tak, aby uživatelé mohli snadno chránit soubory přímo.

- Nakonfigurujte aplikace a služby podporují službu Azure RMS

- Vytvoření [vlastní šablony](https://technet.microsoft.com/library/dn642472.aspx) , který odráží vaše obchodní požadavky. Příklad: šablony pro začátek tajná data, která bude použito všechny hlavní tajný klíč související s e-mailů.

Organizace, které jsou na slabé [klasifikace dat](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a ochranu souborů může být náchylnější k úniku. Bez ochrany správný soubor organizace nebude možné získat obchodní přehledy, sledovat zneužití a brání škodlivým přístupem k souborům.

> [!Note]
> Další informace o službě Azure RMS najdete v článku [Začínáme se službou Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Zabezpečení aplikace (Ochrana)
Zatímco Azure zodpovídá za zabezpečení infrastruktury a platformy, na které vaše aplikace poběží, je vaší odpovědností zajistit vaše vlastní aplikace. Jinými slovy potřebujete pro vývoj, nasazení a správu kódu aplikace a obsah bezpečným způsobem. Bez tohoto kódu aplikace nebo obsah může být zranitelný vůči hrozbám.

### <a name="web-application-firewall-waf"></a>Firewall webových aplikací (WAF)
[Firewall webových aplikací (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) je funkce [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožení zabezpečení.

Firewall webových aplikací je založený na základě [základní sady pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

Mezi běžné webové zranitelnosti, proti kterým firewall webových aplikací chrání, patří tyto:

- Ochrana před útoky prostřednictvím injektáže SQL.

- Ochrana před skriptováním mezi weby.

- Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

- Ochrana před narušením protokolu HTTP.

- Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

- Ochrana před roboty, prohledávacími moduly a skenery.

- Detekce běžných chyb v konfiguraci aplikací (tj. Apache, IIS atd.)

> [!Note]
> Podrobnější seznam pravidel a jejich ochrany najdete v následujících [základních sad pravidel](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

Azure navíc poskytuje několik funkcí snadným ovládáním zabezpečit příchozí i odchozí provoz pro vaši aplikaci. Azure pomáhá zákazníkům zabezpečit svůj kód aplikace tím, že poskytuje externě k dispozici také funkce vyhledávání pro chyby webové aplikace.

- [Nastavení ověřování Azure Active Directory pro vaši aplikaci](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Zabezpečení provozu do vaší aplikace tím, že zabezpečení transportní vrstvy (TLS/SSL) - protokol HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [Vynutit veškerý příchozí provoz přes připojení HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Povolit zabezpečení přenosu striktní (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Omezit přístup do vaší aplikace tak, že IP adresa klienta](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Omezit přístup do vaší aplikace tak, že chování klienta - žádost o četnosti a souběžnost](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Skenovat kód vaší webové aplikace pro chyby pomocí Tinfoil Security Scanning.](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurace vzájemného ověřování TLS vyžadovat klientské certifikáty pro připojení k vaší webové aplikace](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Konfigurace certifikátu klienta pro použití v aplikaci pro zabezpečené připojení k externím prostředkům](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Odebrání hlaviček standardní server, aby nástroje od otisků vaší aplikace](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Bezpečně připojte svou aplikaci k prostředkům v privátní síti pomocí sítě VPN typu Point-To-Site](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Bezpečně připojte svou aplikaci k prostředkům v privátní síti pomocí hybridních připojení](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Služba Azure App Service používá stejné Antimalwarové řešení, které používají Azure Cloud Services a Virtual Machines. Přečtěte si další informace najdete v tématu naše [Antimalwarové dokumentaci](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Zabezpečte svoji síť (Ochrana)
Microsoft Azure obsahuje robustní síťovou infrastrukturu pro podporu aplikací a požadavky na připojení služby. Připojení k síti je možné mezi prostředky umístěné v Azure, mezi místním a hostované v Azure prostředky a do a z Internetu a z Azure.

[Azure síťovou infrastrukturu](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) umožňuje zabezpečené propojení prostředků Azure mezi sebou s [virtuálních sítí (VNets)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Virtuální síť je reprezentace vaší vlastní sítě v cloudu. Virtuální síť se o logickou izolaci sítě cloudu Azure vyhrazenou pro vaše předplatné. Propojení virtuálních sítí na vaše místní sítě.

![Zabezpečte svoji síť (Ochrana)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Pokud potřebujete řízení přístupu na úrovni základní sítě (na základě IP adresy a protokoly TCP nebo UDP), pak můžete použít [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Skupina zabezpečení sítě (NSG) je základní stavových paketů filtrování brány firewall a umožňuje vám k řízení přístupu na základě [5 řazené kolekce členů](https://www.techopedia.com/definition/28190/5-tuple).

Sítě Azure podporuje schopnost přizpůsobit chování směrování síťového provozu ve virtuálních sítích Azure. Můžete to provést konfigurací [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) v Azure.

[Vynucené tunelování](https://www.petri.com/azure-forced-tunneling) virtuálních sítí je mechanismus, který můžete použít k zajištění, že vaše služby nejsou povoleny k navázání připojení k zařízení na Internetu.

Azure podporuje vyhrazená propojení WAN připojení k vaší místní sítí a Azure Virtual Network s [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Propojení mezi Azure a webu používá vyhrazené připojení, které neprochází přes veřejný Internet. Pokud vaše aplikace Azure běží v několika datových centrech, můžete použít [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pro směrování požadavků od uživatelů inteligentně mezi instancemi aplikace. Můžete také směrovat provoz do služeb není spuštěná v Azure, pokud jsou přístupné z Internetu.

## <a name="virtual-machine-security-protect"></a>Zabezpečení virtuálních počítačů (Ochrana)

[Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/) umožňuje širokou škálu výpočetních řešení pružně nasadit. Díky podpoře systémů Windows, Linux, SQL Server, Oracle, IBM, SAP a Azure BizTalk Services můžete nasadit jakoukoli úlohu a jakýkoli jazyk skoro v každém operačním systému.

S Azure, můžete použít [antimalwarový software](https://docs.microsoft.com/azure/security/azure-security-antimalware) od dodavatelů zabezpečení, jako je Microsoft, Symantec, Trend Micro a Kaspersky ochranu virtuálních počítačů před škodlivými soubory, adwarem a dalšími hrozbami.

Microsoft Antimalware pro Azure Cloud Services a Virtual Machines je funkce ochrany v reálném čase, který pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software. Antimalware od Microsoftu nabízí konfigurovatelných upozornění, když označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v Azure systémech.

[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) je škálovatelné řešení, které chrání aplikační data s nulovou kapitálovou investicí a minimálními provozní náklady. Chyby aplikací můžou poškodit vaše data a lidské omyly zase můžou způsobit chyby v aplikacích. Pomocí služby Azure Backup jsou chráněné virtuální počítače s Windows a Linux.

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) pomáhá Orchestrace replikace, převzetí služeb při selhání a obnovení úloh a aplikací tak, aby byly k dispozici ze sekundární lokality v případě, že primární lokalita ocitne mimo provoz.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Zajištění dodržování předpisů: Cloudové služby z důvodu kontrolní seznam co nejopatrněji (Ochrana)

Microsoft vyvinuté [kontrolní vypršení platnosti co Nejopatrněji Cloud Services](https://aka.ms/cloudchecklist.download) umožňující organizacím vykonávat co nejopatrněji vypršení platnosti v rámci zvažování migrace do cloudu. Poskytuje strukturu pro organizaci jakékoli velikosti a typu – soukromých podniků a organizací ve veřejném sektoru, včetně státní správy na všech úrovních a neziskové organizace – identifikovat vlastní výkon, služby, správu dat a cíle zásad správného řízení a požadavky. To umožňuje jejich porovnání nabídek různí poskytovatelé cloudových služeb, takže v konečném důsledku tvoří základ pro smlouvu o cloudových služeb.

Kontrolní seznam poskytuje rozhraní, který zarovnává klauzule pomocí klauzule se novou mezinárodní normu pro smlouvami o cloudu, ISO/IEC 19086. Tento standard nabízí ucelenou sadu informace pro organizace, aby to pomohl ostatním učinit rozhodnutí o přechodu na cloud a vytvoření plnohodnotně pro porovnání nabídek služeb cloudu.

Kontrolní seznam podporuje důkladně prověřené Přesun do cloudu, poskytuje strukturovaných pokynů a konzistentní a opakovatelné přístup pro výběr poskytovatele cloudových služeb.

Přechod na cloud již není jednoduše technologie rozhodnutí. Protože touch kontrolní seznam požadavků na každý aspekt organizace, bude sloužit k svolat všechny klíče interní-rozhodujícím, CIO a Ředitelka, jakož i právní, rizika Odborníci v oblasti správy, nákup a dodržování předpisů. Tím se zvyšuje efektivitu rozhodovacích procesů a základu rozhodnutí v zvukové posuzování, a tím snižuje pravděpodobnost, že nepředvídaných problémy k přijetí.

Kromě toho, kontrolní seznam:

- Poskytuje klíče diskusní témata pro uživatelé s rozhodovací pravomocí na začátku procesu přechod na cloud.

- Podporuje důkladné obchodní diskuse o předpisy a cíle organizace pro ochranu osobních údajů, identifikovatelné osobní údaje (PII) a zabezpečení dat ve službě.

- Pomáhá organizacím identifikovat možné problémy, které mohou ovlivnit projekt cloudové.

- Poskytuje konzistentní sadu otázek, pomocí stejných podmínek, definice, metrik a výstupy pro každého zprostředkovatele pro zjednodušení procesu porovnání nabídek z různými poskytovateli cloudových služeb.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure ověření zabezpečení infrastruktury a aplikací (zjistit)

[Azure Operational Security](https://docs.microsoft.com/azure/security/azure-operational-security) odkazuje na služby, ovládací prvky a funkce, které jsou dostupné pro uživatele k ochraně svých dat, aplikací a dalších prostředků ve službě Microsoft Azure.

![ověření zabezpečení (zjistit)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Provozní zabezpečení Azure je postavená na systém, který zahrnuje poznatky získané při různé schopnosti, které jsou jedinečné pro Microsoftu, mezi které patří Microsoft Security Development Lifecycle (SDL), programu Microsoft Security odpovědi střediska a hloubkové povědomí o zabezpečení.

### <a name="microsoft-azure-log-analytics"></a>Microsoft Azure Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je řešení pro správu IT pro hybridní cloud. Používat samostatně nebo rozšířit existující nasazení nástroje System Center Log Analytics poskytuje maximální flexibilitu a kontrolu pro cloudovou správu infrastruktury.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Pomocí služby Log Analytics můžete spravovat všechny instance v libovolném cloudu, včetně místních, Azure, AWS, Windows Server, Linux, VMware a OpenStack, s nižšími náklady než konkurenční řešení. Určená pro cloudového světa, Log Analytics nabízí nový přístup ke správě vaší organizace, která je cenově nejvýhodnější, nejrychlejší způsob, jak nové obchodní výzvy a zvládnutí nových úloh, aplikací a cloudových prostředí.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics) zajišťuje služby monitorování získáváním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Tato metoda umožňuje konsolidovat data z nejrůznějších zdrojů, takže můžete kombinovat data ze služeb Azure s vaší stávající místní prostředí. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Služba Security Center analyzuje stav zabezpečení vašich prostředků Azure, aby identifikovala potenciální ohrožení zabezpečení. Seznam doporučení vás provede procesem konfigurace potřebných kontrol.

Příklady obsahují:

- Zřizování antimalwaru, aby se pomohl identifikovat a odebrat škodlivý software

- Konfigurace skupin zabezpečení sítě a pravidla pro řízení provozu do virtuálních počítačů

- Zřizování firewallů webových aplikací, které pomáhají bránit útokům zaměřeným na vaše webové aplikace

- Nasazení chybějících aktualizací systému

- Adresování konfigurací operačního systému, které neodpovídají doporučeným standardním hodnotám

Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a řešení partnerů, jako jsou antimalwarové programy a brány firewall. Při zjištění ohrožení zabezpečení se vytvoří výstraha zabezpečení. Příklady zahrnují zjišťování následujících situací:

- Ohrožené virtuální počítače komunikující s známé škodlivé IP adresy

- Pokročilý malware zjištěný pomocí zasílání zpráv o chybách systému Windows

- Útoky hrubou silou na virtuální počítače

- Výstrahy zabezpečení z integrovaných antimalwarových programů a bran firewall

### <a name="azure-monitor"></a>Azure monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) obsahuje odkazy na informace o konkrétní typy prostředků. Nabízí vizualizace, dotazování, směrování, výstrahy, automatické škálování a automatizace na data jak z infrastruktury Azure (protokol aktivit) a jednotlivé prostředky Azure (diagnostické protokoly).

Cloudové aplikace jsou složité s řadu pohyblivých částí. Monitorování poskytuje data k zajištění, že systém zůstane vaší aplikace v provozu a spuštěná v dobrém stavu. Také pomáhá stave vypnout potenciální problémy a řešení potíží s poslední těch, které jsou.

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) navíc data monitorování můžete získat podrobný přehled o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

Auditování zabezpečení sítě je důležité pro zjištění ohrožení zabezpečení sítě a zajištění dodržování předpisů, zabezpečení IT a model dodržování zásad správného řízení. Zobrazení skupiny zabezpečení můžete získat nakonfigurovanou skupinu zabezpečení sítě a pravidel zabezpečení, jakož i platná pravidla zabezpečení. Se seznamem pravidla platila můžete určit, že porty, které jsou otevřené a ss sítě ohrožení zabezpečení.

### <a name="network-watcher"></a>Sledovací proces sítě

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového v, do a z Azure. Pro diagnostiku sítě a vizualizačních nástrojů, které jsou k dispozici pomocí služby Network Watcher pomáhají porozumět, diagnostice a získání přehledu o vaší síti v Azure. Tato služba zahrnuje zachycení paketu, další směrování, IP tok ověřit, zobrazení skupin zabezpečení, protokoly toků NSG. Scénář úrovně monitorování poskytuje komplexní přehled síťové prostředky, na rozdíl od sledování jednotlivých síťových prostředků.

### <a name="storage-analytics"></a>Analýza služby Storage

[Analýza úložiště](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) můžete ukládat metriky, včetně transakce agregované statistiky a dat o kapacitě požadavků na službu úložiště. Transakce jsou zaznamenány na úrovni operace rozhraní API i na úrovni služby úložiště, a je kapacita ohlášená na úrovni služby úložiště. Můžete například měřená data slouží k analýze využití služeb úložiště, Diagnostika potíží s požadavky na službu úložiště a zlepšit výkon aplikace, které používají službu.

### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba správu výkonu aplikací (APM) pro webové vývojáře ve více platformách. Slouží k monitorování živé webové aplikace. Automaticky zjišťuje anomálie ve výkonu. Obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé dělají s vaší aplikací. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Funguje u aplikací na široké škále platforem, jako jsou .NET, Node.js a J2EE, a to hostovaných lokálně i v cloudu. Integruje se s vaším procesem devOps a obsahuje spojovací body do různých vývojových nástrojů.

Monitoruje tyto parametry:

- **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky.

- **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.

- **Výjimky** – analýza souhrnné statistiky nebo vyberte konkrétní instance a přejít k podrobnostem trasování zásobníku a souvisejících požadavcích. Hlásí se výjimky serveru i prohlížeče.

- **Zobrazení a načítání stránek** – Tyto informace hlásí prohlížeče uživatelů.

- **Volání AJAX z webových stránek** – kurzy, doby odezvy a míra selhání.

- **Počty uživatelů a relací.**

- **Čítače výkonu** ze serverových počítačů s Windows nebo Linuxem, jako je třeba CPU, paměť a využití sítě.

- **Diagnostika hostitele** z Dockeru nebo Azure.

- **Protokoly trasování diagnostiky** z vaší aplikace – umožňují zjistit korelaci mezi požadavky a událostmi trasování.

- **Vlastní události a metriky** napíšete sami v kódu klienta nebo serveru ke sledování obchodních událostí, například prodané položky nebo Vyhrané hry.

Infrastrukturu aplikace obvykle tvoří celá řada komponent, může to být třeba virtuální počítač, účet úložiště a virtuální síť nebo webová aplikace, databáze, databázový server a služby jiných výrobců. Tyto komponenty nevidíte jako samostatné entity, ale jako související a vzájemně provázané části jedné entity. Chcete je nasadit, spravovat a monitorovat jako skupinu. [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umožňuje pracovat s prostředky ve vašem řešení jako se skupinou.

Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

**Výhody použití Resource Manageru**

Resource Manager poskytuje několik výhod:

- Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

- Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

- Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

- Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

- Můžete využít řízení přístupu pro všechny služby ve vaší skupině prostředků, protože do platformy pro správu je nativně integrováno řízení přístupu na základě role (RBAC).

- Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

- Můžete zpřehlednit fakturaci svojí organizace zobrazením nákladů na skupinu prostředků, které sdílejí stejnou značku.

> [!Note]
> Resource Manager poskytuje nový způsob nasazení a správy vašich řešení. Pokud jste použili dřívější model nasazení a chcete další informace o změnách, přečtěte si téma [vysvětlení Resource Manager a klasické nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení najdete některé z našich podrobných zabezpečení témata:

- [Auditování a protokolování](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cybercrime](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Celkové řešení a provozní zabezpečení](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Šifrování](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Správa identit a přístupu](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Zabezpečení sítě](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Řízení rizik](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
