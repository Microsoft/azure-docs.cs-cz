---
title: "Microsoft Authenticator phone přihlášení - účty Azure a Microsoft | Microsoft Docs"
description: "Váš telefon používáte pro přihlášení ke svému účtu Microsoft místo zadávání hesla. Tento článek obsahuje nejčastější dotazy k odpovědi na tuto funkci."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 86cf6ba2f2ec86a609b4ec60cc143b07d9e542b3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Přihlaste se pomocí telefonu není heslo

Aplikace Microsoft Authenticator pomáhá vám zabezpečit vaše účty provedením dvoustupňové ověření po zadání hesla. Ale víte, že ho nahraďte heslo pro svůj osobní účet Microsoft zcela?

Tato funkce je k dispozici na zařízení iOS a Android a funguje s osobní účty Microsoft.

## <a name="how-it-works"></a>Jak to funguje

Řada z vás používat aplikaci Microsoft Authenticator pro dvoustupňové ověření při přihlášení ke svému účtu Microsoft. Zadejte heslo a potom přejděte na aplikaci buď schválit oznámení nebo získat ověřovací kód. Phone přihlášení přeskočte heslo a udělejte všechny ověření identity na váš telefon. Vzhledem k tomu, že přihlášení phone je typ dvoustupňové ověření, stále potřebujete poskytovat věcí, které znáte a věcí, budete muset ověřit vaši identitu. Telefon je stále věcí, kterou máte a PIN kód nebo biometrické klíč váš telefon je věcí, které znáte.

## <a name="how-to-get-started"></a>Jak začít

Pro přihlášení ke svému osobnímu účtu Microsoft s telefonu, postupujte takto:

1. Povolte přihlášení phone pro váš účet.

  - Pokud nemáte aplikaci Microsoft Authenticator ještě, nainstalujte a přidejte svůj osobní účet Microsoft podle pokynů na [Microsoft Authenticator stránky](microsoft-authenticator-app-how-to.md). Nově přidané účty jsou automaticky povolené, takže můžete se pustit do práce.

  - Pokud už používáte Microsoft Authenticator pro dvoustupňové ověření, vyberte svůj účet na domovské stránce aplikace a vyberte **povolit přihlášení phone** z rozevírací nabídky.

  >[!NOTE]
  >Abyste ochránili svůj účet, je nutné, kód PIN nebo biometrické zámku na vašem zařízení. Pokud necháte telefonu odemčený, aplikace se zobrazí žádost s dotazem, nastavit zámek před povolením phone přihlásit.

3. Většina stránek, které by normálně zadejte heslo účtu Microsoft mají odkaz s informacemi o tom **místo toho použít aplikaci**. Vyberte tento odkaz se přihlásit pomocí vašeho telefonu.

4. Microsoft odesílá oznámení na váš telefon. Schvalte oznámení pro přihlášení ke svému účtu.   

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Jak se přihlásit telefon bezpečnější než zadáním hesla?  

Dnes většina lidí přihlásit k webům a aplikacím pomocí uživatelského jména a hesla.  Hesla se bohužel často ztráty, krádeže nebo uhádnout hackerů. Při nastavování aplikace Microsoft Authenticator se přihlásit, vygeneruje klíč na váš telefon, který můžete odemknout svůj účet. Jsme chránit tento klíč se režim PIN kódu nebo biometrické, že už používáte na váš telefon.  Když se přihlásíte pomocí telefonu, tento klíč se používá k prokázání své identity bezpečně s dva faktory – telefonu sám a možnost Odemknout. 

Klíč, pomocí kterého je podobná klíčů používaných v Windows Hello a specifikace FIDO Alliance UAF. Vaše bio dat je pouze použít k ochraně klíče místně a je nikdy odeslat nebo uložená v cloudu. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Kde můžete použít telefon nahradit hesla, a kde stále je nutné heslo?  

V současné době phone přihlášení funkce funguje pouze s webové aplikace a služby, které jsou zapnuté osobní účty Microsoft, iOS nebo Android aplikace, které používají osobní účet Microsoft a aplikace na Windows 10, které používají osobní účet Microsoft. Při přihlášení na jednu z těchto webů nebo aplikací na stránce, kde je obvykle zadat heslo je odkaz, který je uveden **místo toho použít aplikaci**. 

Přihlášení Phone nelze použít k odemknutí počítače s Windows, XBOX nebo všechny plochy verze aplikací Microsoftu, jako je například aplikace Office v tuto chvíli.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>To nahradit dvoustupňové ověřování? Měli vypnout jeho nastavení?   

V některých případech. Pracujeme na rozšíření oblasti přihlášení telefon, ale zatím stále existují míst v ekosystému Microsoft, které ji nepodporují. Na těchto místech stále používáme dvoustupňové ověřování pro zabezpečené přihlašování. Z tohoto důvodu Ne, byste neměli vypínat dvoustupňové ověřování pro váš účet.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>V pořádku Pokud I pro můj účet zapnuté dvoustupňové ověření, je nutné schválit dvou oznámení?

Ne, můžete nebude. Přihlášení k účtu Microsoft s telefonu se počítá jako dvoustupňové ověřování. Místo zadání heslo, pak schválení oznámení prokázání své identity tím zároveň budete vědět, jak k odemčení telefonu a potom schválení oznámení. Nebude odešleme vám druhé oznámení ke schválení.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Co když I ztratíte telefon nebo nemáte s mi, jak lze zobrazit Můj účet?  

Vždy můžete kliknout na **místo toho použít heslo** na stránce přihlášení přejít zpět k používání heslo. Uvědomte si, že pokud používáte dvoustupňové ověření, je stále nutné druhé metody ověření vaše přihlášení. Proto důrazně doporučujeme, abyste měli jistotu, že máte navíc, aktuální bezpečnostní údaje na vašem účtu. Můžete spravovat vaše bezpečnostní údaje na adrese https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Jak přestat používat tuto funkci a vraťte se zpátky a zadáním hesla?

Klikněte na tlačítko **místo toho použít heslo** při přihlašování. Jsme mějte na paměti, poslední volba a nabízejí který ve výchozím nastavení při příštím přihlášení. Pokud někdy budete chtít vrátit k přihlášení pomocí telefonu, klikněte na tlačítko **místo toho použít aplikaci**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Můžete použít aplikaci pro přihlášení ke všem účtům se společností Microsoft?   
Tato funkce je k dispozici pro osobní účty Microsoft pouze v tuto chvíli. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Může I Přihlaste se k počítači s telefon?  
U počítačů doporučujeme, abyste přihlášení pomocí Windows Hello ve Windows 10 pomocí vaší řez, otisk prstu nebo PIN kód.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Můžete Moje Windows Phone přihlásit?  
V tuto chvíli jsme nejsou vývoj tuto funkci pro Microsoft Authenticator na Windows Phone. 

## <a name="next-steps"></a>Další postup
Pokud jste ještě nestáhli aplikaci Microsoft Authenticator, ji rezervovat. Aplikace je k dispozici pro [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), a přihlášení phone jsou dostupné v aplikaci Microsoft Authenticator pro [Android](http://go.microsoft.com/fwlink/?Linkid=825072) a [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Pokud máte dotazy týkající se aplikace obecně, podívejte se na [Microsoft Authenticator nejčastější dotazy](microsoft-authenticator-app-faq.md)
