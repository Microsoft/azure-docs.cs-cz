---
title: Co je to bezpečné skóre identity? -Azure Active Directory
description: Jak můžete pomocí bezpečného skóre identity zlepšit stav zabezpečení vašeho adresáře
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: guptashi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23832d9f1205105f1f9711cdf3260b74ee4a9bb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952251"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Jaké je bezpečné skóre identity v Azure Active Directory?

Jak zabezpečený je váš tenant služby Azure AD? Pokud si nejste jisti, jak na tuto otázku odpovědět, v tomto článku se dozvíte, jak vám bezpečné skóre identity pomůže monitorovat a zdokonalovat stav zabezpečení identity.

## <a name="what-is-an-identity-secure-score"></a>Co je bezpečnostní skóre identity?

Bezpečné skóre identity je procentuální hodnota, která funguje jako indikátor, jak vám vyhovuje doporučení osvědčených postupů Microsoftu pro zabezpečení. Každá akce zlepšení v zabezpečeném skóre identity je přizpůsobená konkrétní konfiguraci.  

![Bezpečnostní skóre](./media/identity-secure-score/identity-secure-score-overview.png)

Toto skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity
- Naplánovat vylepšení zabezpečení identity
- Posoudit úspěšnost těchto vylepšení

K tomuto skóre a souvisejícím informacím se dostanete na řídicím panelu bezpečnostního skóre identity. Na tomto řídicím panelu najdete:

- Vaše zabezpečené skóre identity
- Graf porovnání znázorňující, jak vaše zabezpečené skóre identity porovnává s ostatními klienty ve stejném oboru a podobnou velikostí
- Graf trendu znázorňující, jak se v průběhu času mění vaše zabezpečené skóre identity
- Seznam možných vylepšení

Provedením nápravných akcí můžete:

- Zlepšení stav zabezpečení a skóre
- Využijte výhod funkcí dostupných pro vaši organizaci jako součást vašich investic do vaší identity.

## <a name="how-do-i-get-my-secure-score"></a>Jak získám svoje bezpečnostní skóre?

Hodnocení identity Secure je dostupné ve všech edicích služby Azure AD. Organizace mají přístup k zabezpečenému skóre identity z **Azure Portal**  >  **Azure Active Directory**  >    >  **zabezpečeného skóre identity** zabezpečení.

## <a name="how-does-it-work"></a>Jak to funguje?

Azure každých 48 hodin posuzuje vaši konfiguraci zabezpečení a porovnává vaše nastavení s doporučenými osvědčenými postupy. Na základě výsledku tohoto vyhodnocení se pro váš adresář počítá nové skóre. Je možné, že vaše konfigurace zabezpečení není plně zarovnaná s návodem k osvědčeným postupům a akce zlepšování jsou pouze částečně splněné. V těchto scénářích budete mít k dispozici pouze část maximálního skóre dostupného pro tento ovládací prvek.

Každé doporučení se měří na základě vaší konfigurace služby Azure AD. Pokud používáte produkty třetích stran k povolení doporučení osvědčených postupů, můžete tuto konfiguraci označit v nastavení akce zlepšení. Máte také možnost nastavit doporučení, která se mají ignorovat, pokud se nevztahují k vašemu prostředí. Ignorované doporučení nepřispívá k výpočtu vašeho skóre.

![Ignorovat nebo označit akci, kterou pokrývá třetí strana](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>Jak se řídí skóre?

Ovládací prvky mohou být hodnoceny dvěma způsoby. Některé jsou v binárním formátu skóre – Pokud máte funkci nebo nastavení nakonfigurovanou na základě našeho doporučení, získáte 100% skóre. Další hodnocení jsou vypočítána jako procento z celkové konfigurace. Pokud třeba doporučení pro zlepšení stanoví, že získáte maximálně 10,71%, Pokud chráníte všechny uživatele pomocí vícefaktorového ověřování a zadáte jenom 5 ze 100 z celkového počtu chráněných uživatelů, budete mít k dispozici částečné skóre kolem 0,53% (5 Protected/100 Total * 10,71% Max = 0,53% Partial skore).

### <a name="what-does-not-scored-mean"></a>Co znamená, když skóre není určené?

Akce bez určeného skóre jsou akce, které sice můžete v organizaci udělat, ale jejich skóre se neurčí, protože zatím nejsou v tomto nástroji implementované. Svoje zabezpečení tak můžete přesto vylepšit, ale v současnosti se vám za tyto akce nic nepřičte.

### <a name="how-often-is-my-score-updated"></a>Jak často se skóre aktualizuje?

Skóre se počítá jednou za den (přibližně v 1:00 PST). Pokud uděláte změnu u měřené akce, aktualizuje se skóre automaticky následující den. Změna se ve vašem skóre může projevit až za 48 hodin.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Moje skóre se změnilo. Jak zjistím, proč k tomu došlo?

Přejděte do [služby Microsoft 365 Security Center](https://security.microsoft.com/), kde najdete kompletní bezpečnostní skóre Microsoftu. Všechny změny v zabezpečeném skóre můžete snadno zobrazit tak, že zkontrolujete podrobné změny na kartě Historie.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Měří zabezpečené skóre riziko, že došlo k porušení?

Nikoli. Bezpečnostní skóre nevyjadřuje absolutní míru toho, jak je pravděpodobný, že byste se měli porušit. Vyjadřuje, do jaké míry jste si osvojili funkce, které dokážou riziko porušení zabezpečení kompenzovat. Žádná služba nemůže zaručit, že nebudete narušit činnost a že bezpečné skóre by se nemělo v žádném případě vykládat jako záruka.

### <a name="how-should-i-interpret-my-score"></a>Jak mám skóre interpretovat?

Vaše skóre vylepšuje konfiguraci doporučených funkcí zabezpečení nebo provádění úloh souvisejících se zabezpečením (například čtení sestav). U některých akcí obdržíte skóre za částečné dokončení, například za povolení vícefaktorového ověřování pro uživatele. Vaše zabezpečené skóre je přímo reprezentativní pro služby zabezpečení společnosti Microsoft, které používáte. Mějte na paměti, že zabezpečení musí být vyvážené pomocí použitelnosti. Všechna bezpečnostní opatření mají dopad na uživatele. Opatření s nízkým dopadem na uživatele by na každodenní práci uživatelů měla mít minimální nebo nulový vliv.

Pokud chcete zobrazit historii skóre, přejděte do [služby Microsoft 365 Security Center](https://security.microsoft.com/) a Prohlédněte si celkové skóre Microsoftu. Kliknutím na Zobrazit historii můžete zkontrolovat změny v celkovém bezpečném skóre. Zvolením konkrétního data zobrazíte, která opatření byla v ten den zavedena a kolik bodů jste za ně získali.

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>Jak se v rámci zabezpečeného skóre identity vztahuje k Microsoft 365 zabezpečenému skóre?

[Microsoft Secure skore](/office365/securitycompliance/microsoft-secure-score) obsahuje pět kategorií různých ovládacích prvků a skóre:

- Identita
- Data
- Zařízení
- Infrastruktura
- Aplikace

Počet bezpečných identit identity představuje část identity Microsoft Secure skóre. Tento překryv znamená, že vaše doporučení pro bezpečné skóre identity a hodnocení identity v Microsoftu jsou stejná.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace o službě Microsoft Secure skore.](/office365/securitycompliance/microsoft-secure-score)