---
title: Plánování zásad podmíněného přístupu v Azure Active Directory | Microsoft Docs
description: V tomto článku se dozvíte, jak naplánovat zásady podmíněného přístupu pro Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671818"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Postupy: plánování nasazení podmíněného přístupu v Azure Active Directory

Plánování nasazení podmíněného přístupu je důležité, aby bylo zajištěno, že budete mít k požadované strategie přístupu pro aplikace a prostředky ve vaší organizaci. Věnujte většinu času ve fázi plánování nasazení, abyste mohli navrhovat různé zásady, které potřebujete k udělení nebo blokování přístupu uživatelům v rámci podmínek, které zvolíte. Tento dokument popisuje kroky, které byste měli provést při implementaci zabezpečených a efektivních zásad podmíněného přístupu. Než začnete, ujistěte se, že rozumíte tomu, jak [podmíněný přístup](overview.md) funguje a kdy byste ho měli použít.

## <a name="what-you-should-know"></a>Co byste měli vědět

Podmíněný přístup si můžete představit jako rozhraní, které umožňuje řídit přístup k aplikacím a prostředkům vaší organizace místo samostatné funkce. V důsledku toho některé nastavení podmíněného přístupu vyžaduje konfiguraci dalších funkcí. Můžete například nakonfigurovat zásadu, která reaguje na konkrétní [úroveň rizika přihlašování](../identity-protection/howto-identity-protection-configure-risk-policies.md). Zásada, která je založena na úrovni rizika přihlašování, ale vyžaduje, aby byla povolena [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) .

Pokud jsou vyžadovány další funkce, může být také nutné získat související licence. Například když je podmíněný přístup Azure AD Premium funkce P1, ochrana identity vyžaduje licenci Azure AD Premium P2.

Existují dva typy zásad podmíněného přístupu: základní a standardní. [Základní](baseline-protection.md) zásady podmíněného přístupu jsou předdefinované. Cílem těchto zásad je ujistit se, že máte povolenou alespoň úroveň zabezpečení. Základní zásady. Základní zásady jsou k dispozici ve všech edicích služby Azure AD a poskytují pouze omezené možnosti vlastního nastavení. Pokud scénář vyžaduje větší flexibilitu, zakažte základní zásady a implementujte své požadavky do vlastních standardních zásad.

V případě standardních zásad podmíněného přístupu můžete upravit všechna nastavení a upravit zásady na vaše obchodní požadavky. Standardní zásady vyžadují licenci Azure AD Premium P1.

>[!NOTE]
> Doporučujeme použít zásady podmíněného přístupu na základě zařízení Azure AD, abyste získali nejlepší vynucování po počátečním ověření zařízení. Patří sem uzavírací relace, pokud zařízení nedodržuje předpisy a tok kódu zařízení.

## <a name="draft-policies"></a>Koncepty zásad

Azure Active Directory podmíněný přístup umožňuje přenést ochranu vašich cloudových aplikací na novou úroveň. V této nové úrovni je způsob, jakým máte přístup ke cloudové aplikaci, založený na dynamickém vyhodnocení zásad místo konfigurace statického přístupu. Pomocí zásad podmíněného přístupu můžete definovat odpověď (**to udělat**) do podmínky přístupu (**Pokud k tomu dojde**).

![Důvod a odpověď](./media/plan-conditional-access/10.png)

Definujte všechny zásady podmíněného přístupu, které chcete implementovat pomocí tohoto modelu plánování. Plánování cvičení:

- Umožňuje sestavovat odpovědi a podmínky pro jednotlivé zásady.
- Výsledkem je dobře dokumentovaný katalog zásad podmíněného přístupu pro vaši organizaci. 

Katalog můžete použít k vyhodnocení, jestli implementace zásad odráží obchodní požadavky vaší organizace. 

Pro vytvoření zásad podmíněného přístupu pro vaši organizaci použijte následující příklad šablony:

