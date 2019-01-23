---
title: Plánování zásad podmíněného přístupu v Azure Active Directory | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak naplánovat zásady podmíněného přístupu Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
tags: azuread
ms.service: active-directory
ms.component: conditional-access
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2018
ms.author: markvi
ms.reviewer: martincoetzer
ms.openlocfilehash: 1911dd189e21a6d29b2bf1ba3d179b41e948f469
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450503"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Jak: Plánování nasazení podmíněného přístupu v Azure Active Directory

Plánování nasazení podmíněného přístupu je velmi důležité, abyste měli jistotu, že dosáhnout strategie požadovaný přístup k aplikacím a prostředkům ve vaší organizaci. By měl tráví většinu svého času v průběhu fáze plánování nasazení tak, aby návrh různé zásady, které vyžadují udělit nebo blokovat přístup k vašim uživatelům podmínek, které zvolíte. Tento dokument popisuje kroky, které byste měli provést k implementaci zásady podmíněného přístupu na zabezpečené a efektivní. Než začnete, ujistěte se, že rozumíte jak [podmíněného přístupu](overview.md) funguje a kdy byste měli použít.


## <a name="what-you-should-know"></a>Co byste měli vědět

Podmíněným přístupem můžete představit jako systém, který vám umožňuje řídit přístup k aplikacím a prostředkům, namísto samostatné funkce vaší organizace. V důsledku toho některá nastavení podmíněného přístupu vyžadují další funkce, které chcete nakonfigurovat. Můžete například nakonfigurovat zásadu, která reaguje na konkrétní [úroveň rizika přihlášení](../identity-protection/howto-sign-in-risk-policy.md#what-is-the-sign-in-risk-policy). Ale vyžaduje zásadu, která je založena na úroveň rizika přihlášení [Azure Active Directory identity protection](../identity-protection/overview.md) povolit.

Pokud potřebujete další funkce, může být také potřeba získat související licence. Například podmíněný přístup je funkce Azure AD Premium P1, identity protection vyžaduje licenci Azure AD Premium P2.

Existují dva typy zásad podmíněného přístupu: standardní hodnoty a standard. A [základní zásady](baseline-protection.md) je zásada předdefinované podmíněného přístupu. Cílem těchto zásad je Ujistěte se, že máte alespoň o úroveň standardních hodnot zabezpečení povoleno. Zásady na směrný plán. Zásady na směrný plán jsou k dispozici ve všech edicích služby Azure AD a poskytují jenom omezené přizpůsobení možností. Pokud scénář vyžaduje větší flexibilitu, zakažte základní zásady a implementace ve vlastních zásadách pro standardní vašim požadavkům.

V zásady standardní podmíněného přístupu můžete k přizpůsobení všech nastavení můžete změnit zásady tak, aby vaše obchodní požadavky. Standardní zásady vyžaduje licenci Azure AD Premium P1.




## <a name="draft-policies"></a>Návrh zásad

Podmíněný přístup služby Azure Active Directory umožňuje přinést ochranu vašich cloudových aplikacích na zcela novou úroveň. V této nové úrovni přístupu cloudové aplikace podle hodnocení dynamické zásad místo statické přístup konfigurace. Pomocí zásady podmíněného přístupu, definujete odpověď (**tomu**) na podmínku přístup (**v takovém případě**).

![Důvod a odpovědi](./media/plan-conditional-access/10.png)

Definujte každý zásady podmíněného přístupu, které chcete implementovat pomocí tohoto modelu plánování. Plánování výkonu:

- Umožňuje popisují podmínky pro jednotlivé zásady a odpovědi.
- Výsledky v katalogu zásad dobře zdokumentovaná podmíněného přístupu pro vaši organizaci. 

Váš katalog můžete použít k vyhodnocení, zda odráží vaši implementaci zásad podnikové požadavky vaší organizace. 

Následující příklad šablony můžete vytvářet zásady podmíněného přístupu pro vaši organizaci:

|Když *to* se stane:|Proveďte *to*:|
|-|-|
|Je proveden pokus o přístup:<br>– Pokud chcete cloudovou aplikaci*<br>– uživatelé a skupiny*<br>Použití:<br>-Podmínka 1 (například mimo podnikovou sítí)<br>-Podmínka 2 (například riziko přihlášení)|Blokovat přístup k aplikaci|
|Je proveden pokus o přístup:<br>– Pokud chcete cloudovou aplikaci*<br>– uživatelé a skupiny*<br>Použití:<br>-Podmínka 1 (například mimo podnikovou sítí)<br>-Podmínka 2 (například riziko přihlášení)|Udělení přístupu s (a):<br>-Požadavek 1 (například vícefaktorové ověřování)<br>-Požadavek 2 (například dodržování předpisů zařízením)|
|Je proveden pokus o přístup:<br>– Pokud chcete cloudovou aplikaci*<br>– uživatelé a skupiny*<br>Použití:<br>-Podmínka 1 (například mimo podnikovou sítí)<br>-Podmínka 2 (například riziko přihlášení)|Udělení přístupu s (nebo):<br>-Požadavek 1 (například vícefaktorové ověřování)<br>-Požadavek 2 (například dodržování předpisů zařízením)|

Minimálně **v takovém případě** definuje objekt zabezpečení (**kdo**), který se pokouší získat přístup k Cloudová aplikace (**co**). Pokud třeba, můžete použít také **jak** Probíhá pokus o přístup. Podmíněného přístupu, elementy, které definují kdo, co a jak jsou označovány jako podmínky. Další informace najdete v tématu [jaké jsou podmínky podmíněného přístupu Azure Active Directory?](conditions.md) 

S **proveďte to**, definujte odpověď zásady pro podmínku přístup. V odpovědi můžete blokovat nebo udělit přístup s další požadavky, například vícefaktorové ověřování (MFA). Úplný přehled najdete v tématu [co jsou přístupu řídí podmíněného přístupu Azure Active Directory?](controls.md)  
 

Kombinace podmínek a řízení přístupu představuje zásadu podmíněného přístupu.

![Důvod a odpovědi](./media/plan-conditional-access/51.png)


Další informace najdete v tématu [co potřebná k tomu, aby zásada pracovní](best-practices.md#whats-required-to-make-a-policy-work).

V tomto okamžiku má je vhodná doba při rozhodování o standardní pojmenování pro vaše zásady. Standardní pojmenování vám pomůže najít zásady a pochopit jejich účel, aniž byste museli otevírat v portálu pro správu Azure. Byste měli pojmenovat zásadu zobrazit:

- Pořadové číslo
- Cloudové aplikace, které platí pro
- Odpověď
- Kdo se vztahuje na
- Když se vztahuje 
 
![Standardní pojmenování](./media/plan-conditional-access/11.png)





Například následující název uvádí, že zásady vyžadují vícefaktorové ověřování pro uživatele na externí sítě pomocí aplikace Dynamics CRP marketingu:

`CA01-Dynamics CRP: Require MFA For marketing When on external networks`


Kromě aktivních zásad, měli byste také implementovat zakázané zásady, které fungují jako sekundární [řízení přístupu na odolná ve scénářích výpadek/nouzovou](../authentication/concept-resilient-controls.md). Vaše standardní pojmenování by měl také obsahovat tento účel, aby bylo snazší povolit během výpadku. Příklad:

`EM01-Finance app: Require MFA For Sales When on untrusted network`


## <a name="plan-policies"></a>Zásady plánu

Při plánování řešení zásady podmíněného přístupu, vyhodnoťte, jestli je potřeba vytvořit zásady k dosažení následujících výsledků. 


### <a name="block-access"></a>Blokovat přístup

Možnost blokovat přístup je výkonný protože ji:

- Trumps všechna přiřazení uživatele

- Má schopnost blokování celé organizace přihlašování k vašemu tenantovi
 
Pokud chcete blokovat přístup pro všechny uživatele, by měl jeden uživatel (obvykle nouzový přístup účty) alespoň vyloučit ze zásad. Další informace najdete v tématu [výběr uživatelů a skupin](block-legacy-authentication.md#select-users-and-cloud-apps).  
    

### <a name="require-mfa"></a>Vyžadování MFA

Pro zjednodušení přihlašování vašich uživatelů, můžete chtít umožnit přihlásit k vašim cloudovým aplikacím pomocí uživatelského jména a hesla. Obvykle existují však alespoň některé scénáře, pro které je vhodné tak, aby vyžadovala silnější formu ověření účtu. Pomocí zásad podmíněného přístupu můžete omezit požadavek na vícefaktorové ověřování, určitých scénářů. 

Běžné případy použití vyžádáte vícefaktorové ověřování, jsou přístupu:

- [Správci](baseline-protection.md#require-mfa-for-admins)
- [Na konkrétní aplikace](app-based-mfa.md) 
- [Z umístění v síti, kterým nedůvěřujete](untrusted-networks.md).


### <a name="respond-to-potentially-compromised-accounts"></a>Reakce na potenciálně ohrožených účtů

Pomocí podmíněného přístupu zásady, můžete implementovat automatické odpovědi k přihlášení z potenciálně napadeného identit. Pravděpodobnost, že je účet ohrožené je vyjádřena v podobě úrovně rizika. Existují dvě úrovně rizika počítá služba identity protection: rizika přihlášení a uživatelského rizika. K implementaci odpověď na rizika přihlášení, máte dvě možnosti:

- [Podmínka rizika přihlašování](conditions.md#sign-in-risk) v zásadách podmíněného přístupu
- [Zásady rizik přihlašování](../identity-protection/howto-sign-in-risk-policy.md) ve službě identity protection 

Adresování rizika přihlášení jako podmínka je upřednostňuje, protože poskytuje další možnosti vlastního nastavení.

Úroveň rizika uživatele je k dispozici pouze [zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md) ve službě identity protection. 

Další informace najdete v tématu [co je Azure Active Directory Identity Protection?](../identity-protection/overview.md) 


### <a name="require-managed-devices"></a>Vyžadování spravovaných zařízení

Růst počtu podporovaných zařízení pro přístup k prostředkům cloudu pomáhá zvýšit produktivitu vašich uživatelů. Na druhou stranu pravděpodobně nebudete chtít určitých prostředků ve vašem prostředí, ke kterým přistupují zařízení s úrovní Neznámý ochrany. Pro ovlivněné prostředky je potřeba povolit, že uživatelé mohou pouze k nim přistupovat pomocí spravovaných zařízení. Další informace najdete v tématu [vyžadování spravovaných zařízení pro přístup k aplikaci cloud s podmíněným přístupem](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Vyžadování klientem schválených aplikací

Jeden z prvních rozhodnutí, které je třeba provést (BYOD) scénáře typu přineste si vlastní zařízení, je, jestli je potřeba spravovat celé zařízení, nebo jen data na něj. Vaši zaměstnanci používají mobilní zařízení pro osobní a pracovní úkoly. A ujistěte se, vaši zaměstnanci byli produktivní, můžete také chcete zabránit ztrátě dat. S podmíněným přístupem Azure Active Directory (Azure AD) můžete omezit přístup k vašim cloudovým aplikacím na klientem schválených aplikací, které můžete chránit podniková data. Další informace najdete v tématu [jak vyžadovat klientem schválených aplikací pro přístup k aplikaci cloud s podmíněným přístupem](app-based-conditional-access.md).


### <a name="block-legacy-authentication"></a>Blokování starší verze ověřování

Azure AD podporuje některé z nejčastěji používaných ověřovací a autorizační protokoly včetně starší verze ověřování. Jak můžete zabránit aplikacím pomocí starší verze ověřování přístup k prostředkům vašeho tenanta? Doporučuje se jen zablokovat zásadami podmíněného přístupu. V případě potřeby můžete povolit pouze určité uživatele a konkrétní síťová umístění používat aplikace, které jsou založeny na starší verze ověřování. Další informace najdete v tématu [blokování starší verze ověřování do služby Azure AD s podmíněným přístupem](block-legacy-authentication.md).


## <a name="test-your-policy"></a>Vaše zásada testování

Před zavedením zásad do produkčního prostředí, měli byste otestovat ověření, že se chová podle očekávání.

1. Vytvoření testovacích uživatelů

2. Vytvoření testovacího plánu

3. Konfigurace zásad

4. Vyhodnocení Simulovaná přihlášení

5. Vaše zásada testování

6. Vyčištění



### <a name="create-test-users"></a>Vytvoření testovacích uživatelů

K otestování zásady, vytvořte skupinu uživatelů, který je podobný uživatelé ve vašem prostředí. Vytvoření testovacích uživatelů umožňuje ověřit, že vaše zásady fungovat podle očekávání, než dopad na reálné uživatele a může dojít k narušení jejich přístup k aplikacím a prostředkům. 


Některé organizace mají tenantů testů pro tento účel. Však může být obtížné znovu vytvořit všechny podmínky a aplikace s testovacím tenantem k plnému otestování výsledku zásady. 

### <a name="create-a-test-plan"></a>Vytvoření testovacího plánu

Testovací plán je důležité mít srovnání očekávané výsledky a skutečné výsledky. Vždy byste měli mít před testováním něco očekávání. Následující tabulka popisuje příklad testovací případy. Upravte scénáře a očekávané výsledky, které jsou založené na konfiguraci vašich zásad podmíněného přístupu.

|Zásada |Scénář |Očekávaný výsledek | Výsledek |
|---|---|---|---|
|[Vyžadovat vícefaktorové ověřování mimo práci](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Autorizovaný uživatel se přihlásí *aplikace* while v důvěryhodném umístění / práce|Uživatel není vyzván k MFA| |
|[Vyžadovat vícefaktorové ověřování mimo práci](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|Autorizovaný uživatel se přihlásí *aplikace* while není v důvěryhodném umístění / práce|Uživatel je vyzván, aby MFA a můžete úspěšně přihlásit| |
|[Vyžadovat vícefaktorové ověřování (pro správce)](https://docs.microsoft.com/azure/active-directory/conditional-access/baseline-protection#require-mfa-for-admins)|Globální správce přihlásí *aplikace*|Správce se zobrazí výzva k MFA| |
|[Riziková přihlášení](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|Uživatel se přihlásí do *aplikace* pomocí [Tor prohlížeče](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)|Správce se zobrazí výzva k MFA| |
|[Správa zařízení](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Ověřený uživatel se pokusí přihlásit pomocí autorizovaného zařízení|Udělení přístupu| |
|[Správa zařízení](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|Autorizovaný uživatel pokusí přihlásit z neoprávněné zařízení|Přístup zablokován| |
|[Změna hesla pro rizikoví uživatelé](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|Oprávnění uživatele pokusy o přihlášení pomocí zneužití přihlašovacích údajů (vysoce rizikových přihlášení v)|Uživatel je vyzván ke změně hesla nebo zablokován přístup na základě vašich zásad| |


### <a name="configure-the-policy"></a>Konfigurace zásad

Správa zásad podmíněného přístupu je ruční úloha. Na webu Azure Portal můžete spravovat zásad podmíněného přístupu na jediném místě – stránky podmíněného přístupu. Je jeden vstupní bod na stránku pro podmíněný přístup **zabezpečení** tématu **služby Active Directory** navigačním podokně. 

![Podmíněný přístup](media/plan-conditional-access/03.png)


Pokud chcete získat další informace o tom, jak vytvořit zásady podmíněného přístupu, najdete v článku [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md). Tento rychlý start vám pomůže:

- Seznamte se s uživatelským rozhraním.
- Získáte první představu o tom, jak podmíněný přístup funguje. 


### <a name="evaluate-a-simulated-sign-in"></a>Vyhodnocení Simulovaná přihlášení

Teď, když jste nakonfigurovali zásady podmíněného přístupu, pravděpodobně chcete zjistit, jestli fungují podle očekávání. Jako první krok, použijte podmíněný přístup [co když nástroj zásad](what-if-tool.md) simulace u přihlášení z testovacího uživatele. Při této simulaci se odhadne dopad přihlášení na vaše zásady a vygeneruje se sestava simulace.

>[!NOTE]
> I když Simulovaná spustit poskytuje dojem dopad má zásady podmíněného přístupu, nenahrazuje skutečný testovacího běhu.


### <a name="test-your-policy"></a>Vaše zásada testování

Spouštění testovacích případů podle plánu testování. V tomto kroku budete procházet začátku do konce testu každé zásady pro vaše testovací uživatele, abyste měli jistotu, že každá zásada pracuje správně. Použijte scénáře k provedení každého testu vytvořili výše.

Je důležité zajistit, aby že testovací kritéria vyloučení zásady. Například může vyloučit uživatele nebo skupinu na základě zásady, které vyžadují vícefaktorové ověřování. Proto byste otestovat Pokud vyloučení uživatelé se výzva pro vícefaktorové ověřování, protože spojení jiných zásad může vyžadovat vícefaktorové ověřování pro tyto uživatele.


### <a name="cleanup"></a>Vyčištění

Postup vyčištění se skládá z následujících kroků:

1. Zakážete zásadu.

2. Odeberte přiřazení uživatelé a skupiny.

3. Odstraňte testovací uživatele.  




## <a name="move-to-production"></a>Přejít do produkčního prostředí

Jakmile budete připraveni k nasazení nových zásad do vašeho prostředí, byste měli udělat ve fázích:

- Koncovým uživatelům poskytovat změnit interní komunikace.

- Začněte s menší skupinou uživatelů a ověřte, že zásady chová podle očekávání.

- Když rozšiřujete zásadu, která zahrnují další uživatele, i nadále vyloučit všechny správce. S výjimkou správci zajistí, že jiným stále má přístup k zásadě, pokud je požadována změna.

- Zásady platí pro všechny uživatele, pouze v případě, že je to požadováno.

Jako osvědčený postup vytvořte aspoň jeden uživatelský účet, který je:

- Vyhrazený pro správu zásad

- Vyloučené ze všech zásad

 



## <a name="rollback-steps"></a>Postup vrácení zpět

V případě, že je potřeba vrátit zpět nově implementované zásady, použijte jeden nebo více z následujících možností vrácení zpět:

1. **Zakázat zásadu** – zakázání zásady zajišťuje, nevztahuje se, když se uživatel pokusí přihlásit. Vždy můžete vrátit a zásadu povolit, pokud chcete použít.

    ![Zakázat zásadu](media/plan-conditional-access/07.png)

2. **Vyloučit uživatele / skupiny na základě zásady** – Pokud je uživatel nemá přístup k aplikaci, můžete vyloučit ze zásady uživatele

    ![Uživatelé Exluce](media/plan-conditional-access/08.png)

    >[!NOTE]
    > Tato možnost by měly používat střídmě, jenom v situacích, kde uživatel je důvěryhodný. Uživatel, měli byste co nejdříve přidat zpátky do zásad nebo skupiny.

3. **Odstranit zásady** – Pokud zásady už nejsou potřeba, odstraňte ho.

## <a name="next-steps"></a>Další postup

Podívejte se na [dokumentace k Azure AD podmíněného přístupu](index.yml) získat přehled o dostupné informace.
