---
title: Řešení potíží s jednotkami pro správu a nejčastější dotazy – Azure Active Directory | Microsoft Docs
description: Prozkoumejte jednotky pro správu a udělte oprávnění s omezeným rozsahem v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f41dca3b52ce75ba2342506f621cca0618a3bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565882"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Jednotky pro správu Azure AD: řešení potíží a nejčastější dotazy

Pro přesnější administrativní řízení v Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem, který je omezený na jednu nebo víc jednotek pro správu. Ukázkové skripty PowerShellu pro běžné úlohy najdete v tématu [práce s jednotkami pro správu](/powershell/azure/active-directory/working-with-administrative-units).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Proč mi nelze vytvořit jednotku pro správu?**

**A:** Ve službě Azure AD může vytvořit pouze *globální správce* nebo *správce privilegovaných rolí* . Zkontrolujte, jestli se uživatel, který se pokouší vytvořit jednotku pro správu, přiřadí roli správce *globálního správce* nebo *privilegované role* .

**Otázka: Přidal (a) jsem skupinu do jednotky pro správu. Proč se členové skupiny ještě nezobrazuje?**

**A:** Když přidáte skupinu do jednotky pro správu, která nevede k tomu, že se do ní přidávají členové skupiny. Uživatelé musí být přiřazeni přímo do jednotky pro správu.

**Otázka: právě jste přidali (nebo odebrali) člena jednotky pro správu. Proč se člen (nebo stále zobrazuje) na uživatelském rozhraní nezobrazuje?**

**A:** Někdy může přidání nebo odebrání jednoho nebo více členů jednotky pro správu trvat několik minut, než se v podokně **jednotky pro správu** projeví. Alternativně můžete přejít přímo k vlastnostem přidruženého prostředku a zjistit, zda byla akce dokončena. Další informace o uživatelích a skupinách v jednotkách pro správu najdete v tématu [zobrazení seznamu jednotek pro správu pro uživatele](admin-units-add-manage-users.md) a [zobrazení seznamu jednotek pro správu pro skupinu](admin-units-add-manage-groups.md).

**Otázka: jsem delegovaný správce hesel na jednotce pro správu. Proč se mi nepovedlo resetovat heslo konkrétního uživatele?**

**A:** Jako správce jednotky pro správu můžete hesla resetovat pouze pro uživatele, kteří jsou přiřazeni k jednotce pro správu. Ujistěte se, že uživatel, jehož resetování hesla selhává, patří do jednotky pro správu, ke které jste byli přiřazeni. Pokud uživatel patří do stejné jednotky pro správu, ale stále nemůžete resetovat heslo uživatele, ověřte role přiřazené uživateli. 

Aby se zabránilo zvýšení oprávnění, správce s rozsahem jednotky pro správu nemůže resetovat heslo uživatele, který je přiřazen k roli s rozsahem celé organizace.

**Otázka: Proč jsou jednotky pro správu nezbytné? Nemohli jsme použít skupiny zabezpečení jako způsob definování oboru?**

**A:** Skupiny zabezpečení mají existující účel a autorizační model. *Správce uživatele* může například spravovat členství všech skupin zabezpečení v organizaci Azure AD. Role může používat skupiny ke správě přístupu k aplikacím, jako je Salesforce. *Správce uživatele* by neměl být schopný spravovat samotný model delegování, což by vedlo k tomu, že se skupiny zabezpečení rozšířily na podporu scénářů seskupení prostředků. 

Jednotky pro správu, například organizační jednotky ve službě Windows Server Active Directory, mají sloužit jako způsob určení rozsahu správy široké škály objektů adresáře. Samotné skupiny zabezpečení můžou být členy oborů prostředků. Použití skupin zabezpečení k definování sady skupin zabezpečení, které může správce spravovat, může být matoucí.

**Otázka: co znamená přidat skupinu do jednotky pro správu?**

**A:** Přidání skupiny do jednotky pro správu přinese skupinu do rozsahu správy libovolného *správce uživatele* , který je také vymezen pro tuto jednotku správy. Správci uživatelů jednotky pro správu mohou spravovat název a členství samotné skupiny. Neuděluje oprávnění *správce uživatele* ke správě uživatelů skupiny (například k resetování hesel). Chcete-li *uživateli* udělit možnost spravovat uživatele, musí být uživatelé přímo členy jednotky pro správu.

**Otázka: může být prostředek (uživatel nebo skupina) členem více než jedné jednotky pro správu?**

**A:** Ano, prostředek může být členem více než jedné jednotky pro správu. Prostředek může být spravovaný všemi správci v rámci celé organizace a administrativními jednotkami, kteří mají oprávnění k prostředku.

**Otázka: jsou v B2C organizacích dostupné jednotky pro správu?**

**A:** Ne, administrativní jednotky nejsou pro organizace B2C k dispozici.

**Otázka: jsou podporované vložené jednotky pro správu?**

**A:** Ne, vnořené jednotky pro správu se nepodporují.

**Otázka: jsou jednotky pro správu podporované v PowerShellu a v Graph API?**

**Odpověď:** Ano. Najdete podporu pro jednotky pro správu v [dokumentaci k rutinám prostředí PowerShell](/powershell/module/Azuread/) a v [ukázkových skriptech](/powershell/azure/active-directory/working-with-administrative-units).

V Microsoft Graph najdete podporu pro [typ prostředku administrativeUnit](/graph/api/resources/administrativeunit) .

## <a name="next-steps"></a>Další kroky

- [Omezení rozsahu pro role pomocí jednotek pro správu](administrative-units.md)
- [Spravovat jednotky pro správu](admin-units-manage.md)