|V *takovém* případě:|Pak postupujte *takto:*|
|-|-|
|Byl proveden pokus o přístup:<br>– Do cloudové aplikace *<br>– podle uživatelů a skupin*<br>Použití<br>-Podmínka 1 (například mimo síť Corp)<br>-Condition 2 (například platformy zařízení)|Zablokovat přístup k aplikaci|
|Byl proveden pokus o přístup:<br>– Do cloudové aplikace *<br>– podle uživatelů a skupin*<br>Použití<br>-Podmínka 1 (například mimo síť Corp)<br>-Condition 2 (například platformy zařízení)|Udělit přístup pomocí (a):<br>-Požadavek 1 (například MFA)<br>– Požadavek 2 (například dodržování předpisů zařízením)|
|Byl proveden pokus o přístup:<br>– Do cloudové aplikace *<br>– podle uživatelů a skupin*<br>Použití<br>-Podmínka 1 (například mimo síť Corp)<br>-Condition 2 (například platformy zařízení)|Udělit přístup pomocí (nebo):<br>-Požadavek 1 (například MFA)<br>– Požadavek 2 (například dodržování předpisů zařízením)|

Minimálně, pokud k **tomu dojde** , definuje objekt zabezpečení (**který**), který se pokusí o přístup ke cloudové aplikaci (**co**). V případě potřeby můžete také zahrnout **způsob, jakým** je proveden pokus o přístup. V podmíněném přístupu prvky definující, kdo, co a jak jsou známy jako podmínky. Další informace najdete v tématu [co jsou podmínky v Azure Active Directory podmíněný přístup?](concept-conditional-access-conditions.md) 

V **takovém**případě můžete definovat odpověď na zásadu na podmínku přístupu. V odpovědi zablokujete nebo udělíte přístup s dalšími požadavky, například službou Multi-Factor Authentication (MFA). Úplný přehled najdete [v tématu Co jsou ovládací prvky přístupu v Azure Active Directory podmíněný přístup?](controls.md)  

Kombinace podmínek s ovládacími prvky přístupu představuje zásadu podmíněného přístupu.

![Důvod a odpověď](./media/plan-conditional-access/51.png)

