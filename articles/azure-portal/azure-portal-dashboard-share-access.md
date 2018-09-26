---
title: Sdílení řídicích panelů Azure portal pomocí RBAC | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak sdílet řídicí panel na webu Azure Portal pomocí řízení přístupu na základě rolí.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: c07a9d92cac13d6325e66f44426f1a64e8ac53cb
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096196"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Sdílení řídicích panelů Azure prostřednictvím řízení přístupu na základě Role
Po dokončení konfigurace řídicího panelu, můžete ji publikovat a sdílet s ostatními uživateli ve vaší organizaci. Povolit ostatním Chcete-li zobrazit řídicí panel s použitím Azure [řízení přístupu na základě Role](../role-based-access-control/role-assignments-portal.md). Přiřadíte roli uživatele nebo skupiny uživatelů a tuto roli definuje, jestli uživatelé, můžete zobrazit nebo upravit publikovaný řídicí panel. 

Všechny publikované řídicí panely jsou implementovány jako prostředky Azure, což znamená, že existují jako spravovatelné položky v rámci vašeho předplatného a jsou obsaženy ve skupině prostředků.  Řídicí panely jsou z hlediska řízení přístupu, nijak neliší od jiné prostředky, jako je například virtuální počítač nebo účet úložiště.

> [!TIP]
> Jednotlivým dlaždicím na řídicím panelu vynutit své vlastní požadavky na řízení přístupu na základě prostředků, které se zobrazí.  Proto můžete navrhnout, jež jsou sdílena široce přitom budou chráněná data na jednotlivých dlaždicích řídicího panelu.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Principy řízení přístupu pro řídicí panely
Pomocí Role-Based řízení přístupu (RBAC), můžete přiřadit uživatele k rolím na třech různých úrovních oboru:

* předplatné
* skupina prostředků
* prostředek

Oprávnění, která přiřadíte se dědí z předplatného na prostředek. Publikovaný řídicí panel je prostředek. Proto může již máte uživatele přiřadit do rolí pro předplatné, které fungovat i pro publikovaný řídicí panel. 

Tady je příklad.  Řekněme, že máte předplatné Azure a různé členy vašeho týmu mít byla přiřazena role **vlastníka**, **Přispěvatel**, nebo **čtečky** pro předplatné. Uživatelé, kteří jsou vlastníky nebo přispěvateli budou moct seznamu, zobrazit, vytvořit, upravit nebo odstranit řídicí panely v rámci předplatného.  Uživatelé, kteří jsou čtenáři budou moct vypisování a zobrazování řídicích panelů, ale nemůžete upravovat ani odstranit je.  Uživatelé s přístupem čtečky budou moct provádět místní úpravy publikovaný řídicí panel (jako je třeba při řešení problému), ale nebudou moci publikovat tyto změny zpět do serveru.  Mají možnost sami provést vlastní kopii řídicího panelu

Ale může také přiřadíte oprávnění ke skupině prostředků, která obsahuje několik řídicích panelů nebo na individuální řídicí panel. Například můžete rozhodnout, že skupina uživatelů by měla mít omezená oprávnění napříč předplatné, ale vyšší úroveň přístupu pro konkrétní řídicí panel. Přiřadit uživatele k roli pro tento řídicí panel. 

## <a name="publish-dashboard"></a>Publikování řídicího panelu
Předpokládejme, že po dokončení konfigurace řídicího panelu, který chcete sdílet se skupinou uživatelů ve vašem předplatném. Následující postup znázornění vlastní skupinu s názvem Správci úložiště, ale můžete pojmenovat vaše skupina cokoli, co chcete. Informace o vytváření skupiny služby Active Directory a přidání uživatelů do této skupiny, najdete v části [Správa skupin ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Na řídicím panelu vyberte **sdílené složky**.
   
     ![Vyberte sdílenou složku](./media/azure-portal-dashboard-share-access/select-share.png)
2. Před udělením přístupu, je nutné publikovat řídicí panel. Ve výchozím nastavení, bude publikována řídicí panel až po skupinu prostředků s názvem **řídicí panely**. Vyberte **Publikovat**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Řídicí panel je publikováno. Pokud jsou oprávnění zděděná z předplatného vhodné, není potřeba provádět žádnou další. Ostatní uživatelé ve vaší organizaci budou moct používat a upravovat řídicí panel, na základě jejich role úrovně předplatného. Ale pro účely tohoto kurzu, můžeme přiřadit skupině uživatelů roli pro tento řídicí panel.

## <a name="assign-access-to-a-dashboard"></a>Přiřazení přístupu k řídicímu panelu
1. Po publikování řídicího panelu, vyberte **spravovat uživatele**.
   
     ![Správa uživatelů](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Zobrazí se seznam stávajícím uživatelům, kteří jsou už přiřazené roli pro tento řídicí panel. Seznam stávajících uživatelů bude jiné než na obrázku níže. Přiřazení se pravděpodobně, dědí z předplatného. Chcete-li přidat nové uživatele nebo skupiny, **přidat**.
   
     ![Přidat uživatele](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Vyberte roli, která představuje, které chcete udělit oprávnění. V tomto příkladu vyberte **Přispěvatel**.
   
     ![Vyberte roli](./media/azure-portal-dashboard-share-access/select-role.png)
4. Vyberte uživatele nebo skupiny, kterou chcete přiřadit k roli. Pokud nevidíte uživatele nebo skupinu, kterou hledáte, v seznamu, použijte vyhledávací pole. Seznam dostupných skupin bude záviset na skupiny, které jste vytvořili ve službě Active Directory.
   
     ![Vyberte uživatele.](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Až dokončíte přidávání uživatele nebo skupiny, vyberte **OK**. 
6. Nové přiřazení se přidá do seznamu uživatelů. Všimněte si, že jeho **přístup** je uveden jako **přiřazeno** spíše než **zděděné**.
   
     ![přiřazené role](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Další postup
* Seznam rolí, najdete v části [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md).
* Další informace o správě prostředků najdete v tématu [Správa prostředků Azure prostřednictvím portálu](resource-group-portal.md).

