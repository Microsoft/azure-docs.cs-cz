---
title: Plánování zásad podmíněného přístupu ve službě Azure Active Directory | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak plánovat zásady podmíněného přístupu pro Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671818"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Postup: Plánování nasazení podmíněného přístupu ve službě Azure Active Directory

Plánování nasazení podmíněného přístupu je důležité, abyste dosáhli požadované strategie přístupu pro aplikace a prostředky ve vaší organizaci. Věnujte většinu času během fáze plánování nasazení a navrhněte různé zásady, které potřebujete k udělení nebo zablokování přístupu uživatelům za vámi zvolené podmínky. Tento dokument vysvětluje kroky, které byste měli provést k implementaci zásad zabezpečeného a efektivního podmíněného přístupu. Než začnete, ujistěte se, že rozumíte tomu, jak [podmíněný přístup](overview.md) funguje a kdy byste ho měli používat.

## <a name="what-you-should-know"></a>Co byste měli vědět

Podmíněný přístup si můžete myslet jako na architekturu, která umožňuje řídit přístup k aplikacím a prostředkům vaší organizace namísto samostatné funkce. V důsledku toho některá nastavení podmíněného přístupu vyžadují další funkce, které mají být nakonfigurovány. Můžete například nakonfigurovat zásadu, která reaguje na určitou [úroveň rizika přihlášení](../identity-protection/howto-identity-protection-configure-risk-policies.md). Zásada založená na úrovni rizika přihlášení však vyžaduje, aby byla [povolena ochrana identit služby Azure Active Directory.](../identity-protection/overview-identity-protection.md)

Pokud jsou požadovány další funkce, může být také nutné získat související licence. Například zatímco podmíněný přístup je funkce Azure AD Premium P1, ochrana identity vyžaduje licenci Azure AD Premium P2.

Existují dva typy zásad podmíněného přístupu: směrný plán a standard. Zásada [směrného plánu](baseline-protection.md) je předdefinovaná zásada podmíněného přístupu. Cílem těchto zásad je zajistit, aby byla povolena alespoň směrná úroveň zabezpečení. Zásady směrného plánu. Zásady směrného plánu jsou k dispozici ve všech edicích Azure AD a poskytují pouze omezené možnosti vlastního nastavení. Pokud scénář vyžaduje větší flexibilitu, zakažte zásady směrného plánu a implementujte požadavky ve vlastní standardní zásady.

Ve standardních zásadách podmíněného přístupu můžete přizpůsobit všechna nastavení a upravit tak zásady podle obchodních požadavků. Standardní zásady vyžadují licenci Azure AD Premium P1.

>[!NOTE]
> Doporučujeme používat zásady podmíněného přístupu založené na zařízení Azure AD, abyste získali nejlepší vynucení po počátečním ověřování zařízení. To zahrnuje uzávěrky relací, pokud zařízení vypadne z dodržování předpisů a tok kódu zařízení.

## <a name="draft-policies"></a>Návrhy zásad

Podmíněný přístup služby Azure Active Directory umožňuje přenést ochranu cloudových aplikací na novou úroveň. V této nové úrovni, jak můžete přistupovat ke cloudové aplikaci je založena na dynamické vyhodnocení zásad namísto konfigurace statického přístupu. Pomocí zásad podmíněného přístupu definujete odpověď **(udělejte)** na podmínku přístupu **(pokud k tomu dojde).**

![Důvod a odpověď](./media/plan-conditional-access/10.png)

Definujte všechny zásady podmíněného přístupu, které chcete implementovat pomocí tohoto modelu plánování. Plánovací cvičení:

- Pomáhá nastínit odpovědi a podmínky pro jednotlivé zásady.
- Výsledkem je dobře zdokumentovaný katalog zásad podmíněného přístupu pro vaši organizaci. 

Katalog můžete použít k posouzení, zda implementace zásad odráží obchodní požadavky vaší organizace. 

K vytvoření zásad podmíněného přístupu pro vaši organizaci použijte následující vzorovou šablonu:

