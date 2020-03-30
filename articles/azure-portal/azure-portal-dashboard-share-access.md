---
title: Sdílení řídicích panelů portálu Azure pomocí řízení přístupu na základě rolí
description: Tento článek vysvětluje, jak sdílet řídicí panel na webu Azure portal pomocí řízení přístupu na základě rolí.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 711e8a7ae31888c9754252d88404d90f24e8030e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131985"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Sdílení řídicích panelů Azure prostřednictvím Řízení přístupu na základě role

Po konfiguraci řídicího panelu jej můžete publikovat a sdílet s ostatními uživateli ve vaší organizaci. Povolit ostatním zobrazit řídicí panel pomocí Řízení [přístupu na základě rolí](../role-based-access-control/role-assignments-portal.md) Azure (RBAC). Přiřazení uživatele nebo skupiny uživatelů k roli. Tato role definuje, zda tito uživatelé mohou zobrazit nebo upravit publikovaný řídicí panel.

Všechny publikované řídicí panely se implementují jako prostředky Azure. Existují jako spravovatelné položky v rámci vašeho předplatného a jsou obsaženy ve skupině prostředků. Z hlediska řízení přístupu se řídicí panely neliší od jiných prostředků, jako je například virtuální počítač nebo účet úložiště.

> [!TIP]
> Jednotlivé dlaždice na řídicím panelu vynucují vlastní požadavky na řízení přístupu na základě prostředků, které zobrazují. Řídicí panel můžete široce sdílet a současně chránit data na jednotlivých dlaždicích.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Principy řízení přístupu pro řídicí panely

Pomocí řízení přístupu na základě rolí (RBAC) můžete přiřadit uživatele k rolím na třech různých úrovních oboru:

* předplatné
* skupina prostředků
* prostředek

Oprávnění, která přiřadíte dědit z předplatného až do prostředku. Publikovaný řídicí panel je prostředek. Možná už máte uživatele přiřazené k rolím pro předplatné, které platí pro publikovaný řídicí panel.

Řekněme, že máte předplatné Azure a různým členům vašeho týmu byly přiřazeny role *vlastníka*, *přispěvatele*nebo *čtenáře* pro předplatné. Uživatelé, kteří jsou vlastníky nebo přispěvateli, mohou v rámci předplatného zobrazit, zobrazit, vytvořit, upravit nebo odstranit řídicí panely. Uživatelé, kteří jsou čtenáři, mohou řídicí panely zobrazit a zobrazit, ale nemohou je upravovat ani odstraňovat. Uživatelé s přístupem čtečky mohou provádět místní úpravy publikovaného řídicího panelu, například při řešení problému, ale nemohou tyto změny publikovat zpět na server. Mohou si vytvořit soukromou kopii řídicího panelu pro sebe.

Můžete také přiřadit oprávnění skupině prostředků, která obsahuje několik řídicích panelů, nebo jednotlivému řídicímu panelu. Můžete se například rozhodnout, že skupina uživatelů by měla mít omezená oprávnění v rámci předplatného, ale větší přístup k určitému řídicímu panelu. Přiřaďte tyto uživatele k roli pro tento řídicí panel.

## <a name="publish-dashboard"></a>Publikování řídicího panelu

Předpokládejme, že nakonfigurujete řídicí panel, který chcete sdílet se skupinou uživatelů ve vašem předplatném. Následující kroky ukazují, jak sdílet řídicí panel se skupinou s názvem Správci úložišť. Můžete pojmenovat svou skupinu, co se vám líbí. Další informace najdete [v tématu Správa skupin ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Před přiřazením přístupu je nutné řídicí panel publikovat.

1. Na řídicím panelu vyberte **Sdílet**.

    ![vybrat sdílenou položku pro řídicí panel](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. V **ovládacím prvku Sdílení + přístup**vyberte **Publikovat**.

    ![publikování řídicího panelu](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Ve výchozím nastavení sdílení publikuje řídicí panel do **skupiny**prostředků s názvem řídicí panely . Chcete-li vybrat jinou skupinu prostředků, zrušte zaškrtnutí políčka.

Řídicí panel je nyní publikován. Pokud jsou vhodná oprávnění zděděná z předplatného, nemusíte dělat nic víc. Ostatní uživatelé ve vaší organizaci mohou přistupovat k řídicímu panelu a upravovat jej na základě role na úrovni předplatného.

## <a name="assign-access-to-a-dashboard"></a>Přiřazení přístupu k řídicímu panelu

K roli pro tento řídicí panel můžete přiřadit skupinu uživatelů.

1. Po publikování řídicího panelu vyberte možnost **Sdílet** nebo **Zrušit sdílení,** abyste měli přístup k **ovládacímu prvku Sdílení + přístupu**.

1. V **ovládacím prvku Sdílení + přístup**vyberte Spravovat **uživatele**.

    ![správa uživatelů řídicího panelu](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Výběrem **přiřazení rolí** zobrazíte stávající uživatele, kterým je již přiřazena role pro tento řídicí panel.

1. Pokud chcete přidat nového uživatele nebo skupinu, vyberte **Přidat** a pak **Přidat přiřazení role**.

    ![přidání uživatele pro přístup k řídicímu panelu](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Vyberte roli, která představuje oprávnění k udělení. V tomto příkladu vyberte **přispěvatel**.

1. Vyberte uživatele nebo skupinu, kterou chcete roli přiřadit. Pokud v seznamu nevidíte hledaného uživatele nebo skupinu, použijte vyhledávací pole. Seznam dostupných skupin závisí na skupinách, které jste vytvořili ve službě Active Directory.

1. Po přidání uživatelů nebo skupin vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* Seznam rolí najdete [v tématu předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md).
* Další informace o správě prostředků najdete v [tématu Správa prostředků Azure pomocí portálu Azure](resource-group-portal.md).

