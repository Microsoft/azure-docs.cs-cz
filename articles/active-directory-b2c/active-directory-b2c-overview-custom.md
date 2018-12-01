---
title: Vlastní zásady služby Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o vlastních zásadách Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a1457b2aa571b58502b7d819eb3bcf142c10dac1
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725059"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Vlastní zásady v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásady jsou konfigurační soubory, které definují chování vašeho tenanta Azure Active Directory (Azure AD) B2C. Toky uživatelů jsou předdefinovány na portálu Azure AD B2C pro nejběžnější úkoly identity. Vlastní zásady je může plně upravit vývojář identit k dokončení celou řadu různých úloh.

## <a name="comparing-user-flows-and-custom-policies"></a>Porovnání toky uživatelů a vlastních zásad

| | Toky uživatele | Vlastní zásady |
|-|-------------------|-----------------|
| Cílových uživatelů | S nebo bez znalosti identity všechny vývojáře aplikací. | Profesionály pracující identitami, systémovým integrátorům, konzultantům a interních identity týmy. Jsou zvyklí OpenIDConnect toky a pochopit poskytovatelů identit a ověřování nezaloženého na deklaracích. |
| Metodě konfigurace | Azure portal s přívětivější uživatelské rozhraní (UI). | Přímá úprava souborů XML a potom nahrajete na webu Azure portal. |
| Přizpůsobení uživatelského rozhraní | Úplné přizpůsobení uživatelského rozhraní, včetně HTML a CSS.<br><br>Podporu více jazyků pomocí vlastního řetězce. | Stejné |
| Vlastní nastavení atributu | Standardní a vlastní atributy. | Stejné |
| Token a relace správy | Vlastní token a více možností relace. | Stejné |
| Zprostředkovatelé identit | Předdefinované místní nebo sociálních sítí poskytovatele. | Založené na standardech OIDC, protokolu OAUTH a SAML. |
| Identita úlohy | Registrace nebo přihlášení pomocí místních nebo mnoho účtů na sociálních sítích.<br><br>Samoobslužné resetování hesla.<br><br>Upravit profil.<br><br>Ověřování službou Multi-Factor Authentication.<br><br>Přizpůsobení tokenů a relací.<br><br>Toky tokenu přístupu. | Dokončení úloh toky uživatelů pro používání vlastních poskytovatelů identit nebo použijte vlastní obory.<br><br>Poskytnutí uživatelského účtu v jiném systému v době registrace.<br><br>Odeslání Uvítacího e-mailu pomocí vlastního poskytovatele e-mailové služby.<br><br>Použití úložiště uživatele mimo Azure AD B2C.<br><br>Ověření uživatele pomocí rozhraní API poskytuje informace o důvěryhodných systémem. |

## <a name="policy-files"></a>Zásady souborů

Tyto tři typy zásad souborů se používají:

- **Základní soubor** – obsahuje definice. Doporučuje se vytvořit minimální počet změn do tohoto souboru pomoci při řešení potíží a dlouhodobé údržby zásad.
- **Rozšíření souboru** – má jedinečnou konfiguraci změny pro vašeho tenanta.
- **Předávající strany (RP) soubor** – jeden soubor zaměřený na úkol, který je vyvolán přímo z aplikace nebo služby (také označované jako předávající strana). Každý úkol jedinečné vyžaduje svou vlastní RP a v závislosti na branding požadavky, může být tento počet "Celkový počet aplikací x celkového počtu případů použití."

Toky uživatelů v Azure AD B2C mají tvar tři soubor uvedené výše, ale vývojář se zobrazují pouze soubor předávající strany, zatímco na webu Azure portal provede změny na pozadí do souboru rozšíření.

## <a name="custom-policy-core-concepts"></a>Vlastní zásady pro základní koncepty

Služba zákazníka identit a přístupu (ciam od) správy v Azure zahrnuje:

- Adresář uživatele, který je přístupný prostřednictvím Microsoft Graphu a který obsahuje uživatelská data pro místní účty a účty federované.
- Přístup k **architekturu rozhraní identit** , která orchestruje vztah důvěryhodnosti mezi uživateli a entitami a předává deklarací identity mezi nimi dokončete úlohu správy identit a přístupu. 
- Služby tokenů zabezpečení (STS), který vystaví ID tokeny, obnovovací tokeny a přístup tokeny (a ekvivalentní kontrolní výrazy SAML) a ověřuje je k ochraně prostředků.

Azure AD B2C pracuje na pořadí k dosažení úlohu identity pomocí zprostředkovatelů identity, uživatelů a jiných systémů a s adresáři místního uživatele. Například přihlášení uživatele, registrovat nové uživatele nebo resetování hesla. Architekturu rozhraní identit a zásad (také nazývané cesty uživatele nebo zásady důvěryhodnosti framework) vytváří více stran vztah důvěryhodnosti a explicitně definuje účastníky, akce, protokoly a postupně jednotlivé kroky k dokončení.

Architekturu rozhraní identit je plně konfigurovatelné založená na zásadách, cloudové platformy Azure, který orchestruje vztah důvěryhodnosti mezi entitami ve standardní protokol formátech, jako je například REST OpenIDConnect, OAuth, SAML, WSFed a několik nestandardní ty, které jsou Na rozhraní API-na systému deklarací výměny. Rozhraní vytvoří uživatelsky přívětivé, s názvem prázdné prostředí, které podporují HTML a CSS.

Vlastní zásada je vyjádřena jako jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchickém řetězu. Elementů XML definovat schéma deklarace identity, transformace deklarací, definic obsahu, zprostředkovatelů deklarací identity, technické profily a kroků Orchestrace cesty uživatele, mezi další prvky. Vlastní zásady je dostupné jako jeden nebo více souborů XML, které jsou spouštěny příkazem architekturu rozhraní identit, když uživatel vyvolá předávající stranu. Vývojáři vlastní zásady konfigurace musí definovat důvěryhodných vztahů opatrní podrobně zahrnout koncové body metadat, přesné deklarací exchange definice a konfigurace tajné klíče, klíče a certifikáty podle potřeb každého zprostředkovatele identity.

### <a name="inheritance-model"></a>Model dědičnosti

Pokud aplikace zavolá soubor zásad předávající strany, přidá architekturu rozhraní identit v Azure AD B2C všechny prvky ze základního souboru, ze souboru rozšíření a pak ze souboru zásad předávající strany k sestavení aktuální zásady platit.  Prvky stejného typu a název v souboru RP se přepíšou nastavení v rozšíření a rozšíření přepsání base.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md)
