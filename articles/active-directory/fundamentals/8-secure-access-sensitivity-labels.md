---
title: Řízení externího přístupu k prostředkům v Azure Active Directory s popisky citlivosti.
description: Používejte popisky citlivosti jako součást celkového plánu zabezpečení pro externí přístup.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc8ceddce4d41244d72632db058aa58c0d919db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565168"
---
# <a name="control-access-with-sensitivity-labels"></a>Řízení přístupu pomocí popisků citlivosti 

[Popisky citlivosti](/microsoft-365/compliance/sensitivity-labels) vám pomůžou řídit přístup k vašemu obsahu v aplikacích Office 365 a v kontejnerech, jako jsou Microsoft teams, Microsoft 365 skupiny a sharepointové weby. Můžou chránit svůj obsah, aniž by bránili spolupráci a produkčním schopnostem vašich uživatelů. Popisky citlivosti vám umožňují posílat obsah vaší organizace napříč zařízeními, aplikacemi a službami a přitom chránit vaše data a plnit zásady dodržování předpisů a zabezpečení. 

S popisky citlivosti můžete:

* **Klasifikujte obsah bez přidání nastavení ochrany**. K obsahu můžete přiřadit klasifikaci (například nálepku), která bude dál a s vaším obsahem přetrvávat, jak se používá a sdílí. Tato klasifikace umožňuje generovat sestavy využití a pozorovat aktivitu vašeho citlivého obsahu.

* **Vynutili nastavení ochrany, jako je šifrování, vodoznaky a omezení přístupu**. Uživatelé můžou například použít popisek důvěrného dokumentu nebo e-mailu a tento popisek může [Šifrovat obsah](/microsoft-365/compliance/encryption-sensitivity-labels) a přidat vodoznak "důvěrné". Kromě toho můžete [použít popisek citlivosti na kontejner](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) , jako je například web služby SharePoint, a vymáhat, jestli externí uživatelé mají přístup k obsahu, který obsahuje.

Popisky citlivosti na e-mail a další obsah cestují s obsahem. Popisky citlivosti na kontejnerech mohou omezit přístup ke kontejneru, ale obsah kontejneru nedědí popisek. Uživatel může například převzít obsah z chráněného webu, stáhnout ho a pak ho sdílet bez omezení, pokud obsah také nemá popisek citlivosti.

 >[!NOTE]
>Pro použití popisků citlivosti musí být uživatelé přihlášení ke svému pracovnímu nebo školnímu účtu Microsoftu. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Oprávnění potřebná k vytváření a správě úrovní citlivosti

Členové týmu dodržování předpisů, kteří budou vytvářet popisky citlivosti, potřebují oprávnění pro Microsoft 365 centrum dodržování předpisů, Microsoft 365 Security Center nebo centrum dodržování předpisů & zabezpečení.

Ve výchozím nastavení mají globální správci pro vašeho tenanta přístup k těmto centrům pro správu a můžou poskytovat správcům dodržování předpisů a přístup k ostatním lidem, aniž by jim poskytovali všechna oprávnění správce tenanta. Pro tohoto delegovaného přístup správce přidejte uživatele do skupiny role správce dat dodržování předpisů, správce dodržování předpisů nebo správce zabezpečení.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Určení strategie pro popisky citlivosti

Jak si myslíte o řízení přístupu k obsahu pro externí přístup, určete následující:

**Pro veškerý obsah a kontejnery**

* Jak budete definovat, co je vysoký, střední nebo malý dopad na firmu (HBI, MBI, LBI)? Pokud jsou konkrétní typy obsahu nevhodně sdíleny, vezměte v úvahu dopad na vaši organizaci.

   * Obsah s konkrétními typy nesouvisejícího [citlivého obsahu](/microsoft-365/compliance/apply-sensitivity-label-automatically), například kreditními kartami nebo čísly Passport

   * Obsah vytvořený konkrétními skupinami nebo lidmi (například vedoucí na dodržování předpisů, finanční důstojníci nebo vedení)

   * Obsah v určitých knihovnách nebo lokalitách. Například kontejnery hostující organizační strategii nebo soukromá finanční data

   * Další kritéria

* Jaké kategorie obsahu (například obsah HBI) by měly být omezené pro přístup externích uživatelů?

   * Omezení můžou zahrnovat akce, jako je omezení přístupu k kontejnerům a šifrování obsahu.

* Jaké výchozí hodnoty má HBI data, weby nebo skupiny Microsoft 365?