Další informace najdete v tématu [co je potřeba k práci se zásadami](best-practices.md#whats-required-to-make-a-policy-work).

V tuto chvíli je vhodný čas pro rozhodování o standardu pojmenování pro vaše zásady. Standard pojmenování vám pomůže najít zásady a pochopit jejich účel, aniž byste je museli otevírat na portálu pro správu Azure. Pojmenujte zásadu, která se má zobrazit:

- Pořadové číslo
- Cloudová aplikace, na kterou se vztahuje
- Odpověď
- Komu se vztahuje
- Když se použije (Pokud je k dispozici)
 
![Standardní pojmenování](./media/plan-conditional-access/11.png)

I když popisný název pomáhá udržet přehled implementace podmíněného přístupu, pořadové číslo je užitečné v případě, že v konverzaci potřebujete odkazovat na zásadu. Pokud například provedete uživatele s dalšími uživateli na telefonu, můžete požádat, aby otevřeli zásady EM063 k vyřešení problému.

Například následující název uvádí, že zásady vyžadují MFA pro uživatele marketingu v externích sítích pomocí aplikace Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Kromě aktivních zásad je vhodné implementovat taky zakázané zásady, které fungují jako sekundární [ovládací prvky odolného přístupu v případě výpadků nebo mimořádných scénářů](../authentication/concept-resilient-controls.md). Vaše standardní pojmenování zásad pro nepředvídané pracovní postupy by měly zahrnovat několik dalších položek: 

- na začátku se `ENABLE IN EMERGENCY` název, který se má stát mezi ostatními zásadami.
- Může se jednat o název přerušení, na který se má vztahovat.
- Pořadové číslo objednávání, které správci pomůžou zjistit, v jakém pořadí mají být povolené zásady pořadí. 

Například následující název označuje, že tato zásada je první ze čtyř zásad, které byste měli povolit, pokud dojde k přerušení MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Plánování zásad

Při plánování řešení zásad podmíněného přístupu posuďte, jestli potřebujete vytvořit zásady, abyste dosáhli následujících výsledků. 

### <a name="block-access"></a>Blokovat přístup

Možnost blokovat přístup je výkonná, protože:

- Trumfová všechna ostatní přiřazení pro uživatele
- Má zablokovat, aby se celá organizace mohla přihlásit ke svému tenantovi.
 
Pokud chcete blokovat přístup pro všechny uživatele, měli byste aspoň z těchto zásad vyloučit jednoho uživatele (obvykle účty pro nouzový přístup). Další informace najdete v tématu [Výběr uživatelů a skupin](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Vyžadování MFA

Chcete-li zjednodušit přihlašování uživatelů, můžete jim dovolit, aby se přihlásili k vašim cloudovým aplikacím pomocí uživatelského jména a hesla. Obvykle existují alespoň některé scénáře, pro které je vhodné vyžadovat silnější formu ověření účtu. Pomocí zásad podmíněného přístupu můžete omezit požadavek na MFA na určité scénáře. 

Běžné případy použití, které vyžadují MFA, mají přístup:

- [Podle správců](howto-baseline-protect-administrators.md)
- [Na konkrétní aplikace](app-based-mfa.md) 
- [Z umístění v síti nedůvěřujete](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Reakce na potenciálně ohrožené účty

Pomocí zásad podmíněného přístupu můžete implementovat automatizované reakce na přihlášení z potenciálně ohrožených identit. Pravděpodobnost, že dojde k ohrožení bezpečnosti účtu, je vyjádřena ve formě úrovní rizika. Služba Identity Protection počítá dvě úrovně rizik: riziko přihlášení a riziko pro uživatele. Chcete-li implementovat odpověď na riziko přihlášení, máte dvě možnosti:

- [Podmínka rizika přihlašování](concept-conditional-access-conditions.md#sign-in-risk) v zásadách podmíněného přístupu
- [Zásady rizik přihlašování](../identity-protection/howto-sign-in-risk-policy.md) v Identity Protection 

Upřednostňování rizika přihlašování jako podmínka je upřednostňovaná metoda, protože poskytuje další možnosti přizpůsobení.

Úroveň rizika uživatele je k dispozici pouze jako [zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md) v rámci Identity Protection. 

Další informace najdete v tématu [co je Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Vyžadování spravovaných zařízení

Rozšíření podporovaných zařízení pro přístup k vašim cloudovým prostředkům pomáhá zlepšit produktivitu vašich uživatelů. Na překlopení nebudete pravděpodobně chtít, aby zařízení s neznámou úrovní ochrany měla k některým prostředkům ve vašem prostředí. U ovlivněných prostředků byste měli vyžadovat, aby k nim uživatelé měli přístup jenom pomocí spravovaného zařízení. Další informace najdete v tématu [jak vyžadovat spravovaná zařízení pro přístup k cloudovým aplikacím pomocí podmíněného přístupu](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Vyžadování klientem schválených aplikací

Jedním z prvních rozhodnutí, která potřebujete udělat pro scénáře Přineste si vlastní zařízení (BYOD), je to, jestli potřebujete spravovat celé zařízení nebo jenom data. Vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly. I když se rozhodnete, že vaše zaměstnanci můžou být produktivní, budete také chtít zabránit ztrátě dat. Pomocí podmíněného přístupu Azure Active Directory (Azure AD) můžete omezit přístup k vašim cloudovým aplikacím na schválené klientské aplikace, které můžou chránit vaše firemní data. Další informace najdete v tématu [jak vyžadovat schválené klientské aplikace pro přístup k cloudovým aplikacím pomocí podmíněného přístupu](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Blokování starší verze ověřování

Azure AD podporuje několik nejčastěji používaných ověřovacích a autorizačních protokolů, včetně starších verzí ověřování. Jak můžete zabránit tomu, aby aplikace používaly starší ověřování pro přístup k prostředkům vašeho tenanta? Doporučení je jednoduše zablokované zásadou podmíněného přístupu. V případě potřeby povolujete použití aplikací, které jsou založené na starších verzích ověřování, jenom určitým uživatelům a konkrétním síťovým umístěním. Další informace najdete v tématu [Jak blokovat starší verze ověřování ve službě Azure AD s podmíněným přístupem](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testování zásad

Před zavedením zásady do produkčního prostředí byste měli testovat, zda se chová podle očekávání.

1. Vytvoření testovacích uživatelů
1. Vytvoření testovacího plánu
1. Konfigurace zásad
1. Vyhodnotit simulované přihlašování
1. Testování zásad
1. Vyčištění

### <a name="create-test-users"></a>Vytvoření testovacích uživatelů

Pokud chcete zásadu otestovat, vytvořte skupinu uživatelů, která je podobná uživatelům ve vašem prostředí. Vytváření testovacích uživatelů vám umožní ověřit, jestli zásady fungovaly podle očekávání, než se potýká reálného uživatele a případně může rušit přístup k aplikacím a prostředkům. 

Některé organizace mají pro tento účel testovací klienty. Může však být obtížné znovu vytvořit všechny podmínky a aplikace v testovacím tenantovi pro kompletní testování výsledku zásad. 

### <a name="create-a-test-plan"></a>Vytvoření testovacího plánu

Testovací plán je důležitý pro porovnání očekávaných výsledků a skutečných výsledků. Před testováním byste měli vždycky očekávat. Následující tabulka popisuje příklady testovacích případů. Upravte scénáře a očekávané výsledky na základě toho, jak jsou nakonfigurované zásady certifikační autority.

|Zásada |Scénář |Očekávaný výsledek | Výsledek |
|---|---|---|---|
|[Vyžadovat MFA, pokud není v práci](/azure/active-directory/conditional-access/untrusted-networks)|Autorizovaný uživatel se přihlásí do *aplikace* v důvěryhodném umístění/v práci.|Uživatel není vyzván k MFA.| |
|[Vyžadovat MFA, pokud není v práci](/azure/active-directory/conditional-access/untrusted-networks)|Autorizovaný uživatel se přihlásí do *aplikace* , když není v důvěryhodném umístění/v práci.|Uživatel je vyzván k ověřování MFA a úspěšně se může přihlásit.| |
|[Vyžadovat MFA (pro správce)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Globální správce se přihlásí do *aplikace* .|Správce je vyzván k ověřování MFA.| |
|[Riziková přihlášení](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Uživatel se přihlásí do *aplikace* pomocí [prohlížeče pro mandát](/azure/active-directory/active-directory-identityprotection-playbook) .|Správce je vyzván k ověřování MFA.| |
|[Správa zařízení](/azure/active-directory/conditional-access/require-managed-devices)|Autorizovaný uživatel se pokusí přihlásit z autorizovaného zařízení.|Udělen přístup| |
|[Správa zařízení](/azure/active-directory/conditional-access/require-managed-devices)|Autorizovaný uživatel se pokusí přihlásit z neautorizovaného zařízení.|Přístup zablokován| |
|[Změna hesla pro rizikové uživatele](/azure/active-directory/identity-protection/howto-user-risk-policy)|Autorizovaný uživatel se pokusí přihlásit pomocí ohrožených přihlašovacích údajů (přihlašování s vysokým rizikem).|Uživateli se zobrazí výzva ke změně hesla nebo je přístup na základě vašich zásad zablokovaný.| |

### <a name="configure-the-policy"></a>Konfigurace zásad

Správa zásad podmíněného přístupu je ruční úloha. V Azure Portal můžete zásady podmíněného přístupu spravovat v jednom centrálním umístění – na stránce podmíněný přístup. Jedním vstupním bodem na stránce podmíněný přístup je oddíl **zabezpečení** v navigačním podokně **služby Active Directory** . 

![Podmíněný přístup](media/plan-conditional-access/03.png)

Pokud se chcete dozvědět víc o vytváření zásad podmíněného přístupu, přečtěte si téma [vyžádání MFA pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md). Tento rychlý Start vám pomůže:

- Seznámení s uživatelským rozhraním
- Získejte první dojem, jak podmíněný přístup funguje. 

### <a name="evaluate-a-simulated-sign-in"></a>Vyhodnotit simulované přihlašování

Teď, když jste nakonfigurovali zásady podmíněného přístupu, budete pravděpodobně chtít zjistit, jestli funguje podle očekávání. Jako první krok použijte [Nástroj zásady](what-if-tool.md) podmíněného přístupu, který je v případě, že chcete simulovat přihlášení testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.

>[!NOTE]
> I když simulované spuštění přináší dojem o dopadu, který má zásada podmíněného přístupu, nenahrazuje skutečný testovací běh.

### <a name="test-your-policy"></a>Testování zásad

Testovací případy spouštějte podle vašeho testovacího plánu. V tomto kroku provedete ucelený test jednotlivých zásad pro testovací uživatele, abyste se ujistili, že se všechny zásady chovají správně. Pomocí scénářů vytvořených výše spusťte každý test.

Je důležité se ujistit, že testujete kritéria vyloučení zásady. Můžete například vyloučit uživatele nebo skupinu ze zásady, která vyžaduje MFA. Otestujte, jestli jsou vyloučení uživatelé vyzváni k ověřování MFA, protože kombinace jiných zásad může pro tyto uživatele vyžadovat MFA.

### <a name="cleanup"></a>Vyčištění

Postup čištění se skládá z následujících kroků:

1. Zakažte tuto zásadu.
1. Odeberte přiřazené uživatele a skupiny.
1. Odstraňte testovací uživatele.  

## <a name="move-to-production"></a>Přechod k produkčnímu prostředí

Až budou nové zásady připravené pro vaše prostředí, nasaďte je do fází:

- Poskytněte koncovým uživatelům interní změnu komunikace.
- Začněte s malou sadou uživatelů a ověřte, jestli se zásada chová podle očekávání.
- Když rozbalíte zásadu, aby zahrnovala více uživatelů, pokračujte v vyloučení všech správců. Vyloučení správců zajistí, že někdo má stále přístup k zásadám, pokud je potřeba změnit.
- Zásadu použijte pro všechny uživatele jenom v případě, že je to potřeba.

Osvědčeným postupem je vytvořit aspoň jeden uživatelský účet, který je:

- Vyhrazeno pro správu zásad
- Vyloučené ze všech vašich zásad

## <a name="rollback-steps"></a>Kroky vrácení zpět

V případě, že potřebujete vrátit nově implementované zásady, použijte jednu nebo více z následujících možností a vraťte se zpět:

1. **Zakáže zásadu** – zakázáním zásady se ověří, že se uživatel nepoužije, když se uživatel pokusí přihlásit. Kdykoli se můžete vrátit zpátky a povolit zásadu, když byste ji chtěli použít.

   ![Zakázání zásady](media/plan-conditional-access/07.png)

1. **Vyloučení uživatele nebo skupiny ze zásad** – Pokud uživatel nemá přístup k aplikaci, můžete se rozhodnout vyloučit uživatele ze zásad.

   ![Exluce uživatelé](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Tato možnost by se měla používat zřídka, jenom v situacích, kdy je uživatel důvěryhodný. Uživatel by měl být do zásady nebo skupiny co nejdříve přidán.

1. **Odstranit zásadu** – Pokud už tuto zásadu nepotřebujete, odstraňte ji.

## <a name="next-steps"></a>Další kroky

Podívejte se na [dokumentaci podmíněného přístupu Azure AD](index.yml) a Získejte přehled dostupných informací.
