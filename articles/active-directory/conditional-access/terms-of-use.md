---
title: Podmínky použití-Azure Active Directory | Microsoft Docs
description: Začněte používat Azure Active Directory podmínek použití pro prezentování informací zaměstnancům nebo hostům před získáním přístupu.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 12/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1829c69510568b0f9a8cec7fb7d2d57be8515d3
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509984"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory podmínek použití

Azure AD terms of use poskytuje jednoduchou metodu, kterou můžou organizace používat k prezentování informací koncovým uživatelům. Toto předkládání zajistí, že se uživatelům zobrazí příslušná právní omezení pro zákonné požadavky nebo požadavky dodržování předpisů. Tento článek popisuje, jak začít s podmínkami použití (podmínky použití).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Přehled videí

Následující video poskytuje rychlý přehled podmínek použití.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Další videa najdete v těchto tématech:
- [Nasazení podmínek použití v Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Postup uvedení podmínek použití v Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Co můžu s podmínkami použití použít?

Funkce Azure AD terms of use mají tyto možnosti:

- Než budete moct získat přístup, je nutné, aby zaměstnanci nebo hosty přijali vaše podmínky použití.
- Než se dostanete k přístupu, vyžadují zaměstnanci nebo hosty, aby na každém zařízení přijali vaše podmínky použití.
- Požaduje, aby zaměstnanci nebo hosté přijali vaše podmínky použití podle opakovaného plánu.
- Před registrací informací o zabezpečení v Azure AD Multi-Factor Authentication (MFA) vyžadovat, aby zaměstnanci nebo hosté přijali vaše podmínky použití.
- Před registrací informací o zabezpečení ve službě Azure AD Samoobslužné resetování hesla (SSPR) vyžadovat, aby zaměstnanci přijali vaše podmínky použití.
- Předběžných podmínek použití pro všechny uživatele ve vaší organizaci.
- Prezentovat konkrétní podmínek použití na základě atributů uživatele (např. doktoři vs. sestry nebo místní vs. zahraniční pracovníci, a to pomocí [dynamických skupin](../enterprise-users/groups-dynamic-membership.md)).
- Při přístupu k aplikacím s vysokým obchodním dopadem, jako je Salesforce, máte k dispozici specifické výrazy použití.
- Současné použití podmínek použití v různých jazycích.
- Seznam, který z vašich podmínek použití nebo nepřijal.
- Pomoc při plnění předpisů týkajících se ochrany osobních údajů.
- Zobrazit protokol aktivity použití pro dodržování předpisů a audit.
- Vytvářejte a spravujte podmínek použití pomocí [rozhraní Microsoft Graph API](/graph/api/resources/agreement?view=graph-rest-beta) (aktuálně ve verzi Preview).

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít a nakonfigurovat používání podmínek Azure AD, musíte mít:

- Předplatné Azure AD Premium P1, P2, EMS E3 nebo EMS E5.
   - Pokud žádné z těchto předplatných nemáte, můžete [získat Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) nebo [povolit zkušební verzi Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jeden z následujících účtů správce pro adresář, který chcete konfigurovat:
   - Globální správce
   - Správce zabezpečení
   - Správce podmíněného přístupu

## <a name="terms-of-use-document"></a>Dokument podmínek použití

Azure AD terms of use používá formát PDF k prezentaci obsahu. Soubor PDF může mít libovolný obsah, jako jsou například stávající smluvní dokumenty, a umožňuje od koncových uživatelů při přihlášení získat jejich souhlas. Pro podporu uživatelů na mobilních zařízeních je doporučená velikost písma v PDF 24 bodů.

## <a name="add-terms-of-use"></a>Přidat podmínek použití

Po dokončení použití dokumentu s podmínkami používání použijte následující postup k jeho přidání.

1. Přihlaste se k Azure jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

   ![Podmíněný přístup – okno Podmínky použití](./media/terms-of-use/tou-blade.png)

1. Klikněte na **Nové podmínky**.

   ![Nové období použití, které umožňuje zadat nastavení podmínek použití](./media/terms-of-use/new-tou.png)

1. Do pole **název** zadejte název podmínek použití, který bude použit v Azure Portal.
1. Do pole **Zobrazovaný název** zadejte název, který se uživatelům zobrazí při přihlášení.
1. V případě **podmínky použití dokumentu** přejděte k dokončeným podmínkám použití PDF a vyberte ji.
1. Vyberte jazyk pro vaše dokumenty s podmínkami použití. Volba jazyka vám umožní nahrát různé jazykové verze podmínek použití. Verze podmínek použití, která se zobrazí koncovému uživateli, závisí na jeho předvolbách prohlížeče.
1. Chcete-li, aby koncoví uživatelé před přijetím zobrazili podmínek použití, nastavte možnost **vyžadovat, aby uživatelé rozšířili podmínek použití** na **zapnuto**.
1. Pokud chcete, aby koncoví uživatelé přijali vaše podmínky použití na každém zařízení, **ze kterého** přistupují, nastavte vyžadovat od **uživatelů souhlas na každém zařízení** . Pokud je tato možnost povolena, mohou být uživatelé vyzváni k instalaci dalších aplikací. Další informace najdete v tématu věnovaném [podmínkám použití v rámci zařízení](#per-device-terms-of-use).
1. Pokud chcete, aby vypršela platnost podmínek použití podle plánu, nastavte na **zapnuto** **vypršení platnosti** . Pokud je nastaveno na on, zobrazí se dvě další nastavení plánu.

   ![Nastavení souhlasu s vypršenou platností pro nastavení počátečního data, četnosti a trvání](./media/terms-of-use/expire-consents.png)

1. Pokud chcete zadat plán pro **vyprší** platnosti podmínek použití, použijte nastavení **frekvence** od do. Následující tabulka ukazuje výsledek pro několik příkladů nastavení:

   | Platnost začíná dnem | Frekvence | Výsledek |
   | --- | --- | --- |
   | Dnešní datum  | Měsíčně | Od dnešního dne musí uživatelé přijmout podmínky použití a pak každý měsíc znovu přijmout. |
   | Datum v budoucnosti  | Měsíčně | Od dnešního dne musí uživatelé přijmout podmínky použití. Když dojde k budoucímu datu, vyprší platnost souhlasu a pak se uživatelé musí každý měsíc znovu akceptovat.  |

   Pokud například nastavíte vypršení platnosti začínající na **1** a četnost na **měsíčně**, v této části se může vyskytnout vypršení platnosti pro dva uživatele:

   | Uživatel | Datum prvního přijetí | Datum prvního vypršení platnosti | Datum ukončení platnosti druhého dne | Třetí datum vypršení platnosti |
   | --- | --- | --- | --- | --- |
   | Náležící | 1. ledna | 1. února | Březen 1 | Duben 1 |
   | Bob | 15. ledna | 1. února | Březen 1 | Duben 1 |

1. Chcete-li zadat počet dní, než uživatel musí znovu přijmout podmínky použití, použijte nastavení **Doba trvání před přijetím vyžaduje (dny)** . To uživatelům umožňuje postupovat podle vlastního plánu. Pokud například nastavíte dobu trvání na **30** dní, v tomto příkladu se může vyskytnout vypršení platnosti pro dva uživatele:

   | Uživatel | Datum prvního přijetí | Datum prvního vypršení platnosti | Datum ukončení platnosti druhého dne | Třetí datum vypršení platnosti |
   | --- | --- | --- | --- | --- |
   | Náležící | 1. ledna | 31. ledna | Březen 2 | Duben 1 |
   | Bob | 15. ledna | 14. února | 16. března | Duben 15 |

   Je možné použít souhlas a dobu **platnosti vypršení platnosti** **před přijetím hodnoty (dny)** , ale obvykle používáte jednu nebo druhou.

1. V části **podmíněný přístup** použijte seznam **vykonat se šablonou zásad podmíněného přístupu** a vyberte šablonu, která vynutila podmínky použití.

   ![Rozevírací seznam pro podmíněný přístup pro výběr šablony zásad](./media/terms-of-use/conditional-access-templates.png)

   | Template (Šablona) | Popis |
   | --- | --- |
   | **Přístup ke cloudovým aplikacím pro všechny hosty** | Vytvoří se zásada podmíněného přístupu pro všechny hosty a všechny cloudové aplikace. Tato zásada má vliv na Azure Portal. Po vytvoření budete možná muset odhlásit a přihlásit se. |
   | **Přístup ke cloudovým aplikacím pro všechny uživatele** | Vytvoří se zásada podmíněného přístupu pro všechny uživatele a všechny cloudové aplikace. Tato zásada má vliv na Azure Portal. Po vytvoření budete muset odhlásit a přihlásit se. |
   | **Vlastní zásady** | Vyberte uživatele, skupiny a aplikace, pro které se použijí tyto podmínkami použití. |
   | **Vytvořit zásady podmíněného přístupu později** | Tyto podmínky použití se zobrazí v seznamu udělení řízení při vytváření zásad podmíněného přístupu. |

   >[!IMPORTANT]
   >Řízení zásad podmíněného přístupu (včetně podmínek použití) nepodporují vynucování u účtů služeb. Doporučujeme, abyste vyloučili všechny účty služeb ze zásad podmíněného přístupu.

    Vlastní zásady podmíněného přístupu umožňují podrobné podmínky použití, a to až do konkrétní cloudové aplikace nebo skupiny uživatelů. Další informace najdete v tématu [rychlý Start: vyžadování podmínek použití, které se mají přijmout před přístupem ke cloudovým aplikacím](require-tou.md).

1. Klikněte na **Vytvořit**.

   Pokud jste vybrali vlastní šablonu podmíněného přístupu, zobrazí se nová obrazovka, která vám umožní vytvořit vlastní zásady podmíněného přístupu.

   ![Nový podoknem podmíněný přístup, pokud jste zvolili šablonu vlastní zásady podmíněného přístupu](./media/terms-of-use/custom-policy.png)

   Nyní byste měli vidět vaše nové podmínkami použití.

   ![Nové podmínkami použití uvedené v okně podmínek použití](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Zobrazit sestavu, která přijala a odmítla

V okně Terms of Use se zobrazí počet uživatelů, kteří podmínky přijali a odmítli. Tyto počty a osoby přijaté nebo odmítnuté po dobu života podmínek použití jsou uloženy.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

   ![Okno Podmínky použití výpis počtu přijatých a odmítnutých uživatelských zobrazení](./media/terms-of-use/view-tou.png)

1. Pro účely podmínek použití klikněte na čísla v části **přijato** nebo **Odmítnuto** pro zobrazení aktuálního stavu pro uživatele.

   ![Podokno souhlasu Podmínky použití se seznamem uživatelů, kteří přijali](./media/terms-of-use/accepted-tou.png)

1. Chcete-li zobrazit historii pro jednotlivé uživatele, klikněte na tlačítko se třemi tečkami (**...**) a pak **Zobrazte historii**.

   ![Místní nabídka zobrazení historie pro uživatele](./media/terms-of-use/view-history-menu.png)

   V podokně historie zobrazení se zobrazí historie všech přijetí, odmítnutí a vypršení platnosti.

   ![Zobrazit podokno historie obsahuje historii přijetí, odmítnutí a vypršení platnosti uživatele.](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Zobrazit protokoly auditu Azure AD

Pokud chcete zobrazit další aktivitu, služba Azure AD terms of use zahrnuje protokoly auditu. Každý souhlas uživatele aktivuje událost v protokolech auditu, které jsou uložené po dobu **30 dnů**. Tyto protokoly můžete zobrazit na portálu nebo stáhnout jako soubor .csv.

Pokud chcete začít s protokoly auditu Azure AD, použijte následující postup:

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínek použití.
1. Klikněte na **Zobrazit protokoly auditu**.

   ![Okno Podmínky použití s zvýrazněnou možností zobrazit protokoly auditu](./media/terms-of-use/audit-tou.png)

1. Na obrazovce protokoly auditu Azure AD můžete filtrovat informace pomocí uvedených seznamů a zaměřit se na konkrétní informace protokolu auditu.

   Můžete také kliknout na **Stáhnout** a stáhnout tyto informace jako soubor .csv pro místní použití.

   ![Datum výpisu obrazovky protokolů auditu Azure AD, cílové zásady, zahájení a aktivita](./media/terms-of-use/audit-logs-tou.png)

   Pokud kliknete na protokol, zobrazí se podokno s dalšími podrobnostmi o aktivitě.

   ![Podrobnosti o aktivitě pro protokol ukazující aktivitu, stav aktivity, iniciováno, cílové zásady](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Jaké pojem použití vypadá pro uživatele

Po vytvoření a vykonání podmínek použití se uživatelům, kteří jsou v oboru, zobrazí následující obrazovka během přihlašování.

![Příklady podmínek použití, které se zobrazí, když se uživatel přihlásí](./media/terms-of-use/user-tou.png)

Uživatelé mohou zobrazit podmínek použití a v případě potřeby použít tlačítka k přiblížení a oddálení.

![Zobrazení podmínek použití s tlačítky zvětšení](./media/terms-of-use/zoom-buttons.png)

Na následující obrazovce vidíte, jak se při používání podmínek použití na mobilních zařízeních prohlíží.

![Příklady podmínek použití, které se zobrazí, když se uživatel přihlásí k mobilnímu zařízení](./media/terms-of-use/mobile-tou.png)

Uživatelé se potřebují jenom k tomu, aby přijali podmínky použití jenom jednou a znovu neuvidí podmínky použití při dalších přihlášeních.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak můžou uživatelé zkontrolovat svoje podmínek použití

Pomocí následujícího postupu mohou uživatelé zkontrolovat a zjistit, jaké podmínkami použití přijali.

1. Přihlaste se k webu [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. V pravém horním rohu klikněte na své jméno a vyberte **profil**.

   ![Web aplikace Mojeapl s otevřeným podoknem uživatele](./media/terms-of-use/tou14.png)

1. Na stránce svého profilu klikněte na **Přečíst podmínky použití**.

   ![Stránka profilu pro uživatele, který zobrazuje odkaz na kontrolu podmínek použití](./media/terms-of-use/tou13a.png)

1. Tady si můžete přečíst podmínky použití, které jste přijali.

## <a name="edit-terms-of-use-details"></a>Upravit podrobnosti podmínek použití

Můžete upravit některé podrobnosti podmínek použití, ale nemůžete upravit stávající dokument. Následující postup popisuje, jak upravit podrobnosti.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínkami použití, které chcete upravit.
1. Klikněte na **Upravit výrazy**.
1. V podokně upravit podmínek použití můžete změnit následující:
     - **Název** – jedná se o interní název podmínky použití, který není sdílený s koncovými uživateli.
     - **Zobrazovaný název** – jedná se o název, který koncoví uživatelé uvidí při prohlížení podmínky použití
     - **Vyžadovat, aby uživatelé rozšířili podmínek použití** – když toto nastavení nastavíte **na zapnuto** , vynutí se koncovým příkazem rozšířit požadavky na použití dokumentu před přijetím.
     - Tisk Můžete **aktualizovat existující dokument podmínek použití**
     - Do existujícího podmínky použití můžete přidat jazyk.

   Pokud existují další nastavení, která byste chtěli změnit, třeba dokument PDF, vyžadovat, aby uživatelé souhlasili na každém zařízení, souhlas s vypršenou platností, doba před přijetím nebo zásady podmíněného přístupu, musíte vytvořit nové podmínky použití.

    ![Upravit zobrazení různých možností jazyka ](./media/terms-of-use/edit-terms-use.png)

1. Až budete hotovi, uložte změny kliknutím na **Uložit** .

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Aktualizuje verzi nebo PDF stávajícího podmínek použití.

1.  Přihlaste se k Azure a přejděte na [podmínky použití](https://aka.ms/catou)
2.  Vyberte podmínkami použití, které chcete upravit.
3.  Klikněte na **Upravit výrazy**.
4.  Pro jazyk, ve kterém chcete aktualizovat novou verzi, klikněte na **aktualizovat** ve sloupci akce.
 
     ![Podokno úprav podmínek použití znázorňující možnosti název a rozbalení](./media/terms-of-use/edit-terms-use.png)

5.  V pravém podokně nahrajte PDF novou verzi.
6.  K dispozici je také možnost přepínacího parametru, která **vyžaduje opětovné přijetí** , pokud chcete, aby uživatelé při příštím přihlášení přijali tuto novou verzi. Pokud požadujete, aby se vaši uživatelé znovu přijali, při příštím pokusu o přístup k prostředku definovanému v zásadách podmíněného přístupu se zobrazí výzva, abyste tuto novou verzi přijali. Pokud nepožadujete, aby vaši uživatelé znovu přijali, jejich předchozí souhlas zůstane aktuální a jenom noví uživatelé, kteří nesouhlasí s jeho vypršenou platností nebo jejichž souhlas vyprší, uvidí novou verzi.

    ![Upravit vybranou možnost opětovného přijetí podmínek použití](./media/terms-of-use/re-accept.png)

7.  Jakmile nahrajete nový soubor PDF a rozhodnete se o jeho přijetí, klikněte na tlačítko Přidat v dolní části podokna.
8.  Ve sloupci dokumentu se teď zobrazí nejnovější verze.

## <a name="view-previous-versions-of-a-terms-of-use"></a>Zobrazit předchozí verze podmínek použití

1.  Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese https://aka.ms/catou.
2.  Vyberte podmínkami použití, pro které chcete zobrazit historii verzí.
3.  Klikněte na **jazyky a historii verzí** .
4.  Klikněte na **Zobrazit předchozí verze.**

    ![Podrobnosti dokumentu včetně jazykových verzí](./media/terms-of-use/document-details.png)

5.  Pro stažení této verze můžete kliknout na název dokumentu.

## <a name="see-who-has-accepted-each-version"></a>Zobrazit, kdo přijal jednotlivé verze

1.  Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese https://aka.ms/catou.
2.  Pokud chcete zjistit, kdo podmínky použití aktuálně přijal, klikněte na číslo ve sloupci **přijato** pro podmínky použití, který chcete.
3.  Ve výchozím nastavení na další stránce se zobrazí aktuální stav všech uživatelů, kteří přijmou podmínky použití.
4.  Chcete-li zobrazit předchozí události souhlasu, můžete vybrat možnost **vše** z rozevíracího seznamu **aktuální stav** . Nyní uvidíte jednotlivé události uživatelů v podrobnostech o každé verzi a o tom, co se stalo.
5.  Případně můžete vybrat konkrétní verzi z rozevírací nabídky **verze**  , abyste viděli, kdo tuto konkrétní verzi přijal.


## <a name="add-a-terms-of-use-language"></a>Přidat jazyk podmínek použití

Následující postup popisuje, jak přidat jazyk podmínek použití.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínkami použití, které chcete upravit.
1. Klikněte na **Upravit výrazy** .
1. V dolní části stránky klikněte na **Přidat jazyk** .
1. V podokně Přidat podmínek použití jazyka nahrajte lokalizovaný soubor PDF a vyberte jazyk.

   ![Vybraná Podmínky použití a zobrazení karty jazyky v podokně podrobností](./media/terms-of-use/select-language.png)

1. Klikněte na **Přidat jazyk**.
1. Klikněte na **Uložit**.

1. Kliknutím na **Přidat** přidejte jazyk.

## <a name="per-device-terms-of-use"></a>Podmínkami použití pro jednotlivá zařízení

Možnost **vyžadovat, aby uživatelé souhlasí s každým nastavením zařízení** , vám umožní vyžadovat, aby si koncoví uživatelé přijali vaše podmínky použití na každém zařízení, ze kterého přistupuje. Koncový uživatel bude muset zaregistrovat svoje zařízení ve službě Azure AD. Když je zařízení zaregistrované, použije se ID zařízení k vymáhání podmínek použití na jednotlivých zařízeních.

Tady je seznam podporovaných platforem a softwaru.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Jiné |
> | --- | --- | --- | --- | --- |
> | **Native app** | Ano | Ano | Ano |  |
> | **Microsoft Edge** | Ano | Ano | Ano |  |
> | **Internet Explorer** | Ano | Ano | Ano |  |
> | **Chrome (s příponou)** | Ano | Ano | Ano |  |

Podmínky použití podle zařízení mají následující omezení:

- Zařízení lze připojit pouze k jednomu klientovi.
- Uživatel musí mít oprávnění pro připojení k zařízení.
- Registrační aplikace Intune není podporovaná. Zajistěte, aby se vyloučily ze zásad podmíněného přístupu, které vyžadují podmínky použití.
- Uživatelé Azure AD B2B nejsou podporováni.

Pokud zařízení uživatele není připojené, obdrží zprávu, že se musí připojit k zařízení. Jejich činnost bude záviset na platformě a softwaru.

### <a name="join-a-windows-10-device"></a>Připojení k zařízení s Windows 10

Pokud uživatel používá Windows 10 a Microsoft Edge, zobrazí se mu zpráva podobná následujícímu [připojení zařízení](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 a Microsoft Edge – zpráva indikující, že zařízení musí být zaregistrované](./media/terms-of-use/per-device-win10-edge.png)

Pokud používají Chrome, zobrazí se výzva k instalaci [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrace zařízení se systémem iOS

Pokud uživatel používá zařízení se systémem iOS, bude vyzván k instalaci [aplikace Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registrace zařízení s Androidem

Pokud uživatel používá zařízení se systémem Android, zobrazí se výzva k instalaci [aplikace Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Browsers

Pokud uživatel používá prohlížeč, který není podporován, bude požádán o použití jiného prohlížeče.

![Zpráva indikující, že vaše zařízení musí být zaregistrované, ale prohlížeč není podporovaný.](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Odstranit podmínek použití

Pomocí následujícího postupu můžete odstranit stará pravidla použití.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínky použití, které chcete odebrat.
1. Klikněte na **Odstranit podmínky**.
1. Ve zprávě s dotazem, jestli chcete pokračovat, která se zobrazí, klikněte na **Ano**.

   ![Zpráva s výzvou k potvrzení odstranění podmínek použití](./media/terms-of-use/delete-tou.png)

   Nemusíte už zobrazovat vaše podmínkami použití.

## <a name="deleted-users-and-active-terms-of-use"></a>Odstraněné uživatele a aktivní podmínek použití

Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure AD v odstraněném stavu po dobu 30 dnů, během kterých ho v případě potřeby může správce obnovit. Po 30 dnech je tento uživatel odstraněn trvale. Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](../fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období. Jeden uživatel byl trvale odstraněn, další údaje o tomto uživateli budou odebrány z aktivních podmínek použití. Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="policy-changes"></a>Změny zásad

Zásady podmíněného přístupu se projeví okamžitě. Pokud k tomu dojde, správce začne zobrazovat "cloudy sad JSD" nebo "problémy s tokenem Azure AD". Správce se musí odhlásit a znovu přihlásit, aby splnil novou zásadu.

> [!IMPORTANT]
> Příslušní uživatelé se musí odhlásit a znovu přihlásit, aby vyhověli novým zásadám:
>
> - zásada podmíněného přístupu je povolená pro podmínky použití.
> - nebo pokud se vytvoří druhé podmínky použití.

## <a name="b2b-guests"></a>Hosty B2B

Většina organizací má k dispozici proces, který svým zaměstnancům souhlasí s podmínkami použití a prohlášeními o zásadách ochrany osobních údajů organizace. Jak ale můžete vyhovět stejným souhlasům pro hosty Azure AD Business-to-Business (B2B), když se přidávají prostřednictvím SharePointu nebo týmů? Pomocí podmíněného přístupu a podmínek použití můžete zásadu vyhovět přímo ke uživatelům typu Host B2B. Během postupu pro uplatnění pozvánky se uživatel zobrazí s podmínkami použití. Tato podpora je aktuálně ve verzi Preview.

Podmínky použití se zobrazí jenom v případě, že uživatel má účet hosta v Azure AD. SharePoint Online má aktuálně k dispozici [příjemce externích sdílení služby ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) pro sdílení dokumentu nebo složky, která nevyžaduje, aby uživatel měl účet hosta. V takovém případě se nezobrazuje podmínka použití.

![Podokno uživatelé a skupiny – zahrňte kartu se zaškrtnutou možností všichni uživatelé typu Host.](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Podpora pro cloudové aplikace

Podmínky použití lze použít pro různé cloudové aplikace, například Azure Information Protection a Microsoft Intune. Tato podpora je aktuálně ve verzi Preview.

### <a name="azure-information-protection"></a>Azure Information Protection

Můžete nakonfigurovat zásady podmíněného přístupu pro aplikaci Azure Information Protection a vyžadovat podmínky použití, když uživatel přistupuje k chráněnému dokumentu. Tato akce aktivuje podmínek použití dříve, než uživatel poprvé přistupuje k chráněnému dokumentu.

![Podokno cloudových aplikací s vybraným Microsoft Azure Information Protection aplikací](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrace Microsoft Intune

Můžete nakonfigurovat zásady podmíněného přístupu pro aplikaci Microsoft Intune Enrollment a vyžadovat podmínky použití před registrací zařízení v Intune. Další informace najdete v článku o tom, jak se v [příspěvku na blogu pro vaši organizaci nachází v podobě nejvhodnějšího řešení](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Podokno cloudových aplikací s vybraným Microsoft Intune aplikací](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Registrační aplikace Intune není podporovaná pro účely [podmínek použití](#per-device-terms-of-use)v rámci zařízení.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak poznám, kdy/jestli uživatel přijal podmínky použití?**<br />
Odpověď: v okně Podmínky použití klikněte na číslo v části **přijato**. Můžete také zobrazit nebo vyhledat aktivitu přijmout v protokolech auditu služby Azure AD. Další informace najdete v tématu zobrazení zprávy o tom, kdo přijal a zamítl a [zobrazil protokoly auditu Azure AD](#view-azure-ad-audit-logs).

**Otázka: Jak dlouho se informace uchovávají?**<br />
Odpověď: uživatel se vypočítává v sestavě podmínek použití a který přijatý nebo odmítnut je uložený po dobu životnosti podmínek použití. Protokoly auditu Azure AD se ukládají po dobu 30 dnů.

**Otázka: Proč se v sestavě podmínek použití a v protokolech auditu Azure AD zobrazuje jiný počet souhlasů?**<br />
Odpověď: sestava podmínek použití je uložena po dobu životnosti podmínek použití, zatímco protokoly auditu služby Azure AD jsou uloženy po dobu 30 dnů. Sestava podmínek použití zobrazí také aktuální stav souhlasu uživatele. Pokud uživatel například odmítne a pak přijme, bude sestava podmínky použití zobrazovat pouze přijetí tohoto uživatele. Pokud potřebujete zobrazit historii, můžete použít protokoly auditu Azure AD.

**Otázka: Pokud budu upravovat podrobnosti pro podmínky použití, vyžaduje, aby se uživatelé znovu přijali?**<br />
Odpověď: Pokud správce upraví podrobnosti pro podmínky použití (název, zobrazovaný název, vyžadovat, aby uživatelé rozšířili nebo přidali jazyk), nevyžadují, aby uživatelé znovu přijali nové podmínky.

**Otázka: mohu aktualizovat existující dokument podmínek použití?**<br />
Odpověď: v současné době nelze aktualizovat existující dokument s podmínkami použití. Chcete-li změnit dokument s podmínkami použití, budete muset vytvořit novou podmínku použití instance.

**Otázka: Pokud jsou hypertextové odkazy v dokumentu PDF s podmínkami použití, budou koncoví uživatelé moci kliknout na ně?**<br />
Odpověď: Ano, koncoví uživatelé mohou vybrat hypertextové odkazy na další stránky, ale odkazy na oddíly v dokumentu nejsou podporovány. Hypertextové odkazy z pojmu použití souborů PDF nefungují ani při otevření z portálu Azure AD Mojeapl/MyAccount.

**Otázka: Podporují se podmínky použití ve více jazycích?**<br />
Odpověď: Ano. V současné době existuje 108 různých jazyků, které může správce nakonfigurovat pro jedno z podmínek použití. Správce může nahrát několik dokumentů PDF a označit tyto dokumenty Tagy odpovídajícím jazykem (až 108). Když se koncoví uživatelé přihlásí, podíváme se na jazyk prohlížeče předvolby a zobrazí se odpovídající dokument. Pokud se neshoduje, zobrazí se výchozí dokument, který je prvním odeslaným dokumentem.

**Otázka: Kdy se podmínky použití aktivují?**<br />
Odpověď: Podmínky použití se aktivují během přihlašování.

**Otázka: Na které aplikace je možné podmínkami použití cílit?**<br />
Odpověď: můžete vytvořit zásadu podmíněného přístupu pro podnikové aplikace pomocí moderního ověřování. Další informace najdete v tématu věnovaném [podnikovým aplikacím](./../manage-apps/view-applications-portal.md).

**Otázka: Je možné pro konkrétního uživatele nebo aplikaci přidat několikero podmínek použití?**<br />
Odpověď: Ano, vytvořením několika zásad podmíněného přístupu, které cílí na tyto skupiny nebo aplikace. Pokud uživatel spadá do rozsahu používání více podmínek použití, akceptuje jednu z podmínek použití najednou.

**Otázka: Co se stane, když uživatel odmítne podmínky použití?**<br />
Odpověď: Uživatel bude mít k zablokovaný přístup k aplikaci. Uživatel se musí znovu přihlásit a přijmout podmínky, aby získal přístup.

**Otázka: je možné nepřijmout podmínky použití, které byly dříve přijaty?**<br />
Odpověď: můžete [zkontrolovat dřív přijaté podmínky použití](#how-users-can-review-their-terms-of-use), ale v současné době neexistuje způsob, jak nepřijmout.

**Otázka: co se stane, když používám i podmínky Intune?**<br />
O: Pokud jste nakonfigurovali podmínky použití Azure AD a podmínky [a ujednání služby Intune](/intune/terms-and-conditions-create), bude uživatel muset přijmout obojí. Další informace najdete v příspěvku na [blogu pro vaši organizaci](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)v článku věnovaném nejvhodnějšímu řešení.

**Otázka: Jaké koncové body používá používání služby k ověřování?**<br />
Odpověď: Podmínky použití využívá následující koncové body pro ověřování: https://tokenprovider.termsofuse.identitygovernance.azure.com a https://account.activedirectory.windowsazure.com . Pokud má vaše organizace seznam povolených adres URL pro registraci, budete je muset přidat do seznamu povolených bodů spolu s koncovými body služby Azure AD pro přihlášení.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: vyžadovat, aby se před přístupem ke cloudovým aplikacím přijaly podmínkami použití](require-tou.md)