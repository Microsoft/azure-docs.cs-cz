---
title: Poradce při potížích s jednotkami pro správu a nejčastější dotazy – Azure Active Directory | Dokumenty společnosti Microsoft
description: Prozkoumejte jednotky pro správu pro delegování oprávnění s omezeným rozsahem ve službě Azure Active Directory
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
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428144"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Řešení potíží a nejčastější dotazy pro administrativní jednotky ve službě Azure Active Directory

Pro podrobnější řízení správy ve službě Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem omezeným na jednu nebo více jednotek pro správu (AU). Ukázkové skripty prostředí PowerShell pro https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0běžné úkoly najdete na adrese .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Nejsem schopen vytvořit administrativní jednotku**

**A:** Pouze globální správce nebo správce privilegovaných rolí můžete vytvořit jednotku pro správu ve službě Azure AD. Zkontrolujte, zda je uživateli, který se pokouší vytvořit jednotku pro správu, přiřazena role Správce globálního správce nebo Správce privilegovaných rolí.

**Otázka: Přidal jsem skupinu do správní jednotky, ale členové skupiny se stále nezobrazují ve správní jednotce**

**A:** Přidáte-li skupinu do administrativní jednotky, nebude to mít za následek přidání všech členů skupiny do správní jednotky. Uživatelé musí být přímo přiřazeni k administrativní jednotce.

**Otázka: Právě jsem přidal / odstranil člena správní jednotky a stále se objevuje v ui**

**A:** Někdy zpracování přidání nebo odebrání jednoho nebo více členů správní jednotky může trvat několik minut, než se projeví na stránce **Administrativní jednotky.** Můžete se rozhodnout počkat několik minut, než se projeví pod administrativními jednotkami. Případně můžete přejít přímo do vlastností přidruženého prostředku a zjistit, zda byla akce dokončena. Další informace o uživatelích a skupinách v jednotkách řízení uživatelů naleznete v [tématu Seznam jednotek správy pro uživatele](roles-admin-units-add-manage-users.md) a [Seznam jednotek pro správu skupiny](roles-admin-units-add-manage-groups.md).

**Otázka: Jako delegovaný správce hesel na administrativní jednotce nemohu obnovit heslo konkrétního uživatele**

**A:** Správce přiřazený přes administrativní jednotku můžete obnovit heslo pouze pro uživatele přiřazené k vaší administrativní jednotce. Ujistěte se, že uživatel, pro kterého resetování hesla selhává, patří k jednotkám pro správu, kterým byla role přiřazena. Pokud uživatel patří do stejné jednotky pro správu a stále nelze obnovit heslo uživatele, zkontrolujte role, které jsou přiřazeny uživateli. Chcete-li zabránit zvýšení oprávnění, správce s rozsahem správce jednotky nemůže obnovit heslo uživatele, který je přiřazen k roli s rozsahem celé organizace.

**Otázka: Proč jsou administrativní jednotky nezbytné? Nemohli jsme použít skupiny zabezpečení jako způsob, jak definovat obor?**

**A:** Skupiny zabezpečení mají existující účel a autorizační model. Správce uživatele může například spravovat členství ve všech skupinách zabezpečení v organizaci Azure AD, například ke správě skupin ke správě přístupu k aplikacím, jako je Salesforce. Správce uživatele by neměl mít možnost spravovat samotný model delegování, což by bylo výsledkem, pokud by byly skupiny zabezpečení rozšířeny na podporu scénářů seskupení prostředků. Jednotky pro správu, například organizační jednotky ve službě Active Directory systému Windows Server, jsou určeny k zajištění správy rozsahu správy široké škály objektů adresáře. Samotné skupiny zabezpečení mohou být členy oborů prostředků. Použití skupin zabezpečení k definování sady skupin zabezpečení, které může správce spravovat, může být matoucí.

**Otázka: Co to znamená přidat skupinu do správní jednotky?**

**A:** Přidání skupiny do administrativní jednotky přenese samotnou skupinu do oboru správy libovolného správce uživatele, který je také vymezen do této jednotky správce. Uživateli správci pro administrativní jednotku můžete spravovat název a členství ve skupině samotné. Neuděluje správci uživatele pro administrativní jednotku žádné oprávnění ke správě uživatelů skupiny (například resetovat hesla). Aby měl správce uživatelů možnost spravovat uživatele, musí být přímými členy správní jednotky.

**Otázka: Může být prostředek (uživatel nebo skupina) členem více než jedné správní jednotky?**

**A:** Ano, prostředek může být členem více než jedné správní jednotky. Prostředek mohou spravovat všichni správci s rozsahem organizační a správní jednotky, kteří mají oprávnění k prostředku.

**Otázka: Jsou administrativní jednotky k dispozici v organizacích B2C?**

**A:** Ne, administrativní jednotky nejsou k dispozici pro organizace B2C.

**Otázka: Jsou podporovány vnořené jednotky pro správu?**

**A:** Vnořené jednotky pro správu nejsou podporovány.

**Otázka: Jsou jednotky pro správu podporované v rozhraní PowerShell a Graph API?**

**Odpověď:** Ano. Podpora pro jednotky pro správu existuje v [dokumentaci rutiny prostředí PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) a [ukázkové skripty](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)a podpora je v microsoft graphu pro [administrativeUnit typ prostředku](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit).

## <a name="next-steps"></a>Další kroky

- [Administrativní jednotky pro omezení oboru pro přehled rolí](directory-administrative-units.md)
- [Správa administrativních jednotek](roles-admin-units-manage.md)