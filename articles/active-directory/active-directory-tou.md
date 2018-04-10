---
title: Funkce Azure Active Directory Terms of Use | Dokumentace Microsoftu
description: Funkce Azure AD Terms of Use umožní vám a vaší společnosti poskytnout podmínky použití uživatelům služeb Azure AD.
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/29/2018
ms.author: billmath
ms.openlocfilehash: 208a65c09b13acad62c9b6d8e55b6050041c9f5d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funkce Azure Active Directory Terms of Use
Azure AD Terms of Use poskytuje organizacím jednoduchý způsob předkládání informací koncovým uživatelům.  Toto předkládání zajistí, že se uživatelům zobrazí příslušná právní omezení pro zákonné požadavky nebo požadavky dodržování předpisů.

K předkládání obsahu funkce Azure AD Terms of Use využívá formát PDF.   Soubor PDF může mít libovolný obsah, jako jsou například stávající smluvní dokumenty, a umožňuje od koncových uživatelů při přihlášení získat jejich souhlas.  Můžete využít podmínky použití pro aplikace, skupiny uživatelů nebo pokud máte více sad podmínek použití pro různé účely.

Zbývající část tohoto dokumentu popisuje, jak funkci Azure AD Terms of Use začít využívat.  

## <a name="why-use-azure-ad-terms-of-use"></a>Pro používat funkci Azure AD Terms of Use
Je pro vás obtížné získat od zaměstnanců nebo hostů souhlas s vašimi podmínkami použití, než získají přístup? Potřebujete pomoc při zjišťování, kdo souhlasil nebo nesouhlasil s vašimi firemními podmínkami použití?  Azure AD Terms of Use poskytuje organizacím jednoduchý způsob předkládání informací koncovým uživatelům.  Toto předkládání zajistí, že se jim zobrazí příslušná právní omezení pro zákonné požadavky nebo požadavky dodržování předpisů.

Funkci Azure AD Terms of Use je možné využít v následujících scénářích:
-   Obecné podmínky použití pro všechny uživatele ve vaší organizaci.
-   Konkrétní podmínky použití založené na atributech uživatelů (např. doktoři vs. sestry nebo místní vs. zahraniční pracovníci, realizované prostřednictvím [dynamických skupin](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)).
-   Konkrétní podmínky použití založené na přístupu k aplikacím s velkým vlivem na chod firmy, jako je například Salesforce.


## <a name="prerequisites"></a>Požadavky
Při konfiguraci funkce Azure AD Terms of Use použijte následující kroky:

