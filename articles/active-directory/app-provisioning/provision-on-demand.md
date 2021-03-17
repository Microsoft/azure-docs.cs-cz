---
title: Zřízení uživatele na vyžádání pomocí Azure Active Directory
description: Vynutit synchronizaci
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: be03a149f34c16621905081a2f9bb663d85bc53c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255657"
---
# <a name="on-demand-provisioning"></a>Zřizování na vyžádání
Pomocí zřizování na vyžádání můžete zřídit uživatele v aplikaci během několika sekund. Mimo jiné můžete tuto možnost využít k těmto účelům:

* Rychlé řešení potíží s konfigurací
* Ověřte výrazy, které jste definovali.
* Otestuje filtry rozsahu.

## <a name="how-to-use-on-demand-provisioning"></a>Jak používat zřizování na vyžádání

1. Přihlaste se na **Azure Portal**.
1. Přejít na **všechny služby** v  >  **podnikových aplikacích**.
1. Vyberte svou aplikaci a pak klikněte na stránku konfigurace zřizování.
1. Nakonfigurujte zřizování poskytnutím přihlašovacích údajů správce.
1. Vyberte **zřídit na vyžádání**.
1. Vyhledat uživatele podle křestního jména, příjmení, zobrazovaného jména, hlavního názvu uživatele nebo e-mailové adresy.
   > [!NOTE]
   > V případě aplikace pro zřizování cloudového HR (Workday/SuccessFactors k AD/Azure AD) je vstupní hodnota odlišná. V případě scénáře Workday zadejte v Workday uživatele do pole "WID". V případě scénáře SuccessFactors zadejte do SuccessFactors "personIdExternal" uživatele. 
 
1. V dolní části stránky vyberte **zřídit** .

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Snímek obrazovky, který zobrazuje uživatelské rozhraní Azure Portal pro zřizování uživatele na vyžádání.":::

## <a name="understand-the-provisioning-steps"></a>Pochopení kroků zřizování

Proces zřizování na vyžádání se pokusí zobrazit kroky, které služba zřizování potřebuje při zřizování uživatele. Pro zřízení uživatele je obvykle k dispozici pět kroků. Jeden nebo více kroků, které jsou vysvětleny v následujících částech, se zobrazí během zřizování na vyžádání.

### <a name="step-1-test-connection"></a>Krok 1: testování připojení

Služba zřizování se pokusí o autorizaci přístupu k cílové aplikaci tím, že vytvoří žádost o "testovacího uživatele". Služba zřizování očekává odpověď, která indikuje, že služba s oprávněním pro pokračování kroků zřizování. Tento krok je zobrazen pouze v případě, že se nezdařil. Po úspěšném provedení tohoto kroku se tento krok nezobrazuje během zřizování na vyžádání.

#### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

