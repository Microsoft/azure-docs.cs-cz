---
title: Sdílení řídicích panelů Azure Portal pomocí RBAC | Microsoft Docs
description: Tento článek vysvětluje, jak sdílet řídicí panel v Azure Portal pomocí Access Control na základě rolí.
services: azure-portal
documentationcenter: ''
author: mblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: mblythe
ms.openlocfilehash: da983a6a3c86be87f1a24b67252a40adac2fa59d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641380"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Sdílení řídicích panelů Azure prostřednictvím Řízení přístupu na základě role

Po nakonfigurování řídicího panelu ho můžete publikovat a sdílet s ostatními uživateli ve vaší organizaci. Ostatním uživatelům umožníte zobrazit váš řídicí panel pomocí [Access Control založeného na rolích](../role-based-access-control/role-assignments-portal.md)Azure. K roli přiřadíte uživatele nebo skupinu uživatelů a tato role definuje, jestli uživatelé mohou zobrazit nebo upravit publikovaný řídicí panel. 

Všechny publikované řídicí panely se implementují jako prostředky Azure, což znamená, že existují jako spravovatelné položky v rámci vašeho předplatného a jsou součástí skupiny prostředků.  Z perspektivy řízení přístupu se řídicí panely neliší od jiných prostředků, jako je třeba virtuální počítač nebo účet úložiště.

> [!TIP]
> Jednotlivé dlaždice na řídicím panelu vynutily vlastní požadavky na řízení přístupu na základě zobrazovaných prostředků.  Proto můžete navrhovat řídicí panel, který se sdílí široce, a přitom chránit data v jednotlivých dlaždicích.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Principy řízení přístupu pro řídicí panely
Díky Access Controlům na základě rolí (RBAC) můžete uživatelům přiřadit role na třech různých úrovních rozsahu:

* předplatné
* skupina prostředků
* resource

Oprávnění, která přiřadíte, se z předplatného dědí do prostředku. Publikovaný řídicí panel je prostředek. Proto je možné, že již máte přiřazeny uživatele k rolím pro předplatné, které fungují i pro publikovaný řídicí panel. 

Tady je příklad.  Řekněme, že máte předplatné Azure a různé členy týmu mají k předplatnému přiřazené role **vlastníka**, **přispěvatele**nebo **čtenáře** . Uživatelé, kteří jsou vlastníci nebo přispěvatelé, můžou v rámci předplatného vypisovat, zobrazovat, vytvářet, upravovat nebo odstraňovat řídicí panely.  Uživatelé, kteří čtenáři můžou zobrazit a zobrazovat řídicí panely, ale nemůžou je upravovat ani odstraňovat.  Uživatelé s přístupem ke čtenářům můžou při řešení potíží provádět místní úpravy publikovaného řídicího panelu (například při odstraňování problému), ale nemůžou tyto změny publikovat zpátky na server.  Budou mít možnost vytvořit soukromou kopii řídicího panelu pro sebe sama.

Můžete ale také přiřadit oprávnění ke skupině prostředků, která obsahuje několik řídicích panelů nebo k jednotlivým řídicím panelům. Můžete se třeba rozhodnout, že skupina uživatelů by měla mít omezená oprávnění v rámci předplatného, ale větší přístup k určitému řídicímu panelu. Těmto uživatelům přiřadíte roli pro tento řídicí panel. 

## <a name="publish-dashboard"></a>Publikování řídicího panelu
Řekněme, že jste dokončili konfiguraci řídicího panelu, který chcete sdílet se skupinou uživatelů v rámci vašeho předplatného. Níže uvedené kroky popisují vlastní skupinu s názvem Správci úložiště, ale skupinu můžete pojmenovat, ať už chcete. Informace o vytvoření skupiny služby Active Directory a přidání uživatelů do této skupiny najdete v tématu [Správa skupin v Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Na řídicím panelu vyberte **sdílet**.
   
     ![vybrat sdílenou složku](./media/azure-portal-dashboard-share-access/select-share.png)
2. Před přiřazením přístupu musíte řídicí panel publikovat. Ve výchozím nastavení se řídicí panel publikuje do skupiny prostředků s názvem **řídicí panely**. Vyberte **Publikovat**.
   
     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Váš řídicí panel je teď publikovaný. Pokud jsou oprávnění zděděná z předplatného vhodná, nemusíte provádět žádné další akce. Jiní uživatelé ve vaší organizaci budou mít přístup k řídicímu panelu na základě role na úrovni předplatného a můžou ho upravovat. Pro účely tohoto kurzu ale přiřadíme skupině uživatelů roli pro tento řídicí panel.

## <a name="assign-access-to-a-dashboard"></a>Přiřazení přístupu k řídicímu panelu
1. Po publikování řídicího panelu vyberte **Spravovat uživatele**.
   
     ![Správa uživatelů](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Zobrazí se seznam existujících uživatelů, kteří už mají přiřazenou roli pro tento řídicí panel. Seznam stávajících uživatelů se bude lišit od obrázku uvedeného níže. Z předplatného se nejspíš dědí přiřazení. Chcete-li přidat nového uživatele nebo skupinu, vyberte možnost **Přidat**.
   
     ![Přidat uživatele](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Vyberte roli, která představuje oprávnění, která chcete udělit. V tomto příkladu vyberte **Přispěvatel**.
   
     ![vybrat roli](./media/azure-portal-dashboard-share-access/select-role.png)
4. Vyberte uživatele nebo skupinu, které chcete přiřadit k roli. Pokud v seznamu nevidíte uživatele nebo skupinu, které hledáte, použijte vyhledávací pole. Seznam dostupných skupin bude záviset na skupinách, které jste vytvořili ve službě Active Directory.
   
     ![Vybrat uživatele](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Až dokončíte přidávání uživatelů nebo skupin, vyberte **OK**. 
6. Nové přiřazení se přidá do seznamu uživatelů. Všimněte si, že jeho **přístup** je uveden jako **přiřazený** místo **zděděné**.
   
     ![přiřazené role](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Další kroky
* Seznam rolí naleznete v části [RBAC: předdefinované role](../role-based-access-control/built-in-roles.md).
* Další informace o správě prostředků najdete v tématu [Správa prostředků Azure prostřednictvím portálu](resource-group-portal.md).

