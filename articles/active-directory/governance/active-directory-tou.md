---
title: Funkce Azure Active Directory Terms of Use | Microsoft Docs
description: Popisuje, jak začít používat Azure AD Terms of použití předkládání informací zaměstnanci nebo hosté před získáním přístupu.
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
ms.date: 12/10/2018
ms.author: rolyon
ms.openlocfilehash: 448ef296af3ceaffffa1a4a69060916afd9abdc4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185677"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funkce Azure Active Directory Terms of Use
Azure AD Terms of Use poskytuje organizacím jednoduchý způsob předkládání informací koncovým uživatelům. Toto předkládání zajistí, že se uživatelům zobrazí příslušná právní omezení pro zákonné požadavky nebo požadavky dodržování předpisů. Tento článek popisuje, jak začít pracovat s podmínkami použití.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Videa s přehledem

Následující video poskytuje rychlý přehled toho, podmínky použití.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Další videa naleznete v tématu:
- [Jak nasadit podmínky použití ve službě Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Postup zavádění podmínky použití ve službě Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Co můžu dělat s podmínkami použití?
Azure AD Terms of použití má následující možnosti:
- Požadovat, aby zaměstnanci nebo hosté přijmout vaše podmínky použití, než získají přístup.
- Požadovat, aby zaměstnanci nebo hosté přijmout vaše podmínky použití na všech zařízeních, než získají přístup.
- Požadovat, aby zaměstnanci nebo hosté přijmout vaše podmínky použití podle opakovaného plánu.
- Předkládat obecné podmínky použití všem uživatelům ve vaší organizaci.
- Předkládat konkrétní podmínky použití založené na atributech uživatelů (např. doktoři vs. sestry nebo místní vs. zahraniční pracovníci, a to pomocí [dynamických skupin](../users-groups-roles/groups-dynamic-membership.md)).
- Předkládat konkrétní podmínky použití při přístupu k aplikacím s velkým vlivem na chod firmy, jako je například Salesforce.
- Předkládat podmínky použití v různých jazycích.
- Seznam, který byl nebo nebyl přijat na vaše podmínky použití.
- Pomoc při plnění nařízení o ochraně osobních údajů.
- Zobrazte protokol podmínky použití aktivity pro audit a dodržování předpisů.
- Vytváření a správě podmínek použití použití [rozhraní Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (aktuálně ve verzi preview).

## <a name="prerequisites"></a>Požadavky
K používání a konfiguraci funkce Azure AD Terms of Use potřebujete:

- Předplatné Azure AD Premium P1, P2, EMS E3 nebo EMS E5.
    - Pokud žádné z těchto předplatných nemáte, můžete [získat Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) nebo [povolit zkušební verzi Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jeden z následujících účtů správce pro adresář, který chcete konfigurovat:
    - Globální správce
    - Správce zabezpečení
    - Správce podmíněného přístupu

## <a name="terms-of-use-document"></a>Dokument podmínek použití

Funkce Azure AD Terms of Use k předkládání obsahu používá formát PDF. Soubor PDF může mít libovolný obsah, jako jsou například stávající smluvní dokumenty, a umožňuje od koncových uživatelů při přihlášení získat jejich souhlas. Doporučená velikost písma v souboru PDF pro podporu uživatelů na mobilních zařízeních, je 24 bodů.

## <a name="add-terms-of-use"></a>Přidání podmínek použití
Jakmile dokončíte dokument podmínek použití, přidejte ho pomocí následujícího postupu.

1. Přihlaste se do Azure jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.

1. Přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

    ![Okno Terms of Use](./media/active-directory-tou/tou-blade.png)

1. Klikněte na **Nové podmínky**.

    ![Přidání podmínek použití](./media/active-directory-tou/new-tou.png)

1. V **název** pole, zadejte název pro podmínky použití, který se použije na webu Azure Portal.

1. V **zobrazovaný název** pole, zadejte název, který uživatelé uvidí, když se přihlásí.

1. Pro **dokument podmínek použití**finálními podmínkami použití PDF vyhledejte a vyberte ji.

1. Vyberte jazyk pro vaše podmínky použití dokumentů. Volba jazyka vám umožní nahrát různé jazykové verze podmínek použití. Verze podmínek použití, která se zobrazí koncovému uživateli, závisí na jeho předvolbách prohlížeče.

1. Nastavit tak, aby vyžadovala koncovým uživatelům zobrazit podmínky použití před přijetím je **vyžadují, aby uživatelé rozbalili podmínky použití** k **na**.

1. Nastavit tak, aby vyžadovala koncoví uživatelé tak, aby přijímal vaše podmínky použití na všech zařízeních přistupují z **vyžadují, aby uživatelé vyjádřili souhlas na všech zařízeních** k **na**. Další informace najdete v tématu [jednotlivá zařízení podmínky použití](#per-device-terms-of-use).

1. Pokud chcete ukončit platnost podmínky použití souhlasí podle plánu, nastavte **vyprší souhlasy** k **na**. Pokud nastavíte hodnotu na, jsou zobrazeny dvě další nastavení.

    ![Vypršení platnosti souhlasů](./media/active-directory-tou/expire-consents.png)

1. Použít **vypršení platnosti od** a **frekvence** nastavení určete plán, podmínky použití vypršení platnosti. Následující tabulka ukazuje výsledek pro několik příklad nastavení:

    | Vypršení platnosti začíná | Frekvence | Výsledek |
    | --- | --- | --- |
    | Dnešní datum  | Měsíční | Ode dneška, musí uživatelé přijmout podmínky použití a pak znovu přijali jako každý měsíc. |
    | Datum v budoucnosti  | Měsíční | Ode dneška, uživatelé musí přijmout podmínky použití. Pokud dojde k budoucí datum, souhlasu vyprší a pak musí uživatelé ovlivnění každý měsíc.  |

    Například pokud nastavíte ukončit platnost od datum **1. ledna** a četnost **měsíční**, zde je, jak může dojít k vypršení platnosti dva uživatelé:

    | Uživatel | Nejdřív přijmout data | Nejprve vyprší dne | Za druhé vyprší dne | Třetí vyprší dne |
    | --- | --- | --- | --- | --- |
    | Alice | 1. ledna | 1. února | 1. března | Dubna 1 |
    | Bob | 15. ledna | 1. února | 1. března | Dubna 1 |

1. Použít **dobu trvání než (dnů) vyžaduje opětovné přijetí** nastavení určuje počet dní, než uživatel musí znovu přijali jako podmínky použití. To umožňuje uživatelům, postupovat podle svého vlastního plánu. Například pokud nastavíte dobu trvání na **30** dnů, zde je, jak může dojít k vypršení platnosti dva uživatelé:

    | Uživatel | Nejdřív přijmout data | Nejprve vyprší dne | Za druhé vyprší dne | Třetí vyprší dne |
    | --- | --- | --- | --- | --- |
    | Alice | 1. ledna | 31. ledna | Března 2 | Dubna 1 |
    | Bob | 15. ledna | 14. února | Března 16 | Dubna 15 |

    Je možné použít **vyprší souhlasy** a **dobu trvání než (dnů) vyžaduje opětovné přijetí** nastavení, ale většinou můžete použít jeden z nich.

1. V části **podmíněného přístupu**, použijte **vynutit šablonou zásad podmíněného přístupu** seznam a vyberte šablonu, kterou chcete vynucovat podmínky použití.

    ![Šablony podmíněného přístupu](./media/active-directory-tou/conditional-access-templates.png)

    | Šablona | Popis |
    | --- | --- |
    | **Přístup ke cloudovým aplikacím pro všechny hosty** | Vytvoří se zásady podmíněného přístupu pro všechny hostů a všechny cloudové aplikace. Tyto zásady mají vliv na webu Azure portal. Jakmile se vytvoří, budete asi muset odhlásit a přihlásit. |
    | **Přístup ke cloudovým aplikacím pro všechny uživatele** | Vytvoří se zásady podmíněného přístupu pro všechny uživatele a všechny cloudové aplikace. Tyto zásady mají vliv na webu Azure portal. Jakmile se vytvoří, budete muset odhlásit a přihlásit. |
    | **Vlastní zásady** | Vyberte uživatele, skupiny a aplikace, na které se tyto podmínky použití vztahují. |
    | **Vytvoření zásad podmíněného přístupu později** | Tyto podmínky použití se zobrazí v seznamu pro udělení řízení při vytváření zásad podmíněného přístupu. |

    >[!IMPORTANT]
    >Zásady podmíněného přístupu (včetně podmínek použití) nepodporují vynucení u účtů služeb.  Doporučujeme, abyste ze zásad podmíněného přístupu všechny účty služeb vyloučili.

     Vlastní zásady podmíněného přístupu umožňují využít detailní podmínky použití (až na úroveň konkrétních cloudových aplikací nebo skupin uživatelů).  Další informace najdete v tématu [rychlý start: Podmínky použití přijmout před přístup ke cloudovým aplikacím vyžadovat](../conditional-access/require-tou.md).

1. Klikněte na možnost **Vytvořit**.

    Pokud jste vybrali vlastní šablonu podmíněného přístupu, pak nová obrazovka se zobrazí, který vám umožní vytvořit vlastních zásad podmíněného přístupu.

    ![Vlastní zásady](./media/active-directory-tou/custom-policy.png)

    Teď byste měli vidět vaše nové podmínky použití.

    ![Přidání podmínek použití](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Zobrazit sestavu z kteří je přijali a odmítli
V okně Terms of Use se zobrazí počet uživatelů, kteří podmínky přijali a odmítli. Tyto počty a informace o těch, kdo podmínky přijali nebo odmítli, se uchovávají po celou dobu platnosti podmínek použití.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

    ![Okno Terms of Use](./media/active-directory-tou/view-tou.png)

1. Podmínky použití, klikněte na čísla v části **přijato** nebo **Odmítnuto** Chcete-li zobrazit aktuální stav pro uživatele.

    ![Souhlasy s podmínkami použití](./media/active-directory-tou/accepted-tou.png)

1. Chcete-li zobrazit historii pro jednotlivé uživatele, klikněte na tlačítko se třemi tečkami (**...** ) a potom **zobrazit historii**.

    ![Nabídka zobrazení historie](./media/active-directory-tou/view-history-menu.png)

    V podokně zobrazení historie, uvidíte historii všech přijme, odmítne a vypršení platnosti.

    ![Podokno zobrazení historie](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Protokoly auditu zobrazení služby Azure AD
Pokud chcete zobrazit další aktivitu, funkce Azure AD Terms of Use zahrnuje protokoly auditu. Každý souhlasu uživatele aktivuje události z protokolů auditu, které je uložena po dobu **30 dnů**. Tyto protokoly můžete zobrazit na portálu nebo stáhnout jako soubor .csv.

Začínáme s Azure AD protokoly auditu, použijte následující postup:

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Vyberte podmínky použití.

1. Klikněte na **Zobrazit protokoly auditu**.

    ![Okno Terms of Use](./media/active-directory-tou/audit-tou.png)

1. Obrazovce protokolů auditu v Azure AD, můžete filtrovat informace o použití zadaný seznamů pro cílové konkrétní údaje protokolu auditu.

    Můžete také kliknout na **Stáhnout** a stáhnout tyto informace jako soubor .csv pro místní použití.

    ![Protokoly auditu](./media/active-directory-tou/audit-logs-tou.png)

    Pokud kliknete na protokol, zobrazí se podokno s podrobnostmi o další aktivity.

    ![Podrobnosti o aktivitě](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Jak podmínky použití vypadají pro uživatele
Po vytvoření a vynucení podmínek použití se příslušným uživatelům při přihlášení zobrazí následující obrazovka.

![Webové přihlášení](./media/active-directory-tou/user-tou.png)

Následující obrazovka ukazuje, jak podmínky použití vypadají na mobilních zařízeních.

![Mobilní přihlášení uživatele](./media/active-directory-tou/mobile-tou.png)

Uživatelé pouze musí přijmout podmínky použití jednou a neuvidí podmínky použití znovu na následné přihlášení.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak si uživatelé můžou zobrazit a přečíst své podmínky použití
Uživatelé si můžou zobrazit a přečíst podmínky použití, které přijali, pomocí následujícího postupu.

1. Přihlaste se k webu [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. V pravém horním rohu klikněte na název a vyberte **profilu**.

    ![Profil](./media/active-directory-tou/tou14.png)

1. Na stránce svého profilu klikněte na **Přečíst podmínky použití**.

    ![Profil - přečíst podmínky použití](./media/active-directory-tou/tou13a.png)

1. Tady si můžete přečíst podmínky použití, které jste přijali. 

## <a name="edit-terms-of-use-details"></a>Upravit podrobnosti podmínek použití
Můžete upravit některé podrobnosti podmínek použití, ale nelze upravit existující dokument. Následující postup popisuje, jak upravit podrobnosti.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Vyberte podmínky použití, které chcete upravit.

1. Klikněte na tlačítko **upravit podmínky**.

1. V upravit podmínky použití podokna změňte název, zobrazovaný název nebo vyžadovat, aby uživatelé rozbalili hodnoty.

    Pokud existují další nastavení chcete změnit, jako je například dokument PDF, vyžadují, aby uživatelé vyjádřili souhlas na všech zařízeních, vyprší souhlasy, doba trvání před reacceptance nebo zásad podmíněného přístupu, je nutné vytvořit nové podmínky použití.

    ![Upravit podmínky použití](./media/active-directory-tou/edit-tou.png)

1. Klikněte na tlačítko **Uložit** uložte provedené změny.

    Po uložení změn, uživatelé nebudou mít tyto úpravy ovlivnění.

## <a name="add-a-terms-of-use-language"></a>Přidání podmínek použití jazyka
Následující postup popisuje, jak přidat podmínky použití jazyka.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Vyberte podmínky použití, které chcete upravit.

1. V podokně podrobností klikněte na tlačítko **jazyky** kartu.

    ![Přidání podmínek použití](./media/active-directory-tou/languages-tou.png)

1. Klikněte na tlačítko **přidat jazyk**.

1. V přidat podmínky použití jazyka podokně nahrát lokalizované PDF a vyberte požadovaný jazyk.

    ![Přidání podmínek použití](./media/active-directory-tou/language-add-tou.png)

1. Klikněte na tlačítko **přidat** přidat jazyk.

## <a name="per-device-terms-of-use"></a>Na zařízení podmínky použití

**Vyžadují, aby uživatelé vyjádřili souhlas na všech zařízeních** nastavení umožňuje vyžadují, aby koncoví uživatelé tak, aby přijímal vaše podmínky použití na všech zařízeních přistupují z. Koncový uživatel se vyžaduje pro připojení zařízení ve službě Azure AD. Když se zařízení připojí, ID zařízení se používá k vynucení podmínek použití na všech zařízeních.

Tady je seznam podporovaných platforem a software.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Ostatní |
> | --- | --- | --- | --- | --- |
> | **Nativní aplikace** | Ano | Ano | Ano |  |
> | **Edge** | Ano | Ano | Ano |  |
> | **Aplikace Internet Explorer** | Ano | Ano | Ano |  |
> | **(S příponou) pro Chrome** | Ano | Ano | Ano |  |

Podmínky použití na zařízení má následující omezení:

- Zařízení lze připojit pouze do jednoho tenanta.
- Uživatel musí mít oprávnění pro připojení zařízení.
- Registrace aplikace Intune se nepodporuje.

Pokud zařízení uživatele není připojený, obdrží zprávu, která se potřebují připojit svoje zařízení. Jejich prostředí budou závislé na platformy a software.

### <a name="join-a-windows-10-device"></a>Připojte se k zařízení s Windows 10

Pokud uživatel používá Windows 10 a Microsoft Edge, zobrazí zpráva podobná následující [své zařízení připojili](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 a Microsoft Edge – výzvu zařízení spojení](./media/active-directory-tou/per-device-win10-edge.png)

V případě, že používáte Chrome, budete vyzváni k instalaci [účty systému Windows 10 rozšíření](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Prohlížeče

Pokud uživatel používá prohlížeč, který není podporován, bude se mu zobrazit výzva k použijte jiný prohlížeč.

![Nepodporovaný prohlížeč](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Odstranění podmínek použití
K odstranění starých podmínek použití můžete použít tento postup.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

1. Vyberte podmínky použití, které chcete odebrat.

1. Klikněte na **Odstranit podmínky**.

1. Ve zprávě s dotazem, jestli chcete pokračovat, která se zobrazí, klikněte na **Ano**.

    ![Odstranění podmínek použití](./media/active-directory-tou/delete-tou.png)

    Vaše podmínky použití by se teď už neměly zobrazovat.

## <a name="deleted-users-and-active-terms-of-use"></a>Odstranění uživatelé a aktivní podmínky použití
Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure AD v odstraněném stavu po dobu 30 dnů, během kterých ho v případě potřeby může správce obnovit.  Po 30 dnech je tento uživatel odstraněn trvale.  Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](../fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období.  Jakmile je uživatel trvale odstraněný, odeberou se z aktivních podmínek použití i další data o tomto uživateli.  Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="policy-changes"></a>Změny zásad
Zásady podmíněného přístupu vstupují v platnost okamžitě. Pokud k tomu dojde, správci se začnou naleznete v tématu "smutné cloudy" nebo "Token problémy s Azure AD". Správce musí odhlásit a znovu se přihlaste k tyto nové zásady.

>[!IMPORTANT]
> Příslušní uživatelé se musí odhlásit a znovu přihlásit, aby vyhověli novým zásadám:
> - pokud jsou u podmínek použití povolené zásady podmíněného přístupu
> - nebo pokud se vytvoří druhé podmínky použití.

## <a name="b2b-guests-preview"></a>Hosté B2B (Preview)

Většina organizací má proces místo jejich zaměstnanci mohli souhlas s jejich organizace podmínky použití a ochrana osobních údajů. Ale jak můžete vynutit stejné souhlasy pro Azure AD business-to-business (B2B) hosté přidané prostřednictvím služby SharePoint nebo týmy? Pomocí podmíněného přístupu a podmínky použití, můžete vynutit zásady přímo na uživatele typu Host B2B. Během toku uplatnění pozvánky se zobrazí uživatele s podmínkami použití. Tato podpora je aktuálně ve verzi preview.

![Všichni uživatelé typu host](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Podpora pro cloudové aplikace (Preview)

Pro jiné cloudové aplikace, jako je Azure Information Protection a Microsoft Intune je možné podmínkami použití. Tato podpora je aktuálně ve verzi preview.

### <a name="azure-information-protection"></a>Azure Information Protection

Můžete nakonfigurovat zásady podmíněného přístupu pro aplikace Azure Information Protection a vyžadují podmínky použití, když uživatel přistupuje k chráněný dokument. Tím se aktivuje podmínky použití přijmou uživatele, kteří používají chráněný dokument poprvé.

![Aplikace cloudové služby Azure Information Protection](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrace v Microsoft Intune

Můžete nakonfigurovat zásady podmíněného přístupu pro aplikaci registrace v Microsoft Intune a vyžadují podmínky použití ještě před registrací zařízení v Intune. Další informace najdete v tématu čtení [Volba správného řešení pro vaši organizaci blogový příspěvek podmínky](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Cloudové aplikace pro Microsoft Intune](./media/active-directory-tou/cloud-app-intune.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**DOTAZ: Jak můžu zobrazit kdy / jestli uživatel přijal podmínky použití?**</br>
ODPOVĚĎ: V podmínkách použití okna, klikněte na číslo v části **přijato**. Můžete také zobrazit nebo Hledat přijmout aktivity ve službě Azure AD protokoly auditu. Další informace najdete v tématu [zobrazit sestavu kteří je přijali a odmítli](#view-who-has-accepted-and-declined) a [protokolů auditu Azure AD zobrazení](#view-azure-ad-audit-logs).

**DOTAZ: Jak dlouho je informace uložené?**</br>
ODPOVĚĎ: Uživatel se počítá v podmínkách použití sestavy a na koho se přijata nebo odmítnuta se uchovávají po dobu životnosti podmínky použití. Po dobu 30 dnů se ukládají protokoly auditu Azure AD.

**DOTAZ: Proč vidím jiný počet souhlasy v podmínkách použití sestav a služby Azure AD protokoly auditu**</br>
ODPOVĚĎ: Podmínky použití sestavy se ukládají po dobu platnosti této podmínky použití při auditování Azure AD, které protokoly se ukládají po dobu 30 dnů. Podmínky použití sestavy také zobrazí pouze aktuální stav souhlasu uživatele. Například pokud uživatel odmítne a pak přijme, podmínky použití sestavy se zobrazí pouze jeho přijetí. Pokud chcete zobrazit historii, můžete použít Azure AD protokoly auditu.

**DOTAZ: Pokud jsem upravit podrobnosti podmínek použití, je potřeba, aby uživatelé přijali znovu?**</br>
ODPOVĚĎ: Ne, pokud správce upraví podrobnosti pro podmínky použití (název, zobrazovaný název, vyžadovat, aby uživatelé rozbalili nebo přidat jazyk), aby uživatelé znovu přijali jako nové podmínky.

**DOTAZ: Můžete aktualizovat existující podmínky použití dokumentů**</br>
ODPOVĚĎ: V současné době nelze aktualizovat existující podmínky použití dokumentů. Chcete-li změnit podmínky použití dokumentů, budete muset vytvořit nové podmínky použití instance.

**DOTAZ: Pokud jsou hypertextové odkazy v podmínkách použití dokumentů PDF, koncoví uživatelé uvidí na ně?**</br>
ODPOVĚĎ: Soubor PDF se vykreslí ve výchozím nastavení ve formátu JPEG, takže hypertextové odkazy nejsou po kliknutí. Uživatelé mají možnost vybrat si **máte problémy se zobrazením? Kliknutím sem**, který vykreslí PDF nativně kde hypertextové odkazy jsou podporovány.

**DOTAZ: Podmínky použití může podporovat více jazycích?**</br>
ODPOVĚĎ: Ano. Aktuálně nejsou 108 různé jazyky, které správce může nakonfigurovat pro jeden podmínky použití. Správce můžete nahrát několik dokumentů PDF a označte tyto dokumenty pomocí odpovídající jazyk (až 108). Když se koncoví uživatelé přihlásí, jsme podívejte se na jejich upřednostňovaný jazyk prohlížeče a zobrazí odpovídající dokumentu. Pokud není nalezena žádná shoda, zobrazí výchozí dokument, který je první dokument, který se odeslal.

**DOTAZ: Kdy se podmínky použití aktivují?**</br>
ODPOVĚĎ: Podmínky použití se aktivují během přihlašovací prostředí.

**DOTAZ: Jaké aplikace je možné cílit podmínkami použití?**</br>
ODPOVĚĎ: Můžete vytvořit zásady podmíněného přístupu pro podnikové aplikace používající moderní ověřování.  Další informace najdete v tématu věnovaném [podnikovým aplikacím](./../manage-apps/view-applications-portal.md).

**DOTAZ: Můžete pro konkrétního uživatele nebo aplikaci přidat několikero podmínek použití?**</br>
ODPOVĚĎ: Ano, tak, že vytvoříte několik zásad podmíněného přístupu cílí na tyto skupiny nebo aplikace. Pokud uživatel spadá do rozsahu několika podmínek použití, přijetí jedné podmínky použití v čase.
 
**DOTAZ: Co se stane, když uživatel odmítne podmínky použití?**</br>
ODPOVĚĎ: Uživatel je zablokovaný přístup k aplikaci. Uživatel musel znovu přihlásit a přijměte podmínky získal přístup.
 
**DOTAZ: Je možné unaccept podmínky použití, které byly dříve přijali službu?**</br>
ODPOVĚĎ: Je možné [kontrola dříve přijali podmínky použití](#how-users-can-review-their-terms-of-use), ale aktuálně není k dispozici způsob, jak unaccept.

**DOTAZ: Co se stane, pokud také používám Intune podmínky a ujednání?**</br>
ODPOVĚĎ: Pokud jste nakonfigurovali obě Azure AD podmínky použití a [Intune podmínky a ujednání](/intune/terms-and-conditions-create), uživatel bude muset přijmout. Další informace najdete v tématu [Volba správného řešení pro vaši organizaci blogový příspěvek podmínky](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>Další postup

- [Rychlý start: Vyžadovat podmínky použití přijmout před přístupem k cloudových aplikací](../conditional-access/require-tou.md)
- [Osvědčené postupy pro podmíněný přístup v Azure Active Directory](../conditional-access/best-practices.md)
