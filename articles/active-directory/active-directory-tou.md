---
title: Funkce Azure Active Directory Terms of Use | Microsoft Docs
description: Funkce Azure AD Terms of Use umožní vám a vaší společnosti poskytnout podmínky použití uživatelům služeb Azure AD.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 06/18/2018
ms.author: rolyon
ms.openlocfilehash: 2919ce1d7c57b7a92420ac11b61503caa1fdd3b0
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267553"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funkce Azure Active Directory Terms of Use
Azure AD Terms of Use poskytuje organizacím jednoduchý způsob předkládání informací koncovým uživatelům. Toto předkládání zajistí, že se uživatelům zobrazí příslušná právní omezení pro zákonné požadavky nebo požadavky dodržování předpisů. Tento článek popisuje, jak začít s funkcí Azure AD Terms of Use.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>Co můžu dělat s podmínkami použití?
Funkce Azure AD Terms of Use umožňuje následující:
- Vyžadovat souhlas zaměstnanců nebo hostů s vašimi podmínkami použití před tím, než získají přístup.
- Předkládat obecné podmínky použití všem uživatelům ve vaší organizaci.
- Předkládat konkrétní podmínky použití založené na atributech uživatelů (např. doktoři vs. sestry nebo místní vs. zahraniční pracovníci, a to pomocí [dynamických skupin](active-directory-groups-dynamic-membership-azure-portal.md)).
- Předkládat konkrétní podmínky použití při přístupu k aplikacím s velkým vlivem na chod firmy, jako je například Salesforce.
- Předkládat podmínky použití v různých jazycích.
- Zobrazit seznam těch, kdo odsouhlasili nebo neodsouhlasili vaše podmínky použití.
- Zobrazit protokol auditu aktivit podmínek použití.

## <a name="prerequisites"></a>Požadavky
K používání a konfiguraci funkce Azure AD Terms of Use potřebujete:

