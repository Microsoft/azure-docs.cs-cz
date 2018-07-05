---
title: Vytvoření kontroly přístupu pro členy skupiny nebo uživatelé s přístupem k aplikaci s Azure AD | Dokumentace Microsoftu
description: Informace o vytvoření kontroly přístupu pro členy skupiny nebo uživatelé s přístupem k aplikaci.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 853d8f09a94e46db218553500a50dc4ef1ec3d23
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448267"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Vytvoření kontroly přístupu členů skupiny nebo přístupu k aplikacím v Azure AD

Přiřazení přístupu stát "zastaralých", když uživatelé mají přístup, které nepotřebují žádné další. Chcete-li snížit riziko spojené s přiřazení přístupu zastaralé, mohou správci služby Azure Active Directory (Azure AD) k vytvoření kontroly přístupu pro členy skupiny nebo uživatelé přiřazení k aplikaci. Vytváření opakování kontroly přístupu lze ušetří čas. Pokud je potřeba pravidelně kontrolujte uživatelé, kteří nemají přístup k aplikaci, nebo jsou členové skupiny, můžete definovat četnost těchto kontrol. Další informace o těchto scénářích najdete v tématu [spravovat přístup uživatelů](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) a [spravovat přístup hosta](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Vytvořit kontrolu přístupu

1. Jako globální správce nebo správce uživatelských účtů, přejděte [stránku kontrol přístupu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)a vyberte **programy**.

2. Vyberte program, který obsahuje kontroly řízení přístupu, kterou chcete vytvořit. **Výchozí Program** je vždy k dispozici, nebo můžete vytvořit v jiné aplikaci. Například můžete mít jeden program pro každou dodržování předpisů iniciativy nebo obchodní cíle.

3. V rámci programu, vyberte **ovládací prvky**a pak vyberte **přidat** přidání ovládacího prvku.

4. Název kontroly přístupu. Kontrola volitelně zadejte popis. Název a popis se zobrazí pro revidující.

5. Nastavte počáteční datum. Ve výchozím nastavení kontroly přístupu akce proběhne jednou, spustí stejné chvíli, kdy je vytvořen a končí na jeden měsíc. Můžete změnit počáteční a koncové datum měli přístup zkontrolujte start v budoucnu a poslední představ počtu dnů.

6. Aby opakování kontroly přístupu, změnit frekvenci z jednou týdně, měsíčně, čtvrtletně nebo ročně a můžete definovat, kolik dní má být každou recenzi řady opakovaných otevřené pro vstup od recenzentů posuvník nebo textového pole. Například maximální doba trvání můžete nastavit pro měsíční přehled je 27 dnů překrývající se oblasti kontroly. 

7.  Opakované řadu kontrol přístupu můžete ukončit způsoby 3: spuštění kontroly po neomezenou dobu, dokud k určitému datu nebo po dokončení definovaný počet výskytů běží nepřetržitě. Můžete, jiný správce uživatelských účtů nebo jiného globálního správce můžete zastavit řady po vytvoření změnou datum v nastavení tak, aby ho k tomuto datu ukončení.

8. Kontroly přístupu lze pro členy skupiny nebo uživatelé, kteří byly přiřazeny k aplikaci. Můžete dále obor přístupu pouze kontroly kontrola uživatelů typu Host kteří jsou členy (nebo přiřadit k aplikaci), namísto kontroly všech uživatelů, kteří jsou členy nebo kteří mají přístup k aplikaci.

9. Vyberte jeden nebo více lidem zkontrolovat všechny uživatele v oboru. Nebo můžete mít členy kontrolovat svůj vlastní přístup. Pokud prostředek je skupina, můžete požádat vlastníci skupiny ke kontrole. Také můžete vyžadovat, aby revidující zadejte důvod, proč při jejich schválit přístup.

10. Pokud chcete aplikovat ručně výsledky, až se kontrola dokončí, klikněte na tlačítko **Start**.  V opačném případě další část vysvětluje postup konfigurace revizi na automatické použití.

### <a name="configuring-an-access-review-with-auto-apply"></a>Nakonfigurování auto-apply kontroly přístupu

1.  Rozbalte nabídku nastavení činností po dokončení a povolení automaticky zavést výsledky do prostředku. 

2.  V případech, kde se uživatelé zkontroloval kontrolor během období vyhodnocování, máte kontrolu přístupu trvat doporučení systému na odepření/schválit pokračování přístupu uživatele (je-li povoleno), ponechte jejich přístupu beze změny nebo odeberte jejich přístup. Nebude to mít vliv, kteří byly zkontrolovány podle revidující ručně – Pokud poslední revidující rozhodnutí je Deny přístup uživatele se odebere.

3.  Pokud chcete povolit možnost přijmout doporučení by neměly odpovídat revidující, rozbalte upřesňující nastavení a povolte doporučení zobrazit.
 
4.  Nakonec klikněte na tlačítko **Start**.

Na základě vašeho výběru v nastavení činností po dokončení, automaticky – použít bude proveden po koncové datum kontrola nebo když ručně zastavte kontrolu. Stav kontroly se změní z dokončeno prostřednictvím průběžných stavů, jako je například používání a nakonec na stav použito. Měli byste očekávat zobrazíte zamítnutým uživatelům, pokud existuje, odebírán z přiřazení skupiny členství nebo aplikaci za několik minut.


## <a name="manage-the-access-review"></a>Správa kontroly přístupu.

Ve výchozím nastavení Azure AD pošle e-mail revidující krátce po spuštění kontroly. Pokud se rozhodnete odeslat e-mailu se službou Azure AD, nezapomeňte informovat revidující, které čeká na všesměrově jejich dokončení kontroly přístupu. Je možné zobrazit pokyny k [kontrolovat přístup](active-directory-azure-ad-controls-perform-access-review.md). Pokud kontrolu pro hosty kontrolovat svůj vlastní přístup, je zobrazit pokyny k [kontrolovat svůj vlastní přístup](active-directory-azure-ad-controls-perform-access-review.md).

Pokud jsou některé z revidující hosté, hosté upozorněni prostřednictvím e-mailu jenom v případě, že jste již přijetí svou pozvánku.

Pokud chcete spravovat řadu kontrol přístupu, přejděte na kontrolu přístupu z **ovládací prvky**, a budete najít připravované výskyty v naplánované kontroly a upravit koncové datum nebo přidání nebo odebrání revidujících odpovídajícím způsobem. 

Průběh můžete sledovat, jak dokončit revidující své recenze na řídicím panelu Azure AD v **kontroly přístupu** oddílu. Žádné přístupová práva jsou změněny v adresáři, dokud [se kontrola dokončí](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Další postup

Po spuštění kontroly přístupu Azure AD automaticky pošle revidující, který zobrazí výzvu ke kontrole přístupu k e-mailu. Pokud uživatel e-mail neobdrželi, můžete jim poslat pokyny k [kontrolovat přístup](active-directory-azure-ad-controls-perform-access-review.md). 

Pokud je to jednorázové kontroly, po období kontroly přístupu je nad nebo správce zastavení kontroly přístupu, postupujte podle pokynů v [dokončení kontroly přístupu](active-directory-azure-ad-controls-complete-access-review.md) chcete zobrazit a použít výsledky.  

Pokud je to řadu kontrol, přejděte na **kontrola historie** na stránce řady kontroly přístupu k výběru recenzi dokončené přístup.  Nadcházející kontroly budou uvedené v části **naplánované kontroly**, kde můžete upravit dobu trvání a přidání nebo odebrání revidujících u jednotlivých revizí.
