---
title: Pomocí Azure AD Identity Governance můžete zkontrolovat a odebrat externí uživatele, kteří už nemají přístup k prostředkům.
description: Pomocí kontrol přístupu můžete získat přístup k odebrání přístupu od členů partnerských organizací.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: fe68ec498d17ec20778c8f34fc6ffa1f0964c44e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176956"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Použití zásad správného řízení identity Azure Active Directory (Azure AD) ke kontrole a odebírání externích uživatelů, kteří už nemají přístup k prostředkům

Tento článek popisuje funkce a metody, které vám umožní určit a vybrat externí identity, abyste je mohli zkontrolovat a odebrat z Azure AD, pokud už je nepotřebujete. Cloud usnadňuje spolupráci s interními nebo externími uživateli než kdy dřív. Přechodu Office 365, organizace začnou sledovat šíření externích identit (včetně hostů), protože uživatelé pracují společně na datech, dokumentech nebo digitálních pracovních prostorech, jako jsou týmy. Organizace musí vyvážit, povolit spolupráci a zabezpečení a splnit požadavky na řízení. Součástí tohoto úsilí by měla být vyhodnocení a vyčištění externích uživatelů, kteří byli pozváni ke spolupráci ve vašem tenantovi, kteří pocházejí z partnerských organizací, a odebírat je z Azure AD, když už je nepotřebujete.

