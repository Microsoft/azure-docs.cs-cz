---
title: Podmínky použití – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Můžete začít používat podmínky použití služby Azure Active Directory k prezentaci informací zaměstnancům nebo hostům před získáním přístupu.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480959"
---
# <a name="azure-active-directory-terms-of-use"></a>Podmínky použití služby Azure Active Directory

Podmínky použití Azure AD poskytuje jednoduchou metodu, kterou organizace mohou použít k prezentaci informací koncovým uživatelům. Toto předkládání zajistí, že se uživatelům zobrazí příslušná právní omezení pro zákonné požadavky nebo požadavky dodržování předpisů. Tento článek popisuje, jak začít s podmínkami použití.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Přehledvidea

Následující video poskytuje rychlý přehled o podmínkách použití.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Další videa najdete v tématu:
- [Jak nasadit podmínky použití ve službě Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Jak zavést podmínky použití ve službě Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Co mohu dělat s podmínkami použití?

Podmínky použití Azure AD má následující možnosti:

- Vyžadovat, aby zaměstnanci nebo hosté před získáním přístupu přijali vaše podmínky použití.
- Vyžadovat, aby zaměstnanci nebo hosté před získáním přístupu přijali vaše podmínky použití na každém zařízení.
- Vyžadovat, aby zaměstnanci nebo hosté přijali vaše podmínky použití podle opakovaného plánu.
- Vyžadovat, aby zaměstnanci nebo hosté před registrací informací o zabezpečení v Azure Multi-Factor Authentication (MFA) přijali vaše podmínky použití.
- Vyžadovat, aby zaměstnanci před registrací informací o zabezpečení v samoobslužném resetování hesla Azure AD (SSPR) přijali vaše podmínky použití.
- Prezentujte obecné podmínky použití pro všechny uživatele ve vaší organizaci.
- Prezentujte konkrétní podmínky použití na základě atributů uživatele (např. doktoři vs. sestry nebo místní vs. zahraniční pracovníci, a to pomocí [dynamických skupin](../users-groups-roles/groups-dynamic-membership.md)).
- Prezentujte konkrétní podmínky použití při přístupu k aplikacím s velkým dopadem na podnikání, jako je Salesforce.
- Současné podmínky použití v různých jazycích.
- Seznam, který přijal nebo nepřijal vaše podmínky použití.
- Pomozte při plnění předpisů o ochraně osobních údajů.
- Zobrazení protokolu podmínek použití aktivity pro dodržování předpisů a audit.
- Vytvořte a spravujte podmínky použití pomocí [rozhraní API aplikace Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (aktuálně ve verzi Preview).

## <a name="prerequisites"></a>Požadavky

Chcete-li používat a konfigurovat podmínky použití Azure AD, musíte mít:

- Předplatné Azure AD Premium P1, P2, EMS E3 nebo EMS E5.
   - Pokud žádné z těchto předplatných nemáte, můžete [získat Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) nebo [povolit zkušební verzi Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jeden z následujících účtů správce pro adresář, který chcete konfigurovat:
   - Globální správce
   - Správce zabezpečení
   - Správce podmíněného přístupu

## <a name="terms-of-use-document"></a>Dokument podmínek použití

Podmínky použití Azure AD používá formát PDF k prezentaci obsahu. Soubor PDF může mít libovolný obsah, jako jsou například stávající smluvní dokumenty, a umožňuje od koncových uživatelů při přihlášení získat jejich souhlas. Pro podporu uživatelů na mobilních zařízeních je doporučená velikost písma v PDF 24 bodů.

## <a name="add-terms-of-use"></a>Přidání podmínek použití

Po dokončení dokumentu o podmínkách použití jej přidejte následujícím postupem.

1. Přihlaste se k Azure jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

   ![Podmíněný přístup – podmínky použití](./media/terms-of-use/tou-blade.png)

1. Klikněte na **Nové podmínky**.

   ![Nové podokno použití pro určení nastavení podmínek použití](./media/terms-of-use/new-tou.png)

1. Do pole **Název** zadejte název podmínek použití, které se budou používat na webu Azure Portal.
1. Do pole **Zobrazované jméno** zadejte název, který se uživatelům zobrazí při přihlášení.
1. V **části Podmínky použití dokumentu**přejděte do konečného termínu použití PDF a vyberte ho.
1. Vyberte jazyk dokumentu o podmínkách použití. Volba jazyka vám umožní nahrát různé jazykové verze podmínek použití. Verze podmínek použití, která se zobrazí koncovému uživateli, závisí na jeho předvolbách prohlížeče.
1. Chcete-li po koncových uživatelích požadovat, aby si před přijetím zobrazili podmínky použití, nastavte **možnost Vyžadovat, aby uživatelé rozbalili podmínky použití** na **Zapnuto**.
1. Chcete-li vyžadovat, aby koncoví uživatelé přijali vaše podmínky použití na každém zařízení, ze kterých přistupují, nastavte **možnost Vyžadovat, aby uživatelé u každého zařízení souhlasili** s **uživatelem On**. Pokud je tato možnost povolena, může být vyžadována instalace dalších aplikací uživateli. Další informace naleznete [v tématu Podmínky použití pro zařízení](#per-device-terms-of-use).
1. Chcete-li vypršet podmínky použití souhlasu podle plánu, nastavte **Platnost souhlasy** **na On**. Pokud je nastavena na zapnuto, zobrazí se dvě další nastavení plánu.

   ![Vyprší nastavení souhlasu pro nastavení počátečního data, četnosti a doby trvání.](./media/terms-of-use/expire-consents.png)

1. Pomocí nastavení **Vypršení platnosti a** **frekvence** určete plán vypršení platnosti podmínek použití. V následující tabulce je uveden výsledek několika ukázkových nastavení:

   | Platnost vyprší od | Frequency | Výsledek |
   | --- | --- | --- |
   | Dnešní datum  | měsíčně | Počínaje dneškem musí uživatelé přijmout podmínky použití a pak znovu přijmout každý měsíc. |
   | Datum v budoucnosti  | měsíčně | Počínaje dneškem musí uživatelé přijmout podmínky použití. Pokud dojde k budoucímu datu, platnost souhlasů vyprší a uživatelé je musí každý měsíc znovu přijmout.  |

   Pokud například nastavíte dobu odzahájení od **1.** **Monthly**

   | Uživatel | Datum prvního přijetí | První datum vypršení platnosti | Druhé datum vypršení platnosti | Třetí datum vypršení platnosti |
   | --- | --- | --- | --- | --- |
   | Alice | 1. ledna | 1. února | 1. března | Duben 1 |
   | Bob | 15. ledna | 1. února | 1. března | Duben 1 |

1. Pomocí nastavení **Doba před opětovným přijetím (dny)** určete počet dní, po které musí uživatel znovu přijmout podmínky použití. To umožňuje uživatelům sledovat jejich vlastní plán. Pokud například nastavíte dobu trvání na **30** dní, může dojít k vypršení platnosti pro dva uživatele:

   | Uživatel | Datum prvního přijetí | První datum vypršení platnosti | Druhé datum vypršení platnosti | Třetí datum vypršení platnosti |
   | --- | --- | --- | --- | --- |
   | Alice | 1. ledna | Jan 31 | 2. března | Duben 1 |
   | Bob | 15. ledna | Únor 14 | 16. března | Duben 15 |

   Je možné použít **expire consents** and **Duration před opětovným přijetím vyžaduje (dny)** nastavení společně, ale obvykle používáte jeden nebo druhý.

1. V **části Podmíněný přístup**použijte seznam šablon zásad **Vynutit s podmíněným přístupem** a vyberte šablonu, která vynucuje podmínky použití.

   ![Rozevírací seznam Podmíněného přístupu pro výběr šablony zásad](./media/terms-of-use/conditional-access-templates.png)

   | Šablona | Popis |
   | --- | --- |
   | **Přístup ke cloudovým aplikacím pro všechny hosty** | Zásady podmíněného přístupu budou vytvořeny pro všechny hosty a všechny cloudové aplikace. Tato zásada má vliv na portál Azure. Po vytvoření může být nutné odhlásit a přihlásit. |
   | **Přístup ke cloudovým aplikacím pro všechny uživatele** | Zásady podmíněného přístupu se vytvoří pro všechny uživatele a všechny cloudové aplikace. Tato zásada má vliv na portál Azure. Po vytvoření budete muset odhlásit a přihlásit. |
   | **Vlastní zásady** | Vyberte uživatele, skupiny a aplikace, na které budou tyto podmínky použití použity. |
   | **Vytvořit zásady podmíněného přístupu později** | Tyto podmínky použití se zobrazí v seznamu řízení grantu při vytváření zásad podmíněného přístupu. |

   >[!IMPORTANT]
   >Ovládací prvky zásad podmíněného přístupu (včetně podmínek použití) nepodporují vynucení účtů služeb. Doporučujeme vyloučit všechny účty služeb ze zásad podmíněného přístupu.

    Vlastní zásady podmíněného přístupu umožňují podrobné podmínky použití až po konkrétní cloudovou aplikaci nebo skupinu uživatelů. Další informace najdete [v tématu Úvodní příručka: Před přístupem ke cloudovým aplikacím vyžadují přijetí podmínek použití.](require-tou.md)

1. Klikněte na **Vytvořit**.

   Pokud jste vybrali vlastní šablonu podmíněného přístupu, zobrazí se nová obrazovka, která umožňuje vytvořit vlastní zásady podmíněného přístupu.

   ![Nové podokno podmíněného přístupu, pokud jste zvolili vlastní šablonu zásad podmíněného přístupu](./media/terms-of-use/custom-policy.png)

   Nyní byste měli vidět nové podmínky použití.

   ![Nové podmínky použití uvedené v podmínkách použití čepele](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Zobrazit sestavu, kdo přijal a odmítl

V okně Terms of Use se zobrazí počet uživatelů, kteří podmínky přijali a odmítli. Tyto počty a kteří přijali / odmítli jsou uloženy po dobu životnosti podmínek použití.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).

   ![Podmínky použití čepele se seznamem počtu uživatelských show přijaly a odmítly](./media/terms-of-use/view-tou.png)

1. Chcete-li zobrazit aktuální stav uživatelů, klepněte na čísla v části **Přijato** nebo **Odmítnuto.**

   ![Podokno Podmínek použití s souhlasy se seznamem uživatelů, kteří přijali](./media/terms-of-use/accepted-tou.png)

1. Chcete-li zobrazit historii jednotlivých uživatelů, klepněte na tři tečky (**...**) a potom **zobrazit historii**.

   ![Zobrazit kontextovou nabídku Historie pro uživatele](./media/terms-of-use/view-history-menu.png)

   V podokně historie zobrazení se zobrazí historie všech přijetí, zamítnutí a vypršení platnosti.

   ![Zobrazit podokno Historie uvádí seznam položek, které historie přijímá, odmítá a vyprší platnost pro uživatele.](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Zobrazení protokolů auditu služby Azure AD

Pokud chcete zobrazit další aktivity, azure ad podmínky použití zahrnuje protokoly auditu. Každý souhlas uživatele spustí událost v protokolech auditu, která je uložena po dobu **30 dnů**. Tyto protokoly můžete zobrazit na portálu nebo stáhnout jako soubor .csv.

Chcete-li začít s protokoly auditu Azure AD, použijte následující postup:

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínky použití.
1. Klikněte na **Zobrazit protokoly auditu**.

   ![Okno podmínky použití se zvýrazněnou možností Zobrazit protokoly auditu](./media/terms-of-use/audit-tou.png)

1. Na obrazovce protokolů auditu Azure AD můžete filtrovat informace pomocí poskytnutých seznamů a cílit na konkrétní informace protokolu auditu.

   Můžete také kliknout na **Stáhnout** a stáhnout tyto informace jako soubor .csv pro místní použití.

   ![Azure AD audit protokoluje výpis obrazovky datum, cílové zásady, iniciované a aktivity](./media/terms-of-use/audit-logs-tou.png)

   Klepnete-li na protokol, zobrazí se podokno s dalšími podrobnostmi o aktivitě.

   ![Podrobnosti o aktivitě protokolu zobrazující aktivitu, stav aktivity, iniciované, cílové zásady](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Jak vypadají podmínky použití pro uživatele

Po vytvoření a vynucení podmínek použití se uživatelům, kteří mají k ony prostor, zobrazí při přihlašování následující obrazovka.

![Příklady použití, které se zobrazí, když se uživatel přihlásí](./media/terms-of-use/user-tou.png)

Uživatelé mohou zobrazit podmínky použití a v případě potřeby pomocí tlačítek přiblížit a oddálit.

![Zobrazení podmínek použití s tlačítky lupy](./media/terms-of-use/zoom-buttons.png)

Následující obrazovka ukazuje, jak vypadají podmínky použití na mobilních zařízeních.

![Příklady použití, které se zobrazí, když se uživatel přihlásí na mobilním zařízení](./media/terms-of-use/mobile-tou.png)

Uživatelé jsou povinni přijmout podmínky použití pouze jednou a nebudou vidět podmínky použití znovu na následné přihlášení.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak mohou uživatelé zkontrolovat své podmínky použití

Uživatelé mohou zkontrolovat a zobrazit podmínky použití, které přijali pomocí následujícího postupu.

1. Přihlaste [https://myapps.microsoft.com](https://myapps.microsoft.com)se do .
1. V pravém horním rohu klikněte na své jméno a vyberte **Profil**.

   ![Web MyApps s otevřeným podoknem uživatele](./media/terms-of-use/tou14.png)

1. Na stránce svého profilu klikněte na **Přečíst podmínky použití**.

   ![Stránka profilu uživatele s odkazem Zkontrolovat podmínky použití](./media/terms-of-use/tou13a.png)

1. Tady si můžete přečíst podmínky použití, které jste přijali.

## <a name="edit-terms-of-use-details"></a>Upravit podrobnosti o podmínkách použití

Můžete upravit některé podrobnosti o podmínkách použití, ale nemůžete upravit existující dokument. Následující postup popisuje, jak upravit podrobnosti.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínky použití, které chcete upravit.
1. Klepněte na **tlačítko Upravit termíny**.
1. V podokně Upravit podmínky použití změňte název, zobrazovaný název nebo požadujte, aby uživatelé rozbalili hodnoty.

   Pokud existují další nastavení, která chcete změnit, například dokument PDF, vyžadovat, aby uživatelé souhlasili na každém zařízení, vypršení platnosti souhlasu, dobu trvání před opětovným přijetím nebo zásady podmíněného přístupu, musíte vytvořit nové podmínky použití.

   ![Upravit podokno použití zobrazující název a rozbalit možnosti](./media/terms-of-use/edit-tou.png)

1. Kliknutím na **Uložit** uložte změny.

   Po uložení změn nebudou uživatelé muset tyto úpravy znovu přijmout.

## <a name="add-a-terms-of-use-language"></a>Přidání jazyka použití

Následující postup popisuje, jak přidat podmínky použití jazyka.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínky použití, které chcete upravit.
1. V podokně podrobností klikněte na kartu **Jazyky.**

   ![Vybrané podmínky použití a zobrazení karty Jazyky v podokně podrobností](./media/terms-of-use/languages-tou.png)

1. Klepněte na tlačítko **Přidat jazyk**.
1. V podokně Přidat podmínky použití jazyka nahrajte lokalizovaný PDF a vyberte jazyk.

   ![Přidání podokna jazyka s podmínkami použití s možnostmi nahrávání lokalizovaných PDF](./media/terms-of-use/language-add-tou.png)

1. Kliknutím na **Přidat** přidáte jazyk.

## <a name="per-device-terms-of-use"></a>Podmínky použití pro zařízení

**Vyžadovat, aby uživatelé u každé nastavení zařízení souhlasili,** umožňuje vyžadovat, aby koncoví uživatelé přijali vaše podmínky použití na všech zařízeních, ze kterých přistupují. Koncový uživatel bude muset zaregistrovat své zařízení ve službě Azure AD. Když je zařízení zaregistrováno, ID zařízení se používá k vynucení podmínek použití na každém zařízení.

Zde je seznam podporovaných platforem a softwaru.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Ostatní |
> | --- | --- | --- | --- | --- |
> | **Native app** | Ano | Ano | Ano |  |
> | **Microsoft Edge** | Ano | Ano | Ano |  |
> | **Internet Explorer** | Ano | Ano | Ano |  |
> | **Chrom (s rozšířením)** | Ano | Ano | Ano |  |

Podmínky použití pro zařízení mají následující omezení:

- Zařízení lze připojit pouze k jednomu klientovi.
- Uživatel musí mít oprávnění k připojení k zařízení.
- Aplikace Registrace Intune není podporovaná.
- Uživatelé Azure AD B2B nejsou podporovány.

Pokud zařízení uživatele není připojeno, obdrží zprávu, že se k němu musí připojit. Jejich zkušenosti budou záviset na platformě a softwaru.

### <a name="join-a-windows-10-device"></a>Připojení k zařízení s Windows 10

Pokud uživatel používá Windows 10 a Microsoft Edge, obdrží zprávu podobnou následující [připojení k jejich zařízení](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 a Microsoft Edge – zpráva oznamující, že vaše zařízení musí být zaregistrováno](./media/terms-of-use/per-device-win10-edge.png)

Pokud používají Chrome, budou vyzváni k instalaci [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrace iOS zařízení

Pokud uživatel používá iOS zařízení, bude vyzván k instalaci [aplikace Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registrace zařízení se systémem Android

Pokud uživatel používá zařízení Android, bude vyzván k instalaci [aplikace Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Browsers

Pokud uživatel používá prohlížeč, který není podporován, bude požádán o použití jiného prohlížeče.

![Zpráva oznamující, že vaše zařízení musí být zaregistrováno, ale prohlížeč není podporován.](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Odstranit podmínky použití

Staré podmínky použití můžete odstranit pomocí následujícího postupu.

1. Přihlaste se k Azure a přejděte na stránku funkce **Terms of Use** na adrese [https://aka.ms/catou](https://aka.ms/catou).
1. Vyberte podmínky použití, které chcete odebrat.
1. Klikněte na **Odstranit podmínky**.
1. Ve zprávě s dotazem, jestli chcete pokračovat, která se zobrazí, klikněte na **Ano**.

   ![Zpráva s žádostí o potvrzení odstranění podmínek použití](./media/terms-of-use/delete-tou.png)

   Podmínky použití by se vám již neměly vidět.

## <a name="deleted-users-and-active-terms-of-use"></a>Smazaní uživatelé a aktivní podmínky použití

Ve výchozím nastavení zůstane odstraněný uživatel ve službě Azure AD v odstraněném stavu po dobu 30 dnů, během kterých ho v případě potřeby může správce obnovit. Po 30 dnech je tento uživatel odstraněn trvale. Kromě toho může globální správce pomocí portálu Azure Active Directory explicitně [trvale odstranit nedávno odstraněného uživatele](../fundamentals/active-directory-users-restore.md) ještě před dosažením tohoto časového období. Jeden uživatel byl trvale odstraněn, následná data o tomto uživateli budou odebrána z aktivních podmínek použití. Informace o auditování o odstraněných uživatelích zůstávají v protokolu auditu.

## <a name="policy-changes"></a>Změny zásad

Zásady podmíněného přístupu se projeví okamžitě. V takovém případě správce začne vidět "smutné mraky" nebo "Problémy tokenu Azure AD". Správce se musí odhlásit a znovu přihlásit, aby splnil nové zásady.

> [!IMPORTANT]
> Příslušní uživatelé se musí odhlásit a znovu přihlásit, aby vyhověli novým zásadám:
>
> - zásady podmíněného přístupu jsou povoleny za podmínek použití
> - nebo pokud se vytvoří druhé podmínky použití.

## <a name="b2b-guests-preview"></a>B2B hosté (náhled)

Většina organizací má zavedený proces, kdy jejich zaměstnanci souhlasí s podmínkami použití a prohlášeními o zásadách ochrany osobních údajů své organizace. Ale jak můžete vynutit stejné souhlasy pro hosty Azure AD business-to-business (B2B), když jsou přidány přes SharePoint nebo Teams? Pomocí podmíněného přístupu a podmínek použití můžete vynutit zásady přímo vůči uživatelům typu Host B2B. Během toku uplatnění pozvánky se uživateli zobrazí podmínky použití. Tato podpora je v současné době ve verzi Preview.

Podmínky použití se zobrazí jenom v případě, že uživatel má účet hosta ve službě Azure AD. SharePoint Online má momentálně [prostředí pro externí sdílení ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) pro sdílení dokumentu nebo složky, která nevyžaduje, aby uživatel měl účet hosta. V tomto případě se nezobrazí podmínky použití.

![Podokno Uživatelé a skupiny – karta Zahrnout se zaškrtnutou možností Všichni uživatelé typu Host](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Podpora cloudových aplikací (Preview)

Podmínky použití se můžou použít pro různé cloudové aplikace, jako je Azure Information Protection a Microsoft Intune. Tato podpora je v současné době ve verzi Preview.

### <a name="azure-information-protection"></a>Azure Information Protection

Můžete nakonfigurovat zásady podmíněného přístupu pro aplikaci Azure Information Protection a vyžadovat podmínky použití, když uživatel přistupuje k chráněnému dokumentu. Tím se spustí podmínky použití před uživatelem přístup k chráněnému dokumentu poprvé.

![Podokno Cloudových aplikací s vybranou aplikací Microsoft Azure Information Protection](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrace Microsoft Intune

Můžete nakonfigurovat zásady podmíněného přístupu pro aplikaci Microsoft Intune Enrollment a před registrací zařízení v Intune vyžadovat podmínky použití. Další informace najdete v tématu Čtení [výběru správného řešení podmínek pro blogový příspěvek vaší organizace](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Podokno Cloudových aplikací s vybranou aplikací Microsoft Intune](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Aplikace Intune Enrollment není podporovaná pro [podmínky použití pro zařízení](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak poznám, kdy/jestli uživatel přijal podmínky použití?**<br />
A: Na podmínky použití čepel, klikněte na číslo v části **Přijato**. Můžete také zobrazit nebo prohledat aktivitu přijmout v protokolech auditu Azure AD. Další informace naleznete v tématu Zobrazení sestavy, kdo přijal a odmítl a [zobrazit protokoly auditu služby Azure AD](#view-azure-ad-audit-logs).

**Otázka: Jak dlouho se informace uchovávají?**<br />
A: Uživatel počítá v přehledu podmínek použití a kdo přijal / odmítl jsou uloženy po dobu životnosti podmínek použití. Protokoly auditu Azure AD jsou uloženy po dobu 30 dnů.

**Otázka: Proč se mi v sestavě podmínek použití vs. protokoly auditu Azure AD zobrazuje jiný počet souhlasů?**<br />
A: Podmínky použití sestavy je uloženpo dobu životnosti těchto podmínek použití, zatímco protokoly auditu Azure AD jsou uloženy po dobu 30 dnů. Sestava podmínek použití také zobrazuje pouze aktuální stav souhlasu uživatelů. Pokud například uživatel odmítne a potom přijme, sestava podmínek použití zobrazí pouze přijetí tohoto uživatele. Pokud potřebujete zobrazit historii, můžete použít protokoly auditu Azure AD.

**Otázka: Pokud upravím podrobnosti o podmínkách použití, vyžaduje to, aby uživatelé znovu přijali?**<br />
Odpověď: Ne, pokud správce upraví podrobnosti o podmínkách použití (jméno, zobrazované jméno, vyžaduje, aby uživatelé rozbalili nebo přidali jazyk), nevyžaduje, aby uživatelé nové podmínky znovu přijali.

**Otázka: Mohu aktualizovat existující podmínky použití dokumentu?**<br />
Odpověď: V současné době nelze aktualizovat existující podmínky použití dokumentu. Chcete-li změnit podmínky použití dokumentu, budete muset vytvořit nové podmínky použití instance.

**Otázka: Pokud jsou hypertextové odkazy v dokumentu PDF s podmínkami použití, budou na ně moci koncové uživatelé kliknout?**<br />
A: Ano, koncoví uživatelé mohou vybrat hypertextové odkazy na další stránky, ale odkazy na oddíly v dokumentu nejsou podporovány.

**Otázka: Podporují se podmínky použití ve více jazycích?**<br />
Odpověď: Ano. V současné době existuje 108 různých jazyků, které může správce nakonfigurovat pro jednu dobu použití. Správce může nahrát více dokumentů PDF a označit je odpovídajícím jazykem (až 108). Když se koncoví uživatelé přihlásí, podíváme se na jejich jazykové předvolby prohlížeče a zobrazíme odpovídající dokument. Pokud neexistuje žádná shoda, zobrazíme výchozí dokument, což je první dokument, který je odeslán.

**Otázka: Kdy se podmínky použití aktivují?**<br />
Odpověď: Podmínky použití se aktivují během přihlašování.

**Otázka: Na které aplikace je možné podmínkami použití cílit?**<br />
A: Můžete vytvořit zásady podmíněného přístupu na podnikové aplikace pomocí moderního ověřování. Další informace najdete v tématu věnovaném [podnikovým aplikacím](./../manage-apps/view-applications-portal.md).

**Otázka: Je možné pro konkrétního uživatele nebo aplikaci přidat několikero podmínek použití?**<br />
A: Ano, vytvořením více zásad podmíněného přístupu zaměřených na tyto skupiny nebo aplikace. Pokud uživatel spadá do rozsahu více podmínek použití, přijímají jednu podmínky použití najednou.

**Otázka: Co se stane, když uživatel odmítne podmínky použití?**<br />
Odpověď: Uživatel bude mít k zablokovaný přístup k aplikaci. Uživatel by se musel znovu přihlásit a přijmout podmínky, aby získal přístup.

**Otázka: Je možné zrušit podmínky použití, které byly dříve přijaty?**<br />
A: Můžete [zkontrolovat dříve přijaté podmínky použití](#how-users-can-review-their-terms-of-use), ale v současné době neexistuje způsob, jak zrušit.

**Otázka: Co se stane, když taky používám podmínky intune?**<br />
A: Pokud jste nakonfigurovali oba podmínky použití Azure AD a [Podmínky Intune](/intune/terms-and-conditions-create), uživatel bude muset přijmout obojí. Další informace najdete v [tématu Výběr správného řešení podmínek pro blogový příspěvek vaší organizace](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**Otázka: Jaké koncové body používají podmínky použití služby pro ověřování?**<br />
A: Podmínky použití využívá následující koncové body https://tokenprovider.termsofuse.identitygovernance.azure.com https://account.activedirectory.windowsazure.compro ověřování: a . Pokud vaše organizace má seznam povolených adres URL pro registraci, budete muset přidat tyto koncové body do seznamu povolených, spolu s koncovými body Azure AD pro přihlášení.

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka: Před přístupem ke cloudovým aplikacím vyžadovat přijetí podmínek použití.](require-tou.md)
- [Doporučené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md)
