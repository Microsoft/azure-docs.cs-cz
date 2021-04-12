---
title: Nejčastější dotazy – ověřené přihlašovací údaje Azure (Preview)
description: Najít odpovědi na časté otázky týkající se ověřitelných přihlašovacích údajů
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280713"
---
# <a name="frequently-asked-questions-faq"></a>Časté otázky

Tato stránka obsahuje nejčastější dotazy týkající se ověřitelných přihlašovacích údajů a decentralizované identity. Otázky jsou uspořádány do následujících částí.

- [Slovník a základy](#the-basics)
- [Koncepční otázky týkající se decentralizované identity](#conceptual-questions)
- [Dotazy týkající se používání ověřitelných přihlašovacích údajů ve verzi Preview](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Základy

### <a name="what-is-a-did"></a>Co je to? 

Decentralizovaná identifiers (DIDs) jsou identifikátory, které se dají použít k zabezpečení přístupu k prostředkům, k podepisování a ověření přihlašovacích údajů a k usnadnění výměny dat aplikací. Na rozdíl od tradičních uživatelských jmen a e-mailových adres jsou DIDs vlastněné a řízené vlastní entitou (Jedná se o osobu, zařízení nebo společnost). DIDs existují nezávisle na žádné externí organizaci nebo na důvěryhodném prostředníci. Podrobnosti [decentralizovaného identifikátoru W3C](https://www.w3.org/TR/did-core/) tyto informace podrobněji vysvětlují.

### <a name="why-do-we-need-a-did"></a>Proč potřebujeme nějakého?

Digitální vztah důvěryhodnosti v podstatě vyžaduje, aby účastníci mohli vlastnit a řídit své identity, a identita začíná na identifikátoru.
V rámci denního stáří a útoků ve velkém měřítku v Honeypots centralizovaném identifikátorem je decentralizace identity velmi kritická potřeba pro zákazníky a firmy.
Uživatelé vlastnící a řídící své identity mohou vyměňovat data a ověření. Distribuované prostředí přihlašovacích údajů umožňuje automatizovat mnoho obchodních procesů, které jsou aktuálně ruční a náročné na práci.

### <a name="what-is-a-verifiable-credential"></a>Co je ověřitelné přihlašovací údaje? 

Přihlašovací údaje jsou součástí každodenního života. licence ovladače se používají k vyhodnocení toho, že je možné provozovat motorové vozidlo, vysoké úrovně školy se dají použít k vyhodnocení naší úrovně vzdělávání a passportům vydaným státním zákazníkům, abychom mohli cestovat mezi zeměmi. Ověřitelné přihlašovací údaje poskytují mechanismus, který umožňuje vyjádřit tyto přihlašovací údaje na webu způsobem, který je kryptograficky zabezpečen, dodržování ochrany osobních údajů a počítačově ověřitelný. [Specifikace ověřovatele ověřitelných přihlašovacích údajů W3C](https://www.w3.org/TR/vc-data-model//) podrobněji vysvětluje.


## <a name="conceptual-questions"></a>Koncepční otázky

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Co se stane, když uživatel ztratí svůj telefon? Můžou obnovit svoji identitu?

K dispozici je několik způsobů, jak pro uživatele nabídnout mechanismus obnovení, z nichž každá má vlastní kompromisy. Aktuálně vyhodnocujeme možnosti a navrhujeme přístupy k obnovení, které nabízí pohodlí a zabezpečení při respektování ochrany osobních údajů uživatelů a jejich soběstačnosti.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Jak může uživatel důvěřovat žádosti od vystavitele nebo ověřovatele? Jak ví, že je to skutečně u organizace?

Implementovali jsme [dobře známou specifikaci konfigurace s decentralizovaným rozhraním Identity Foundation](https://identity.foundation/.well-known/resources/did-configuration/) , aby bylo možné připojit objekt k velmi známému stávajícímu systému, názvům domén. Každý vytvořený pomocí Azure Active Directory ověřitelných přihlašovacích údajů má možnost zahrnout název kořenové domény, který bude kódovaný v dokumentu. Pokud se chcete dozvědět víc, postupujte podle článku s názvem [propojení vaší domény s vaším distribuovaným identifikátorem](how-to-dnsbind.md) .  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Proč je ve verzi Preview ověřovacích přihlašovacích údajů použito IONTy jako jeho metoda, a proto Bitcoin k zajištění decentralizované infrastruktury veřejných klíčů?

ION je decentralizovaná, bez oprávnění, škálovatelná síť vrstvy 2 s neoprávněným decentralizovaným identifikátorem, která spouští základem Bitcoin. Dosahuje škálovatelnosti bez zahrnutí speciálního cryptoasset tokenu, důvěryhodných validátorů nebo mechanismů centralizovaných konsensu. Bitcoin se používá pro nosiče základní vrstvy 1 z důvodu síly decentralizované sítě, která poskytuje vysoký stupeň neměnnosti pro systém záznamů chronologických událostí.

## <a name="using-the-preview"></a>Používání verze Preview

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Proč je nutné použít NodeJS k ověření přihlašovacích údajů ve verzi Preview? Jakékoli plány pro jiné programovací jazyky? 

Zvolili jsme NodeJS, protože se jedná o velmi oblíbenou platformu pro vývojáře aplikací. Budeme vydávat rozhraní REST API, které vývojářům umožní vystavovat a ověřit přihlašovací údaje. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Je některý z kódu, který se používá v open source Preview?

Ano. V následujících úložištích jsou otevřené zdrojové komponenty našich služeb.

1. [SideTree, na GitHubu](https://github.com/decentralized-identity/sidetree)
2. [Sada VC SDK for Node, na GitHubu](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [Android SDK pro sestavování decentralizovaných kapesních identit na GitHubu](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [Sada iOS SDK pro sestavování decentralizovaných kapesních identit, na GitHubu](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Jaké jsou licenční požadavky?

K používání verze Preview ověřitelných přihlašovacích údajů se vyžaduje licence Azure AD P2. To je dočasný požadavek, protože očekáváme, že se ceny za tuto službu účtují na základě využití. 


## <a name="next-steps"></a>Další kroky

- [Postup přizpůsobení Azure Active Directory ověřitelných přihlašovacích údajů](credential-design.md)
