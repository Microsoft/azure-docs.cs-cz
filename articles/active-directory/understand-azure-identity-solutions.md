---
title: Pochopení identit Azure | Microsoft Docs
description: Získáte základní znalosti o podmínky řešení identity Microsoft Azure, koncepty a doporučení pro vám umožní provádět rozhodnutí nejlepší identity zásad správného řízení pro vaši organizaci.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: ''
ms.service: azure-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 7bdd2138c861e9fd335b1c579c6ab80400777035
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714795"
---
# <a name="understand-azure-identity-solutions"></a>Princip řešení identit Azure
Microsoft Azure Active Directory (Azure AD) je cloudové řešení pro správu identit a přístupu, které poskytuje adresářové služby, správu identit a správu přístupu k aplikacím. Azure AD rychle [umožňuje jednotné přihlašování (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) na hodnotu 1 000 je předem integrovaných aplikací komerční a vlastní v [galerii aplikací Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/). Mnoho z těchto aplikací, které pravděpodobně již používáte například Office 365, Salesforce.com, pole, ServiceNow a během pracovního dne.

Jeden adresář Azure AD se automaticky přidruží předplatné Azure, když je vytvořeno. Jako identity služby v Azure Azure AD poté poskytuje všechny funkce řízení přístupu a správu identit pro cloudové prostředky. Tyto prostředky mohou zahrnovat uživatele, aplikace a skupiny pro jednoho klienta (organizace), jak je znázorněno v následujícím diagramu:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure nabízí několik způsobů, jak využívat identity jako služby (IDaaS) s různými úrovněmi složitost tak, aby splňovalo potřeby vaší jednotlivé organizace. Zbývající část tohoto článku vám pomůže pochopit základní identit Azure terminologie a koncepty, jakož i doporučení pro vás nejlepší volbu z dostupných možností.

## <a name="terms-to-know"></a>Podmínky vědět

Než v rámci řešení identit Azure, můžete rozhodnout pro vaši organizaci, je třeba základní znalosti podmínky pro běžně používané při posuzování služby Azure identity.

|Termín vědět| Popis|
|-----|-----|
|Předplatné Azure |Odběry se používají k platbě za cloudové služby Azure a jsou obvykle propojit s platební karty. Může mít několik odběrů, ale může být složité ke sdílení prostředků mezi předplatnými.|
|Klientovi Azure | Klient služby Azure AD je typický pro potřeby jedné organizace. Je vyhrazené důvěryhodné instanci Azure AD, která se automaticky vytvoří při organizace zaregistruje k odběru služby Microsoft cloudu, například Azure, Intune nebo Office 365. Klienti může získat přístup ke službám v vyhrazené prostředí (jednoho klienta) nebo v prostředí sdílené s dalšími organizacemi, (víceklientská).|
|Adresář Azure AD | Každý klient Azure má vyhrazený, důvěryhodné Azure AD adresář, který obsahuje uživatele, skupiny a aplikace klienta. Slouží k provádění identity a přístup k funkce správy pro klientské prostředky. Protože jedinečný adresář Azure AD je automaticky zřizovat představují organizaci při registraci v cloudové službě Microsoftu jako Azure, Microsoft Intune nebo Office 365, uvidíte někdy podmínky *klienta*,  *Azure AD*, a *adresář Azure AD* zaměnitelné. |
|Vlastní doména | Při první registraci k odběru služby Microsoft cloudu, vašeho klienta (organizace) používá *. onmicrosoft.com* název domény. Avšak většina organizací má jeden nebo více názvů domén, které se používají k obchodní a této koncoví uživatelé používat pro přístup k prostředkům společnosti. Do služby Azure AD můžete přidat vlastní název domény, tak, že je název domény pro uživatele, jako například *alice@contoso.com* místo *alice@contoso.onmicrosoft.com*. |
|Účet Azure AD | Jedná se o identity, které jsou vytvořené pomocí služby Azure AD nebo jiné cloudové službě Microsoftu, jako je například Office 365. Jsou uložené ve službě Azure AD a dostupný pro všechny organizace předplatných cloudové služby. |
|Správce předplatného Azure| Správce účtu je osoba, která registraci aplikace nebo kód zakoupili předplatné Azure. Mohou používat [centra účtů](https://account.azure.com/Subscriptions) Pokud chcete provádět různé úlohy správy, jako jsou vytvářet odběry, zrušit předplatné, změňte fakturace předplatného nebo změnit správce služeb. |
|Globální správce Azure AD | Azure AD globální Správci mají úplný přístup ke všem funkcím pro správu Azure AD. Osoba, která se zaregistruje k odběru služby Microsoft cloudu automaticky stane globálním správcem ve výchozím nastavení. Může mít více než jeden globální správce, ale jenom globální správci můžete přiřadit některé z [další role správců](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) uživatelům. |
|Účet Microsoft | Účty Microsoft (vytvořené vámi pro osobní použití) poskytují přístup k příjemce orientované produkty společnosti Microsoft a cloudovým službám, jako je Outlook (Hotmail), OneDrive, Xbox LIVE nebo Office 365. Tyto identity jsou vytvořeny a uloženy v sadě Microsoft příjemce identitu účtu společnosti Microsoft.|
|Pracovní nebo školní účty | Pracovní nebo školní účty (vystavený správce pro firmy nebo academic použití) zajišťují přístup k podnikové firemní úrovni cloudových služeb Microsoftu, jako je například Azure, Intune nebo Office 365.|


## <a name="concepts-to-understand"></a>Koncepty

Nyní když znáte základní identit Azure podmínky, musí se další informace o tyto koncepty Azure identity, které vám pomůžou provést informované Azure identity služby rozhodnutí.

|Koncept pochopit |Popis|
|-----|-----|
|[Jak je předplatné Azure propojeno se službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Každé předplatné služby Azure má vztah důvěryhodnosti s adresář služby Azure AD k ověřování uživatelů, služeb a zařízení. *Několik předplatných může důvěřovat stejného adresáře Azure AD, ale předplatné bude důvěřovat pouze jeden adresář Azure AD*. Tento vztah důvěryhodnosti se liší od vztahu, který má předplatné s další prostředky Azure (weby, databáze a tak dále), které jsou spíše podřízené prostředky předplatného. Pokud platnost předplatného vyprší, poté přístup k prostředkům, které jsou přidružené k předplatnému než Azure AD také se zastaví. Adresář Azure AD ale zůstává v Azure, tak, aby můžete přidružit jiné předplatné adresáře a pokračovat ke správě prostředků klienta.|
|[Jak Azure AD funguje licencování](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Pokud jste je zakoupili nebo aktivovat Enterprise Mobility Suite, Azure AD Premium nebo Azure AD Basic, adresáře je aktualizován předplatného, včetně jeho platnosti a předplacenou licence. Jakmile bude předplatné aktivní, můžete službu spravuje globální správce Azure AD a používá licencovaní uživatelé. Informace o vašem předplatném, včetně počtu licencí, přiřazené nebo k dispozici, je k dispozici na webu Azure portal z **Azure Active Directory** > **licence** okno. Toto je nejlepší místo k spravovat vaše přiřazení licence.|
|[Řízení přístupu na základě rolí na portálu Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)|Azure na základě rolí řízení přístupu (RBAC) pomáhá zajistit přesnou správu přístupu k prostředkům Azure. Příliš mnoho oprávnění můžete vystavit a účet útočníci. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. Pomocí RBAC, můžete udělit zaměstnanci přesný oprávnění potřebují na základě tři základní rolí, které platí pro všechny skupiny zdrojů: vlastník, Přispěvatel, čtečky. Můžete také vytvořit až 2 000 vlastní [vlastní role RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) ke splnění svých konkrétních potřeb. |
|[Hybridní identita](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Hybridní identita se dosáhne integrací vaší místní Windows Server Active Directory (AD DS) s Azure AD pomocí [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). To umožňuje poskytovat společnou identitu pro vaše uživatele pro Office 365, Azure a místní aplikace nebo aplikace SaaS integrované s Azure AD. S hybridní identitou efektivně rozšířit vaše místní prostředí do cloudu pro identit a přístupu.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Rozdíl mezi Windows Server AD DS a Azure AD
Azure Active Directory (Azure AD) a v místní službě Active Directory (Active Directory Domain Services nebo služby AD DS) jsou systémy, které ukládají data adresáře a spravovat komunikaci mezi uživateli a prostředky, včetně přihlášení uživatele, ověřování, a prohledávání adresáře.

Pokud jste již obeznámeni s místními systému Windows Server Active Directory Domain Services (AD DS), poprvé v systému Windows 2000 Server pak pravděpodobně pochopit základní koncepci služby identity. Také je ale důležité si uvědomit, že Azure AD není právě řadiče domény v cloudu. Jedná se o zcela nový způsob poskytování identity jako služby (IDaaS) v Azure, který vyžaduje zcela nový způsob myslím plně zapojení možnosti založené na cloudu a vaši organizaci chránit před moderními hrozbami. 

Služba AD DS je role serveru v systému Windows Server, což znamená, že můžou být nasazené na fyzický nebo virtuální počítače. Je hierarchická struktura podle X.500. DNS se používá pro nalezením objektů, může být zpracoval pomocí protokolu LDAP, a hlavně použije Kerberos pro ověřování. Služby Active Directory umožňuje organizačních jednotek (OU) a objekty zásad skupiny (GPO) kromě připojení počítače k doméně a vytvoří se vztahy důvěryhodnosti mezi doménami.

IT chrání jejich zabezpečení hraniční let pomocí služby AD DS, ale moderní, bez hraniční podniky podpora potřebám identity pro zaměstnance, Zákazníci a partneři vyžadují nové rovině řízení. Azure AD je touto rovinou ovládací prvek identity. Zabezpečení překročil podnikové brány firewall do cloudu, kde Azure AD chrání firemním prostředkům a přístup tím, že poskytuje jeden společnou identitu pro uživatele (místně nebo v cloudu). To dává uživatelům flexibilní bezpečný přístup k aplikacím, které potřebují ke své pracovní úkoly z takřka všech zařízení. Ovládací prvky ochrany bezproblémové dat založených na riziko, zálohován možnosti strojové učení a podrobné sestavy, jsou také za předpokladu, že IT musí zachovat data společnosti zabezpečení.

Azure AD je více zákazníků veřejné adresářová služba, což znamená, že v rámci Azure AD můžete vytvořit klienta pro cloud servery a aplikace, jako je Office 365. Uživatelé a skupiny se vytvoří v ploché struktuře bez organizační jednotky nebo objekty zásad skupiny. Ověřování se provádí přes protokoly, například SAML, WS-Federation a OAuth. Je možné zadat dotaz Azure AD, ale místo použití LDAP musí používat rozhraní REST API volat AD Graph API. Tyto všechny fungovat přes protokol HTTP a HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Rozšířit možnosti správy a zabezpečení Office 365
Už používá Office 365? Vaše digitální transformace lze urychlit tím, že rozšíří integrované funkce Office 365 s Azure AD zabezpečit všechny prostředky, povolení zabezpečeného produktivitu pro všechny zaměstnance. Pokud používáte Azure AD, kromě funkce Office 365, můžete zabezpečit portfolia celý aplikací se jednu identitu, která umožňuje jednotné přihlašování pro všechny aplikace. Můžete rozšířit vaše možnosti podmíněného přístupu na základě nejen na umístění stavu, ale uživatel, zařízení, aplikace a také riziko. Vícefaktorové ověřování (MFA) možnosti poskytují i další ochranu v případě potřeby. Můžete získat další dohledu uživatelská oprávnění a poskytovat přístup pro správu na vyžádání, za běhu. Uživatelé se zvýšit produktivitu a vytvořit méně lístků technické podpory, díky schopnosti samoobslužné služby, které poskytuje Azure AD jako resetovat zapomenutá hesla, požadavky na přístup aplikace a vytváření a Správa skupin.

> [!TIP]
> Chcete další informace o používání správy identit Azure AD s Office 365? [Získat elektronickou knihu](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Řešeních identity v Microsoft Azure

Microsoft Azure nabízí několik způsobů, jak spravovat identity uživatelů, zda jsou zachována plně místní, jenom v cloudu, nebo i někde v mezi. Tyto možnosti patří: samoobslužné AD DS (DIY) v Azure, Azure Active Directory (Azure AD), hybridní identita a Azure AD Domain Services.

### <a name="do-it-yourself-diy-ad-ds"></a>Samoobslužné (DIY) AD DS
Pro společnosti, které potřebují jenom malé nároky v cloudu **samoobslužné (DIY) služby AD DS** v Azure lze použít. Tato volba podporuje mnoho scénářů služby Windows Server AD DS, které jsou vhodné pro nasazení jako virtuální počítače (VM) v Azure. Například můžete vytvořit virtuální počítač Azure jako řadič domény spuštěný ve vzdálených datovém centru, která je připojena k vzdálené sítě. Odtud bude virtuální počítač může podporovat požadavky na ověření vzdálených uživatelů a zlepšit výkon ověřování. Tato možnost je také vhodné jako náhrada relativně nízkonákladové do lokalit obnovení po havárii jinak nákladná hostováním malý počet řadičů domény a jedné virtuální sítě v Azure. Nakonec budete muset nasadit aplikaci v Azure, například SharePoint, které vyžaduje Windows Server AD DS, ale nemá žádné závislosti na místní sítě nebo podnikové Windows Server Active Directory. V takovém případě můžete nasadit izolované doménové struktuře v Azure, aby splňoval požadavky na serverové farmě SharePoint. Je také podporována pro nasazení síťových aplikací, které vyžadují připojení k místní síti a místní služby Active Directory.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
**Azure AD samostatné** je plně cloudových identit a přístupu správy jako řešení služby (IDaaS). Azure AD poskytuje výkonnou sadu funkcí pro správu uživatelů a skupin. Pomáhá zabezpečit přístup k místním a cloudovým aplikacím, včetně webových služeb Microsoftu, třeba k Office 365, a řadě dalších aplikací SaaS (software jako služba), které neposkytuje Microsoft. Azure AD se dodává v tři edicích: volné, základní a Premium. Azure AD zvyšuje efektivitu organizace a rozšiřuje zabezpečení nad rámec hraniční bránu firewall, aby nové rovině řízení chráněné pomocí Azure machine learning a další pokročilé funkce zabezpečení.

### <a name="hybrid-identity"></a>Hybridní identita
Místo výběr mezi místními a řešení cloudových identit, mnoho dopředného přemýšlení ředitelé informačních technologií a podnikům, které jste začali očekávání dlouhodobý směr své společnosti, jsou rozšíření svých místních adresářů do cloudu prostřednictvím **hybridní identita** řešení. S hybridní identitou získat identitu skutečně globální a řešení pro správu přístupu, která poskytuje zabezpečené a výkonné přístup uživatelům aplikace je potřeba udělat svou práci.

> [!TIP]
> Další informace o tom, jak ředitelé informačních technologií provedli Azure Active Directory centrální součástí strategie jejich IT, stáhněte si [ředitel IT pro Průvodce služby Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services
**Azure AD Domain Services** nabízí možnost cloudové služby AD DS pro prosté řízení konfigurace virtuálního počítače Azure a způsob, jak splňují požadavky na identity místní pro sítě vývoj a testování aplikací používat. Azure AD Domain Services není určena k navýšení a posunutí místní infrastrukturu služby AD DS na virtuálních počítačích Azure spravované službou Azure AD Domain Services. Místo toho virtuálních počítačů Azure ve spravovaných domén slouží pro podporu vývoj, testování a přesun místní aplikace, které vyžadují metody ověřování služby AD DS do cloudu.

## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení

Tady jsou některé běžné scénáře přístupu a identit a doporučení, které může být nejvhodnější pro jednotlivé možnost identity.

|Scénář identity| Doporučení|
|-----|-----|
|Moje organizace udělal velkých investic v místním systému Windows Server Active Directory, ale chceme rozšířit identit do cloudu.| Nejčastěji používané řešení identit Azure je [hybridní identita](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Pokud jste již provedli investice na místní služby AD DS, můžete snadno rozšířit identity v cloudu pomocí Azure AD Connect.|
|Firmy se narodila v cloudu a žádné investice máme v řešení identity na místě.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je nejlepší volbou pro firmy jenom pro cloud bez místní investice.|
|Potřebuji lightweight konfigurace virtuálního počítače Azure a řízení ke splnění místní požadavky identity pro vývoj aplikací a testování.|[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) je vhodný, pokud potřebujete pro prosté řízení konfigurace virtuálního počítače Azure pomocí služby AD DS nebo se zaměřujete na vývoj nebo migrovat starší verze, podporou directory místní aplikace do cloudu.|  
|Je nutné podporovat několik virtuálních počítačů v Azure, ale Moje společnost je stále výraznou investovali v místní službě Active Directory (AD DS).|Použít [DIY služby AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) používat virtuální počítače Azure, když potřebujete podporovat několik virtuálních počítačů a máte velké investice do služby AD DS na místě. |

## <a name="where-can-i-learn-more"></a>Kde můžete další informace?
Jsme spousta skvělé prostředky online můžete zjistit všechno o Azure AD. Tady je seznam článků skvělý začátek:

* [Povolení adresáře pro správu hybridní službou Azure AD Connect](active-directory-aadconnect.md)
* [Další zabezpečení pro někdy připojené world](authentication/multi-factor-authentication.md)
* [Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Začínáme s generováním sestav Azure AD](active-directory-reporting-getting-started.md)
* [Spravovat hesla k odkudkoli.](active-directory-passwords-update-your-own-password.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](manage-apps/application-proxy.md)
* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)
* [Co je licencování služby Microsoft Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Jak může zjišťovat nedovolené cloudové aplikace, které se používají v rámci Moje organizace](manage-apps/cloud-app-discovery.md)

## <a name="next-steps"></a>Další postup

Teď, když znáte konceptech Azure identity a dostupné možnosti, můžete začít implementace možnosti, kterou jste zvolili v následujících zdrojích informací:

[Další informace o Azure hybridní řešení identit](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[Další informace v prostředí Azure testování konceptu](https://aka.ms/aad-poc)

[Nasazení služby Azure AD v produkčním prostředí](https://aka.ms/aad-onboard)