* Kde budete používat popisky citlivosti k [označování a monitorování](/microsoft-365/compliance/sensitivity-labels), k [vymáhání šifrování](/microsoft-365/compliance/encryption-sensitivity-labels) nebo k [vymáhání omezení přístupu k kontejneru](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)?

**Pro e-mail a obsah**

* Chcete [automaticky použít popisky citlivosti](/microsoft-365/compliance/apply-sensitivity-label-automatically) k obsahu, nebo to udělat ručně?

   * Pokud se rozhodnete tak učinit ručně, chcete [doporučit, aby uživatelé použili popisek](/microsoft-365/compliance/apply-sensitivity-label-automatically)?

**Pro kontejnery**

* Jaká kritéria určí, jestli mají M365 skupiny, týmy nebo weby služby SharePoint vyžadovat přístup k omezení pomocí popisků citlivosti?

* Chcete v těchto kontejnerech přesunout jenom obsah popisku, nebo chcete [automaticky označovat](/microsoft-365/compliance/apply-sensitivity-label-automatically) existující soubory na SharePointu a OneDrivu?

V těchto [běžných scénářích najdete popisky citlivosti](/microsoft-365/compliance/get-started-with-sensitivity-labels) pro další nápady, jak můžete použít popisky citlivosti.

### <a name="sensitivity-labels-on-email-and-content"></a>Popisky citlivosti v e-mailu a obsahu

Když přiřadíte označení citlivosti dokumentu nebo e-mailu, bude to jako razítko, které se používá pro obsah, který je přizpůsobitelný, nešifrovaný text a trvalý. 

* **Přizpůsobitelné** znamená, že můžete vytvořit štítky vhodné pro vaši organizaci a zjistit, co se stane, když budou aplikovány.

* **Nešifrovaný text** znamená, že se jedná o součást metadat položky a je čitelná pro aplikace a služby, aby mohly uplatňovat své vlastní ochranné akce.

* **Persistent** znamená, že popisek a veškerá přidružená ochrana v roamingu s obsahem a slouží jako základ pro použití a vynucování zásad.

 

> [!NOTE]
> U každé položky obsahu může být použit jeden popisek s citlivostí.


### <a name="sensitivity-labels-on-containers"></a>Popisky citlivosti na kontejnerech

Můžete použít popisky citlivosti na kontejnerech, jako jsou [Microsoft 365 skupiny](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)a [sharepointové weby](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites). Když použijete tento popisek citlivosti na podporovaný kontejner, popisek automaticky použije nastavení klasifikace a ochrany pro připojenou lokalitu nebo skupinu. Popisky citlivosti na těchto kontejnerech mohou řídit následující aspekty kontejnerů:

* **Ochrana osobních údajů**. Můžete zvolit, kdo může zobrazit web: konkrétní uživatele, všechny interní uživatele nebo kdokoli.

* **Přístup externích uživatelů** Určuje, jestli vlastník skupiny může do skupiny přidat hosty.

* **Přístup z nespravovaných zařízení**. Určuje, jak a jak mají nespravovaná zařízení přístup k obsahu.

 

![Snímek obrazovky s úpravami popisků citlivosti](media/secure-external-access/8-edit-label.png)

 

Když použijete popisek citlivosti na kontejner, jako je například web služby SharePoint, nepoužije se pro obsah: popisky citlivosti v kontejnerech řídí přístup k obsahu v rámci kontejneru. 

* Pokud chcete automaticky použít popisky pro obsah v rámci kontejneru, přečtěte si téma [použití citlivosti na obsah automaticky](/microsoft-365/compliance/apply-sensitivity-label-automatically).

* Pokud chcete, aby uživatelé mohli ručně použít popisky na tento obsah, ujistěte se, že jste [povolili popisky citlivosti pro soubory Office v SharePointu a OneDrivu](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files).

### <a name="plan-to-implement-sensitivity-labels"></a>Plánování implementace popisků citlivosti

Jakmile určíte, jak chcete používat popisky citlivosti a na jaký obsah a weby chcete použít, přečtěte si následující dokumentaci, která vám pomůžete provést vaši implementaci.

1. [Začínáme s popisky citlivosti](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Vytvoření strategie nasazení](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Vytváření a publikování popisků citlivosti](/microsoft-365/compliance/create-sensitivity-labels)

4. [Omezení přístupu k obsahu pomocí popisků citlivosti pro použití šifrování](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Použití popisků citlivosti s týmy, skupinami a weby](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [Povolit popisky citlivosti pro soubory Office v SharePointu a OneDrivu](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení požadovaného stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md) (jste tady.)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)