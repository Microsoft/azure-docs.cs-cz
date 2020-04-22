---
title: Oddělení pro správu a nejčastější dotazy – Azure Active Directory | Dokumenty společnosti Microsoft
description: Prozkoumejte jednotky pro správu a udělte oprávnění s omezeným rozsahem ve službě Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684857"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Administrativní jednotky Azure AD: Řešení potíží a nejčastější dotazy

Pro podrobnější řízení správy ve službě Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem, který je omezený na jednu nebo více jednotek pro správu (AU). Ukázkové skripty prostředí PowerShell pro běžné úkoly naleznete v [tématu Práce s jednotkami pro správu](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč nelze vytvořit administrativní jednotku?**

**A:** Pouze *globální správce* nebo *správce privilegovaných rolí* můžete vytvořit jednotku pro správu ve službě Azure AD. Zkontrolujte, zda uživateli, který se pokouší vytvořit jednotku pro správu, přiřazena role *Globální správce* nebo *Správce privilegovaných rolí.*

**Otázka: Do správní jednotky jsem přidal skupinu. Proč se tam členové skupiny pořád neukazují?**

**A:** Přidáte-li skupinu do administrativní jednotky, nebude to mít za následek přidání všech členů skupiny. Uživatelé musí být přímo přiřazeni k administrativní jednotce.

**Otázka: Právě jsem přidal (nebo odstranil) člena správní jednotky. Proč se člen nezobrazuje (nebo se stále zobrazuje) v uživatelském rozhraní?**

**A:** V některých případě může zpracování přidání nebo odebrání jednoho nebo více členů správní jednotky trvat několik minut, než se projeví na stránce **Administrativní jednotky.** Případně můžete přejít přímo do vlastností přidruženého prostředku a zjistit, zda byla akce dokončena. Další informace o uživatelích a skupinách v jednotkách řízení uživatelů naleznete v [tématu Seznam jednotek správy pro uživatele](roles-admin-units-add-manage-users.md) a [Seznam jednotek pro správu skupiny](roles-admin-units-add-manage-groups.md).

**Otázka: Jsem delegovaný správce hesel na administrativní jednotce. Proč nelze obnovit heslo konkrétního uživatele?**

**A:** Jako správce administrativní jednotky můžete resetovat hesla pouze pro uživatele, kteří jsou přiřazeni k vaší administrativní jednotce. Ujistěte se, že uživatel, jehož resetování hesla selhává, patří do administrativní jednotky, ke které jste byli přiřazeni. Pokud uživatel patří do stejné jednotky pro správu, ale stále nelze obnovit jeho heslo, zkontrolujte role, které jsou přiřazeny k uživateli. 

Chcete-li zabránit zvýšení oprávnění, správce jednotky s rozsahem správce nemůže obnovit heslo uživatele, který je přiřazen k roli s rozsahem celé organizace.

**Otázka: Proč jsou administrativní jednotky nezbytné? Nemohli jsme použít skupiny zabezpečení jako způsob, jak definovat obor?**

**A:** Skupiny zabezpečení mají existující účel a autorizační model. *Správce uživatelů*, například můžete spravovat členství ve všech skupinách zabezpečení v organizaci Azure AD. Role může používat skupiny ke správě přístupu k aplikacím, jako je Salesforce. *Správce uživatele* by neměl být schopen spravovat samotný model delegování, což by bylo výsledkem, pokud by byly skupiny zabezpečení rozšířeny na podporu scénářů seskupení prostředků. Jednotky pro správu, například organizační jednotky ve službě Windows Server Active Directory, jsou určeny k zajištění správy rozsahu správy široké škály objektů adresáře. Samotné skupiny zabezpečení mohou být členy oborů prostředků. Použití skupin zabezpečení k definování sady skupin zabezpečení, které může správce spravovat, může být matoucí.

**Otázka: Co to znamená přidat skupinu do správní jednotky?**

**A:** Přidání skupiny do administrativní jednotky přenese samotnou skupinu do oboru správy libovolného *správce uživatelů,* který je také vymezen do této správní jednotky. Uživatelé pro administrativní jednotku mohou spravovat název a členství v samotné skupině. Neuděluje *správci oprávnění správce* ke správě uživatelů skupiny (například k resetování hesel). Aby měl správce *uživatelů* možnost spravovat uživatele, musí být přímými členy správní jednotky.

**Otázka: Může být prostředek (uživatel nebo skupina) členem více než jedné správní jednotky?**

**A:** Ano, prostředek může být členem více než jedné správní jednotky. Prostředek mohou spravovat všichni správci s rozsahem organizační a správní jednotky, kteří mají oprávnění k prostředku.

**Otázka: Jsou administrativní jednotky k dispozici v organizacích B2C?**

**A:** Ne, administrativní jednotky nejsou k dispozici pro organizace B2C.

**Otázka: Jsou podporovány vnořené jednotky pro správu?**

**A:** Ne, vnořené jednotky pro správu nejsou podporovány.

**Otázka: Jsou jednotky pro správu podporované v Prostředí PowerShell a rozhraní API grafu?**

**Odpověď:** Ano. Podporu pro administrativní jednotky najdete v [dokumentaci rutiny prostředí PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) a [ukázkových skriptech](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview). 

Najděte podporu pro [typ prostředku administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) v microsoft graphu.

## <a name="next-steps"></a>Další kroky

- [Omezení oboru pro role pomocí jednotek pro správu](directory-administrative-units.md)
- [Správa administrativních jednotek](roles-admin-units-manage.md)