- Předplatné Azure AD Premium P1, P2, EMS E3 nebo EMS E5.
    - Pokud žádné z těchto předplatných nemáte, můžete [získat Azure AD Premium](fundamentals/active-directory-get-started-premium.md) nebo [povolit zkušební verzi Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jeden z následujících účtů správce pro adresář, který chcete konfigurovat:
    - Globální správce
    - Správce zabezpečení
    - Správce podmíněného přístupu

## <a name="terms-of-use-document"></a>Dokument podmínek použití

Funkce Azure AD Terms of Use k předkládání obsahu používá formát PDF. Soubor PDF může mít libovolný obsah, jako jsou například stávající smluvní dokumenty, a umožňuje od koncových uživatelů při přihlášení získat jejich souhlas. Doporučená velikost písma v souboru PDF je 24.

## <a name="add-terms-of-use"></a>Přidání podmínek použití
Jakmile dokončíte dokument podmínek použití, přidejte ho pomocí následujícího postupu.

1. Přihlaste se k Azure jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.

1. Přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

    ![Okno Terms of Use](media/active-directory-tou/tou-blade.png)

1. Klikněte na **Nové podmínky**.

    ![Přidání podmínek použití](media/active-directory-tou/new-tou.png)

1. Zadejte **název** pro podmínky použití.

2. Zadejte **zobrazovaný název**.  Tato hlavička se uživatelům zobrazí při přihlášení.

3. Pomocí **Procházet** přejděte k souboru PDF s finálními podmínkami použití a vyberte ho.

4. **Vyberte** jazyk podmínek použití.  Volba jazyka vám umožní nahrát různé jazykové verze podmínek použití.  Verze podmínek použití, která se zobrazí koncovému uživateli, závisí na jeho předvolbách prohlížeče.

5. U možnosti **Vyžadovat, aby uživatelé rozbalili podmínky použití** vyberte Zapnuto nebo Vypnuto.  Pokud je toto nastavení zapnuté, budou si muset koncoví uživatelé před přijetím podmínek použití tyto podmínky zobrazit.

6. V části **Podmíněný přístup** můžete nahrané podmínky použití **vynutit** výběrem šablony v rozevíracím seznamu nebo vlastních zásad podmíněného přístupu.  Vlastní zásady podmíněného přístupu umožňují využít detailní podmínky použití (až na úroveň konkrétních cloudových aplikací nebo skupin uživatelů).  Další informace najdete v tématu o [konfiguraci zásad podmíněného přístupu](active-directory-conditional-access-best-practices.md).

    >[!IMPORTANT]
    >Zásady podmíněného přístupu (včetně podmínek použití) nepodporují vynucení u účtů služeb.  Doporučujeme, abyste ze zásad podmíněného přístupu všechny účty služeb vyloučili.

7. Klikněte na možnost **Vytvořit**.

8. Pokud jste vybrali vlastní šablonu podmíněného přístupu, zobrazí se nová obrazovka, která vám umožní přizpůsobit zásady podmíněného přístupu.

    Teď byste měli vidět vaše nové podmínky použití.

    ![Přidání podmínek použití](media/active-directory-tou/create-tou.png)

## <a name="view-who-has-accepted-and-declined"></a>Zobrazení seznamu těch, kdo podmínky přijali a odmítli
V okně Terms of Use se zobrazí počet uživatelů, kteří podmínky přijali a odmítli. Tyto počty a informace o těch, kdo podmínky přijali nebo odmítli, se uchovávají po celou dobu platnosti podmínek použití.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

    ![Událost auditu](media/active-directory-tou/view-tou.png)

1. Kliknutím na čísla v části **Přijato** nebo **Odmítnuto** zobrazíte aktuální stav uživatelů.

    ![Událost auditu](media/active-directory-tou/accepted-tou.png)

## <a name="view-audit-logs"></a>Zobrazení protokolů auditu
Pokud chcete zobrazit další aktivitu, funkce Azure AD Terms of Use zahrnuje protokoly auditu. Každé vyjádření souhlasu uživatele aktivuje událost v protokolech auditu, které se ukládají po dobu 30 dnů. Tyto protokoly můžete zobrazit na portálu nebo stáhnout jako soubor .csv.

Pokud chcete začít s protokoly auditu, použijte následující postup:

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Klikněte na **Zobrazit protokoly auditu**.

    ![Událost auditu](media/active-directory-tou/audit-tou.png)

1. Na obrazovce protokolů auditu Azure AD můžete pomocí rozevíracích seznamů filtrovat informace a najít tak konkrétní údaje protokolu auditu.

    ![Událost auditu](media/active-directory-tou/audit-logs-tou.png)

1. Můžete také kliknout na **Stáhnout** a stáhnout tyto informace jako soubor .csv pro místní použití.

## <a name="what-terms-of-use-looks-like-for-users"></a>Jak podmínky použití vypadají pro uživatele
Po vytvoření a vynucení podmínek použití se příslušným uživatelům při přihlášení zobrazí následující obrazovka.

![Událost auditu](media/active-directory-tou/user-tou.png)

Následující obrazovka ukazuje, jak podmínky použití vypadají na mobilních zařízeních.

![Událost auditu](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>Jak si uživatelé můžou zobrazit a přečíst své podmínky použití
Uživatelé si můžou zobrazit a přečíst podmínky použití, které přijali, pomocí následujícího postupu.

1. Přihlaste se na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. V pravém horním rohu klikněte na své jméno a v rozevírací nabídce vyberte **Profil**.

    ![Profil](media/active-directory-tou/tou14.png)

1. Na stránce svého profilu klikněte na **Přečíst podmínky použití**.

    ![Událost auditu](media/active-directory-tou/tou13a.png)

1. Tady si můžete přečíst podmínky použití, které jste přijali. 

## <a name="delete-terms-of-use"></a>Odstranění podmínek použití
K odstranění starých podmínek použití můžete použít tento postup.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Vyberte podmínky použití, které chcete odebrat.

1. Klikněte na **Odstranit podmínky**.

1. Ve zprávě s dotazem, jestli chcete pokračovat, která se zobrazí, klikněte na **Ano**.

    ![Přidání podmínek použití](media/active-directory-tou/delete-tou.png)

    Vaše podmínky použití by se teď už neměly zobrazovat.

## <a name="deleted-users-and-active-terms-of-use"></a>Odstranění uživatelé a aktivní podmínky použití
Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure AD v odstraněném stavu po dobu 30 dnů, během kterých ho v případě potřeby může správce obnovit.  Po 30 dnech je tento uživatel odstraněn trvale.  Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období.  Jakmile je uživatel trvale odstraněný, odeberou se z aktivních podmínek použití i další data o tomto uživateli.  Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="policy-changes"></a>Změny zásad
Zásady podmíněného přístupu vstupují v platnost okamžitě. Když k tomu dojde, správci se začnou zobrazovat „smutné cloudy“ nebo „potíže s tokenem Azure AD“. Správce se musí odhlásit a znovu přihlásit, aby tyto nové zásady splnil.

>[!IMPORTANT]
> Příslušní uživatelé se musí odhlásit a znovu přihlásit, aby vyhověli novým zásadám:
> - pokud jsou u podmínek použití povolené zásady podmíněného přístupu
> - nebo pokud se vytvoří druhé podmínky použití.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak poznám, kdy/jestli uživatel přijal podmínky použití?**</br>
Odpověď: Můžete kliknout na číslo v části Přijato vedle vašich podmínek použití.  Další informace najdete v části [Zobrazení seznamu těch, kdo podmínky přijali a odmítli](#view-who-has-accepted-and-declined).  Vyjádření souhlasu uživatele s podmínkami použití se také zapíše do protokolu auditu. Můžete prohledat protokol auditu Azure AD a prohlédnout si výsledky.  

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
 
**Otázka: Jak dlouho se informace uchovávají?**</br>
Odpověď: Počty uživatelů a informace o těch, kdo podmínky přijali nebo odmítli, se uchovávají po celou dobu platnosti podmínek použití. Protokoly auditu se uchovávají po dobu 30 dnů.