|Když k *tomu dojde:*|Pak *udělejte toto*:|
|-|-|
|Je proveden pokus o přístup:<br>- Chcete-li cloud app*<br>- Podle uživatelů a skupin*<br>Pomocí:<br>- Podmínka 1 (například mimo síť Corp)<br>- Podmínka 2 (například platformy zařízení)|Blokovat přístup k aplikaci|
|Je proveden pokus o přístup:<br>- Chcete-li cloud app*<br>- Podle uživatelů a skupin*<br>Pomocí:<br>- Podmínka 1 (například mimo síť Corp)<br>- Podmínka 2 (například platformy zařízení)|Udělit přístup pomocí (AND):<br>- Požadavek 1 (například MFA)<br>- Požadavek 2 (například shoda se zařízením)|
|Je proveden pokus o přístup:<br>- Chcete-li cloud app*<br>- Podle uživatelů a skupin*<br>Pomocí:<br>- Podmínka 1 (například mimo síť Corp)<br>- Podmínka 2 (například platformy zařízení)|Udělit přístup pomocí (OR):<br>- Požadavek 1 (například MFA)<br>- Požadavek 2 (například shoda se zařízením)|

Minimálně, **když k tomu dojde** definuje hlavní (**kdo),** který se pokusí o přístup ke cloudové aplikaci (**co**). V případě potřeby můžete také **zahrnout** způsob provedení pokusu o přístup. V podmíněném přístupu prvky, které definují kdo, co a jak jsou označovány jako podmínky. Další informace najdete v tématu [Co jsou podmínky v podmíněném přístupu služby Azure Active Directory?](concept-conditional-access-conditions.md) 

Pomocí **tohoto provést**, můžete definovat odpověď zásady na podmínku přístupu. Ve vaší odpovědi buď blokovat nebo udělit přístup s dalšími požadavky, například vícefaktorové ověřování (MFA). Úplný přehled najdete v tématu [Co jsou ovládací prvky přístupu v podmíněném přístupu Azure Active Directory?](controls.md)  

Kombinace podmínek s ovládacími prvky přístupu představuje zásady podmíněného přístupu.

![Důvod a odpověď](./media/plan-conditional-access/51.png)

