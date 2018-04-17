---
title: Sdílet řídicí panely Azure portálu pomocí RBAC | Microsoft Docs
description: Tento článek vysvětluje, jak sdílet řídicí panel portálu Azure pomocí řízení přístupu na základě Role.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: 75f06e49aa317fc887831a9e04861d9a7989bdb2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Sdílet řídicí panely Azure pomocí řízení přístupu na základě Role
Po dokončení konfigurace na řídicí panel, můžete ji publikovat a sdílet s jinými uživateli ve vaší organizaci. Můžete povolit zobrazení řídicího panelu pomocí služby Azure [řízení přístupu na základě Role](../role-based-access-control/role-assignments-portal.md). Přiřadíte roli uživatele nebo skupinu uživatelů, a tato role definuje, zda uživatelům, můžete zobrazit nebo upravit publikované řídicího panelu. 

Všechny publikované řídicí panely jsou implementované jako prostředky Azure, což znamená, že existovat jako spravovat položky v rámci vašeho předplatného a jsou obsaženy ve skupině prostředků.  Z hlediska řízení k přístupu řídicí panely jsou nejsou jiné než jiné prostředky, jako je virtuální počítač nebo účet úložiště.

> [!TIP]
> Jednotlivé dlaždice na řídicím panelu vynutit vlastní požadavky řízení přístupu na základě prostředků, které se zobrazí.  Proto můžete navrhnout řídicí panel, který je sdílen široce přitom dat na jednotlivé dlaždice.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Principy řízení přístupu pro řídicí panely
S na základě rolí řízení přístupu (RBAC) můžete přiřadit uživatele k rolím na třech různých úrovních oboru:

* předplatné
* skupina prostředků
* prostředek

Oprávnění, která přiřadíte se dědí z předplatného dolů prostředku. Publikované řídicí panel je prostředek. Proto už můžete mít uživatelé přiřazení k role pro předplatné, které také použít publikované řídicího panelu. 

Tady je příklad.  Řekněme, že máte předplatné Azure a různé členy týmu přiřazení rolí **vlastníka**, **Přispěvatel**, nebo **čtečky** pro předplatné. Uživatelé, kteří jsou vlastníci a přispěvatelé dokážou seznamu, zobrazení, vytvořit, upravit nebo odstranit řídicí panely v rámci předplatného.  Uživatelé, kteří jsou čtečky dokážou seznamu a zobrazení řídicích panelů, ale nelze upravit nebo odstranit je.  Uživatelé s přístupem čtečky jsou možnost provádět místní úpravy publikované řídicí panel (jako je třeba při řešení potíží), ale nejsou schopná publikovat tyto změny zpět na server.  Budou mít možnost vytvořit kopii řídicího panelu privátní sami

Ke skupině prostředků, který obsahuje několik řídicí panely, nebo na jednotlivé řídicí panel, ale může přiřadit také oprávnění. Například může rozhodnout, že skupina uživatelů by měl mají omezenou oprávnění napříč předplatné, ale vyšší úroveň přístupu konkrétním řídicího panelu. Tito uživatelé přiřadíte roli pro tento řídicí panel. 

## <a name="publish-dashboard"></a>Publikování řídicího panelu
Předpokládejme, že dokončení konfigurace řídicí panel, který chcete sdílet s skupinu uživatelů v rámci vašeho předplatného. Následující postup zobrazit v ní vlastní skupinu s názvem Správci úložiště, ale můžete název vaší skupiny ať chcete. Informace o vytváření skupiny služby Active Directory a přidání uživatelů k této skupině najdete v tématu [Správa skupin v Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

1. Na řídicím panelu, vyberte **sdílené složky**.
   
     ![Vyberte sdílenou složku](./media/azure-portal-dashboard-share-access/select-share.png)
2. Před udělením přístupu, musíte publikovat řídicího panelu. Ve výchozím nastavení, řídicí panel bude publikována do skupiny prostředků s názvem **řídicí panely**. Vyberte **publikování**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Řídicí panel je nyní publikován. Pokud jsou vhodná oprávnění, dědí z předplatného, není potřeba provádět žádnou další. Ostatní uživatelé ve vaší organizaci bude moct používat a upravit řídicí panel, na základě jejich role úrovně předplatného. Ale v tomto kurzu budeme přiřadit skupinu uživatelů do rolí pro tento řídicí panel.

## <a name="assign-access-to-a-dashboard"></a>Přiřadit přístup na řídicí panel
1. Po publikování řídicího panelu, vyberte **Správa uživatelů**.
   
     ![Správa uživatelů](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Zobrazí se seznam stávajícím uživatelům, kteří jsou již přiřazena role pro tento řídicí panel. Seznam stávajících uživatelů bude jiné než následující obrázek. S největší pravděpodobností přiřazení se dědí z předplatného. Chcete-li přidat nového uživatele nebo skupiny, vyberte **přidat**.
   
     ![Přidat uživatele](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Vyberte roli, která představuje, které chcete udělit oprávnění. V tomto příkladu vyberte **Přispěvatel**.
   
     ![Vyberte roli](./media/azure-portal-dashboard-share-access/select-role.png)
4. Vyberte uživatele nebo skupinu, kterou chcete přiřadit k roli. Pokud uživatele nebo skupinu, kterou hledáte, v seznamu nevidíte, pomocí vyhledávacího pole. Seznam dostupných skupin bude záviset na skupiny, které jste vytvořili ve službě Active Directory.
   
     ![Vyberte uživatele](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Až dokončíte přidávání uživatelů nebo skupin, vyberte **OK**. 
6. Nové přiřazení se přidá do seznamu uživatelů. Všimněte si, že jeho **přístup** je uveden jako **přiřazeno** místo **zděděné**.
   
     ![přiřazené role](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Další postup
* Seznam rolí, najdete v části [RBAC: předdefinované role](../role-based-access-control/built-in-roles.md).
* Další informace o správě prostředků najdete v tématu [Azure spravovat prostředky prostřednictvím portálu](resource-group-portal.md).

