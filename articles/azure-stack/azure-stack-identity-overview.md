---
title: Přehled identity pro Azure zásobníku | Microsoft Docs
description: Další informace o systémů identit, které můžete použít s Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 607c7938a789b3504a425057645b291bd4c8235b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31399027"
---
# <a name="overview-of-identity-for-azure-stack"></a>Přehled identity pro Azure zásobníku

Azure zásobníku vyžaduje Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS), založenou na službě Active Directory jako zprostředkovatele identity. Výběr zprostředkovatele je jednorázové rozhodnutí, které je nutné provést při prvním nasazení Azure zásobníku. Koncepty a podrobnosti o ověřování v tomto článku můžete zvolit poskytovatelů identit. 

Režim, ve které nasazujete zásobník Azure mohou být určena zvoleného Azure AD ani AD FS: 
- Při nasazení v režimu připojení, můžete použít buď Azure AD nebo služby AD FS. 
- Při nasazení v odpojeném režimu, bez připojení k Internetu, je podporována pouze služba AD FS.

Další informace o možnostech, které závisí na vašem prostředí zásobníku Azure, najdete v následujících článcích:
- Azure kit nasazení zásobník: [Identity aspekty](azure-stack-datacenter-integration.md#identity-considerations).
- Azure zásobníku integrované systémy: [plánování rozhodnutí pro Azure zásobníku nasazení integrované systémy](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Běžné koncepty pro identitu
Další části popisují běžné koncepty o zprostředkovatelů identity a jejich použití v Azure zásobníku.

![Terminologie pro zprostředkovatele identity](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Directory klientů a o organizaci
Adresář je kontejner, který obsahuje informace o *uživatelé*, *aplikace*, *skupiny*, a *služby objekty*.
 
Adresář klienta je *organizace*, jako je například Microsoft nebo vaše vlastní společnost. 
- Azure AD podporuje více klientů a může podporovat víc organizacím, každou v jeho vlastní adresáře. Pokud používáte Azure AD a máte víc klientů, můžete udělit pro aplikace a uživatele z jedné klientovi přístup do jiných klientů stejný adresář.
- Služba AD FS podporuje pouze jednoho klienta a tedy pouze jedné organizace. 

### <a name="users-and-groups"></a>Uživatelé a skupiny
Uživatelské účty (identity) jsou standardní účty, které ověřují jednotlivce pomocí ID uživatele a heslo. Skupiny mohou obsahovat uživatele nebo jiné skupiny. 

Jak vytvořit a spravovat uživatele a skupiny závisí na řešení identit, které používáte. 

V zásobníku Azure, uživatelské účty: 
- Jsou vytvořené v *username@domain* formátu. I když služby AD FS mapuje uživatelské účty na instanci služby Active Directory, služby AD FS nepodporuje použití  *\<domény >\<alias >* formátu. 
- Můžete nastavit na používání služby Multi-Factor authentication. 
- Jsou omezeny na adresář, kde se nejprve zaregistrovat, což je adresáře své organizace.
- Je možné importovat z místních adresářů. Další informace najdete v tématu [integraci místních adresářů se službou Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect). 

Při přihlášení na portál klienta vaší organizace, můžete použít *https://portal.local.azurestack.external* adresy URL. 

### <a name="guest-users"></a>Uživatelé typu host
Uživatele typu Host jsou uživatelské účty od ostatních klientů adresáře, kterým byl udělen přístup k prostředkům ve vašem adresáři. Pro podporu uživatelů typu Host, použít Azure AD a povolit podporu pro více klientů. Pokud je zapnutá podpora, můžete pozvat uživatele typu Host pro přístup k prostředkům ve vašem klientu adresář, který zase umožňuje vzájemnou spolupráci s mimo organizacemi. 

Pozvaným uživatelům hosta operátorům cloudu a uživatelé můžou používat [spolupráce Azure AD B2B](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Pozvané uživatele získat přístup k dokumentům, prostředky a aplikace z adresáře a zachovat kontrolu nad vlastní prostředkům a datům. 

Jako uživatel guest můžete se přihlásit k jiné organizaci directory klienta. Uděláte to tak připojit název adresáře dané organizace na portál pro adresu URL. Pokud patří organizaci Contoso a chcete k přihlášení do adresáře společnosti Fabrikam, můžete použít například https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Aplikace
Můžete zaregistrovat aplikací do Azure AD ani AD FS a pak nabízejí aplikace pro uživatele ve vaší organizaci. 

Použití patří:
- **Webovou aplikaci**: Příklady portál Azure a Azure Resource Manager. Volání webového rozhraní API podporují. 
- **Nativní klient**: Příklady prostředí Azure PowerShell, sady Visual Studio a rozhraní příkazového řádku Azure.

Aplikace podporují dva typy klientů: 
- **Jednoho klienta**: podporuje uživatele a služby pouze ze stejného adresáře, kde je registrovaná aplikace. 

  > [!NOTE]    
  > Vzhledem k tomu, že služba AD FS podporuje pouze jeden adresář, aplikací, které vytvoříte v topologii služby AD FS se standardně jednoho klienta aplikace.

- **Víceklientské**: podporuje použití uživatele a služby z adresáře, kde je registrovaná aplikace a další klienta adresáře. S více klienty aplikace uživatelé jiného klienta adresáře (jiného klienta Azure AD) může přihlásit do vaší aplikace. 

  Další informace o víceklientské najdete v tématu [povolit víceklientský](azure-stack-enable-multitenancy.md). 

  Další informace o vývoji víceklientské aplikace najdete v tématu [víceklientské aplikace](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Když si zaregistrujete aplikaci, můžete vytvořit dva objekty:

- **Objekt aplikace**: globální reprezentace aplikace přes všechny klienty. Tento vztah je 1: 1 s aplikací softwaru a existuje pouze v adresáři, kde je aplikaci nejprve zaregistrovat.

- **Objekt zabezpečení služby**: pověření, které se vytvoří pro aplikaci v adresáři, kde je aplikaci nejprve zaregistrovat. Hlavní název služby je vytvořen také v adresáři každý další klient, kde se používá tuto aplikaci. Tento vztah může být jeden mnoho s aplikací softwaru. 

Další informace o aplikaci a hlavní objekty služby najdete v tématu [aplikace a služby hlavní objekty ve službě Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Objekty služby 
Hlavní název služby je sada *pověření* pro aplikace nebo služba, která uděluje přístup k prostředkům v Azure zásobníku. Použití objektu služby odděluje oprávnění aplikací z oprávnění uživatele aplikace.

Objekt služby se vytvoří v každém klientovi, kde se používá aplikace. Objekt služby vytváří identity pro přihlašování a přístupu k prostředkům (třeba uživatelé), které jsou zabezpečeny tohoto klienta. 

- Single klientské aplikace má pouze jeden objekt služby, v adresáři, kde je prvním vytvoření. Tento objekt zabezpečení služby se vytvoří a souhlasí na používá při registraci aplikace. 
- Víceklientské webovou aplikaci nebo API má hlavní název služby, který je vytvořen v každého klienta, kde uživatele z tohoto tenanta souhlasí s použitím aplikace. 

Přihlašovací údaje pro objekty služby může být buď klíč, který se vygeneruje prostřednictvím portálu Azure nebo certifikát. Použití certifikátu je vhodné pro automatizaci, protože certifikáty jsou považovány za bezpečnější než klíče. 


> [!NOTE]    
> Při použití služby AD FS se zásobníkem Azure, můžete vytvořit pouze správce objekty služby. Se službou AD FS objekty služby vyžadují certifikáty a vytvoří se prostřednictvím privilegované koncového bodu (období). Další informace najdete v tématu [poskytnout přístup aplikace k Azure zásobníku](azure-stack-create-service-principals.md).

Další informace o objektech služby pro Azure zásobníku, najdete v části [vytvořit objekty služby](azure-stack-create-service-principals.md).


### <a name="services"></a>Služby
Služby v zásobníku Azure, které spolupracují s zprostředkovatele identity je zaregistrovaný jako aplikace pomocí zprostředkovatele identity. Podobně jako aplikace umožňuje registraci služby k ověřování pro systém identit. 

Použití všech služeb Azure [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokoly a [webových tokenů JSON](/azure/active-directory/develop/active-directory-token-and-claims) a vytvořit tak jeho identitu. Protože Azure AD a služby AD FS používají protokoly konzistentně, můžete použít [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) k ověřování místní nebo do Azure (v případě připojené). Pomocí knihovny ADAL můžete taky nástroje, jako je Azure PowerShell a rozhraní příkazového řádku Azure pro správu napříč cloudu a místních prostředků. 


### <a name="identities-and-your-identity-system"></a>Identity a systému identit 
Identit pro Azure zásobníku zahrnovat uživatelské účty, skupiny a objekty služby. 

Při instalaci Azure zásobníku několik předdefinovaných aplikacím a službám automatickou registraci v rámci zprostředkovatele identity v klientovi adresáře. Některé služby, které registrují se používají pro správu. Další služby jsou k dispozici pro uživatele. Registrace výchozí poskytnout základní služby identity, které mohou komunikovat navzájem i s identitami, které můžete přidat později.

Pokud jste nastavili Azure AD s víceklientský, některé aplikace rozšíří do nového adresáře. 

## <a name="authentication-and-authorization"></a>Ověřování a autorizace
 

### <a name="authentication-by-applications-and-users"></a>Ověřování aplikace a uživatelé
  
![Identity mezi vrstvy zásobníkové Azure](media/azure-stack-identity-overview/identity-layers.png)

Pro aplikace a uživatelé se popisuje architekturu zásobník Azure čtyři vrstvy. Interakce mezi každou z těchto úrovní můžete použít různé typy ověřování.


|Vrstva    |Ověřování mezi vrstvami  |
|---------|---------|
|Nástroje a klienty, například portál pro správu     | Přístup k nebo upravit prostředek v zásobníku Azure, nástroje a klienti používají [webových tokenů JSON](/azure/active-directory/develop/active-directory-token-and-claims) volání do Azure Resource Manageru. <br>Azure Resource Manager ověří webových tokenů JSON a prohlédne *deklarace identity* v vystavený token, který má odhad úrovně autorizace tohoto uživatele nebo instanční objekt má v zásobníku Azure. |
|Azure Resource Manager a jeho základní služby     |Azure Resource Manager komunikuje se zprostředkovatelé prostředků pro přenos komunikace od uživatelů. <br> Přenosy použití *přímé imperativní* volání nebo *deklarativní* volá prostřednictvím [šablon Azure Resource Manageru](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Poskytovatelé prostředků     |Volání, které se budou předávat zprostředkovatelé prostředků jsou zabezpečené pomocí ověřování pomocí certifikátů. <br>Azure Resource Manager a poskytovatele prostředků pak zůstat v komunikaci prostřednictvím rozhraní API. Poskytovatel prostředků pro každé volání, které se získaly ze Správce prostředků Azure, ověří volání tímto certifikátem.|
|Infrastruktura a obchodní logiky     |Zprostředkovatelé prostředků komunikovat s obchodní logiky a infrastrukturu pomocí režim ověřování podle svého výběru. Výchozí zprostředkovatele prostředků dodávaných se zásobník Azure pomocí ověřování systému Windows pro zabezpečení této komunikace.|

![Informace potřebné pro ověřování](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Ověření do Azure Resource Manageru
Ověření pomocí zprostředkovatele identity a přijímat webového tokenu JSON, musíte mít následující informace: 
1.  **Adresa URL pro systém identit (autorita)**: adresa URL, na které bude možné spojit zprostředkovatele identity. Například *https://login.windows.net*. 
2.  **Identifikátor ID URI aplikace pro Azure Resource Manager**: Jedinečný identifikátor pro Azure Resource Manager, která je zaregistrovaná u poskytovatele identit. Je také jedinečné pro každou instalaci Azure zásobníku.
3.  **Přihlašovací údaje**: přihlašovací údaje použijete k ověření pomocí zprostředkovatele identity. 
4.  **Adresa URL pro Azure Resource Manager**: adresa URL je umístění služby Správce prostředků Azure. Například *https://management.azure.com* nebo *https://management.local.azurestack.external*.

Pokud objekt zabezpečení (klienta, aplikace nebo uživatele) provede požadavek ověřování pro přístup k prostředkům, musí žádosti zahrnovat:
- Přihlašovací údaje objektu zabezpečení.
- Identifikátor ID URI prostředku, který vyžaduje přístup k objektu zabezpečení aplikace. 

Přihlašovací údaje se ověří pomocí zprostředkovatele identity. Poskytovatel identity zároveň ověří, že identifikátor ID URI je aplikace pro zaregistrovanou aplikaci a že objekt má správná oprávnění k získání tokenu pro tento prostředek. Pokud je požadavek platný, je oprávnění webového tokenu JSON. 

Token musí pak předejte v hlavičce požadavku do Azure Resource Manageru. Azure Resource Manager provede následující žádné konkrétní pořadí:
- Ověří *vystavitele* (iss) deklarace identity, abyste se ujistili, že token od zprostředkovatele identity správné. 
- Ověří *cílovou skupinu* deklarace identity (oblast), potvrďte, že byl token vydán pro Azure Resource Manager. 
- Ověří, že webových tokenů JSON je podepsaná pomocí certifikátu, který je nakonfigurovaný pomocí OpenID se označuje do Azure Resource Manageru. 
- Zkontrolujte *vydané v* (iat) a *vypršení platnosti* (exp) deklarace identity k potvrzení, že token je aktivní a nelze přijmout. 

Po dokončení všech ověření Azure Resource Manager používá *proti* (oid) a *skupiny* deklarace identity, aby seznam zdrojů, kteří mohou přistupovat k objektu zabezpečení. 

![Diagram protokolu tokenu exchange](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Pomocí řízení přístupu na základě rolí  
Na základě rolí řízení přístupu (RBAC) v zásobníku Azure je konzistentní s implementací v Microsoft Azure. Můžete spravovat přístup k prostředkům přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím. Informace o tom, jak používat funkci RBAC zásobníkem Azure, najdete v následujících článcích:
- [Začínáme s řízením přístupu na základě rolí na portálu Azure](/azure/role-based-access-control/overview).
- [Pomocí řízení přístupu na základě Role můžete spravovat přístup k prostředkům na předplatné](/azure/role-based-access-control/role-assignments-portal).
- [Vytvořit vlastní role pro řízení přístupu](/azure/role-based-access-control/custom-roles).
- [Správa řízení přístupu na základě rolí](azure-stack-manage-permissions.md) v Azure zásobníku.


### <a name="authenticate-with-azure-powershell"></a>Ověření pomocí Azure PowerShell  
Podrobnosti o použití prostředí Azure PowerShell k ověření pomocí Azure zásobníku naleznete na adrese [nakonfigurovat prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Ověření pomocí rozhraní příkazového řádku Azure
Informace o použití prostředí Azure PowerShell k ověření pomocí Azure zásobníku najdete v tématu [instalace a konfigurace rozhraní příkazového řádku Azure pro použití s Azure zásobníku](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Další postup
- [Architektura identity](azure-stack-identity-architecture.md)   
- [Integrace Datacenter - identity](azure-stack-integrate-identity.md)




