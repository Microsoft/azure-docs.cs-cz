---
title: Přehled identity pro službu Azure Stack | Dokumentace Microsoftu
description: Další informace o systémech identity, která vám pomůže s využitím Azure stacku.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: 4ffbf552b2c35039ceadfdf6940873a22cb194aa
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306535"
---
# <a name="overview-of-identity-for-azure-stack"></a>Přehled identity pro službu Azure Stack

Azure Stack vyžaduje Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS), podporovaný službou Active Directory jako zprostředkovatele identity. Výběr zprostředkovatele je jednorázový rozhodnutí, která učiníte při prvním nasazení Azure Stack. Koncepty a podrobnosti o ověřování v tomto článku můžete vybrat mezi zprostředkovatele identity.

Azure AD nebo AD FS podle vašeho výběru se určuje podle režimu, ve kterém nasadíte Azure Stack:

- Při nasazení v režimu připojení, můžete použít buď Azure AD nebo AD FS.
- Při nasazení v odpojeném režimu, bez připojení k Internetu, je podporována pouze služba AD FS.

Další informace o možnostech, které závisí na vašem prostředí Azure Stack, najdete v následujících článcích:

- Sada nasazení Azure Stack: [Důležité informace o identitu](azure-stack-datacenter-integration.md#identity-considerations).
- Integrované systémy Azure Stack: [Integrované systémy pro nasazení, plánování rozhodnutí pro službu Azure Stack](azure-stack-deployment-decisions.md).

## <a name="common-concepts-for-identity"></a>Koncepty používané v identity

Následující části popisují běžné koncepty o zprostředkovatelů identity a jejich použití ve službě Azure Stack.

![Terminologie pro zprostředkovatele identity](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Tenanti adresáře a organizace

Adresář je kontejner, který obsahuje informace o *uživatelé*, *aplikací*, *skupiny*, a *instanční*.

Tenanta adresáře. je *organizace*, jako je například Microsoft nebo z vaší společnosti.

- Azure AD podporuje více tenantů a může podporovat více organizacemi, každá do vlastního adresáře. Pokud používáte Azure AD a máte více tenantů, můžete udělit aplikacím a uživatelům z jednoho tenanta přístup do jiných tenantů stejného adresáře.
- Služba AD FS podporuje pouze jednoho tenanta a tedy pouze jedna organizace.

### <a name="users-and-groups"></a>Uživatelé a skupiny

Uživatelské účty (identity), standardní účty, které se ověřují jednotlivce pomocí ID uživatele a heslo. Skupiny mohou obsahovat uživatele nebo jiné skupiny.

Jak vytvořit a spravovat uživatele a skupiny závisí na řešení identit, které používáte.

Ve službě Azure Stack, uživatelských účtů:

- Vytvářejí *username@domain* formátu. I když služba AD FS mapuje uživatelské účty do instance služby Active Directory, služby AD FS nepodporuje použití  *\\ \<domény >\\\<alias >* formátu.
- Můžete nastavit na použití služby Multi-Factor authentication.
- Jsou omezeny do adresáře, ve kterém se nejprve zaregistrovat, tedy adresáře své organizace.
- Je možné importovat z místních adresářů. Další informace najdete v tématu [integrace místních adresářů se službou Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Při přihlášení k portálu tenanta vaší organizace, je použít *https://portal.local.azurestack.external* adresy URL. Při přihlašování k portálu Azure Stack z jiných domén, než jaký se používá k registraci Azure Stack, použili k registraci ve službě Azure Stack název domény musí být připojeno k portálu pro adresu url. Pokud Azure Stack je zaregistrován s fabrikam.onmicrosoft.com a uživatelský účet přihlášení je například admin@contoso.com, by měla adresa url pro použití k protokolování do portálu user portal: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Uživatelé typu host

Uživatelé typu Host jsou uživatelské účty z jiných tenantů adresáře, kterým byl udělen přístup k prostředkům ve vašem adresáři. Pro podporu uživatelů typu Host, použijte Azure AD a povolit podporu pro více tenantů. Pokud je povolena podpora, můžete pozvat uživatele typu Host pro přístup k prostředkům ve vašem tenantovi adresář, který umožňuje vzájemnou spolupráci s vnějším organizacemi.

Pozvat uživatele typu Host, můžete použít operátoři cloudů a uživatelé [spolupráce B2B ve službě Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Pozvaní uživatelé získat přístup k dokumentům, prostředky a aplikace z adresáře a zachovat kontrolu nad prostředky a data. 

Jako uživatele typu Host můžete se přihlásit k adresáři tenanta jiné organizace. Uděláte to tak, připojte název adresáře vaší organizace na portálu pro adresu URL. Například pokud patří do organizace Contoso a chcete k přihlášení do adresáře společnosti Fabrikam, použijete https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Aplikace

Můžou registrovat aplikace do Azure AD nebo AD FS a potom ve své organizaci nabízet aplikace pro uživatele.

Použití patří:

- **Webová aplikace**: Mezi příklady patří Azure Resource Manageru a webu Azure portal. Volání webového rozhraní API podporují.
- **Nativní klient systému**: Mezi příklady patří prostředí Azure PowerShell, sady Visual Studio a rozhraní příkazového řádku Azure.

Aplikace podporuje dva typy tenantů:

- **Jednoho tenanta**: Podporuje uživatele a služby pouze ze stejného adresáře, ve kterém aplikace bude zaregistrovaná.

  > [!NOTE]
  > Vzhledem k tomu, že služba AD FS podporuje pouze jeden adresář, aplikace, které vytvoříte v topologii AD FS jsou, návrh aplikace s jedním tenantem.

- **Víceklientské**: Podporuje použití podle uživatele a služby z adresáře, ve kterém aplikace bude zaregistrovaná a další tenanta adresáře. Pomocí aplikace s více tenanty uživatelé z jiného tenanta adresáře (s jiným tenantem Azure AD) můžou přihlašovat do vaší aplikace. 

  Další informace o víceklientské architektury, najdete v části [povolení víceklientské architektury](azure-stack-enable-multitenancy.md).

  Další informace o vývoji aplikace s více tenanty najdete v tématu [aplikace s více tenanty](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Když si zaregistrujete aplikaci, můžete vytvořit dva objekty:

- **Objekt aplikace**: Globální reprezentace aplikace ve všech tenantech. Tento vztah 1: 1 s aplikací softwaru a existuje pouze v adresáři, ve kterém je aplikace nejprve registrována.

- **Instanční objekt**: Přihlašovací údaj, který je vytvořen pro aplikaci v adresáři, ve kterém je aplikace nejprve registrována. Instanční objekt služby se také vytvoří v adresáři každý další klient, kde se tato aplikace používá. Tento vztah může být jeden mnoho s softwarová aplikace.

Další informace o aplikaci a instanční objekty, najdete v článku [aplikace a instanční objekty v Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Instanční objekty

Instanční objekt služby je sada *pověření* pro aplikace nebo služba, která uděluje přístup k prostředkům ve službě Azure Stack. Použití instančního objektu odděluje oprávnění aplikace z oprávnění uživatele aplikace.

Instanční objekt služby se vytvoří v každého klienta, kde se aplikace používá. Instanční objekt služby vytvoří identitu pro přihlášení a přístup k prostředkům (třeba uživatelé), které jsou zabezpečené pomocí tohoto tenanta.

- Aplikace jedním tenantem má pouze jeden objekt služby, v adresáři, ve kterém se nejdřív vytvoří. Tento instanční objekt je vytvořen a souhlasí na používá při registraci aplikace.
- Víceklientské webová aplikace nebo rozhraní API má hlavní název služby, která je vytvořená v rámci každého tenanta, kde souhlas uživatele z tohoto tenanta se používání aplikace.

Přihlašovací údaje pro instanční objekty můžou být buď klíč, který je generován prostřednictvím webu Azure portal nebo certifikát. Použití certifikátu je vhodný pro automatizaci, protože certifikáty se považuje za bezpečnější než klíče. 

> [!NOTE]
> Když používáte službu AD FS pomocí služby Azure Stack, můžete vytvořit pouze správce instančních objektů. Instanční objekty se službou AD FS, vyžadují certifikáty a jsou vytvořeny pomocí privilegovaných koncového bodu (období). Další informace najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](azure-stack-create-service-principals.md).

Další informace o instančních objektů pro službu Azure Stack, najdete v článku [vytvoření instančních objektů](azure-stack-create-service-principals.md).

### <a name="services"></a>Služby

Služby ve službě Azure Stack, které pracují s poskytovatelem identity jsou registrovány jako aplikace pomocí zprostředkovatele identity. Podobně jako aplikace umožňuje registraci služby k ověření pomocí systému identit.

Všechny služby Azure pomocí [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokoly a [webových tokenů JSON](/azure/active-directory/develop/active-directory-token-and-claims) vytvořit svoji identitu. Protože Azure AD a AD FS používají protokoly konzistentně, můžete použít [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) pro ověření v místním nebo do Azure (v případě propojené). Ověřování adal můžete také použít nástroje, jako je Azure PowerShell a rozhraní příkazového řádku Azure pro správu cloudu a místních prostředků.

### <a name="identities-and-your-identity-system"></a>Identity a systému identit

Identit pro službu Azure Stack obsahují uživatelské účty, skupiny a instanční objekty.

Při instalaci Azure Stack několik integrovaných aplikací a služeb automaticky registrovat pomocí zprostředkovatele identity v tenantu Active directory. Některé služby, které registrují se používají pro správu. Další služby jsou k dispozici pro uživatele. Výchozí registrace poskytují základní služby identity, které mohou komunikovat mezi sebou i s identitami, které přidáte později.

Pokud jste nastavili Azure AD s více tenantů, některé aplikace se rozšíří do nového adresáře.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

### <a name="authentication-by-applications-and-users"></a>Ověřování aplikace a uživatelé

![Identity mezi vrstvami služby Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

Pro aplikace a uživatele je popsán čtyři vrstvy architektury Azure Stack. Interakce mezi jednotlivými tyto vrstvy můžete použít různé typy ověřování.

|Vrstva    |Ověřování mezi vrstvami  |
|---------|---------|
|Nástroje a klienty, jako je například portál pro správu     | Do prostředků ve službě Azure Stack, upravit nebo přistoupit k nástrojům a klienti používat [webového tokenu JSON](/azure/active-directory/develop/active-directory-token-and-claims) volání do Azure Resource Manageru. <br>Azure Resource Manageru JSON Web Token ověří a prohlédne *deklarace identity* v vydaný token odhadnout úroveň autorizace uživatele nebo instanční objekt služby má ve službě Azure Stack. |
|Azure Resource Manageru a jeho základní služby     |Azure Resource Manageru komunikuje s poskytovatele prostředků pro přenos komunikace od uživatelů. <br> Přenese použití *přímé dnešní* volání nebo *deklarativní* volá prostřednictvím [šablon Azure Resource Manageru](/azure/azure-stack/user/azure-stack-arm-templates).|
|Poskytovatelé prostředků     |Volání, které jsou předány do poskytovatele prostředků jsou zabezpečené pomocí ověřování prostřednictvím certifikátu. <br>Azure Resource Manageru a poskytovatele prostředků a zůstat v komunikaci přes rozhraní API. Pro každé volání, které se získaly z Azure Resource Manageru poskytovatel prostředků ověří volání pomocí tohoto certifikátu.|
|Infrastrukturu a obchodní logiky     |Poskytovatelé prostředků komunikovat s obchodní logiky a infrastruktury s využitím režimu ověřování podle vlastní volby. Výchozí poskytovatele prostředků, které se dodávají pomocí služby Azure Stack pomocí ověřování Windows pro zabezpečení této komunikace.|

![Informace potřebné pro ověřování](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Ověřování do Azure Resource Manageru

Ověřování pomocí zprostředkovatele identity a přijímat webového tokenu JSON, musíte mít následující informace:

1. **Adresa URL pro systém identit (autorita)**: Adresa URL, na které se dá kontaktovat zprostředkovatele identity. Příklad: *https://login.windows.net*.
2. **Identifikátor URI ID aplikace pro Azure Resource Manageru**: Jedinečný identifikátor pro Azure Resource Manageru, který je zaregistrovaný u vašeho zprostředkovatele identity. Je také jedinečná pro každou instalaci Azure Stack.
3. **přihlašovací údaje**: Přihlašovací údaje, které použijete k ověřování pomocí zprostředkovatele identity.
4. **Adresa URL pro Azure Resource Manageru**: Adresa URL je umístění služby Azure Resource Manageru. Například *https://management.azure.com* nebo *https://management.local.azurestack.external*.

Pokud objekt zabezpečení (klienta, aplikace nebo uživatele) odešle požadavek na ověření pro přístup k prostředku, musí zahrnovat požadavek:

- Přihlašovací údaje daného objektu zabezpečení.
- Identifikátor URI pro ID prostředku, který chce, aby se objekt zabezpečení pro přístup k aplikaci.

Přihlašovací údaje jsou ověřeny poskytovatelem identity. Zprostředkovatel identity také ověří, že aplikace identifikátor URI ID je pro zaregistrovanou aplikaci a, objekt zabezpečení má správná oprávnění k získání tokenu pro daný prostředek. Pokud je požadavek platný, je povoleno webového tokenu JSON.

Token, který pak je nutné předat v hlavičce požadavku na Azure Resource Manager. Azure Resource Manageru nepodporuje následující v žádné konkrétní pořadí:

- Ověřuje *vystavitele* potvrďte, že token pochází od poskytovatele správnou identitu deklarací identity (iss).
- Ověřuje *cílovou skupinu* deklarace identity (aud) pro potvrzení, zda byl token vydán pro Azure Resource Manageru.
- Ověřuje, že webového tokenu JSON je podepsaný certifikátem, který je nakonfigurovaný pomocí OpenID se označuje do Azure Resource Manageru.
- Zkontrolujte *vystaveno* (iat) a *vypršení platnosti* (exp) deklarace identity pro potvrzení, že token, který je aktivní a může být přijata.

Po dokončení všech ověření pomocí Azure Resource Manageru *námitky* (oid) a *skupiny* deklarací identity, aby seznam prostředků, které můžete přístup k objektu zabezpečení.

![Diagram průběhu výměny tokenů protokolu](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Po nasazení není potřeba oprávnění globálního správce Azure Active Directory. Některé operace však může vyžadovat přihlašovací údaje globálního správce. Například skript instalační program zprostředkovatele prostředků nebo nová funkce vyžaduje oprávnění bylo uděleno. Můžete buď dočasně znovu vytvořit oprávnění účtu globálního správce nebo použijte samostatné globální správce účtu, který je vlastníkem *výchozí předplatné poskytovatele*.

### <a name="use-role-based-access-control"></a>Použití řízení přístupu na základě rolí

Na základě rolí řízení přístupu (RBAC) ve službě Azure Stack je konzistentní s implementací v Microsoft Azure. Správa přístupu k prostředkům přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím.
Informace o tom, jak pomocí RBAC pro Azure Stack, najdete v následujících článcích:

- [Začínáme s řízením přístupu na základě rolí na portálu Azure portal](/azure/role-based-access-control/overview).
- [Použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal).
- [Vytvoření vlastních rolí pro řízení přístupu](/azure/role-based-access-control/custom-roles).
- [Správa řízení přístupu na základě rolí](azure-stack-manage-permissions.md) ve službě Azure Stack.

### <a name="authenticate-with-azure-powershell"></a>Ověřování pomocí Azure Powershellu

Podrobnosti o použití Azure Powershellu k ověření pomocí služby Azure Stack najdete v [konfigurace uživatele služby Azure Stack Powershellu prostředí](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Ověřování pomocí Azure CLI

Informace o použití Azure Powershellu k ověření pomocí služby Azure Stack najdete v tématu [instalace a konfigurace rozhraní příkazového řádku Azure pro použití s Azure Stackem](/azure/azure-stack/user/azure-stack-connect-cli).

## <a name="next-steps"></a>Další postup

- [Architektura identity](azure-stack-identity-architecture.md)
- [Integrace datových center - identity](azure-stack-integrate-identity.md)
