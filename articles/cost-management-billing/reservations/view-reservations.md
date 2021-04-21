---
title: Oprávnění k zobrazení a správě rezervací Azure
description: Naučte se zobrazovat a spravovat rezervace Azure v Azure Portal.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780456"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Oprávnění k zobrazení a správě rezervací Azure

V tomto článku se dozvíte, jak fungují oprávnění rezervace a jak můžou uživatelé zobrazit a spravovat rezervace Azure v Azure Portal.

## <a name="who-can-manage-a-reservation-by-default"></a>Kdo může ve výchozím nastavení spravovat rezervaci

Ve výchozím nastavení mohou rezervace zobrazovat a spravovat následující uživatelé:

- Do objednávky rezervace se přidá osoba, která rezervaci koupí, a správce účtu pro fakturační předplatné použité k nákupu rezervace.
- Správci fakturace pro smlouvy Enterprise a Smlouvy se zákazníkem Microsoftu
- Uživatelé se zvýšeným oprávněním ke správě všech předplatných Azure a skupin pro správu

Životní cyklus rezervace nezávisí na předplatném Azure, takže rezervace není prostředkem v rámci předplatného Azure. Místo toho se jedná o prostředek na úrovni tenanta s vlastními oprávněními Azure RBAC oddělenými od předplatných. Rezervace nedědí oprávnění z předplatných po nákupu.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Jak můžou správci fakturace zobrazit nebo spravovat rezervace

Pokud jste správcem fakturace, použijte následující postup k zobrazení a správě všech rezervací a transakcí rezervace.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte na **cost management + fakturace**.
    - Pokud jste správce služby EA, vyberte v levé nabídce položku **obory fakturace** a potom v seznamu rozsahů fakturace vyberte jednu.
    - Pokud jste vlastníkem fakturačního profilu, který je zákazníkem Microsoftu, vyberte v levé nabídce možnost **profily fakturace**. V seznamu profilů fakturace vyberte jednu.
1. V nabídce vlevo vyberte rezervace **produkty + služby**  >  .
1. Zobrazí se úplný seznam rezervací pro registraci EA nebo profil pro fakturaci.
1. Správci fakturace můžou převzít vlastnictví rezervace tím, že je vyberete a pak v zobrazeném okně vyberete **udělit přístup** .

### <a name="how-to-add-billing-administrators"></a>Postup přidání správců fakturace

Přidání uživatele jako správce fakturace pro smlouvu Enterprise nebo Smlouvu se zákazníkem Microsoftu:

- V případě smlouvy Enterprise přidejte uživatele s rolí _Podnikový správce_, kteří mohou zobrazovat a spravovat všechny objednávky rezervací související se smlouvou Enterprise. Podnikoví správci mohou zobrazovat a spravovat rezervace v **cost management a fakturaci**.
    - Uživatelé s rolí _Enterprise Administrators (jen pro čtení)_ můžou zobrazit jenom rezervaci z **cost management + fakturace**. 
    - Správci oddělení a vlastníci účtů mohou rezervace zobrazovat _jenom v případě_, že je k nim explicitně přidáte pomocí řízení přístupu (IAM). Další informace najdete v článku [Správa rolí Azure Enterprise](../manage/understand-ea-roles.md).
- V případě Smlouvy se zákazníkem Microsoftu mohou všechny nákupy rezervací realizované prostřednictvím konkrétního fakturačního profilu spravovat uživatelé s rolí vlastníka nebo přispěvatele tohoto fakturačního profilu. Čtenáři fakturačního profilu a správci faktur mohou zobrazovat všechny rezervace, které se pro daný fakturační profil platí. Nemohou ale v rezervacích dělat změny.
    Podrobnosti najdete v části [Role a úlohy související s fakturačním profilem](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Zobrazení rezervací s přístupem k Azure RBAC

Pokud jste si zakoupili rezervaci nebo jste přidaní k rezervaci, použijte následující postup k zobrazení a správě rezervací.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Výběrem **Všechny služby** > **Rezervace** zobrazte seznam rezervací, ke kterým máte přístup.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Uživatelé se zvýšeným přístupem můžou spravovat všechna předplatná Azure a skupiny pro správu.

Můžete zvýšit [přístup uživatele ke správě všech předplatných Azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json).

Po získání vyšší úrovně přístupu:

1. Přejděte na **všechny**  >  **rezervace** služeb a zobrazte všechny rezervace, které jsou v tenantovi.
1. Pokud chcete provést úpravy rezervace, přidejte sami sebe jako vlastníka objednávky rezervace pomocí řízení přístupu (IAM).

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Poskytněte uživatelům přístup k jednotlivým rezervacím Azure RBAC.

Uživatelé, kteří mají oprávnění vlastníka na rezervacích a správcích fakturace, mohou delegovat správu přístupu pro jednotlivé objednávky rezervace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Výběrem **Všechny služby** > **Rezervace** zobrazte seznam rezervací, ke kterým máte přístup.
1. Vyberte rezervaci, ke které chcete delegovat přístup jiným uživatelům.
1. V části Podrobnosti rezervace vyberte objednávku požadované rezervace.
1. Vyberte **Řízení přístupu (IAM)** .
1. Vyberte **Přidat přiřazení role** > **Role** > **Vlastník**. Pokud chcete udělit omezený přístup, vyberte jinou roli.
1. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka.
1. Vyberte uživatele a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

- [Správa rezervací Azure](manage-reserved-vm-instance.md)