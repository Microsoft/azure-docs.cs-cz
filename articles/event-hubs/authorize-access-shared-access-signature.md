---
title: Autorizace přístupu pomocí sdíleného přístupového podpisu v Azure Event Hubs
description: Tento článek obsahuje informace o autorizaci přístupu k prostředkům Centra událostí Azure pomocí sdílených přístupových podpisů (SAS).
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992793"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizace přístupu k prostředkům centra událostí pomocí sdílených přístupových podpisů
Sdílený přístupový podpis (SAS) poskytuje způsob, jak udělit omezený přístup k prostředkům v oboru názvů Event Hubs. SAS chrání přístup k prostředkům Centra událostí na základě pravidel autorizace. Tato pravidla jsou konfigurována buď v oboru názvů, nebo v entitě (rozbočovač událostí nebo téma). Tento článek obsahuje přehled modelu SAS a recenze SAS osvědčené postupy.

## <a name="what-are-shared-access-signatures"></a>Co jsou sdílené přístupové podpisy?
Sdílený přístupový podpis (SAS) poskytuje delegovaný přístup k prostředkům centra událostí na základě autorizačních pravidel. Autorizační pravidlo má název, je přidruženo k určitým právům a nese dvojici kryptografických klíčů. Název a klíč pravidla se používají prostřednictvím klientů Event Hubs nebo ve vlastním kódu ke generování tokenů SAS. Klient pak může předat token do centra událostí, aby prokázal autorizaci pro požadovanou operaci.

SAS je mechanismus autorizace založený na deklaracích pomocí jednoduchých tokenů. Pomocí SAS klíče nejsou nikdy předány na drátě. Klíče se používají ke kryptograficky podepisovat informace, které mohou být později ověřeny službou. SAS lze použít podobně jako uživatelské jméno a heslo schéma, kde klient je v bezprostředním držení název pravidla autorizace a odpovídající klíč. SAS lze použít podobně jako federovaný model zabezpečení, kde klient obdrží časově omezený a podepsaný přístupový token ze služby tokenů zabezpečení, aniž by někdy převzal podpisový klíč.

