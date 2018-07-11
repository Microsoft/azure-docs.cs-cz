---
title: Co je Azure Active Directory B2C? | Microsoft Docs
description: Přečtěte si o tom, jak můžete vytvořit a spravovat možnosti přihlašování v aplikaci pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 04/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6949ab89cf806818783c86199e6df334e263b046
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440877"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co je Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C je služba pro správu identit, která umožňuje přizpůsobení a řízení způsobu, jakým se zákazníci registrují, přihlašují a jakým při používání vašich aplikací spravují své profily. To zahrnuje mimo jiné aplikace vyvinuté pro iOS, Android a .NET. Azure AD B2C umožňuje tyto akce, přitom ale zároveň chrání identity vašich zákazníků.

Můžete nakonfigurovat aplikaci registrovanou pomocí Azure AD B2C k provádění různých akcí správy identit. Tady je několik příkladů:

- Povolení zákazníkovi registrovat se, aby mohl používat vaši registrovanou aplikaci
- Povolení registrovanému zákazníkovi přihlásit se a začít používat vaši aplikaci
- Povolení registrovanému zákazníkovi upravovat svůj profil
- Povolení vícefaktorového ověřování ve vaší aplikaci
- Povolení zákazníkovi registrovat se a přihlásit se pomocí konkrétních zprostředkovatelů identity
- Udělení přístupu z vaší aplikace k rozhraním API, která vytváříte
- Přizpůsobení vzhledu a chování prostředí registrace a přihlášení
- Správa relací jednotného přihlašování pro vaši aplikaci

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Co je třeba zvážit před použitím Azure AD B2C?

- Jak má zákazník pracovat s mojí aplikací?
- Jaké je uživatelské rozhraní, které chci zákazníkům poskytovat?
- Z kterých zprostředkovatelů identity chci dát zákazníkům v mé aplikaci na výběr?
- Vyžaduje můj proces přihlášení, aby běžela další rozhraní API?

### <a name="customer-interaction"></a>Interakce zákazníků

