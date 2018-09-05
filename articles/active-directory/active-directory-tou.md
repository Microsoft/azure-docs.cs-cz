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
ms.topic: conceptual
ms.component: compliance
ms.date: 09/04/2018
ms.author: rolyon
ms.openlocfilehash: 0171cd3e3a39d1fe70e7c97b618d854857a1c376
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43695519"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funkce Azure Active Directory Terms of Use
Azure AD Terms of Use poskytuje organizacím jednoduchý způsob předkládání informací koncovým uživatelům. Toto předkládání zajistí, že se uživatelům zobrazí příslušná právní omezení pro zákonné požadavky nebo požadavky dodržování předpisů. Tento článek popisuje, jak začít s funkcí Azure AD Terms of Use.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>Co můžu dělat s podmínkami použití?
Funkce Azure AD Terms of Use umožňuje následující:
- Vyžadovat souhlas zaměstnanců nebo hostů s vašimi podmínkami použití před tím, než získají přístup.
- Předkládat obecné podmínky použití všem uživatelům ve vaší organizaci.
- Předkládat konkrétní podmínky použití založené na atributech uživatelů (např. doktoři vs. sestry nebo místní vs. zahraniční pracovníci, a to pomocí [dynamických skupin](users-groups-roles/groups-dynamic-membership.md)).
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

Funkce Azure AD Terms of Use k předkládání obsahu používá formát PDF. Soubor PDF může mít libovolný obsah, jako je například stávající smluvní dokumenty, a umožňuje od koncových uživatelů, které smlouvy během uživatele přihlásit. Doporučená velikost písma v souboru PDF je 24.

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

6. V části **Podmíněný přístup** můžete nahrané podmínky použití **vynutit** výběrem šablony v rozevíracím seznamu nebo vlastních zásad podmíněného přístupu.  Vlastní zásady podmíněného přístupu umožňují využít detailní podmínky použití (až na úroveň konkrétních cloudových aplikací nebo skupin uživatelů).  Další informace najdete v tématu o [konfiguraci zásad podmíněného přístupu](conditional-access/best-practices.md).

    >[!IMPORTANT]
    >Zásady podmíněného přístupu (včetně podmínek použití) nepodporují vynucení u účtů služeb.  Doporučujeme, abyste ze zásad podmíněného přístupu všechny účty služeb vyloučili.

7. Klikněte na možnost **Vytvořit**.

