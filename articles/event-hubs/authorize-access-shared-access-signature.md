---
title: Autorizace přístupu pomocí sdíleného přístupového podpisu v Azure Event Hubs
description: Tento článek poskytuje informace o autorizaci přístupu k prostředkům Azure Event Hubs pomocí sdílených přístupových podpisů (SAS).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 6a2d7385f82864e8d378055333377fb9c3f73c19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85323113"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů
Sdílený přístupový podpis (SAS) poskytuje způsob, jak udělit omezený přístup k prostředkům v oboru názvů Event Hubs. SAS chrání přístup k Event Hubs prostředkům na základě autorizačních pravidel. Tato pravidla se konfigurují buď v oboru názvů, nebo v entitě (centra událostí nebo téma). Tento článek poskytuje přehled modelu SAS a kontroluje osvědčené postupy SAS.

## <a name="what-are-shared-access-signatures"></a>Co jsou signatury sdíleného přístupu?
Sdílený přístupový podpis (SAS) poskytuje delegovaný přístup k Event Hubs prostředkům na základě autorizačních pravidel. Autorizační pravidlo má název, je spojeno s konkrétními právy a přináší dvojici kryptografických klíčů. Název a klíč pravidla použijete prostřednictvím klientů Event Hubs nebo ve vlastním kódu pro generování tokenů SAS. Klient pak může token předat Event Hubs, aby prokáže autorizaci pro požadovanou operaci.

SAS je mechanismus ověřování založený na deklaracích, který využívá jednoduché tokeny. Pomocí SAS se klíče nikdy nepředávají na lince. Klíče se používají pro kryptografické podepisování informací, které může služba později ověřit. SAS se dá použít podobně jako uživatelské jméno a schéma hesla, kde je klient v bezprostředním vlastnictví názvu autorizačního pravidla a odpovídajícího klíče. SAS se dá použít podobně jako model federovaného zabezpečení, kde klient obdrží token zabezpečení s omezeným časem a podpisem ze služby tokenu zabezpečení, aniž by musel mít k dispozici podpisový klíč.

