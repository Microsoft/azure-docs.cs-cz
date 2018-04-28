---
title: Co je Azure Active Directory B2C? | Dokumenty Microsoft
description: Další informace o tom, jak můžete vytvořit a spravovat vaše aplikace přihlašování pomocí Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.openlocfilehash: ca9e45a214639da86cf8e0c4a39b3e3d6b6d6491
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-active-directory-b2c"></a>Co je Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C je služba pro správu identit, která umožňuje přizpůsobení a řízení způsobu, jakým se zákazníci registrují, přihlašují a jakým při používání vašich aplikací spravují své profily. To zahrnuje mimo jiné aplikace vyvinuté pro iOS, Android a .NET. Azure AD B2C umožňuje tyto akce při ochraně identity vašich zákazníků ve stejnou dobu.

Můžete nakonfigurovat aplikaci registrovanou v Azure AD B2C k provádění různých akcí správy identit. Tady je několik příkladů:

- Povolit zákazníka se zaregistrujte používat zaregistrovanou aplikaci
- Povolit zákazníka zaregistrujete přihlásit a začít používat aplikaci
- Povolit zákazník zaregistrujete upravovat svůj profil
- Povolit službu Multi-Factor authentication v aplikaci
- Povolit zákazník si zaregistrovat a přihlásit se pomocí poskytovatelů identit konkrétní
- Udělení přístupu z vaší aplikace pro rozhraní API, který můžete vytvořit
- Přizpůsobení vzhledu a chování registrace a přihlášení
- Spravovat jedné relace přihlášení pro vaši aplikaci

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Co je potřeba myslet před použitím Azure AD B2C?

- Jak se má zákazník pro interakci s Moje aplikace?
- Co je uživatelské rozhraní (UI) možnosti, které chcete poskytnout zákazníkům?
- Které zprostředkovatelů identity chcete zákazníky vybírat v mé aplikaci?
- Vyžaduje proces přihlášení další rozhraní API pro spustit?

### <a name="customer-interaction"></a>Zásahu zákazníka

