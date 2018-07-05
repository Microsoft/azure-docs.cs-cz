---
title: Vlastní zásady služby Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o vlastních zásadách Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0724227da425eb2faeee9ac4ae8449782e62a241
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445956"
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: Vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>Co jsou vlastní zásady?

Vlastní zásady jsou konfigurační soubory, které určují chování tenanta služby Azure AD B2C. Vzhledem k tomu **integrované zásady** předdefinovaných na portálu Azure AD B2C pro nejběžnější úkoly identity vlastní zásady je může plně upravit vývojář identit k dokončení skoro neomezené množství úloh. Pokračujte ve čtení a určí, jestli jsou vlastní zásady pro vás a vašich scénářů s identitou.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Porovnání integrovaných zásad a vlastních zásad

| | Předdefinované zásady | Vlastní zásady |
|-|-------------------|-----------------|
|Cílových uživatelů | Všichni vývojáři aplikací s nebo bez znalosti identity | Profesionály pracující identitami: systémových integrátorů, konzultantům a interních identity týmy. Vyhovuje OpenIDConnect toky a seznamte se s poskytovateli identit a ověřování nezaloženého na deklaracích |
|Metodě konfigurace | Azure portal s uživatelsky přívětivou UI | Přímá úprava souborů XML a potom nahrajete na webu Azure portal |
|Přizpůsobení uživatelského rozhraní | Úplné přizpůsobení uživatelského rozhraní, včetně podpory HTML, CSS a javascript (vyžaduje vlastní domény)<br><br>Podpora více jazyků pomocí vlastního řetězce | Stejné |
| Vlastní nastavení atributu | Standardní a vlastní atributy | Stejné |
|Token a relace správy | Vlastní token a více možností relace | Stejné |
|Zprostředkovatelé identit| **Dnes**: předdefinovaného poskytovatele místní, na sociálních sítích<br><br>**Budoucnost**: OAuth založené na standardech OIDC SAML, | **Dnes**: založené na standardech OIDC OAUTH, SAML<br><br>**Budoucnost**: WsFed |
|Identita úlohy (příklady) | Registrace nebo přihlašování pomocí místní a mnoho účtů na sociálních sítích<br><br>Samoobslužné resetování hesla<br><br>Upravit profil<br><br>Scénáře ověřování služby Multi-Factor Authentication<br><br>Přizpůsobení tokenů a relace<br><br>Přístupový Token toky | Stejné úlohy jako integrované zásady používání vlastních poskytovatelů identit dokončíte. nebo použijte vlastní obory<br><br>Zřízení uživatele v jiném systému v době registrace<br><br>Odeslání Uvítacího e-mailu pomocí vlastního poskytovatele e-mailové služby<br><br>Použití úložiště uživatele mimo B2C<br><br>Ověřit informace o důvěryhodných systému prostřednictvím rozhraní API poskytnutého uživatelem |

## <a name="policy-files"></a>Zásady souborů

Vlastní zásady je vyjádřena jako jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchické řetězu. Elementy XML definice: schéma deklarace identity, deklarace identity transformace, obsahu definice, deklarace identity zprostředkovatelé/technické profily a kroků Orchestrace cesty uživatele, mezi další prvky.

Doporučujeme vám používat ze tří typů zásad souborů:

- **ZÁKLADNÍ soubor**, který obsahuje většinu definic a pro které Azure poskytuje úplnou ukázku.  Doporučujeme že vytvořit minimální počet změn do tohoto souboru pomoci při řešení potíží a dlouhodobé údržbě zásad
- **soubor rozšíření** změny jedinečnou konfiguraci, která obsahuje pro vašeho tenanta
- **soubor předávající strany (RP)** , který je jeden úkol zaměřený soubor, který je vyvolán přímo z aplikace nebo služby (neboli předávající strana).  Přečtěte si článek na soubor definice zásad Další informace.  Každý úkol jedinečné vyžaduje svou vlastní RP a v závislosti na branding požadavky může být tento počet "Celkový počet aplikací x celkového počtu případů použití."


Předdefinované zásady v Azure AD B2C mají tvar tři soubor uvedené výše, ale vývojář se zobrazují pouze soubor předávající strany (RP), zatímco portál provede změny na pozadí do souboru rozšíření.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Základní koncepty, které byste měli vědět při použití vlastních zásad

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure identit a přístupu (ciam od) správy služby zákazníkům. Služba zahrnuje:

1. Adresář uživatele ve formuláři speciální Azure Active Directory dostupné prostřednictvím Microsoft Graphu a který obsahuje uživatelská data pro místní účty a účty federované 
2. Přístup k **architekturu rozhraní identit** , která orchestruje vztah důvěryhodnosti mezi uživateli a entitami a předá deklarací identity mezi nimi dokončete úlohu správy identit a přístupu 
3. Služby tokenů zabezpečení (STS) vydávání ID tokeny, obnovovací tokeny a přístup tokeny (a ekvivalentní kontrolní výrazy SAML) a ověřování je k ochraně prostředků.

Azure AD B2C spolupracuje s poskytovateli identit, uživatelé, jiné systémy a adresáři místního uživatele postupně k dosažení úlohu identity (třeba přihlašovací jméno uživatele, registrace nového uživatele, resetování hesla). Základní platforma, která vytváří více stran vztah důvěryhodnosti a provede tyto kroky se nazývá architekturu rozhraní identit a zásad (také nazývané cesty uživatele nebo zásady důvěryhodnosti framework) explicitně definuje účastníky, akce a protokoly a posloupnost kroků dokončete.