Další informace naleznete v [tématu, co je nutné k tomu, aby zásada fungovala](best-practices.md#whats-required-to-make-a-policy-work).

V tomto okamžiku je vhodná doba rozhodnout o pojmenování standard pro zásady. Standard pojmenování vám pomůže najít zásady a pochopit jejich účel, aniž byste je museli otevírat na portálu pro správu Azure. Pojmenujte zásady tak, aby zobrazovala:

- Pořadové číslo
- Cloudová aplikace, na kterou se vztahuje
- Odpověď
- Na koho se vztahuje
- Pokud se použije (je-li k dispozici)
 
![Pojmenování standard](./media/plan-conditional-access/11.png)

Zatímco popisný název vám pomůže udržet přehled o implementaci podmíněného přístupu, pořadové číslo je užitečné, pokud potřebujete odkazovat na zásadu v konverzaci. Pokud například mluvíte s kolegou správcem po telefonu, můžete je požádat, aby otevřeli zásadu EM063, aby vyřešili problém.

Například následující název uvádí, že zásada vyžaduje vícefaktorové informace pro marketingové uživatele v externích sítích pomocí aplikace Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Kromě aktivních zásad je vhodné také implementovat zásady pro zakázáno, které fungují jako sekundární [odolné kontroly přístupu v případech výpadku nebo nouzového stavu](../authentication/concept-resilient-controls.md). Váš standard pojmenování pro zásady pro nepředvídané události by měl obsahovat několik dalších položek: 

- `ENABLE IN EMERGENCY`na začátku, aby se jméno vyniknout mezi ostatními politikami.
- Název narušení, na které by se měl vztahovat.
- Pořadové číslo pořadí, které pomáhá správci vědět, ve kterém pořadí by měly být povoleny zásady. 

Například následující název označuje, že tato zásada je první zásadou ze čtyř, kterou byste měli povolit, pokud dojde k přerušení vícefaktorové žádosti:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Plánování zásad

Při plánování řešení zásad podmíněného přístupu vyhodnoťte, zda je třeba vytvořit zásady k dosažení následujících výsledků. 

### <a name="block-access"></a>Blokovat přístup

Možnost blokovat přístup je silný, protože:

- Trumfne všechny ostatní úkoly pro uživatele
- Má moc zablokovat celé organizaci v přihlášení k tenantovi.
 
Pokud chcete blokovat přístup pro všechny uživatele, měli byste alespoň vyloučit jednoho uživatele (obvykle účty nouzového přístupu) ze zásad. Další informace naleznete v [tématu výběr uživatelů a skupin](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Vyžadování MFA

Chcete-li zjednodušit přihlašování uživatelů, můžete jim povolit přihlášení k cloudovým aplikacím pomocí uživatelského jména a hesla. Obvykle však existují alespoň některé scénáře, pro které je vhodné vyžadovat silnější formu ověření účtu. Pomocí zásad podmíněného přístupu můžete omezit požadavek na vícefaktorové povolení na určité scénáře. 

Běžné případy použití vyžadovat vícefaktorové použití jsou přístup:

- [Podle administrátorů](howto-baseline-protect-administrators.md)
- [Ke konkrétním aplikacím](app-based-mfa.md) 
- [Ze síťových umístění nedůvěřujete](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Reakce na potenciálně ohrožené účty

Pomocí zásad podmíněného přístupu můžete implementovat automatické odpovědi na přihlášení z potenciálně ohrožených identit. Pravděpodobnost, že byl účet ohrožen, je vyjádřena ve formě úrovní rizika. Existují dvě úrovně rizika vypočítané podle ochrany identity: riziko přihlášení a riziko uživatele. Chcete-li implementovat odpověď na riziko přihlášení, máte dvě možnosti:

- [Podmínka rizika přihlášení](concept-conditional-access-conditions.md#sign-in-risk) v zásadách podmíněného přístupu
- [Zásady rizika přihlášení](../identity-protection/howto-sign-in-risk-policy.md) v ochraně identity 

Řešení rizika přihlášení jako podmínky je upřednostňovanou metodou, protože poskytuje další možnosti přizpůsobení.

Úroveň rizika uživatele je k dispozici pouze jako [zásady rizika uživatele](../identity-protection/howto-user-risk-policy.md) v ochraně identity. 

Další informace najdete v tématu [Co je ochrana identit y služby Azure Active Directory?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Vyžadování spravovaných zařízení

Rozšíření podporovaných zařízení pro přístup ke cloudovým prostředkům pomáhá zvýšit produktivitu uživatelů. Na druhou stranu pravděpodobně nechcete, aby určité prostředky ve vašem prostředí byly přístupné zařízením s neznámou úrovní ochrany. U ohrožených prostředků byste měli vyžadovat, aby k nim uživatelé měli přístup pouze pomocí spravovaného zařízení. Další informace najdete v tématu [Jak vyžadovat spravovaná zařízení pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Vyžadování klientem schválených aplikací

Jedním z prvních rozhodnutí, které musíte udělat pro vlastní scénáře zařízení (BYOD), je, zda potřebujete spravovat celé zařízení nebo jen data na něm. Vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly. Při zajištění produktivity vašich zaměstnanců chcete také zabránit ztrátě dat. S podmíněným přístupem Azure Active Directory (Azure AD) můžete omezit přístup ke svým cloudovým aplikacím na schválené klientské aplikace, které můžou chránit vaše podniková data. Další informace najdete v tématu [Jak vyžadovat schválené klientské aplikace pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Blokování starší verze ověřování

Azure AD podporuje několik nejpoužívanějších ověřovacích a autorizačních protokolů, včetně staršíverze ověřování. Jak můžete zabránit aplikacím používajícím starší verze ověřování v přístupu k prostředkům vašeho tenanta? Doporučujeme je pouze zablokovat pomocí zásad podmíněného přístupu. V případě potřeby povolíte pouze určitým uživatelům a určitým síťovým umístěním používat aplikace založené na starším ověřování. Další informace najdete v tématu [Jak blokovat starší verze ověřování ve službě Azure AD s podmíněným přístupem](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Otestujte své zásady

Před zavedením zásady do produkčního prostředí byste měli otestovat ověření, zda se chová podle očekávání.

1. Vytvořit testovací uživatele
1. Vytvoření testovacího plánu
1. Konfigurace zásad
1. Vyhodnocení simulovanéregistrace
1. Otestujte své zásady
1. Vyčištění

### <a name="create-test-users"></a>Vytvořit testovací uživatele

Chcete-li otestovat zásadu, vytvořte sadu uživatelů, která je podobná uživatelům ve vašem prostředí. Vytváření testovacích uživatelů umožňuje ověřit, zda vaše zásady fungují podle očekávání, než ovlivníte skutečné uživatele a potenciálně narušíte jejich přístup k aplikacím a prostředkům. 

Některé organizace mají testovací klienty pro tento účel. Však může být obtížné znovu vytvořit všechny podmínky a aplikace v testovacím tenantovi plně otestovat výsledek zásady. 

### <a name="create-a-test-plan"></a>Vytvoření testovacího plánu

Plán testování je důležité mít srovnání mezi očekávané výsledky a skutečné výsledky. Vždy byste měli mít očekávání před testováním něco. Následující tabulka popisuje příklad testovacích případů. Upravte scénáře a očekávané výsledky na základě konfigurace zásad certifikační autority.

|Zásada |Scénář |Očekávaný výsledek | Výsledek |
|---|---|---|---|
|[Vyžadovat vícefaktorové finanční vyhodžení, když není v práci](/azure/active-directory/conditional-access/untrusted-networks)|Oprávněný uživatel se přihlásí do *Aplikace* na důvěryhodném místě / práci|Uživatel není vyzván k vícefaktorovému přístupu| |
|[Vyžadovat vícefaktorové finanční vyhodžení, když není v práci](/azure/active-directory/conditional-access/untrusted-networks)|Oprávněný uživatel se přihlásí do *aplikace,* když není na důvěryhodném místě / práci|Uživatel je vyzván k vícefaktorové ověřování a může se úspěšně přihlásit| |
|[Vyžadovat vícefaktorové finanční hodování (pro správce)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Globální správce se přihlásí do *aplikace*|Správce je vyzván k vícefaktorovému přístupu| |
|[Riziková přihlášení](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Uživatel se přihlásí do *Aplikace* pomocí [prohlížeče Tor](/azure/active-directory/active-directory-identityprotection-playbook)|Správce je vyzván k vícefaktorovému přístupu| |
|[Správa zařízení](/azure/active-directory/conditional-access/require-managed-devices)|Oprávněný uživatel se pokusí přihlásit z autorizovaného zařízení|Přístup udělen| |
|[Správa zařízení](/azure/active-directory/conditional-access/require-managed-devices)|Oprávněný uživatel se pokusí přihlásit z neautorizovaného zařízení|Přístup blokován| |
|[Změna hesla pro rizikové uživatele](/azure/active-directory/identity-protection/howto-user-risk-policy)|Oprávněný uživatel se pokusí přihlásit pomocí kompromitovaných přihlašovacích údajů (vysoce rizikové přihlášení)|Uživatel je vyzván ke změně hesla nebo je přístup blokován na základě vašich zásad.| |

### <a name="configure-the-policy"></a>Konfigurace zásad

Správa zásad podmíněného přístupu je ruční úloha. Na webu Azure Portal můžete spravovat zásady podmíněného přístupu v jednom centrálním umístění – na stránce Podmíněný přístup. Jedním vstupním bodem na stránku Podmíněného přístupu je oddíl **Zabezpečení** v navigačním podokně **služby Active Directory.** 

![Podmíněný přístup](media/plan-conditional-access/03.png)

Pokud se chcete dozvědět víc o tom, jak vytvořit zásady podmíněného přístupu, přečtěte si informace [o vyžadování vícefaktorové žádosti pro konkrétní aplikace s podmíněným přístupem Služby Azure Active Directory](app-based-mfa.md). Tento rychlý start vám pomůže:

- Seznamte se s uživatelským rozhraním.
- Získejte první dojem o tom, jak podmíněný přístup funguje. 

### <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení simulovanéregistrace

Nyní, když jste nakonfigurovali zásady podmíněného přístupu, pravděpodobně budete chtít vědět, zda funguje podle očekávání. Jako první krok použijte nástroj zásad podmíněného přístupu [co když](what-if-tool.md) k simulaci přihlášení testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.

>[!NOTE]
> Zatímco simulované spuštění poskytuje dojem dopadu zásady podmíněného přístupu, nenahrazuje skutečný test.

### <a name="test-your-policy"></a>Otestujte své zásady

Spusťte testovací případy podle testovacího plánu. V tomto kroku spustíte test od konce každé zásady pro testovací uživatele a ujistěte se, že se jednotlivé zásady chovají správně. Pomocí výše vytvořených scénářů proveďte každý test.

Je důležité, abyste otestovali kritéria vyloučení zásady. Můžete například vyloučit uživatele nebo skupinu ze zásady, která vyžaduje vícefaktorové ověřování. Otestujte, zda jsou vyloučení uživatelé vyzváni k vícefaktorové muziky, protože kombinace jiných zásad může pro tyto uživatele vyžadovat vícefaktorové zabezpečení.

### <a name="cleanup"></a>Vyčištění

Postup čištění se skládá z následujících kroků:

1. Zakažte zásadu.
1. Odeberte přiřazené uživatele a skupiny.
1. Odstraňte testovací uživatele.  

## <a name="move-to-production"></a>Přechod k produkčnímu prostředí

Když jsou nové zásady připraveny pro vaše prostředí, nasazujte je ve fázích:

- Poskytněte koncovým uživatelům interní komunikaci se změnami.
- Začněte s malou sadou uživatelů a ověřte, zda se zásada chová podle očekávání.
- Když rozbalíte zásadu tak, aby zahrnovala více uživatelů, pokračujte v vyloučení všech správců. Vyloučení správců zajistí, že někdo má stále přístup k zásadám, pokud je požadována změna.
- Zásadu použijte u všech uživatelů pouze v případě, že je to nutné.

Osvědčeným postupem je vytvořit alespoň jeden uživatelský účet, který je:

- Věnováno správě politik
- Vyloučeno ze všech vašich zásad

## <a name="rollback-steps"></a>Kroky vrácení zpět

V případě, že potřebujete vrátit zpět nově implementované zásady, použijte jednu nebo více z následujících možností, které chcete vrátit zpět:

1. **Zakázat zásadu** – zakázání zásady zajistí, že se nepoužije, když se uživatel pokusí přihlásit. Vždy se můžete vrátit a povolit zásady, když chcete použít.

   ![Zakázání zásady](media/plan-conditional-access/07.png)

1. **Vylučovat uživatele nebo skupinu ze zásady** – Pokud uživatel nemůže získat přístup k aplikaci, můžete uživatele ze zásadvyloučit.

   ![Exluce uživatelé](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Tato možnost by měla být používána střídmě, pouze v situacích, kdy je uživatel důvěryhodný. Uživatel by měl být přidán zpět do zásady nebo skupiny co nejdříve.

1. **Odstranit zásadu** – Pokud zásada již není vyžadována, odstraňte ji.

## <a name="next-steps"></a>Další kroky

Podívejte se na [dokumentaci podmíněného přístupu Azure AD](index.yml) a získejte přehled dostupných informací.