>[!NOTE]
>K používání kontrol přístupu Azure AD se vyžaduje platná Azure AD Premium P2, Enterprise Mobility + Security E5 nebo zkušební licence. Další informace najdete v tématu [Azure Active Directory edice](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Proč kontrolovat uživatele z externích organizací ve vašem tenantovi?

Ve většině organizací koncoví uživatelé spouštějí proces pozvání obchodních partnerů a dodavatelů ke spolupráci. Potřeba spolupracovat na organizacích, aby poskytovaly vlastníky prostředků a koncovým uživatelům možnost pravidelného vyhodnocování a ověřování externích uživatelů. Proces připojování nových partnerů pro spolupráci se často plánuje a je k dispozici pro, ale mnoho spolupracuje bez jasného koncového data není vždy zřejmé, pokud uživatel už nepotřebuje přístup. Kromě toho Správa životního cyklu identit, které podniky zachovají, udržují Azure AD Clean a odebírá uživatele, kteří už nepotřebují přístup k prostředkům organizace. Udržování pouze relevantních odkazů na identity pro partnery a dodavatele v adresáři pomáhá snížit riziko zaměstnanců a neúmyslně vybrat a udělit přístup externím uživatelům, kteří by měli být odebráni. Tento dokument vás provede několika možnostmi, které jsou v rozsahu od doporučených proaktivních návrhů až po reaktivní a čisticí aktivity, abyste mohli řídit externí identity.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Udělení a odvolání přístupu pomocí správy nároků

Funkce pro správu nároků umožňují [Automatické životní cyklus externích identit](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) s přístupem k prostředkům. Díky vytváření procesů a postupů pro správu přístupu prostřednictvím správy nároků a publikování prostředků prostřednictvím přístupových balíčků je udržování přehledu o přístupu externích uživatelů k prostředkům mnohem méně komplikovanější problém vyřešit. Když spravujete přístup prostřednictvím [balíčků pro přístup pro správu opravňujících](entitlement-management-overview.md) k přístupu ve službě Azure AD, může vaše organizace centrálně definovat a spravovat přístup pro vaše uživatele i uživatele z partnerských organizací podobně. Správa nároků využívá schválení a přiřazování balíčků přístupu ke sledování, kde si vyžádali externí uživatelé a mají přiřazený přístup. Pokud externí uživatel ztratí všechna jejich přiřazení, Správa nároků může z klienta automaticky odebrat tyto externí uživatele. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Najít hosty Nezvaní prostřednictvím správy nároků

Když mají zaměstnanci autorizaci spolupracovat s externími uživateli, můžou pozvat libovolný počet uživatelů mimo vaši organizaci. Hledání a seskupení externích partnerů do dynamických skupin zarovnaných společností a jejich přezkoumání nemusí být proveditelné, protože může kontrolovat příliš mnoho různých individuálních společností nebo neexistují žádní vlastník nebo sponzor organizace. Microsoft poskytuje vzorový skript PowerShellu, který vám může pomáhat analyzovat použití externích identit v tenantovi. Skript vytvoří výčet externích identit a zařadí je do kategorií. Tento skript vám může pomáhat identifikovat a vyčistit externí identity, které už nemusí být potřeba. V rámci výstupu skriptu podporuje ukázka skriptu automatizované vytváření skupin zabezpečení, které obsahují identifikovaných externích partnerů bez skupin – pro další analýzy a použití s recenzemi přístupu Azure AD.
Skript je k dispozici na [GitHubu](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Po dokončení spuštění skriptu vygeneruje výstupní soubor HTML, který objednává externí identity:

- V tenantovi už není žádné členství ve skupině.
- Přiřazení privilegované role v tenantovi
- Přiřazení aplikace v tenantovi

Výstup obsahuje také jednotlivé domény pro každou z těchto externích identit. 

>[!NOTE]
>Skript odkazovaný výše je ukázkový skript, který kontroluje členství ve skupině, přiřazení rolí a přiřazení aplikací v Azure AD. V aplikacích můžou být jiná přiřazení, která externí uživatelé přijali mimo Azure AD, jako je SharePoint (přímé přiřazení členství) nebo Azure RBAC nebo Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Kontrola prostředků používaných externími identitami

Pokud máte externí identity pomocí prostředků, jako jsou týmy nebo jiné aplikace, které se ještě neřídí správou nároků, můžete chtít pravidelně kontrolovat přístup k těmto prostředkům. Kontroly [přístupu ke](create-access-review.md) službě Azure AD umožňují kontrolovat přístup externích identit tím, že zakazují vlastníkovi prostředku, externím identitám nebo jiným delegovaným osobám, které důvěřujete, jestli chcete pokračovat v přístupu, který vyžaduje. Kontroly přístupu cílí na prostředek a vytvářejí aktivity revize pro každého, kdo má přístup pouze k prostředkům nebo uživatelům typu Host. Kontrolor pak uvidí výsledný seznam uživatelů, které potřebují ke kontrole – buď všichni uživatelé, včetně zaměstnanců vaší organizace, nebo externích identit.

![Kontrola přístupu pomocí skupiny](media/access-reviews-external-users/group-members.png)

Vytvoření jazykové verze kontroly řízené vlastníkem prostředků pomáhá řídit přístup k externím identitám. Vlastníci prostředků, jako je účet pro přístup, dostupnost a zabezpečení informací, které vlastní, jsou ve většině případů nejlepší skupinou pro rozhodování o přístupu ke svým prostředkům a přiblížní se k uživatelům, kteří mají přístup k nim, než je centrální IT nebo sponzor, který spravuje mnoho externích typů.

## <a name="create-access-reviews-for-external-identities"></a>Vytváření kontrol přístupu pro externí identity

Uživatelé, kteří už nemají přístup k žádným prostředkům ve vašem tenantovi, se dají odebrat, pokud už nebudou spolupracovat s vaší organizací. Než zablokujete a odstraníte tyto externí identity, můžete se obrátit na tyto externí uživatele a ujistit se, že jste projekt nezměnili nebo máte stálý přístup, který ale ještě potřebují. Když vytvoříte skupinu, která obsahuje všechny externí identity jako členové, které jste zjistili, že nemají přístup k žádným prostředkům ve vašem tenantovi, můžete použít kontroly přístupu ke všem externím datům, ať už k nim ještě potřebují, nebo jestli mají přístup, nebo bude i nadále potřebovat přístup v budoucnu. V rámci přezkoumání může autor recenze při kontrolách přístupu použít funkci **vyžadovat důvod pro schválení** , aby externím uživatelům poskytl odůvodnění pro pokračování přístupu, pomocí kterého se můžete dozvědět, kde a jak stále potřebují přístup ve vašem tenantovi. Můžete také povolit funkci nastavení **Další obsah pro e-mailovou zprávu kontrolora** , aby uživatelé věděli, že ztratí přístup, pokud neodpoví a že budou pořád potřebovat přístup, vyžaduje se odůvodnění. Pokud chcete pokračovat a nechat kontroly přístupu **zakázat a odstranit** externí identity, měli by nereagovat nebo poskytnout platný důvod pro pokračování přístupu, můžete použít možnost zakázat a odstranit, jak je popsáno v následující části.

![omezení rozsahu kontroly jenom na uživatele typu Host](media/access-reviews-external-users/guest-users-only.png)

Po dokončení kontroly se na stránce **výsledky** zobrazí přehled odpovědi dané každou externí identitou. Můžete zvolit automatické použití výsledků a nechat kontroly přístupu zakázat a odstranit. Případně můžete projít uvedené odpovědi a rozhodnout se, jestli chcete před rozhodnutím odebrat přístup uživatele nebo jeho následné změny a získat další informace. Pokud někteří uživatelé stále mají přístup k prostředkům, které jste ještě nezkontrolovali, můžete tuto kontrolu použít jako součást zjišťování a rozšířit svoji další kontrolu a cyklus ověření identity.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Zakázání a odstranění externích identit pomocí kontrol přístupu Azure AD

Kromě možnosti odebrání nežádoucích externích identit z prostředků, jako jsou skupiny nebo aplikace, můžou kontroly přístupu Azure AD blokovat externí identity přihlášení k vašemu tenantovi a odstranit externí identity z vašeho tenanta po uplynutí 30 dnů. Jakmile vyberete možnost **zablokovat uživatele v přihlášení po dobu 30 dnů, pak uživatele z klienta odeberete**, kontrola zůstane ve stavu "používá se" po dobu 30 dnů. Během tohoto období nebude možné zobrazit ani konfigurovat nastavení, výsledky, kontrolory nebo protokoly auditu v rámci aktuální revize. 

![nastavení po dokončení](media/access-reviews-external-users/upon-completion-settings.png)

Při vytváření nové kontroly přístupu v části nastavení po dokončení pro **akci, která se má použít u odepřených uživatelů** , můžete určit, že **Uživatelé budou přihlášení zablokovat po dobu 30 dnů, a pak z tenanta odebrat uživatele**.

Toto nastavení umožňuje určit, blokovat a odstranit externí identity z vašeho tenanta Azure AD. Externí identity, které se kontrolují a zakázaly pokračování přístupu kontrolorem, se zablokují a odstraní bez ohledu na přístup k prostředkům nebo jejich členství ve skupině. Toto nastavení se nejlépe používá jako poslední krok poté, co ověříte, že už neprobíhá přístup k prostředkům, a můžete ho bezpečně odebrat z vašeho tenanta, nebo pokud se chcete ujistit, že jsou odebrané, bez ohledu na jejich stálý přístup. Funkce zakázat a odstranit nejprve blokuje externího uživatele a odhlašuje jejich schopnost přihlašovat se k vašemu tenantovi a přistupovat k prostředkům. Přístup k prostředkům není v této fázi odvolán a pro případ, že byste chtěli znovu vytvořit instanci externího uživatele, jejich schopnost přihlášení lze překonfigurovat. Po uplynutí žádné další akce se zablokovaná externí identita odstraní z adresáře po 30 dnech a odebere účet i jeho přístup.

## <a name="next-steps"></a>Další kroky

- [Kontroly přístupu – Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Správa nároků – Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
