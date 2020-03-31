---
title: Co je skóre zabezpečení identity? - Služba Azure Active Directory
description: Jak můžete pomocí skóre zabezpečení identity zlepšit stav zabezpečení adresáře
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523100"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Jaké je skóre zabezpečení identity ve službě Azure Active Directory?

Jak zabezpečený je váš tenant služby Azure AD? Pokud nevíte, jak odpovědět na tuto otázku, tento článek vysvětluje, jak skóre zabezpečení identity pomáhá sledovat a zlepšit stav zabezpečení identity.

## <a name="what-is-an-identity-secure-score"></a>Co je bezpečnostní skóre identity?

Skóre zabezpečení identity je číslo mezi 1 a 223, které funguje jako indikátor pro to, jak jste zarovnáni s doporučeními společnosti Microsoft pro osvědčené postupy pro zabezpečení. Každá akce vylepšení v skóre zabezpečení identity je přizpůsobena vaší konkrétní konfiguraci.  

![Bezpečnostní skóre](./media/identity-secure-score/identity-secure-score-overview.png)

Toto skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity
- Naplánovat vylepšení zabezpečení identity
- Posoudit úspěšnost těchto vylepšení

K tomuto skóre a souvisejícím informacím se dostanete na řídicím panelu bezpečnostního skóre identity. Na tomto řídicím panelu najdete:

- Vaše identita zabezpečené skóre
- Srovnávací graf znázorňující, jak vaše skóre zabezpečení identity porovnává s ostatními klienty ve stejném odvětví a podobné velikosti
- Graf trendů znázorňující, jak se vaše skóre zabezpečení identity v průběhu času měnilo
- Seznam možných vylepšení

Provedením nápravných akcí můžete:

- Zlepšete své bezpečnostní držení těla a skóre
- Využijte funkce, které má vaše organizace k dispozici v rámci investic do identity

## <a name="how-do-i-get-my-secure-score"></a>Jak získám svoje bezpečnostní skóre?

Skóre zabezpečení identity je k dispozici ve všech edicích Azure AD. Organizace mají přístup ke svému skóre zabezpečení identity z **webu Azure Portal** > **Azure Active Directory** > **Security** > **Identity Secure Score**.

## <a name="how-does-it-work"></a>Jak to funguje?

Azure každých 48 hodin posuzuje vaši konfiguraci zabezpečení a porovnává vaše nastavení s doporučenými osvědčenými postupy. Na základě výsledku tohoto hodnocení se pro váš adresář vypočítá nové skóre. Je možné, že konfigurace zabezpečení není plně v souladu s pokyny pro osvědčené postupy a akce vylepšení jsou splněny pouze částečně. V těchto scénářích obdržíte pouze část maximálního skóre, které je k dispozici pro ovládací prvek.

Každé doporučení se měří na základě vaší konfigurace služby Azure AD. Pokud používáte produkty jiných výrobců k povolení doporučené ho schodišti, můžete tuto konfiguraci označit v nastavení akce vylepšení. Máte také možnost nastavit doporučení, která mají být ignorována, pokud se nevztahují na vaše prostředí. Ignorované doporučení nepřispívá k výpočtu vašeho skóre.

![Ignorovat nebo označit akci jako pokrytou třetí stranou](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>Jak jsou kontroly hodnoceny?

Ovládací prvky lze skórovat dvěma způsoby. Některé jsou hodnoceny binárním způsobem - získáte 100% skóre, pokud máte funkci nebo nastavení nakonfigurované na základě našeho doporučení. Ostatní skóre se počítají jako procento z celkové konfigurace. Pokud například doporučení ke zlepšení uvádí, že získáte 30 bodů, pokud ochráníte všechny uživatele pomocí vícefaktorové žádosti o platbu a máte chráněno pouze 5 ze 100 uživatelů, budete mít částečné skóre kolem 2 bodů (5 chráněných / 100 celkem * 30 maximálních bodů = 2 body částečné skóre).

### <a name="what-does-not-scored-mean"></a>Co znamená, když skóre není určené?

Akce bez určeného skóre jsou akce, které sice můžete v organizaci udělat, ale jejich skóre se neurčí, protože zatím nejsou v tomto nástroji implementované. Svoje zabezpečení tak můžete přesto vylepšit, ale v současnosti se vám za tyto akce nic nepřičte.

### <a name="how-often-is-my-score-updated"></a>Jak často se skóre aktualizuje?

Skóre se počítá jednou za den (přibližně v 1:00 PST). Pokud uděláte změnu u měřené akce, aktualizuje se skóre automaticky následující den. Změna se ve vašem skóre může projevit až za 48 hodin.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Moje skóre se změnilo. Jak zjistím, proč k tomu došlo?

Zamiřte do [centra zabezpečení Microsoft 365](https://security.microsoft.com/), kde najdete kompletní skóre zabezpečení microsoftu. Všechny změny zabezpečeného skóre můžete snadno zobrazit kontrolou podrobných změn na kartě Historie.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Měří bezpečné skóre mé riziko porušení?

Nikoli. Bezpečné skóre nevyjadřuje absolutní míru toho, jak je pravděpodobné, že budete porušeni. Vyjadřuje, do jaké míry jste si osvojili funkce, které dokážou riziko porušení zabezpečení kompenzovat. Žádná služba nemůže zaručit, že nebudete porušeni, a bezpečné skóre by nemělo být vykládáno jako záruka v žádném případě.

### <a name="how-should-i-interpret-my-score"></a>Jak mám skóre interpretovat?

Dostáváte body za konfiguraci doporučených funkcí zabezpečení nebo za provádění úloh souvisejících se zabezpečením (například za prohlížení sestav). U některých akcí obdržíte skóre za částečné dokončení, například za povolení vícefaktorového ověřování pro uživatele. Vaše skóre zabezpečení je přímo reprezentativní pro služby zabezpečení společnosti Microsoft, které používáte. Nezapomeňte, že bezpečnost musí být vyvážena použitelností. Všechna bezpečnostní opatření mají dopad na uživatele. Opatření s nízkým dopadem na uživatele by na každodenní práci uživatelů měla mít minimální nebo nulový vliv.

Chcete-li zobrazit historii skóre, přejděte do [centra zabezpečení Microsoft 365](https://security.microsoft.com/) a zkontrolujte celkové skóre zabezpečení společnosti Microsoft. Můžete zkontrolovat změny celkového skóre zabezpečení kliknutím na Historii zobrazení. Zvolením konkrétního data zobrazíte, která opatření byla v ten den zavedena a kolik bodů jste za ně získali.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Jak bezpečnostní skóre identity souvisí s bezpečnostním skóre Office 365?

[Skóre zabezpečení společnosti Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) obsahuje pět různých kategorií ovládacích a srovnávacích bodů:

- Identita
- Data
- Zařízení
- Infrastruktura
- Aplikace

Skóre zabezpečení identity představuje část identity skóre zabezpečení společnosti Microsoft. Toto překrytí znamená, že vaše doporučení pro skóre zabezpečení identity a skóre identity v Microsoftu jsou stejné.

## <a name="next-steps"></a>Další kroky

[Další informace o skóre zabezpečení microsoftu](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