Azure AD B2C podporuje [OpenID Connect](https://openid.net/connect/) pro všechny zákazníka dojde. Aplikace v Azure AD B2C implementaci OpenID Connect, zahájí této cesty uživatele zasláním požadavků na ověření do Azure AD B2C. Výsledkem požadavku je `id_token`. Tento token zabezpečení představuje identitu zákazníka.

Každá aplikace, která používá Azure AD B2C musí být zaregistrován v klienta Azure AD B2C pomocí portálu Azure. Proces registrace shromažďuje a přiřazuje hodnoty do vaší aplikace. Tyto hodnoty zahrnují ID aplikace, které jednoznačně identifikuje aplikaci a přesměrování identifikátor URI, který slouží k přímé odpovědi na jiné místo.

Interakce každé aplikace, postupuje podle podobného vzoru:

1. Aplikace přesměruje o spuštění zásadu.
2. Zákazník vykoná zásadu podle definice zásady.
3. Aplikace obdrží token zabezpečení.
4. Aplikace používá token zabezpečení se pokusit o přístup k chráněnému prostředku.
5. Server prostředků ověří token zabezpečení, aby ověřil, zda lze udělit přístup.
6. Aplikace se pravidelně aktualizuje token zabezpečení.

Tyto kroky můžou mírně lišit v závislosti na typu aplikace, které vytváříte.

Azure AD B2C komunikuje pomocí poskytovatelů identit, zákazníky, jinými systémy a s místním adresáři v pořadí k dokončení úkolu identity. Například přihlásit zákazníků, zaregistrovat nový zákazník nebo resetovat heslo. Základní platforma, která vytváří více stran vztah důvěryhodnosti a dokončení těchto kroků se nazývá rozhraní prostředí Identity. Toto rozhraní a zásady (také nazývané cesty uživatele nebo zásady důvěryhodnosti Framework) explicitně definuje aktéři, akce, protokoly a pořadí kroků dokončete.

Azure AD B2C chrání před útoky DOS a heslo útoky na vaše aplikace několika způsoby. Azure AD B2C používá detekce a zmírnění techniky, jako třeba SYN soubory cookie a rychlost a připojení limity pro ochranu před útoky DoS prostředků. Zmírnění dopadů je rovněž obsažena pro útoky hrubou silou heslo a slovníkovým útokům heslo.

#### <a name="built-in-policies"></a>Předdefinované zásady

Každý požadavek zaslaný do Azure AD B2C Určuje zásadu. Zásady řídí chování jak vaše aplikace komunikuje s Azure AD B2C. Předdefinované zásady jsou předdefinované pro běžné úkoly, identity, jako například registrace, přihlášení a úpravy profilu.  Například registrační zásadě můžete řídit chování konfigurací následujícího nastavení:

- Sociální účty, které zákazníka můžete použít k registraci pro aplikaci
- Data shromážděná z zákazníkovi, jako je jméno nebo PSČ
- Ověřování pomocí služby Multi-Factor Authentication
- Vzhled a chování registrace všechny stránky
- Informace o aplikaci vrácena

#### <a name="custom-policies"></a>Vlastní zásady 

[Vlastní zásady](active-directory-b2c-overview-custom.md) jsou konfigurační soubory, které definují chování rozhraní prostředí Identity ve vašem klientovi Azure AD B2C. Vlastní zásady se dá plně upravit k dokončení mnoho úloh. Vlastní zásady je reprezentován jako jednoho nebo několika souborů ve formátu XML odkazující na sebe navzájem v hierarchické řetězu. 

Více vlastních zásad různých typů lze použít v klientovi služby Azure AD B2C, podle potřeby a lze opětovně použít napříč aplikacemi. Tato možnost umožňuje definování a úprava činnosti identity zákazníků s minimální nebo žádný změny kódu. Zásady můžete použít tak, že přidáte parametr speciální dotazu na požadavky HTTP ověřování.

Vlastní zásady můžete použít k řízení cesty uživatele těmito způsoby:

- Definování interakci s rozhraními API zachytit dodatečné informace, zkontrolujte zákazníka, která poskytuje deklarace identity nebo aktivovat externí procesy.
- Změna chování na základě deklarací z rozhraní API nebo z deklarací identity v adresáři, jako *migrationStatus*.
- Jakýkoli pracovní postup nevztahuje integrovaných zásad. Shromažďování Další informace od zákazníka během přihlašování uživatelů a provádění povolení například zkontrolujte přístup k prostředku.

### <a name="identity-providers"></a>Zprostředkovatelé identit

Zprostředkovatele identity je služba, která ověřuje identit zákazníka a vydává tokeny zabezpečení. V Azure AD B2C můžete nakonfigurovat několik poskytovatelů identit ve vašem klientovi, jako je například účet Microsoft, Facebook nebo Amazon ostatních. 

Konfigurace zprostředkovatele identity ve vašem klientovi Azure AD B2C, musí záznam identifikátor aplikace nebo identifikátor klienta a tajný klíč heslo nebo klienta z aplikace zprostředkovatele identity, které vytvoříte. Tento identifikátor a heslo jsou potom použít ke konfiguraci vaší aplikace.

### <a name="user-interface-experience"></a>Uživatelského rozhraní

Většina obsah HTML a CSS, který zákazníkům lze řídit. Pomocí funkce přizpůsobení uživatelského rozhraní stránky přizpůsobit vzhled a chování žádné zásady. Můžete také udržovat značky a visual konzistenci mezi aplikací a Azure AD B2C.

Azure AD B2C spuštěním kódu v prohlížeči zákazníka a používá moderní přístup názvem sdílení prostředků různých původů (CORS). Nejdřív zadejte adresu URL v zásadách s přizpůsobený obsah HTML. Azure AD B2C sloučí elementy uživatelského rozhraní pomocí obsah HTML, který je načten z vaší adresy URL a potom zobrazí stránku zákazník.

Můžete odeslat parametry do Azure AD B2C v řetězci dotazu. Pomocí předání parametru váš koncový bod HTML, můžete dynamicky měnit obsah stránky. Například můžete změnit obrázek pozadí na Azure AD B2C registrace nebo přihlášení stránky, na základě parametr, který můžete předat z webu nebo mobilních aplikací.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Jak můžu začít pracovat s Azure AD B2C?

V Azure AD B2C klient představuje vaší organizace a je adresáře uživatelů. Každý klient Azure AD B2C je oddělený od ostatních klientů Azure AD B2C. Klienta obsahuje informace o zákazníci, kteří jsou přihlášeni k vaší aplikaci používat. Například hesla, data profilu a oprávnění.

Potřebujete připojit k předplatnému Azure povolit všechny funkce a platit poplatky za používání klienta služby Azure AD B2C. Povolit Azure AD B2C zákazníků k přihlášení do aplikace, je nutné zaregistrovat aplikaci v klienta Azure AD B2C.

Než začnete konfigurovat aplikace pomocí Azure AD B2C, musíte nejprve vytvořit klienta Azure AD B2C a registrace vaší aplikace. Pro registraci aplikace, proveďte kroky v [kurz: zaregistrovat aplikaci k povolení registrace a přihlášení pomocí Azure AD B2C](tutorial-register-applications.md).
  
Pokud jste vývojář webové aplikace ASP.NET, nastavení aplikace k ověření účty pomocí kroků v [kurz: Povolit webových aplikací k ověření s účty pomocí Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Pokud vaše jsou vývojář aplikace pracovní plochy nastavit aplikaci pro ověření účty pomocí kroků v [kurz: povolení aplikace k ověření s účty pomocí Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Pokud jste vývojář jednostránkové aplikace pomocí Node.js, nastavte aplikaci k ověření účty pomocí kroků v [kurz: Povolit jednostránkové aplikace k ověření s účty pomocí Azure AD B2C](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Další postup

Spusťte konfiguraci vaší aplikace pro prostředí registrace a přihlášení a pokračovat v tomto kurzu.

> [!div class="nextstepaction"]
> [Kurz: Zaregistrujte aplikaci k povolení registrace a přihlášení pomocí Azure AD B2C](tutorial-register-applications.md)