> [!NOTE]
> Azure Event Hubs podporuje autorizaci prostředků Event Hubs pomocí Azure Active Directory (Azure AD). Autorizace uživatelů nebo aplikací pomocí tokenu OAuth 2.0 vráceného službou Azure AD poskytuje vynikající zabezpečení a snadné použití oproti sdíleným přístupovým podpisům (SAS). S Azure AD, není nutné ukládat tokeny ve vašem kódu a riskovat potenciální ohrožení zabezpečení.
>
> Společnost Microsoft doporučuje používat Azure AD s aplikacemi Azure Event Hubs, pokud je to možné. Další informace najdete [v tématu Autorizace přístupu k prostředkům Centra událostí Azure pomocí služby Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Tokeny SAS (sdílené přístupové podpisy) jsou důležité pro ochranu vašich prostředků. Při poskytování rozlišovací schopnost, SAS uděluje klientům přístup k prostředkům Centra událostí. Neměly by být veřejně sdíleny. Při sdílení, pokud je to nutné z důvodu řešení potíží, zvažte použití redukované verze všech souborů protokolu nebo odstranění tokenů SAS (pokud jsou k dispozici) ze souborů protokolu a ujistěte se, že snímky obrazovky neobsahují informace SAS.

## <a name="shared-access-authorization-policies"></a>Zásady autorizace sdíleného přístupu
Každý obor názvů Event Hubs a každá entita Event Hubs (instance centra událostí nebo téma Kafka) má zásady autorizace sdíleného přístupu tvořené pravidly. Zásady na úrovni oboru názvů platí pro všechny entity uvnitř oboru názvů, bez ohledu na jejich individuální konfiguraci zásad.
Pro každé pravidlo zásad autorizace se rozhodnete pro tři informace: název, obor a práva. Název je jedinečný název v tomto oboru. Obor je identifikátor URI daného prostředku. Pro obor názvů Event Hubs je obor plně kvalifikovaný název domény (FQDN), například `https://<yournamespace>.servicebus.windows.net/`.

Práva poskytovaná pravidlem zásad mohou být kombinací:
- **Odeslat** – dává právo odesílat zprávy entitě
- **Poslech** - dává právo naslouchat nebo přijímat na subjektu
- **Správa** – uděluje právo spravovat topologii oboru názvů, včetně vytváření a odstraňování entit.

> [!NOTE]
> Právo **Spravovat** zahrnuje práva **pro odesílání** a **naslouchání.**

Obor názvů nebo zásady entity mohou obsahovat až 12 pravidel autorizace sdíleného přístupu, která poskytují prostor pro tři sady pravidel, z nichž každá zahrnuje základní práva a kombinaci odeslat a naslouchat. Toto omezení zdůrazňuje, že úložiště zásad SAS není určeno jako úložiště uživatelských účtů nebo účtů služeb. Pokud vaše aplikace potřebuje udělit přístup k prostředkům Centra událostí na základě identit uživatelů nebo služeb, měla by implementovat službu tokenů zabezpečení, která vydává tokeny SAS po ověření a kontrole přístupu.

Autorizačnímu pravidlu je přiřazen **primární a** **sekundární klíč**. Tyto klíče jsou kryptograficky silné klíče. Neztraťte je ani je neprosakujte. Budou vždycky dostupné na webu Azure Portal. Můžete použít některý z generovaných klíčů a můžete je kdykoli znovu vygenerovat. Pokud znovu vygenerujete nebo změníte klíč v zásadách, všechny dříve vydané tokeny založené na tomto klíči se okamžitě stanou neplatnými. Probíhající připojení vytvořená na základě těchto tokenů však budou pokračovat v práci, dokud platnost tokenu nevyprší.

Při vytváření oboru názvů Event Hubs je pro obor názvů automaticky vytvořeno pravidlo zásady s názvem **RootManageSharedAccessKey.** Tato zásada má **oprávnění ke správě** pro celý obor názvů. Doporučujeme zacházet s tímto pravidlem jako s kořenovým účtem pro správu a nepoužívat ho ve vaší aplikaci. Další pravidla zásad můžete vytvořit na **kartě Konfigurace** pro obor názvů na portálu, přes PowerShell nebo Azure CLI.

## <a name="best-practices-when-using-sas"></a>Osvědčené postupy při používání SAS
Při použití sdílených přístupových podpisů v aplikacích je třeba si být vědom a vědomy se dvou potenciálních rizik:

- Pokud je únik u SAS, může ji použít každý, kdo ji získá, což může potenciálně ohrozit vaše prostředky Centra událostí.
- Pokud vyprší platnost SAS poskytované klientské aplikaci a aplikace nemůže načíst nový SAS z vaší služby, pak může být bráněno funkcím aplikace.

Následující doporučení pro použití sdílených přístupových podpisů mohou pomoci zmírnit tato rizika:

- **Klienti v případě potřeby automaticky obnovují SAS**: Klienti by měli obnovit SAS ještě před vypršením platnosti, aby měli čas na opakování, pokud služba poskytující SAS není k dispozici. Pokud vaše SAS je určen pro malý počet okamžité, krátkodobé operace, které se očekává, že bude dokončena v rámci období vypršení platnosti, pak může být zbytečné, protože sas se neočekává, že bude obnovena. Pokud však máte klienta, který běžně podává žádosti prostřednictvím sas, pak se do hry dostane možnost vypršení platnosti. Klíčovým aspektem je vyvážit potřebu SAS být krátkodobý (jak již bylo uvedeno) s potřebou zajistit, že klient žádá o obnovení dostatečně brzy (aby se zabránilo narušení z důvodu SAS končí před úspěšným obnovením).
- **Buďte opatrní s časem spuštění SAS**: Pokud nastavíte čas spuštění pro SAS **nyní**, pak z důvodu zkosení hodin (rozdíly v aktuálním čase podle různých počítačů), selhání může být pozorováno přerušovaně po dobu prvních několika minut. Obecně platí, že nastavte čas zahájení alespoň 15 minut v minulosti. Nebo ji vůbec nenastavujte, což bude ve všech případech okamžitě platné. Totéž obecně platí i pro dobu vypršení platnosti. Nezapomeňte, že můžete pozorovatele až 15 minut hodiny zkosení v obou směrech na jakýkoli požadavek. 
- **Buďte konkrétní s prostředkem, ke kterým má být přistupujete**: Osvědčeným postupem zabezpečení je poskytnout uživateli minimální požadovaná oprávnění. Pokud uživatel potřebuje pouze přístup pro čtení k jedné entitě, udělte mu přístup pro čtení k této jedné entitě a nepřístup pro čtení, zápis/odstranění všem entitám. Pomáhá také snížit poškození, pokud je ohrožena SAS, protože SAS má méně energie v rukou útočníka.
- **Nepoužívejte vždy SAS**: Někdy rizika spojená s konkrétní operace proti event huby převažují nad výhodami SAS. Pro tyto operace vytvořte službu střední vrstvy, která zapisuje do centra událostí po ověření, ověřování a auditování obchodních pravidel.
- **Vždy používejte HTTPs**: Vždy používejte protokol Https k vytvoření nebo distribuci SAS. Pokud je SAS předán přes HTTP a zachycen, útočník provádějící man-in-the-middle připojit je schopen číst SAS a pak použít stejně jako zamýšlený uživatel mohl mít, potenciálně ohrožení citlivých dat nebo umožňující poškození dat uživatelem se zlými úmysly.

## <a name="conclusion"></a>Závěr
Podpisy přístupu ke sdílení jsou užitečné pro poskytování omezených oprávnění k prostředkům Centra událostí vašim klientům. Jsou důležitou součástí modelu zabezpečení pro všechny aplikace pomocí Azure Event Hubs. Pokud budete postupovat podle osvědčených postupů uvedených v tomto článku, můžete použít SAS poskytnout větší flexibilitu přístupu k prostředkům, aniž by byla ohrožena zabezpečení vaší aplikace.

## <a name="next-steps"></a>Další kroky
Podívejte se na následující související články: 

- [Ověřování požadavků na Centra událostí Azure z aplikace pomocí Služby Azure Active Directory](authenticate-application.md)
- [Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí](authenticate-managed-identity.md)
- [Ověřování požadavků na Centra událostí Azure pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
- [Autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory](authorize-access-azure-active-directory.md)