Azure AD B2C podporuje [OpenID Connect](https://openid.net/connect/) pro všechna zákaznická prostředí. V implementaci OpenID Connect v Azure AD B2C zahajuje aplikace tuto cestu uživatele zasíláním žádostí o ověření do Azure AD B2C. Výsledkem žádosti je `id_token`. Tento token zabezpečení představuje identitu zákazníka.

Každá aplikace, která používá Azure AD B2C, musí být zaregistrovaná v tenantovi Azure AD B2C na webu Azure Portal. Proces registrace shromáždí a přiřadí vaší aplikaci hodnoty. Mezi tyto hodnoty patří ID aplikace, které jednoznačně identifikuje aplikaci, a identifikátor URI pro přesměrování, který umožňuje přímé reakce zpátky do aplikace.

Interakce každé aplikace probíhá podle podobného vzoru:

1. Aplikace směruje zákazníka ke spuštění zásady.
2. Zákazník provede zásadu podle její definice.
3. Aplikace obdrží token zabezpečení.
4. Aplikace použije token zabezpečení a pokusí se o přístup k chráněnému prostředku.
5. Server prostředků ověří token zabezpečení, aby ověřil, zda lze udělit přístup.
6. Aplikace token zabezpečení pravidelně aktualizuje.

Tento postup se může mírně lišit v závislosti na typu aplikace, kterou sestavujete.

Azure AD B2C pracuje se zprostředkovateli identity, zákazníky, jinými systémy a s místním adresářem (v uvedeném pořadí) na provedení úkolu identity. Například přihlašování zákazníka, registrace nového zákazníka nebo resetování hesla. Základní platforma, která vytváří vztah důvěryhodnosti mezi více stranami a provádí tyto kroky, se nazývá Rozhraní prostředí pro identity. Toto rozhraní a zásady (také nazývané cesta uživatele nebo zásady architektury důvěryhodnosti) explicitně definuje účastníky, akce, protokoly a posloupnost kroků, které se mají provést.

Azure AD B2C chrání před útoky na dostupnost služby (DOS) a před útoky na hesla proti vaší aplikaci několika různými způsoby. K ochraně prostředků před útoky na dostupnost služby používá zjišťovací a omezovací techniky, jako jsou soubory cookie SYN a omezení frekvence a počtu připojení. Omezení rizik je rovněž obsaženo pro útoky na hesla hrubou silou a slovníkové útoky na hesla.

#### <a name="built-in-policies"></a>Předdefinované zásady

Každá žádost zaslaná do Azure AD B2C určuje zásadu. Zásada řídí chování, jak aplikace komunikuje s Azure AD B2C. Integrované zásady jsou předdefinované pro nejběžnější úkoly identity, například registraci, přihlašování a úpravy profilu.  Například zásada registrace umožňuje řídit chování pomocí konfigurace následujícího nastavení:

- Účty na sociálních sítích, které zákazník může použít k registraci pro aplikaci
- Data shromážděná od zákazníka, jako jsou jméno nebo PSČ
- Vícefaktorové ověřování (Multi-factor Authentication)
- Vzhled a chování všech registračních stránek
- Informace vrácené do aplikace

#### <a name="custom-policies"></a>Vlastní zásady 

[Vlastní zásady](active-directory-b2c-overview-custom.md) jsou konfigurační soubory, které určují chování Rozhraní prostředí pro identity v tenantovi služby Azure AD B2C. Vlastní zásady se dají plně upravovat k provádění mnoha úkolů. Vlastní zásada je vyjádřena jako jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchickém řetězu. 

Více vlastních zásad různých typů se dá podle potřeby použít ve vašem tenantovi Azure AD B2C a opětovně použít v aplikacích. Díky této flexibilitě je možné definovat a upravovat prostředí identity zákazníků s minimálními změnami kódu nebo úplně bez nich. Zásady můžete použít tak, že do žádostí o ověření pomocí protokolu HTTP přidáte speciální parametr dotazu.

Vlastní zásady se dají následujícími způsoby použít k řízení cest uživatelů:

- Definování interakce s rozhraními API k zachycování dalších informací, k ověřování deklarací identity zadaných zákazníky nebo ke spouštění externích procesů.
- Změna chování na základě deklarací identity z rozhraní API nebo z deklarací identity v adresáři, jako *migrationStatus*.
- Jakýkoliv pracovní postup, který nepokrývají předdefinované zásady. Například shromažďování více informací od zákazníka během přihlašování a autorizace, kterou se zkontroluje přístup k prostředku.

### <a name="identity-providers"></a>Zprostředkovatelé identit

Zprostředkovatel identity je služba, která ověřuje identity zákazníků a vydává tokeny zabezpečení. V Azure AD B2C můžete nakonfigurovat několik zprostředkovatelů identit ve vašem tenantovi, jako je třeba účet Microsoft, Facebook nebo Amazon. 

Pokud chcete nakonfigurovat zprostředkovatele identity ve vašem tenantovi Azure AD B2C, musíte zaznamenat identifikátor aplikace nebo identifikátor klienta a heslo nebo tajný kód klienta z aplikace zprostředkovatele identity, kterou vytváříte. Tento identifikátor a heslo potom slouží ke konfiguraci vaší aplikace.

### <a name="user-interface-experience"></a>Prostředí uživatelského rozhraní

Většina obsahu HTML a CSS, který se zobrazí zákazníkům, se dá řídit. Pomocí funkce pro vlastní nastavení uživatelského rozhraní stránky si přizpůsobíte vzhled a chování každé zásady. Můžete také udržovat konzistenci značky a vizuální konzistenci mezi vaší aplikací a službou Azure AD B2C.

Azure AD B2C spustí v prohlížeči zákazníka kód a využívá moderní přístup, který se nazývá sdílení prostředků mezi zdroji (Cross-Origin Resource Sharing, CORS). Nejprve zadáte v zásadě s vlastním obsahem ve formátu HTML adresu URL. Azure AD B2C sloučí elementy uživatelského rozhraní s obsahem HTML, který je načtený z vaší adresy URL, a pak zobrazí stránku zákazníkovi.

Do Azure AD B2C můžete poslat parametry v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Jak začít pracovat s Azure AD B2C?

V Azure AD B2C představuje tenant vaši organizaci a je také adresářem uživatelů. Každý tenant Azure AD B2C se odlišuje a je oddělený od jiných tenantů Azure AD B2C. Tenant obsahuje informace o zákaznících, kteří se zaregistrovali k používání vaší aplikace. Například hesla, data profilu a oprávnění.

Potřebujete propojit vašeho tenanta Azure AD B2C s předplatným Azure, aby se povolily všechny funkce a poplatky za využívání. Pokud chcete zákazníkům Azure AD B2C povolit přihlášení do aplikace, je třeba aplikaci zaregistrovat do tenanta Azure AD B2C.

Před konfigurací aplikace k používání Azure AD B2C musíte nejprve vytvořit tenanta Azure AD B2C a zaregistrovat si aplikaci. Pokud si chcete zaregistrovat aplikaci, proveďte kroky v článku [Kurz: Registrace aplikace k povolení registrování a přihlašování pomocí služby Azure AD B2C](tutorial-register-applications.md).
  
Pokud jste vývojář webové aplikace ASP.NET, nastavte aplikaci na ověřování účtů pomocí postupu v článku [Kurz: Povolení ověřování s účty pomocí Azure AD B2C přes webovou aplikaci](active-directory-b2c-tutorials-web-app.md).

Pokud jste vývojář desktopové aplikace, nastavte aplikaci na ověřování účtů pomocí postupu v článku [Kurz: Povolení ověřování s účty pomocí Azure AD B2C přes desktopovou aplikaci](active-directory-b2c-tutorials-desktop-app.md).

Pokud jste vývojář jednostránkové aplikace pomocí Node.js, nastavte aplikaci na ověřování účtů pomocí postupu v článku [Kurz: Povolení ověřování s účty pomocí Azure AD B2C přes jednostránkovou aplikaci](active-directory-b2c-tutorials-spa.md).

## <a name="next-steps"></a>Další kroky

Začněte konfigurovat svoji aplikaci pro prostředí registrace a přihlášení – pokračujte tímto kurzem.

> [!div class="nextstepaction"]
> [Kurz: Registrace aplikace k povolení registrování a přihlašování pomocí služby Azure AD B2C](tutorial-register-applications.md)
