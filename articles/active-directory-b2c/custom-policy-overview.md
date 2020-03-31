---
title: Vlastní zásady služby Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si o vlastních zásadách služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f72aedb010301f9c7b12778432c4f10feb10f7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246042"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Vlastní zásady ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásady jsou konfigurační soubory, které definují chování vašeho klienta Azure Active Directory B2C (Azure AD B2C). Toky uživatelů jsou předdefinované na portálu Azure AD B2C pro nejběžnější úlohy identity. Vlastní zásady mohou být plně upraveny vývojářem identity k dokončení mnoha různých úkolů.

## <a name="comparing-user-flows-and-custom-policies"></a>Porovnání toků uživatelů a vlastních zásad

| | Toky uživatele | Vlastní zásady |
|-|-------------------|-----------------|
| Cílení na uživatele | Všichni vývojáři aplikací s odbornými znalostmi identity nebo bez ní. | Profesionálové identity, systémoví integrátoři, konzultanti a interní týmy identit. Jsou spokojeni s toky OpenID Connect a rozumí poskytovatelům identit a ověřování založenému na deklaracích identit. |
| Metoda konfigurace | Portál Azure s uživatelsky přívětivým uživatelským rozhraním (UI). | Přímo upravujte soubory XML a pak se nahrajte na portál Azure. |
| Přizpůsobení uživatelského rozhraní | Úplné přizpůsobení uživatelského rozhraní včetně HTML, CSS a JavaScriptu.<br><br>Podpora více jazyků s vlastní řetězce. | Stejné |
| Přizpůsobení atributu | Standardní a vlastní atributy. | Stejné |
| Správa tokenů a relací | Vlastní token a více možností relace. | Stejné |
| Zprostředkovatelé identit | Předdefinovaný místní nebo sociální zprostředkovatel a většina poskytovatelů identitoidc, jako je například federace s tenanty Azure Active Directory. | OIDC, OAUTH a SAML založené na standardech.  Ověřování je také možné pomocí integrace s REST API. |
| Úlohy identity | Zaregistrujte se nebo se přihlaste pomocí místních nebo mnoha sociálních účtů.<br><br>Samoobslužné resetování hesla.<br><br>Úprava profilu.<br><br>Vícefaktorové ověřování.<br><br>Přizpůsobte tokeny a relace.<br><br>Toky přístupového tokenu. | Dokončete stejné úkoly jako toky uživatelů pomocí vlastních zprostředkovatelů identit nebo použijte vlastní obory.<br><br>Zřizování uživatelského účtu v jiném systému v době registrace.<br><br>Pošlete uvítací e-mail pomocí vlastního poskytovatele e-mailových služeb.<br><br>Použijte úložiště uživatelů mimo Azure AD B2C.<br><br>Ověřte informace poskytnuté uživatelem pomocí důvěryhodného systému pomocí rozhraní API. |

## <a name="policy-files"></a>Soubory zásad

Používají se tyto tři typy souborů zásad:

- **Základní soubor** - obsahuje většinu definic. Doporučujeme provést minimální počet změn tohoto souboru, které vám pomohou s řešením potíží a dlouhodobou údržbou zásad.
- **Přípony –** obsahuje jedinečné změny konfigurace pro vašeho tenanta.
- **Předávající strana (RP) soubor** - jeden soubor zaměřený na úkol, který je vyvolán přímo aplikací nebo službou (také známý jako předávající strana). Každý jedinečný úkol vyžaduje vlastní RP a v závislosti na požadavcích na branding může být číslo "celkový počet aplikací x celkový počet případů použití".

Toky uživatelů v Azure AD B2C postupujte podle vzoru tří souborů zobrazených výše, ale vývojář uvidí jenom soubor RP, zatímco portál Azure provede změny na pozadí souboru rozšíření.

## <a name="custom-policy-core-concepts"></a>Základní koncepty vlastních zásad

Služba správy identit a přístupu zákazníka (CIAM) v Azure zahrnuje:

- Uživatelský adresář, který je přístupný pomocí aplikace Microsoft Graph a který uchovává uživatelská data pro místní i federované účty.
- Přístup k **rozhraní Identity Experience Framework,** který orchestruje vztah důvěryhodnosti mezi uživateli a entitami a předává mezi nimi deklarace identity k dokončení úlohy správy identity nebo přístupu.
- Služba tokenů zabezpečení (STS), která vydává tokeny ID, tokeny aktualizace a přístupové tokeny (a ekvivalentní kontrolní výrazy SAML) a ověřuje je za účelem ochrany prostředků.

Azure AD B2C spolupracuje s poskytovateli identit, uživateli, jinými systémy a s adresářem místního uživatele v pořadí k dosažení úlohy identity. Přihlaste se například k uživateli, zaregistrujte nového uživatele nebo obnovte heslo. Rozhraní identity experience framework a zásady (také nazývané cesta uživatele nebo zásady architektury důvěryhodnosti) vytváří důvěryhodnost více stran a explicitně definuje aktéry, akce, protokoly a posloupnost kroků k dokončení.

Rozhraní Identity Experience Framework je plně konfigurovatelná cloudová platforma Azure založená na zásadách, která organizuje důvěryhodnost mezi entitami ve standardních formátech protokolu, jako je OpenID Connect, OAuth, SAML a několik nestandardních, například REST Výměny deklarací systému a systému založené na rozhraní API. Rámec vytváří uživatelsky přívětivé prostředí s bílým popiskem, které podporují html a css.

Vlastní zásada je vyjádřena jako jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchickém řetězu. Elementy XML definují schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací identity, technické profily a kroky orchestrace cesty uživatele, mimo jiné prvky. Vlastní zásady jsou přístupné jako jeden nebo několik souborů XML, které jsou spouštěny rozhraním Identity Experience Framework při vyvolání předávající stranou. Vývojáři konfigurující vlastní zásady musí pečlivě definovat důvěryhodné vztahy tak, aby zahrnovaly koncové body metadat, definice výměny přesných deklarací identity a podle potřeby nakonfigurovaly tajné klíče, klíče a certifikáty podle potřeby každého poskytovatele identity.

### <a name="inheritance-model"></a>Model dědičnosti

Když aplikace volá soubor zásad RP, rozhraní Identity Experience Framework v Azure AD B2C přidá všechny prvky ze základního souboru, ze souboru přípony a pak ze souboru zásad RP sestavit aktuální zásady v platnosti.  Prvky stejného typu a názvu v souboru RP přepíší prvky v příponech a rozšíření přepíše základnu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s vlastními zásadami](custom-policy-get-started.md)