### <a name="identity-experience-framework"></a>Architektura prostředí identit

Formátuje plně konfigurovatelné založená na zásadách, cloudové platformy Azure, která orchestruje vztah důvěryhodnosti mezi entitami (široce zprostředkovatelů deklarací identity) ve standardní protokol jako OpenIDConnect, OAuth, SAML, WSFed a několik nestandardní značky (například REST Výměna deklarací API-based systému system). I2E vytvoří uživatelsky přívětivé, s názvem prázdné prostředí, které podporují HTML, CSS a JavaScriptu.  V současné době je architekturu rozhraní identit k dispozici pouze v kontextu služby Azure AD B2C a pro úlohy související s ciam od seřazený podle priority.

### <a name="built-in-policies"></a>Předdefinované zásady

Předdefinovaná konfigurace soubory, které budou řídit chování Azure AD B2C k většině běžně používané úlohy identity (to znamená, registrace uživatele, přihlášení, resetování hesla) a interakci s považujete za důvěryhodné jehož relace je také předdefinovaných (Azure AD B2C například Facebook zprostředkovatele identity, LinkedIn, Account Microsoft, Google účty).  V budoucnu předdefinovaných zásadách může také poskytnout pro přizpůsobení zprostředkovatelů identity, které jsou obvykle v podnikové sféře jako je Azure Active Directory Premium, Active Directory a AD FS, Salesforce ID poskytovatele atd.


### <a name="custom-policies"></a>Vlastní zásady

Konfigurační soubory, které definují chování architekturu rozhraní identit ve vašem tenantovi Azure AD B2C. Vlastní zásady je dostupné jako jeden nebo více souborů XML (viz definice zásad souborů), které jsou spouštěny příkazem architekturu rozhraní identit, když uživatel vyvolá předávající strany (například aplikace). Vývojář identit k dokončení skoro neomezené množství úloh může přímo upravit vlastní zásady. Vývojáři vlastní zásady konfigurace musí definovat důvěryhodných vztahů opatrní podrobně zahrnout koncové body metadat, přesné deklarací exchange definice a konfigurace tajné klíče, klíče a certifikáty podle potřeb každého zprostředkovatele identity.

## <a name="policy-file-definitions-for-identity-experience-framework-trust-frameworks"></a>Soubor definice zásad pro architekturu rozhraní identit architektury důvěryhodnosti

### <a name="policy-files"></a>Zásady souborů

Vlastní zásady je vyjádřena jako jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchické řetězu. Elementy XML definice: schéma deklarace identity, deklarace identity transformace, obsahu definice, deklarace identity zprostředkovatelé/technické profily a kroků Orchestrace cesty uživatele, mezi další prvky.  Doporučujeme vám používat ze tří typů zásad souborů:

- **ZÁKLADNÍ soubor** obsahuje většinu definic a pro které Azure poskytuje úplnou ukázku.  Doporučujeme že vytvořit minimální počet změn do tohoto souboru pomoci při řešení potíží a dlouhodobé údržbě zásad
- **soubor rozšíření** změny jedinečnou konfiguraci, která obsahuje pro vašeho tenanta
- **soubor předávající strany (RP)** je jeden soubor zaměřený na úkol, který je vyvolán přímo z aplikace nebo služby (neboli předávající strana).  Přečtěte si článek na soubor definice zásad Další informace.  Každý úkol jedinečné vyžaduje svou vlastní RP a v závislosti na branding požadavky může být tento počet "Celkový počet aplikací x celkového počtu případů použití".

![Typy zásad souborů](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Typ zásad souboru | Název souboru příklady | Doporučené použití | Dědí z |
|---------------------|--------------------|-----------------|---------------|
| ZÁKLAD |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Zahrnuje základní schéma deklarace identity, transformace deklarací identity, zprostředkovatelů deklarací identity a jízdy uživatel nakonfiguroval Microsoft<br><br>Provést minimální změny tohoto souboru | Žádný |
| Rozšíření (ext, Přípona) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Sloučit změny základního souboru<br><br>Upravené deklarací identity<br><br>Upravené uživatele jízdy<br><br>Vlastní definice vlastní schéma | ZÁKLADNÍ soubor |
| Přijímající strany (RP) | B2C_1A_sign_up_sign_in.xml| Změnit token tvar a relace nastavení tady| Soubor Extensions(ext) |

### <a name="inheritance-model"></a>Model dědičnosti

Pokud aplikace zavolá soubor zásad předávající strany, architekturu rozhraní identit v B2C přidá všechny prvky z BASE a potom z rozšíření a nakonec ze souboru zásad předávající strany k sestavení aktuální zásady platit.  Prvky stejného typu a název v souboru RP se přepíšou nastavení v rozšíření, a rozšíření přepíše BASE.

**Předdefinované zásady** v Azure AD B2C mají tvar souboru 3 uvedené výše, ale vývojář se zobrazují pouze soubor předávající strany (RP), zatímco portál provede změny na pozadí do souboru rozšíření.  Všechny Azure AD B2C sdílí soubor základní zásady, které je pod kontrolou týmu Azure B2C a se často aktualizuje.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md)