* Ujistěte se, že jste do cílové aplikace zadali platné přihlašovací údaje, jako je například tajný token a adresa URL tenanta. Požadovaná pověření se liší podle aplikace. Podrobné kurzy konfigurace najdete v [seznamu kurzů](../saas-apps/tutorial-list.md). 
* Ujistěte se, že cílová aplikace podporuje filtrování na odpovídající atributy definované v podokně **mapování atributů** . Je možné, že budete muset v dokumentaci k rozhraní API poskytnutým vývojářem aplikace zjistit podporované filtry.
* V případě aplikací pro správu identit mezi doménami (SCIM) můžete použít nástroj, jako je například post. Tyto nástroje vám pomůžou zajistit, že aplikace reaguje na požadavky na autorizaci způsobem, který služba zřizování služby Azure Active Directory (Azure AD) očekává. Podívejte se na [příklad žádosti](./use-scim-to-provision-users-and-groups.md#request-3).

### <a name="step-2-import-user"></a>Krok 2: import uživatele

V dalším kroku služba zřizování načte uživatele ze zdrojového systému. Atributy uživatele, které služba načítá, se použijí později:

* Vyhodnoťte, jestli je uživatel v oboru pro zřizování.
* Ověřte cílový systém pro stávajícího uživatele.
* Určení uživatelských atributů pro export do cílového systému.

#### <a name="view-details"></a>Zobrazit podrobnosti


V části **zobrazení podrobností** se zobrazují vlastnosti uživatele importované ze zdrojového systému (například Azure AD).

#### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

* Import uživatele může selhat, pokud v objektu uživatele ve zdrojovém systému chybí atribut pro hledání. Chcete-li tuto chybu vyřešit, vyzkoušejte jeden z těchto přístupů:

  * Aktualizujte objekt uživatele hodnotou pro atribut pro porovnání.
  * Změňte atribut Matching v konfiguraci zřizování.

* Pokud v importovaném seznamu chybí atribut, který jste očekávali, ujistěte se, že atribut má hodnotu objektu uživatele ve zdrojovém systému. Služba zřizování aktuálně nepodporuje zřizování atributů s hodnotou null.
* Ujistěte se, že stránka **mapování atributů** vaší konfigurace zřizování obsahuje atribut, který očekáváte.

### <a name="step-3-determine-if-user-is-in-scope"></a>Krok 3: určení, jestli je uživatel v oboru

Dále služba zřizování určí, jestli je uživatel v [oboru](./how-provisioning-works.md#scoping) pro zřizování. Služba zohledňuje následující aspekty:

* Určuje, zda je uživatel přiřazen k aplikaci.
* Určuje, zda je obor nastaven na možnost **synchronizace byla přiřazena** nebo **synchronizována**.
* Filtry oborů definované v konfiguraci zřizování.  

#### <a name="view-details"></a>Zobrazit podrobnosti

V části **zobrazení podrobností** se zobrazují podmínky oboru, které byly vyhodnoceny. Může se zobrazit jedna nebo více následujících vlastností:

* **Aktivní ve zdrojovém systému** znamená, že uživatel má vlastnost `IsActive` nastavenou na **hodnotu true** ve službě Azure AD.
* **Přiřazeno k aplikaci** indikuje, že je uživatel přiřazený k aplikaci ve službě Azure AD.
* **Obor synchronizace vše** označuje, že nastavení oboru umožňuje všem uživatelům a skupinám v tenantovi.
* **Uživatel má požadovanou roli** , která indikuje, že uživatel má v aplikaci potřebné role, které se mají zřídit. 
* Pokud jste definovali filtry oborů pro vaši aplikaci, zobrazí se také **filtry oborů** . Filtr se zobrazí v následujícím formátu: {obor filtru název} {atribut oboru filtru} {obor filtru Hodnota} {Rozsah filtru Value}.

#### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

* Ujistěte se, že jste definovali platnou roli oboru. Nepoužívejte například [operátor Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) s hodnotou, která není typu Integer.
* Pokud uživatel nemá nezbytnou roli, přečtěte si [tipy pro zřizování uživatelů přiřazených k výchozí roli přístupu](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>Krok 4: vyhledání uživatele mezi zdrojem a cílem

V tomto kroku se služba pokusí porovnat uživatele, který byl načten v kroku importu, s uživatelem v cílovém systému.

#### <a name="view-details"></a>Zobrazit podrobnosti

Stránka **Zobrazit podrobnosti** zobrazuje vlastnosti uživatelů, které byly spárovány v cílovém systému. Vlastnosti, které vidíte v podokně v kontextu, se liší následujícím způsobem:

* Pokud v cílovém systému neodpovídají žádní uživatelé, nezobrazí se žádné vlastnosti.
* Pokud v cílovém systému odpovídá jeden uživatel, zobrazí se vlastnosti odpovídajícího uživatele z cílového systému.
* Pokud se shodují více uživatelů, zobrazí se vlastnosti obou odpovídajících uživatelů.
* Pokud je více odpovídajících atributů součástí mapování atributů, každý odpovídající atribut je vyhodnocen postupně a odpovídajícími uživateli pro daný atribut jsou zobrazeny.

#### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

* Služba zřizování nemusí být schopná najít uživatele ve zdrojovém systému jedinečně s uživatelem v cíli. Tento problém vyřešíte tak, že zaručíte, že odpovídající atribut je jedinečný.
* Ujistěte se, že cílová aplikace podporuje filtrování u atributu, který je definován jako odpovídající atribut.  

### <a name="step-5-perform-action"></a>Krok 5: provedení akce

Služba zřizování pak provede akci, jako je například vytvoření, aktualizace, odstranění nebo přeskočení uživatele.

Tady je příklad toho, co se může zobrazit po úspěšném zřízení uživatele na vyžádání:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Snímek obrazovky, který ukazuje úspěšné zřízení uživatele na vyžádání.":::

#### <a name="view-details"></a>Zobrazit podrobnosti

V části **Zobrazit podrobnosti** se zobrazí atributy, které byly v cílové aplikaci změněny. Toto zobrazení představuje konečný výstup aktivity zřizování služby a exportovaných atributů. Pokud se tento krok nezdaří, zobrazené atributy reprezentují atributy, které služba zřizování provedla při pokusu o změnu.

#### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

* Selhání při exportu změn se může značně lišit. V [dokumentaci k protokolům zřizování](../reports-monitoring/concept-provisioning-logs.md#error-codes) najdete běžné chyby.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Potřebujete zapnout zřizování, abyste mohli používat zřizování na vyžádání?** Pro aplikace, které používají dlouhý nosný token nebo uživatelské jméno a heslo k autorizaci, nejsou nutné žádné další kroky. Aplikace, které používají OAuth pro autorizaci: aktuálně vyžadují, aby se úloha zřizování zastavila před použitím zřizování na vyžádání. Do této kategorie patří aplikace, jako je například G Suite, box, pracoviště po Facebooku a časová rezerva. V práci probíhá podpora zřizování na vyžádání pro všechny aplikace, aniž byste museli zastavovat úlohy zřizování.

* **Jak dlouho trvá zřizování na vyžádání?** Zřizování na vyžádání obvykle trvá méně než 30 sekund.

## <a name="known-limitations"></a>Známá omezení

Pro zřizování na vyžádání je aktuálně k dispozici několik známých omezení. Vystavte své [návrhy a zpětnou vazbu](https://aka.ms/appprovisioningfeaturerequest) , abychom mohli lépe určit, která vylepšení se mají udělat dál.

> [!NOTE]
> Následující omezení jsou specifická pro možnost zřizování na vyžádání. Informace o tom, jestli aplikace podporuje zřizování skupin, odstranění nebo dalších možností, najdete v kurzu této aplikace.

* Aplikace Amazon Web Services (AWS) nepodporuje zřizování na vyžádání. 
* Zřizování skupin a rolí na vyžádání se nepodporuje.
* Zřizování na vyžádání podporuje zakázání uživatelů, kteří byli z aplikace nepřiřazeni. Nepodporuje ale zakázání nebo odstranění uživatelů, kteří jsou zakázané nebo odstraněné ze služby Azure AD. Uživatelé se nebudou při hledání uživatele zobrazovat.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s zřizováním](./application-provisioning-config-problem.md)