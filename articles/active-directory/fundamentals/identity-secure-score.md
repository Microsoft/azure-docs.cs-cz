---
title: Co je zabezpečené skóre identity? – Azure Active Directory
description: Použití zabezpečeného skóre identity a zlepšit stav zabezpečení vašeho adresáře
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988687"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Co je skóre zabezpečení identit v Azure Active Directory?

Jak zabezpečený je váš tenant služby Azure AD? Pokud si nejste jisti, jak na tuto otázku odpovědět, tento článek vysvětluje, jak skóre zabezpečení identity vám umožňuje monitorovat a zlepšit tak stav zabezpečení identity.

## <a name="what-is-an-identity-secure-score"></a>Co je bezpečnostní skóre identity?

Skóre zabezpečení identit je číslo z rozsahu 1 až 223, která slouží jako indikátor jak zarovnané jste se od Microsoftu doporučené osvědčené postupy pro zabezpečení. Každá akce zlepšení v zabezpečené skóre identity je vytvořený na míru vaší konkrétní konfiguraci.  

![Secure Score](./media/identity-secure-score/identity-secure-score-overview.png)

Toto skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity
- Naplánovat vylepšení zabezpečení identity
- Posoudit úspěšnost těchto vylepšení

K tomuto skóre a souvisejícím informacím se dostanete na řídicím panelu bezpečnostního skóre identity. Na tomto řídicím panelu najdete:

- Skóre zabezpečení vaší identity
- Porovnání graf, který znázorňuje, jak zabezpečit vaši identitu skóre porovnává do jiných tenantů stejného odvětví a podobně jako velikost
- Trend graf s informacemi o tom, jak má vaše skóre zabezpečení Identity mění v průběhu času
- Seznam možných zlepšení

Provedením nápravných akcí můžete:

- Zlepšit tak stav zabezpečení a vaše skóre
- Využijte funkce dostupné pro vaši organizaci jako součást své investice identity

## <a name="how-do-i-get-my-secure-score"></a>Jak získám svoje bezpečnostní skóre?

Skóre zabezpečení identit je k dispozici ve všech edicích služby Azure AD. Svoje skóre uvidíte, když přejdete na [řídicí panel s přehledem služby Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).

## <a name="how-does-it-work"></a>Jak to funguje?

Azure každých 48 hodin posuzuje vaši konfiguraci zabezpečení a porovnává vaše nastavení s doporučenými osvědčenými postupy. Na základě výsledku vyhodnocení, se vypočítá nové skóre pro svůj adresář. Je možné, že konfiguraci zabezpečení není nejsou plně v souladu s nejlepších praktik a zlepšování akce, které jsou jen částečně splněny. V těchto scénářích vám bude jenom udělovány část maximální skóre, které jsou k dispozici pro ovládací prvek.

Každé doporučení se měří na základě vaší konfigurace služby Azure AD. Používáte-li povolit osvědčených postupů doporučení produktů třetích stran, můžete určit tuto konfiguraci v nastaveních zlepšování akce. Máte také možnost nastavit doporučení, která se ignoruje, pokud nemůžete použít pro vaše prostředí. Ignorované doporučení nepřispívá k výpočtu vašeho skóre.

![Ignorovat nebo označit akce, jako je předmětem třetích stran](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>Jak jsou hodnocené ovládacích prvků

Ovládací prvky mohou být upraveny dvěma způsoby. Některé jsou hodnocené v binární podobě – získání skóre 100 %, pokud máte funkci, nebo nakonfigurované nastavení založené na naše doporučení. Další skóre, které se vypočítají jako procento celkové konfiguraci. Například pokud doporučení zlepšování stavy, pokud budete chránit všechny uživatele s MFA a máte 5 100 celkový počet uživatelů, které jsou chráněné, zobrazí se 30 bodů, bude by nabídnuta částečné skóre přibližně 2 body (5 chráněné / celkem 100 * 30 maximální počet bodů = 2 částečné skóre bodů) .

### <a name="what-does-not-scored-mean"></a>Co znamená, když skóre není určené?

Akce bez určeného skóre jsou akce, které sice můžete v organizaci udělat, ale jejich skóre se neurčí, protože zatím nejsou v tomto nástroji implementované. Svoje zabezpečení tak můžete přesto vylepšit, ale v současnosti se vám za tyto akce nic nepřičte.

### <a name="how-often-is-my-score-updated"></a>Jak často se skóre aktualizuje?

Skóre se počítá jednou za den (přibližně v 1:00 PST). Pokud uděláte změnu u měřené akce, aktualizuje se skóre automaticky následující den. Změna se ve vašem skóre může projevit až za 48 hodin.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Moje skóre se změnilo. Jak zjistím, proč k tomu došlo?

Přejděte na [Centrum zabezpečení Microsoft 365](https://security.microsoft.com/), kde najdete kompletní zabezpečené skóre Microsoft. Můžete snadno zobrazit všechny změny na zabezpečené skóre kontrolou podrobné změny na kartě Historie.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Měření zabezpečeného skóre Moje riziko získávání dojde k porušení zabezpečení?

Nikoli. Zabezpečené skóre neznamenají absolutní měřítko, jak pravděpodobně jste získat dojde k porušení zabezpečení. Vyjadřuje, do jaké míry jste si osvojili funkce, které dokážou riziko porušení zabezpečení kompenzovat. Žádná služba může zaručit, že nebude splněn a zabezpečené skóre neměly by být vykládány jako záruky žádným způsobem.

### <a name="how-should-i-interpret-my-score"></a>Jak mám skóre interpretovat?

Dostáváte body za konfiguraci doporučených funkcí zabezpečení nebo za provádění úloh souvisejících se zabezpečením (například za prohlížení sestav). U některých akcí obdržíte skóre za částečné dokončení, například za povolení vícefaktorového ověřování pro uživatele. Zabezpečené skóre je přímo zástupce služby Microsoft zabezpečení, které používáte. Mějte na paměti, že zabezpečení musí být vyrovnávaném pomocí použitelnost. Všechna bezpečnostní opatření mají dopad na uživatele. Opatření s nízkým dopadem na uživatele by na každodenní práci uživatelů měla mít minimální nebo nulový vliv.

Pokud chcete zobrazit historii skóre, přejděte na [Centrum zabezpečení Microsoft 365](https://security.microsoft.com/) a zkontrolovat celkové skóre zabezpečení Microsoftu. Změny můžete zkontrolovat na celkových zabezpečené skóre se kliknutím na Zobrazit historii. Zvolením konkrétního data zobrazíte, která opatření byla v ten den zavedena a kolik bodů jste za ně získali.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Jak bezpečnostní skóre identity souvisí s bezpečnostním skóre Office 365?

[Microsoft secure skóre](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) obsahuje pět různých ovládacího prvku a skóre kategorie:

- Identita
- Data
- Zařízení
- Infrastruktura
- Aplikace

Skóre zabezpečení identity představuje součást identity skóre zabezpečení Microsoftu. Tento překrytí znamená, že vaše doporučení pro identitu zabezpečení skóre a skóre identity ve službě Microsoft jsou stejné.

## <a name="next-steps"></a>Další postup

[Další informace o Microsoft zabezpečené skóre](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
