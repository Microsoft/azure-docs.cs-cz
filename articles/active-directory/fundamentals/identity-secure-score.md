---
title: Co je zabezpečené skóre Identity? – Azure Active Directory (Public preview) | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak můžete pomocí identity zabezpečení skóre a zlepšit stav zabezpečení vašeho tenanta Azure AD.
services: active-directory
keywords: bezpečnostní skóre identity, Azure AD, zabezpečený přístup k firemním prostředkům
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: 5d1ad01c775c270b83b49ee5f6ba5cb4706ff97c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094066"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory-public-preview"></a>Co je skóre zabezpečení identit v Azure Active Directory? (Public preview)

Jak zabezpečený je váš tenant služby Azure AD? Pokud nevíte, jak na tuto otázku odpovědět, dozvíte se v tomto článku, jak vám bezpečnostní skóre identity umožní monitorovat a zlepšit stav zabezpečení vaší identity. 

## <a name="what-is-an-identity-secure-score"></a>Co je bezpečnostní skóre identity?

Bezpečnostní skóre identity je číslo mezi 1 a 248, které funguje jako ukazatel toho, nakolik dodržujete doporučení osvědčených postupů zabezpečení Microsoftu.


![Bezpečnostní skóre](./media/identity-secure-score/01.png)



Toto skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity

- Naplánovat vylepšení zabezpečení identity

- Posoudit úspěšnost těchto vylepšení 


K tomuto skóre a souvisejícím informacím se dostanete na řídicím panelu bezpečnostního skóre identity. Na tomto řídicím panelu najdete:

- Vaše skóre

    ![Bezpečnostní skóre](./media/identity-secure-score/02.png)

- Srovnávací graf

    ![Bezpečnostní skóre](./media/identity-secure-score/03.png)

- Graf trendu

    ![Bezpečnostní skóre](./media/identity-secure-score/04.png)

- Seznam osvědčených postupů pro zabezpečení identity 

    ![Bezpečnostní skóre](./media/identity-secure-score/05.png)


Provedením nápravných akcí můžete:

- Vylepšit stav zabezpečení a svoje skóre
 
- Využít výhody funkcí pro správu identity od Microsoftu 



## <a name="how-do-i-get-my-secure-score"></a>Jak získám svoje bezpečnostní skóre?

Bezpečnostní skóre identity je dostupné ve všech edicích služby Azure AD.

Svoje skóre uvidíte, když přejdete na [řídicí panel s přehledem služby Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).



## <a name="how-does-it-work"></a>Jak to funguje?

Azure každých 48 hodin posuzuje vaši konfiguraci zabezpečení a porovnává vaše nastavení s doporučenými osvědčenými postupy. Na základě výsledku tohoto vyhodnocení se vypočítá nové skóre pro vašeho tenanta. Může proto trvat až 48 hodin, než se změna konfigurace, kterou jste udělali, projeví ve vašem skóre. 

Každé doporučení se měří na základě vaší konfigurace služby Azure AD. Pokud doporučený osvědčený postup prosadíte pomocí produktů jiných výrobců, můžete to vyznačit v nastavení nápravné akce.

![Bezpečnostní skóre](./media/identity-secure-score/07.png)


Navíc také máte možnost nastavit doporučení, která se mají ignorovat, pokud se nevztahují na vaše prostředí. Ignorované doporučení nepřispívá k výpočtu vašeho skóre. 
 
![Bezpečnostní skóre](./media/identity-secure-score/06.png)



## <a name="how-does-it-help-me"></a>Jak mi toto skóre pomůže?

Bezpečnostní skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity

- Naplánovat vylepšení zabezpečení identity

- Posoudit úspěšnost těchto vylepšení



## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="who-can-use-the-identity-secure-score"></a>Kdo může používat bezpečnostní skóre identity?

Bezpečnostní skóre identity můžou používat následující role:

- Globální správce
- Správce zabezpečení 
- Čtenáři zabezpečení 

### <a name="what-does-not-scored-mean"></a>Co znamená, když skóre není určené?

Akce bez určeného skóre jsou akce, které sice můžete v organizaci udělat, ale jejich skóre se neurčí, protože zatím nejsou v tomto nástroji implementované. Svoje zabezpečení tak můžete přesto vylepšit, ale v současnosti se vám za tyto akce nic nepřičte.

### <a name="how-often-is-my-score-updated"></a>Jak často se skóre aktualizuje?

Skóre se počítá jednou za den (přibližně v 1:00 PST). Pokud uděláte změnu u měřené akce, aktualizuje se skóre automaticky následující den. Změna se ve vašem skóre může projevit až za 48 hodin.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>Moje skóre se změnilo. Jak zjistím, proč k tomu došlo?

Na stránce analyzátoru skóre na [portálu bezpečnostního skóre](https://securescore.microsoft.com/#!/score) klikněte na datový bod konkrétního dne a posuňte se dolů, kde uvidíte dokončené a nedokončené akce za tento den. Z nich zjistíte, co se změnilo.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Je bezpečnostní skóre měřítkem rizika, s jakým dojde k porušení mého zabezpečení?

Nikoli. Bezpečnostní skóre nevyjadřuje absolutní měřítko toho, s jakou pravděpodobností dojde k porušení vašeho zabezpečení. Vyjadřuje, do jaké míry jste si osvojili funkce, které dokážou riziko porušení zabezpečení kompenzovat. Žádná služba nemůže zaručit, že nedojde k porušení zabezpečení, a bezpečnostní skóre by se v žádném případě nemělo interpretovat jako určitá záruka.

### <a name="how-should-i-interpret-my-score"></a>Jak mám skóre interpretovat?

Dostáváte body za konfiguraci doporučených funkcí zabezpečení nebo za provádění úloh souvisejících se zabezpečením (například za prohlížení sestav). U některých akcí obdržíte skóre za částečné dokončení, například za povolení vícefaktorového ověřování pro uživatele. Vaše bezpečnostní skóre přímo reprezentuje služby zabezpečení Microsoftu, které používáte. Mějte na paměti, že zabezpečení by vždy mělo být v rovnováze s použitelností. Všechna bezpečnostní opatření mají dopad na uživatele. Opatření s nízkým dopadem na uživatele by na každodenní práci uživatelů měla mít minimální nebo nulový vliv.

Historii skóre zobrazíte tak, že přejdete na stránku analyzátoru skóre na [portálu bezpečnostního skóre](https://securescore.microsoft.com/#!/score). Zvolením konkrétního data zobrazíte, která opatření byla v ten den zavedena a kolik bodů jste za ně získali.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Jak bezpečnostní skóre identity souvisí s bezpečnostním skóre Office 365? 

[Bezpečnostní skóre Office 365](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) bude zanedlouho převedeno na agregaci pěti různých skóre:

- Identita

- Data

- Zařízení

- Infrastruktura

- Aplikace

Bezpečnostní skóre identity představuje tu součást bezpečnostního skóre Office 365, která se týká identity. To znamená, že doporučení pro bezpečnostní skóre identity a skóre identity v Office 365 jsou stejná. 


## <a name="next-steps"></a>Další postup

Kliknutím [sem](https://www.youtube.com/watch?v=jzfpDJ9Kg-A) se můžete podívat na video o bezpečnostním skóre Office 365.
 
