---
title: Microsoft Authenticator přihlašování telefonem - účtů Azure a Microsoft | Dokumentace Microsoftu
description: Váš telefon používáte k přihlášení ke svému účtu Microsoft místo zadávání hesla. Tento článek obsahuje nejčastější dotazy k odpovědi informace o této funkci.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/12/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 589309c956ebbbebe6c423cfcca117a86c796be3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060114"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Přihlášení pomocí telefonu, ne pomocí hesla

Aplikace Microsoft Authenticator pomáhá vám zabezpečit vaše účty pomocí dvoustupňového ověření po zadání hesla. Ale víte, že ho můžete zcela nahradit heslo pro svůj osobní účet Microsoft?

Tato funkce je k dispozici v Iosu a zařízení s Androidem a funguje s osobní účty Microsoft.

## <a name="how-it-works"></a>Jak to funguje

Celá řada z vás pomocí aplikace Microsoft Authenticator pro dvoustupňové ověřování při přihlašování ke svému účtu Microsoft. Zadejte heslo a pak přejděte do aplikace a schválit oznámení nebo získat kód pro ověření. Pomocí přihlášení telefonem přeskočte heslo a proveďte ověření identity na váš telefon. Protože přihlašování telefonem je typem dvoustupňové ověření, potřebujete stále poskytují věcí, kterou budete vědět a to je nutné ověřit vaši identitu. Telefon je stále věcí, kterou máte a PIN kód nebo biometrické klíč váš telefon je věcí, které už znáte.

## <a name="how-to-get-started"></a>Jak začít

Přihlásit se ke svému osobnímu účtu Microsoft s telefonem, postupujte podle těchto kroků:

1. Povolte přihlašování telefonem pro váš účet.

  - Pokud nemáte aplikaci Microsoft Authenticator, nainstalujte a přidejte svůj osobní účet Microsoft podle pokynů na [stránky Microsoft Authenticator](microsoft-authenticator-app-how-to.md). Nově přidané účty jsou automaticky povolené, takže všechno je připravené.

  - Pokud už používáte Microsoft Authenticator pro dvoustupňové ověření, vyberte svůj účet na domovské stránce aplikace a vyberte **povolit přihlašování telefonem** z rozevírací nabídky.

  >[!NOTE]
  >Pokud chcete chránit svůj účet, požadujeme, aby PIN kód nebo biometrické zámek na vašem zařízení. Pokud váš telefon odemčený, aplikace se zobrazí požadavek s výzvou k nastavení zámku před povolením přihlašování telefonem.

3. Většina stránky, kde by normálně zadat heslo ke svému účtu Microsoft mají odkaz, který uvádí, že **použít raději aplikaci**. Vyberte tento odkaz na přihlášení pomocí telefonu.

4. Microsoft pošle oznámení na váš telefon. Schvalte oznámení pro přihlášení ke svému účtu.   

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>Jak přihlášení pomocí telefonu bezpečnější než zadáním hesla?  

Dnes většina lidí přihlášení k webové stránky nebo aplikace pomocí uživatelského jména a hesla.  Hesla se bohužel často ztráty, krádeže nebo uhodnout hackerů. Při nastavování aplikace Microsoft Authenticator pro přihlášení, jsme vygenerovat klíč v telefonu můžete odemknout svůj účet. Budeme chránit tento klíč se biometrické, že už používáte na svém telefonu nebo PIN.  Když jste přihlášení pomocí telefonu, tento klíč se používá k prokázání své identity bezpečně pomocí dvou faktorů – telefon samotného a schopností k jeho odemčení. 

Klíč, pomocí kterého se podobá klíče používané ve Windows Hello a FIDO Alliance UAF specifikací. Vaše životopis dat je jenom použitým k zamknutí klíč místně a je nikdy odeslaných a uložená v cloudu. 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>Kde lze použít mi na telefon k nahrazení hesla, a kde stále je třeba heslo?  

V současné době phone přihlášení funkce funguje pouze s webovými aplikacemi a službami, které využívají osobní účty Microsoft, zařízení s iOS nebo Android aplikace, které používají osobní účet Microsoft a aplikace ve Windows 10, které používají osobní účet Microsoft. Když se přihlásí do jednoho z těchto webů nebo aplikací na stránce, kde je obvykle zadat vaše heslo je odkaz, který uvádí, že **použít raději aplikaci**. 

K odemknutí počítače s Windows, XBOX a všechny desktopové verze této aplikace Microsoftu, třeba aplikace Office v tuto chvíli nelze použít přihlášení telefonem.
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>To nahradit dvoustupňové ověřování? Doporučujeme zapnout ho?   

V některých případech. Pracujeme na rozšiřování obor přihlášení telefonem, ale zatím stále existují míst v ekosystému Microsoft, na které ho nepodporují. Na těchto místech stále používáme dvoustupňové ověřování pro zabezpečené přihlašování. Z tohoto důvodu Ne, by neměla vypnete dvoustupňové ověřování pro váš účet.
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>Dobře pokud mám pro svůj účet zapnuté dvoustupňové ověřování, nutné schválení dvě oznámení?

Ne, které nebudou. Přihlášení k účtu Microsoft v telefonu se počítá jako dvoustupňové ověřování. Místo zadání své heslo, potom schválit oznámení prokážete svou identitu vědět, jak odemčení telefonu a potom schválit oznámení. Nebude pošleme vám druhé oznámení schválit.

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>Co když mám ztratíte telefon nebo nemají se se mnou, jak můžete I přístup ke svému účtu?  

Vždy můžete kliknout na **místo toho použít heslo** na přihlašovací stránku chcete přejít zpátky k použití hesla. Uvědomte si, že pokud používáte dvoustupňové ověřování, stále potřebujete druhou metodu k ověření přihlášení. Proto důrazně doporučujeme, abyste měli jistotu, že máte navíc a aktuální bezpečnostní údaje na vašem účtu. Můžete spravovat bezpečnostní údaje na adrese https://account.live.com/proofs/manage.
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>Jak přestat používat tuto funkci a vraťte se k zadání hesla?

Klikněte na tlačítko **místo toho použít heslo** při přihlášení. Mějte na paměti nejnovější podle vašeho výběru jsme nabídky, která ve výchozím nastavení při příštím přihlášení. Pokud někdy budete chtít vrátit k přihlášení pomocí telefonu, klikněte na tlačítko **použít raději aplikaci**. 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>Můžete použít aplikaci pro přihlášení ke všem účtům s Microsoftem   
Tato funkce dostupná jenom pro osobní účty Microsoft v tuto chvíli. 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>Můžete zaregistrovat do počítače se mi na telefon?  
Pro počítače doporučujeme přihlášení pomocí Windows Hello ve Windows 10 pomocí vaší pro rozpoznávání tváře, otisk prstu nebo PIN kód.   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>Můžu se přihlásit pomocí své Windows Phone?  
V tuto chvíli jsme nejsou vývoj této funkce pro aplikaci Microsoft Authenticator na Windows Phone. 

## <a name="next-steps"></a>Další postup
Pokud jste nestáhli aplikaci Microsoft Authenticator, projděte si. Aplikace je k dispozici pro [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), a přihlašování telefonem je k dispozici v aplikaci Microsoft Authenticator pro [Android](http://go.microsoft.com/fwlink/?Linkid=825072) a [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Pokud máte dotazy týkající se aplikace obecně platí, podívejte se na [nejčastější dotazy k aplikaci Microsoft Authenticator](microsoft-authenticator-app-faq.md)