8. Pokud jste vybrali vlastní šablonu podmíněného přístupu, zobrazí se nová obrazovka, která vám umožní přizpůsobit zásady podmíněného přístupu.

    Teď byste měli vidět vaše nové podmínky použití.

    ![Přidání podmínek použití](media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Zobrazit sestavu z kteří je přijali a odmítli
V okně Terms of Use se zobrazí počet uživatelů, kteří podmínky přijali a odmítli. Tyto počty a informace o těch, kdo podmínky přijali nebo odmítli, se uchovávají po celou dobu platnosti podmínek použití.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

    ![Událost auditu](media/active-directory-tou/view-tou.png)

1. Kliknutím na čísla v části **Přijato** nebo **Odmítnuto** zobrazíte aktuální stav uživatelů.

    ![Událost auditu](media/active-directory-tou/accepted-tou.png)

## <a name="view-azure-ad-audit-logs"></a>Protokoly auditu zobrazení služby Azure AD
Pokud chcete zobrazit další aktivitu, funkce Azure AD Terms of Use zahrnuje protokoly auditu. Každý souhlasu uživatele aktivuje události z protokolů auditu, která je uložena po dobu 30 dnů. Tyto protokoly můžete zobrazit na portálu nebo stáhnout jako soubor .csv.

Začínáme s Azure AD protokoly auditu, použijte následující postup:

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Klikněte na **Zobrazit protokoly auditu**.

    ![Událost auditu](media/active-directory-tou/audit-tou.png)

1. Na obrazovce protokolů auditu Azure AD můžete pomocí rozevíracích seznamů filtrovat informace a najít tak konkrétní údaje protokolu auditu.

    ![Událost auditu](media/active-directory-tou/audit-logs-tou.png)

1. Můžete také kliknout na **Stáhnout** a stáhnout tyto informace jako soubor .csv pro místní použití.

## <a name="what-terms-of-use-looks-like-for-users"></a>Jak podmínky použití vypadají pro uživatele
Po podmínky použití se vytvoří a vynutit, uživatele, kteří jsou v oboru, zobrazí se následující obrazovka během svého přihlašování.

![Událost auditu](media/active-directory-tou/user-tou.png)

Následující obrazovka ukazuje, jak podmínky použití vypadají na mobilních zařízeních.

![Událost auditu](media/active-directory-tou/mobile-tou.png)

Uživatelé pouze musí přijmout podmínky použití jednou a neuvidí podmínky použití znovu na následné přihlášení.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak si uživatelé můžou zobrazit a přečíst své podmínky použití
Uživatelé si můžou zobrazit a přečíst podmínky použití, které přijali, pomocí následujícího postupu.

1. Přihlaste se k [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

1. V pravém horním rohu klikněte na své jméno a v rozevírací nabídce vyberte **Profil**.

    ![Profil](media/active-directory-tou/tou14.png)

1. Na stránce svého profilu klikněte na **Přečíst podmínky použití**.

    ![Událost auditu](media/active-directory-tou/tou13a.png)

1. Tady si můžete přečíst podmínky použití, které jste přijali. 

## <a name="edit-terms-of-use-details"></a>Upravit podrobnosti podmínek použití
Můžete upravit některé podrobnosti podmínek použití, ale nelze upravit existující dokument. Následující postup popisuje, jak upravit podrobnosti.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Vyberte podmínky použití, které chcete upravit.

1. Klikněte na tlačítko **upravit podmínky**.

1. V upravit podmínky použití podokna změňte název, zobrazovaný název nebo vyžadovat, aby uživatelé rozbalili hodnoty.

    ![Přidání podmínek použití](media/active-directory-tou/edit-tou.png)

1. Klikněte na tlačítko **Uložit** uložte provedené změny.

    Po uložení změn, budou mít uživatelé znovu přijali jako nové podmínky.

## <a name="add-a-terms-of-use-language"></a>Přidání podmínek použití jazyka
Následující postup popisuje, jak přidat podmínky použití jazyka.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Vyberte podmínky použití, které chcete upravit.

1. V podokně podrobností klikněte na tlačítko **jazyky** kartu.

    ![Přidání podmínek použití](media/active-directory-tou/languages-tou.png)

1. Klikněte na tlačítko **přidat jazyk**.

1. V přidat podmínky použití jazyka podokně nahrát lokalizované PDF a vyberte požadovaný jazyk.

    ![Přidání podmínek použití](media/active-directory-tou/language-add-tou.png)

1. Klikněte na tlačítko **přidat** přidat jazyk.

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
Zásady podmíněného přístupu vstupují v platnost okamžitě. Pokud k tomu dojde, správci se začnou naleznete v tématu "smutné cloudy" nebo "Token problémy s Azure AD". Správce musí odhlásit a znovu se přihlaste k tyto nové zásady.

>[!IMPORTANT]
> Příslušní uživatelé se musí odhlásit a znovu přihlásit, aby vyhověli novým zásadám:
> - pokud jsou u podmínek použití povolené zásady podmíněného přístupu
> - nebo pokud se vytvoří druhé podmínky použití.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak poznám, kdy/jestli uživatel přijal podmínky použití?**</br>
Odpověď: na podmínky použití okna, klikněte na číslo pod **přijato**. Můžete také zobrazit nebo Hledat přijmout aktivity ve službě Azure AD protokoly auditu. Další informace najdete v tématu [zobrazit sestavu kteří je přijali a odmítli](#view-who-has-accepted-and-declined) a [protokolů auditu Azure AD zobrazení](#view-azure-ad-audit-logs).
 
**Otázka: Jak dlouho se informace uchovávají?**</br>
Odpověď: uživatel se počítá v podmínkách použití sestavy a na koho se přijata nebo odmítnuta se uchovávají po dobu životnosti podmínky použití. Po dobu 30 dnů se ukládají protokoly auditu Azure AD.

**Otázka: Proč vidím jiný počet souhlasy v podmínkách použití sestav a služby Azure AD protokoly auditu**</br>
Odpověď: podmínky použití sestavy se ukládají po dobu platnosti této podmínky použití při auditování Azure AD, které protokoly se ukládají po dobu 30 dnů. Podmínky použití sestavy také zobrazí pouze aktuální stav souhlasu uživatele. Například pokud uživatel odmítne a pak přijme, podmínky použití sestavy se zobrazí pouze jeho přijetí. Pokud chcete zobrazit historii, můžete použít Azure AD protokoly auditu.

**D: Pokud upravím podrobnosti podmínek použití, je potřeba, aby uživatelé přijali znovu?**</br>
Odpověď: Ano, pokud správce upraví podrobnosti podmínek použití, vyžaduje uživatelé znovu přijali jako nové podmínky.

**Dotaz: lze aktualizovat existující podmínky použití dokumentů?**</br>
Odpověď: v současné době nelze aktualizovat existující podmínky použití dokumentů. Chcete-li změnit podmínky použití dokumentů, budete muset vytvořit nové podmínky použití instance.

**Otázka: Pokud jsou hypertextové odkazy v podmínkách použití dokumentů PDF, koncoví uživatelé uvidí na ně?**</br>
Odpověď: soubor PDF se vykreslí ve výchozím nastavení ve formátu JPEG, takže hypertextové odkazy nejsou po kliknutí. Uživatelé mají možnost vybrat si **máte problémy se zobrazením? Kliknutím sem**, který vykreslí PDF nativně kde hypertextové odkazy jsou podporovány.

**Otázka: Podporují se podmínky použití ve více jazycích?**</br>
Odpověď: Ano. Aktuálně jsou dostupné více než 100 různých jazyků, které správce může nakonfigurovat pro jeden podmínky použití.

**Otázka: Kdy se podmínky použití aktivují?**</br>
Odpověď: Podmínky použití se aktivují během přihlašování.

**Otázka: Na které aplikace je možné podmínkami použití cílit?**</br>
Odpověď: Můžete vytvořit zásady podmíněného přístupu pro podnikové aplikace s využitím moderního ověřování.  Další informace najdete v tématu věnovaném [podnikovým aplikacím](./manage-apps/view-applications-portal.md).

**Otázka: Je možné pro konkrétního uživatele nebo aplikaci přidat několikero podmínek použití?**</br>
Odpověď: Ano, a to tak, že vytvoříte několik zásad podmíněného přístupu, které cílí na tyto skupiny nebo aplikace. Pokud uživatel spadá do rozsahu několika podmínek použití, musí je odsouhlasit postupně.
 
**Otázka: Co se stane, když uživatel odmítne podmínky použití?**</br>
Odpověď: Uživatel bude mít k zablokovaný přístup k aplikaci. Uživatel musel znovu přihlásit a souhlasit s podmínkami, aby bylo možné získat přístup.
 
**Otázka: je možné unaccept podmínky použití, které byly dříve přijali službu?**</br>
Odpověď: můžete [kontrola dříve přijali podmínky použití](#how-users-can-review-their-terms-of-use), ale aktuálně není k dispozici způsob, jak unaccept.

## <a name="next-steps"></a>Další postup

- [Osvědčené postupy pro podmíněný přístup v Azure Active Directory](conditional-access/best-practices.md)