1. Přihlaste se k Azure AD jako globální správce, správce zabezpečení nebo správce podmíněného přístupu pro adresář, pro který chcete funkci Azure AD Terms of Use nakonfigurovat.
2. Zkontrolujte, že tento adresář má předplatné Azure AD Premium P1, P2, EMS E3 nebo EMS E5.  Pokud ne, [získejte předplatné služby Azure AD Premium](active-directory-get-started-premium.md) nebo [začněte se zkušební verzí](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Prohlédněte si řídicí panel funkce Azure AD Terms of Use na adrese [https://aka.ms/catou](https://aka.ms/catou).

>[!IMPORTANT]
>Zásady podmíněného přístupu (včetně podmínek použití) nepodporují vynucení u účtů služeb.  Doporučujeme, abyste ze zásad podmíněného přístupu všechny účty služeb vyloučili.

## <a name="add-company-terms-of-use"></a>Přidání firemních podmínek použití
Jakmile dokončíte vaše podmínky použití, přidejte je pomocí následujícího postupu.

### <a name="to-add-terms-of-use"></a>Přidání podmínek použití
1. Přejděte na řídicí panel na adrese [https://aka.ms/catou](https://aka.ms/catou).
2. Klikněte na Přidat.</br>
![Přidání podmínek použití](media/active-directory-tou/tou12.png)
3. Zadejte **název** pro podmínky použití.
4. Zadejte **zobrazovaný název**.  Tato hlavička se uživatelům zobrazí při přihlášení.
5. Pomocí **Procházet** přejděte k souboru PDF s finálními podmínkami použití a vyberte ho.  Doporučená velikost písma je 24.
6. **Vyberte** jazyk podmínek použití.  Volba jazyka vám umožní nahrát různé jazykové verze podmínek použití.  Verze podmínek použití, která se zobrazí koncovému uživateli, závisí na jeho předvolbách prohlížeče.
7. Vyberte, zda chcete **vyžadovat, aby uživatelé rozbalili podmínky použití**.  Pokud je tato volba zapnutá, než uživatelé přijmou podmínky použití, budou je muset zobrazit.
8. V části **Podmíněný přístup** můžete nahrané podmínky **vynutit** výběrem šablony v rozevíracím seznamu nebo vlastních zásad podmíněného přístupu.  Vlastní zásady podmíněného přístupu umožňují využít detailní podmínky použití (až na úroveň konkrétních cloudových aplikací nebo skupin uživatelů).  Další informace najdete v tématu o [konfiguraci zásad podmíněného přístupu](active-directory-conditional-access-best-practices.md).
9. Klikněte na možnost **Vytvořit**.
10. Pokud jste vybrali vlastní šablonu podmíněného přístupu, zobrazí se nová obrazovka, která vám umožní přizpůsobit zásady CA.
11. Teď byste měli vidět vaše nové podmínky použití.</br>

![Přidání podmínek použití](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Odstranění podmínek použití
K odebrání nebo odstranění starých podmínek použití můžete použít tento postup:

### <a name="to-delete-terms-of-use"></a>Odstranění podmínek použití
1. Přejděte na řídicí panel na adrese [https://aka.ms/catou](https://aka.ms/catou).
2. Vyberte podmínky použití, které chcete odebrat.
3. Klikněte na **Odstranit**.
4. Vaše nové podmínky použití by se teď neměly zobrazovat.


## <a name="viewing-current-user-status"></a>Zobrazení aktuálního stavu uživatelů
Uvidíte, že vaše podmínky použití zobrazují počet uživatelů, kteří je přijali a odmítli.

![Událost auditu](media/active-directory-tou/tou15.png)

Kliknutím na čísla v části **Přijato** nebo **Odmítnuto** můžete zobrazit aktuální stav uživatelů.

![Událost auditu](media/active-directory-tou/tou16.png)

## <a name="audit-terms-of-use"></a>Audit podmínek použití
Pokud chcete zobrazit historii přijetí a odmítnutí, a nikoli pouze aktuální stav, funkce Azure AD Terms of Use poskytuje snadno použitelné auditování.  Toto auditování umožňuje zjistit, kdo a kdy přijal vaše podmínky použití.  

V závislosti na tom, co se právě pokoušíte provést, můžete auditování použít dvěma způsoby.  


Pokud chcete začít s auditováním použijte následující postup:

### <a name="to-audit-terms-of-use"></a>Audit podmínek použití
1. Přejděte na řídicí panel na adrese [https://aka.ms/catou](https://aka.ms/catou).
2. Klikněte na Zobrazit protokoly auditu.</br>
![Událost auditu](media/active-directory-tou/tou8.png)
3.  Na obrazovce protokolů auditu Azure AD můžete pomocí rozevíracích seznamů filtrovat informace a najít tak konkrétní údaje protokolu auditu.
![Událost auditu](media/active-directory-tou/tou9.png)
4.  Tyto informace můžete také stáhnout jako soubor .csv a použít místně.

## 

## <a name="what-users-see"></a>Co se zobrazí uživatelům
Příslušným uživatelům se po vytvoření a vynucení podmínek použití zobrazí následující obrazovky.  Uvidí je během svého přihlašování.
-   Osvědčeným postupem je nastavit velikost písma v souboru PDF na 24.
![Událost auditu](media/active-directory-tou/tou10.png)
-   Takto se tato obrazovka zobrazí na mobilních zařízeních:</br></br>
![Událost auditu](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>Přečtení podmínek použití
Uživatelé si můžou zobrazit a přečíst podmínky použití, které přijali.  Pokud si chcete přečíst podmínky použití, postupujte následovně:

1. Přejděte na adresu [https://myapps.microsoft.com](https://myapps.microsoft.com) a přihlaste se.
2. V pravém horním rohu klikněte na své jméno a z rozevírací nabídky vyberte **Profil**.
![Profil](media/active-directory-tou/tou14.png)

3. Na svém profilu klikněte na **Přečíst podmínky použití**.
![Událost auditu](media/active-directory-tou/tou13a.png)

4.  Tady si můžete přečíst podmínky použití, které jste přijali. 


## <a name="additional-information"></a>Další informace
Následující informace byste měli znát. Mohou vám být při používání podmínek použití užitečné.

>[!IMPORTANT]
> Příslušní uživatelé se musí odhlásit a znovu přihlásit, aby vyhověli novým zásadám:
> - pokud jsou u podmínek použití povolené zásady podmíněného přístupu
> - nebo pokud se vytvoří druhé podmínky použití.
>
>Zásady podmíněného přístupu vstupují v platnost okamžitě. Když k tomu dojde, správci se začnou zobrazovat „smutné cloudy“ nebo „potíže s tokenem Azure AD“. Správce se musí odhlásit a znovu přihlásit, aby tyto nové zásady splnil.





## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak poznám, kdy/jestli uživatel přijal podmínky použití?**</br>
Odpověď: Stačí kliknout na číslo v části Přijato vedle vašich podmínek použití.  Další informace najdete v části [Zobrazení aktuálního stavu uživatelů](#viewing-current-user-status).  Vyjádření souhlasu uživatele s podmínkami použití se také zapíše do protokolu auditu. Můžete prohledat protokol auditu Azure AD a prohlédnout si výsledky.  

**Otázka: Pokud změníte podmínky použití, je potřeba, aby je uživatelé přijali znovu?**</br>
Odpověď: Ano, správce může změnit podmínky použití. Vyžaduje to opakované přijetí nových podmínek.

**Otázka: Podporují se podmínky použití ve více jazycích?**</br>
Odpověď: Ano.  Správce v současné době může pro jedny podmínky použití nakonfigurovat 18 různých jazyků. 

**Otázka: Kdy se podmínky použití aktivují?**</br>
Odpověď: Podmínky použití se aktivují během přihlašování.

**Otázka: Na které aplikace je možné podmínkami použití cílit?**</br>
Odpověď: Můžete vytvořit zásady podmíněného přístupu pro podnikové aplikace s využitím moderního ověřování.  Další informace najdete v tématu věnovaném [podnikovým aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**Otázka: Je možné pro konkrétního uživatele nebo aplikaci přidat několikero podmínek použití?**</br>
Odpověď: Ano, a to tak, že vytvoříte několik zásad podmíněného přístupu, které cílí na tyto skupiny nebo aplikace. Pokud uživatel spadá do rozsahu několika podmínek použití, musí je odsouhlasit postupně.
 
**Otázka: Co se stane, když uživatel odmítne podmínky použití?**</br>
Odpověď: Uživatel bude mít k zablokovaný přístup k aplikaci. Aby uživatel získal přístup, musel by se musel znovu přihlásit a s těmito podmínkami souhlasit.