> [!NOTE]
> Azure Event Hubs podporuje autorizaci pro Event Hubs prostředků pomocí Azure Active Directory (Azure AD). Ověřování uživatelů nebo aplikací pomocí tokenu OAuth 2,0 vráceného službou Azure AD poskytuje vynikající zabezpečení a usnadňuje použití přes sdílené přístupové podpisy (SAS). V případě Azure AD není nutné ukládat tokeny do kódu a ohrozit potenciální ohrožení zabezpečení.
>
> Pokud je to možné, Microsoft doporučuje používat Azure AD s vašimi aplikacemi Azure Event Hubs. Další informace najdete v tématu [autorizace přístupu k prostředkům Azure Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Tokeny SAS (Shared Access Signaturs) jsou klíčové k ochraně vašich prostředků. Při poskytování členitosti uděluje SAS klientům přístup k prostředkům Event Hubs. Neměly by se sdílet veřejně. V případě potřeby sdílení, pokud je to vyžadováno z důvodů řešení potíží, zvažte použití omezené verze všech souborů protokolu nebo odstranění tokenů SAS (Pokud je k dispozici) ze souborů protokolu a ujistěte se, že snímky obrazovky neobsahují informace o SAS.

## <a name="shared-access-authorization-policies"></a>Zásady autorizace sdíleného přístupu
Každý Event Hubs obor názvů a každá Event Hubs entita (instance centra událostí nebo Kafka) má zásady autorizace sdíleného přístupu vytvořené pravidly. Zásady na úrovni oboru názvů platí pro všechny entity v oboru názvů bez ohledu na jejich jednotlivé konfigurace zásad.
U každého pravidla zásad autorizace se rozhodujete o třech údajích, které se týkají názvu, oboru a práv. Název je jedinečný název v daném oboru. Rozsah je identifikátor URI daného prostředku. V případě oboru názvů Event Hubs obor je plně kvalifikovaný název domény (FQDN), například `https://<yournamespace>.servicebus.windows.net/` .

Práva poskytnutá pravidlem zásad mohou být kombinací:
- **Send** – poskytuje právo odesílat zprávy entitě.
- **Poslech** – udělí právo naslouchat nebo přijímat entitě.
- **Spravovat** – poskytuje právo ke správě topologie oboru názvů, včetně vytváření a odstraňování entit.

> [!NOTE]
> Právo **Spravovat** zahrnuje práva pro **odesílání** a **příjem** .

Zásady oboru názvů nebo entit můžou obsahovat až 12 autorizačních pravidel přístupu a poskytují prostor pro tři sady pravidel, každý pokrývá základní práva a kombinaci odesílání a naslouchat. Tato mezní hodnota je podtržená, protože úložiště zásad SAS není určené pro uživatele nebo úložiště účtů služeb. Pokud vaše aplikace potřebuje udělit přístup k Event Hubs prostředkům na základě identit uživatelů nebo služeb, měli byste implementovat službu tokenů zabezpečení, která vydává tokeny SAS po ověření a kontrole přístupu.

Autorizačnímu pravidlu je přiřazen **primární klíč** a **sekundární klíč**. Tyto klíče jsou kryptograficky silné klíče. Neztratí je nebo je neprovádějte. Budou vždy k dispozici v Azure Portal. Můžete použít buď vygenerované klíče, a můžete je kdykoli znovu vygenerovat. Pokud znovu vygenerujete nebo změníte klíč v zásadě, všechny dříve vydané tokeny na základě tohoto klíče se okamžitě stanou neplatnými. Probíhající připojení vytvořená na základě těchto tokenů ale budou fungovat i nadále, dokud nevyprší platnost tokenu.

Když vytvoříte obor názvů Event Hubs, pro obor názvů se automaticky vytvoří pravidlo zásad s názvem **RootManageSharedAccessKey** . Tato zásada má oprávnění ke **správě** pro celý obor názvů. Doporučuje se toto pravidlo zakládat jako kořenový účet pro správu a nepoužívat ho v aplikaci. Další pravidla zásad můžete vytvořit na kartě **Konfigurace** pro obor názvů na portálu prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure CLI.

## <a name="best-practices-when-using-sas"></a>Osvědčené postupy při používání SAS
Pokud používáte sdílené přístupové podpisy ve svých aplikacích, je nutné mít na paměti dvě možná rizika:

- Pokud se SAS nevrátí, může ho použít kdokoli, kdo ho získá, což může potenciálně ohrozit vaše Event Hubs prostředky.
- Pokud platnost SAS poskytnutá klientské aplikaci vyprší a aplikace nebude moct z vaší služby načíst nové přidružení zabezpečení, může být narušena funkčnost aplikace.

Následující doporučení pro použití sdílených přístupových podpisů pomáhají zmírnit tato rizika:

- **Klienti automaticky Obnovují SAS v případě potřeby**: klienti by měli obnovit správné zabezpečení SAS před vypršením platnosti, aby bylo možné pokusy o opakování, pokud služba poskytující SAS není k dispozici. Pokud se má vaše SAS použít pro malý počet okamžitých, krátkodobých operací, které se mají dokončit v rámci období vypršení platnosti, může být zbytečné, protože se neočekává obnovení SAS. Pokud ale máte klienta, který provádí zpracování požadavků prostřednictvím SAS, pak se možnost vypršení platnosti stane hrát. Klíčovým aspektem je vyrovnávání nutnosti, aby SAS bylo krátkodobé (jak bylo uvedeno dříve), aby se zajistilo, že klient požaduje prodloužení na včas. (aby nedocházelo k přerušení, protože platnost SAS vypršela před úspěšným obnovením).
- **Buďte opatrní při počátečním čase SAS**: Pokud nastavíte čas zahájení pro SAS na **nyní**, pak se chyby můžou považovat za občasně po dobu prvních pár minut. V části Obecné nastavte čas spuštění aspoň 15 minut v minulosti. Nebo ji vůbec nenastavte, což zajistí, že bude platit okamžitě ve všech případech. Totéž platí i pro čas vypršení platnosti. Mějte na paměti, že v obou směrech každé žádosti můžete sledovat až 15 minut hodinového zkosení. 
- **Být specifické pro přístup k prostředku**: osvědčeným postupem zabezpečení je poskytnout uživateli minimální požadovaná oprávnění. Pokud uživatel potřebuje k jedné entitě oprávnění ke čtení, přidělte jim přístup pro čtení k této jedné entitě, a ne přístup pro čtení, zápis a odstranění u všech entit. Pomáhá také snížit škodu v případě ohrožení zabezpečení SAS, protože SAS má méně energie jako útočník.
- **Nepoužívejte vždy SAS**: někdy rizika spojená s určitou operací s vaším Event Hubs převažují nad výhodami SAS. U takových operací vytvořte službu střední vrstvy, která po ověření obchodního pravidla, ověřování a auditování zapisuje do vaší Event Hubs.
- **Vždy používat protokol HTTPS**: k vytvoření nebo distribuci SAS použijte vždy protokol HTTPS. Pokud se SAS předává přes protokol HTTP a zachytává, útočník, který provádí připojení prostředníkem, je schopen přečíst SAS a pak ho použít stejným způsobem jako zamýšlený uživatel, potenciálně ohrozit citlivá data nebo umožnit poškození dat uživatelem se zlými úmysly.

## <a name="conclusion"></a>Závěr
Signatury přístupu pro sdílení jsou užitečné pro poskytování omezených oprávnění pro Event Hubs prostředků vašim klientům. Jsou zásadní součástí modelu zabezpečení pro libovolnou aplikaci, která používá Azure Event Hubs. Pokud budete postupovat podle osvědčených postupů uvedených v tomto článku, můžete použít SAS k zajištění větší flexibility přístupu k prostředkům, aniž by došlo k narušení zabezpečení aplikace.

## <a name="next-steps"></a>Další kroky
Podívejte se na následující související články: 

- [Ověřování požadavků do Azure Event Hubs z aplikace pomocí Azure Active Directory](authenticate-application.md)
- [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authenticate-managed-identity.md)
- [Ověřování požadavků na Azure Event Hubs pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
- [Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md)


