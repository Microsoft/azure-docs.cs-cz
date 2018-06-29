---
title: Vytvoření kontrola přístupu pro členy skupiny nebo uživatelé s přístupem k aplikaci s Azure AD | Microsoft Docs
description: Naučte se vytvářet kontrola přístupu pro členy skupiny nebo uživatelé s přístupem k aplikaci.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 20f238a7d8a3882f2a126c900e04ecf7be613be5
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084973"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Vytvoření kontrola přístupu členů skupiny nebo přístup k aplikaci s Azure AD

Přiřazení přístupu stát "zastaralé", když uživatelé mají přístup, které nepotřebují víc. Abyste snížili riziko spojené s přiřazením zastaralé přístup, mohou správci Azure Active Directory (Azure AD) k vytvoření kontrola přístupu pro členy skupiny nebo uživatelé přiřazení k aplikaci. Vytváření opakovaně recenze přístup může být ušetřit čas. Pokud potřebujete pravidelně kontrolujte uživatelů, kteří mají přístup k aplikaci nebo jsou členy skupiny, můžete definovat četnost těchto recenze. Další informace o těchto scénářích najdete v tématu [spravovat přístup uživatelů](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) a [spravovat přístup hosta](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Vytvořit kontrolu přístupu

1. Jako globální správce nebo správce účtu uživatele, přejděte na [přístupu zkontroluje stránky](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)a vyberte **programy**.

2. Vyberte program, který obsahuje zkontrolujte řízení přístupu, kterou chcete vytvořit. **Výchozí Program** je vždy k dispozici, nebo můžete vytvořit v jiné aplikaci. Například můžete mít jeden program pro každou initiative dodržování předpisů nebo obchodní cíle.

3. V rámci programu, vyberte **ovládací prvky**a potom vyberte **přidat** přidání ovládacího prvku.

4. Název kontrola přístupu. Kontrola volitelně zadejte popis. Kontroloři se zobrazují název a popis.

5. Nastavte počáteční datum. Ve výchozím nastavení kontrola přístupu proběhne jednou, spustí stejnou dobu, kdy je vytvořen a je končí na jeden měsíc. Spuštění, můžete změnit a koncovém datu měli přístup zkontrolujte spuštění v budoucnu a poslední ale chcete počet dní.

6. Aby opakovaného zkontrolujte přístup, změnit frekvenci z jednou týdně, měsíčně, čtvrtletně nebo ročně a definovat, jak dlouho bude každý zkontrolujte řady opakovaných otevřené pro vstup z kontroloři pomocí posuvníku nebo textové pole. Maximální délka trvání pro lze nastavit pro měsíční kontrolu je například 27 dnů překrývající se recenze. 

7.  Řada opakovaných kontrola přístupu můžete ukončit způsoby 3: běží nepřetržitě zahájíte recenze po neomezenou dobu, až do určitého data, nebo po dokončení definovaný počet výskytů. Můžete, jiný správce účtu uživatele nebo jiného globálního správce můžete zastavit řady po vytvoření změnou datum v nastavení tak, aby ho končí na datum.

8. Recenze přístup může být pro členy skupiny nebo pro uživatele, kteří byly přiřazené k aplikaci. Můžete vytvořit další obor přístupu zkontrolujte kontrola pouze uživatele typu Host kdo jsou členy (nebo přiřazené k aplikaci), namísto kontroly všech uživatelů, kteří jsou členy, nebo kteří mají přístup k aplikaci.

9. Vyberte jeden nebo více osob, zobrazíte všechny uživatele v oboru. Nebo můžete vybrat tak, aby měl členy Zkontrolujte své vlastní přístup. Pokud prostředek je skupina, můžete požádat vlastníků skupiny ke kontrole. Také můžete vyžadovat, aby kontroloři při schválí přístup zadat příslušný důvod.

10. Pokud chcete po dokončení kontrola použít ručně výsledky, klikněte na tlačítko **spustit**.  Jinak v další části vysvětluje postup konfigurace zkontrolujte na automatické použít.

### <a name="configuring-an-access-review-with-auto-apply"></a>Konfigurace kontrola přístupu s auto-apply

1.  Po dokončení nastavení a povolit automatické vztahuje výsledky na prostředek, rozbalte nabídku. 

2.  V případech, kde uživatelé nebyly zkontrolovány uživatelem kontrolorovi v období kontrolní, může mít kontrola přístupu buď trvat doporučení systému (Pokud je povoleno) na odepření/schválení nepřetržitý přístup uživatele, ponechejte jejich přístup beze změny nebo odstraňte jejich přístup. Uživatelé, kteří byly zkontrolovány podle kontroloři ručně – to nemá vliv, pokud poslední kontrolor rozhodnutí odepřít, pak přístupu uživatele se odebere.

3.  Pokud chcete povolit možnost provést doporučení by neměly odpovídat kontrolorů, rozbalte položku Upřesnit nastavení a povolte zobrazit doporučení.
 
4.  Nakonec klikněte na **spustit**.

Podle výběru v po dokončení nastavení, automaticky použít se provést po koncové datum kontrola nebo když ručně zastavit kontrola. Stav revize se změní z dokončeno prostřednictvím zprostředkující stavu třeba Applying a nakonec do stavu použita. Byste měli očekávat zobrazit odepření uživatele, pokud existuje, odebírán z přiřazení skupiny členství nebo aplikace za pár minut.


## <a name="manage-the-access-review"></a>Spravovat kontrola přístupu

Ve výchozím nastavení Azure AD odešle e-mail recenzentům krátce po spustí kontrola. Pokud se rozhodnete, že není mít službu Azure AD odesílat e-mailu, nezapomeňte informovat o tom, kontrolorů, které kontrola přístupu čeká na jejich dokončení. Je možné zobrazit pokyny, jak [kontrolujte přístup](active-directory-azure-ad-controls-perform-access-review.md). Pokud zkontrolovali pro hosty zkontrolovat svůj vlastní přístup, zobrazit jejich pokyny, jak [zkontrolujte přístup k vlastní](active-directory-azure-ad-controls-perform-access-review.md).

Je-li některé z kontroloři jsou hosté, hosté upozornění e-mailem pouze v případě, že jste již přijetí jejich pozvánku.

Chcete-li spravovat řadu recenze přístup, přejděte na kontrola přístupu z **ovládací prvky**, a budete najít nadcházející události v naplánovaná recenze a upravit koncové datum nebo přidat nebo odebrat kontroloři odpovídajícím způsobem. 

Průběh můžete sledovat jejich recenze v řídicím panelu Azure AD v dokončení kontroloři **zkontroluje přístup** části. Žádné oprávnění došlo ke změně v adresáři, dokud [byla dokončena kontrola](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Další postup

Po spuštění kontrola přístupu Azure AD automaticky odesílá kontroloři e-mailu, který zobrazí výzvu k kontrolujte přístup. Pokud uživatel nedostali e-mailu, můžete jim poslat pokyny, jak [kontrolujte přístup](active-directory-azure-ad-controls-perform-access-review.md). 

Pokud je to jednorázové kontrolu, po dobu zkontrolujte přístup je nad nebo správce zastaví kontrola přístupu, postupujte podle pokynů v [dokončit kontrola přístupu](active-directory-azure-ad-controls-complete-access-review.md) zobrazit a použít výsledky.  

Pokud je kontrola řady, pak přejděte do **zkontrolujte historii** na stránce řady zkontrolujte přístup k výběru kontrola dokončené přístupu.  Nadcházející recenze, zobrazí se v části **naplánované zkontrolujte**, kde můžete upravit dobu trvání a přidání nebo odebrání revidujících pro jednotlivé recenze.
