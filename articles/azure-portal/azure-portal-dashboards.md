---
title: Vytvoření a sdílení Azure portálu řídicí panely | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit a upravit řídicí panely na portálu Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: timlt
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: sewatson
ms.openlocfilehash: 4e5a5ae944b5f0059ee78a2171a9688902aaf6db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Vytvářet a sdílet řídicí panely na portálu Azure
Můžete vytvořit více řídicí panely a sdílet je s ostatními uživateli, kteří mají přístup k vašemu předplatnému Azure.  Tento článek probírá základní informace o vytváření, úpravy, publikování a správa přístupu k řídicí panely.

## <a name="create-a-dashboard"></a>Vytvořit řídicí panel
Chcete-li vytvořit řídicí panel, vyberte **novým řídicím panelem** tlačítko vedle názvu aktuálního řídicího panelu.  

![Vytvořit řídicí panel](./media/azure-portal-dashboards/new-dashboard.png)

Tato akce vytvoří řídicí panel nový, prázdný, privátní a vloží je do režimu přizpůsobení, kde můžete název řídicího panelu a přidání nebo změna uspořádání dlaždic.  V tomto režimu galerii sbalitelné dlaždice má levé navigační nabídce.  Galerie dlaždice umožňuje najít dlaždice pro vaše prostředky Azure různými způsoby: můžete procházet podle [skupiny prostředků](../azure-resource-manager/resource-group-overview.md#resource-groups), pomocí typ prostředku, pomocí [značky](../azure-resource-manager/resource-group-using-tags.md), nebo pomocí vyhledávání podle názvu prostředku.  

![přizpůsobit řídicí panel](./media/azure-portal-dashboards/customize-dashboard.png)

Přetahování a vkládání na plochu řídicí panel, kde chcete přidáte dlaždice.

Je nová kategorie **Obecné** pro dlaždice, které nejsou přidružené k určitému prostředku.  V tomto příkladu jsme připněte si dlaždici Markdownu.  Tato dlaždice je možné použít k přidání vlastního obsahu na řídicí panel.  Dlaždice podporuje prostý text, [syntaxe Markdownu](https://daringfireball.net/projects/markdown/syntax)a omezená sada HTML.  (Pro zabezpečení, že nemůžete provádět akce jako vložit `<script>` značky nebo použijte element určité stylů CSS, který může narušovat portálu.) 

![Přidat markdownu](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Úprava řídicího panelu
Po vytvoření řídicího panelu, budete moct připnout dlaždice z Galerie dlaždice nebo dlaždice reprezentace okna. Umožňuje připnout reprezentace naší skupiny prostředků. Můžete buď kódu pin při procházení položka, nebo v okně skupiny prostředků. Obou přístupů za následek Připnutí reprezentace dlaždice skupiny prostředků.

![Připnout na řídicí panel](./media/azure-portal-dashboards/pin-to-dashboard.png)

Po Připnutí položku, zobrazí se na řídicím panelu.

![Zobrazení řídicí panel](./media/azure-portal-dashboards/view-dashboard.png)

Teď, když máme dlaždici Markdownu a skupinu prostředků připnuli k řídicímu panelu, jsme změnit velikost a změna uspořádání dlaždice do vhodný rozložení.

Mohou přesouváním ukazatele myši a výběrem "...", nebo kliknete pravým tlačítkem na dlaždici se zobrazí všechny kontextové příkazy pro tuto dlaždici. Ve výchozím nastavení existují dvě položky:

1. **Odepnout z řídicího panelu** – odebere dlaždice z řídicího panelu
2. **Přizpůsobení** – vstupuje do režimu přizpůsobení

![přizpůsobení dlaždice](./media/azure-portal-dashboards/customize-tile.png)

Výběrem přizpůsobit, můžete změnit velikost a změnit pořadí dlaždice. Ke změně velikosti dlaždici, vyberte novou velikost z kontextové nabídky, jak je znázorněno na následujícím obrázku.

![změnit velikost dlaždice](./media/azure-portal-dashboards/resize-tile.png)

Nebo, pokud dlaždice podporuje jakékoli velikosti, můžete přetáhnout pravém dolním na požadovanou velikost.

![změnit velikost dlaždice](./media/azure-portal-dashboards/resize-corner.png)

Po změně velikosti dlaždic, zobrazení řídicího panelu.

![dlaždice zobrazení](./media/azure-portal-dashboards/view-tile.png)

Jakmile budete hotovi, přizpůsobení řídicí panel, jednoduše vyberte **provádí přizpůsobení** ukončíte režimu přizpůsobení, nebo klikněte pravým tlačítkem a vyberte **provádí přizpůsobení** v místní nabídce.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Řídicí panel publikování a správě řízení přístupu
Když vytváříte řídicí panel, je soukromé ve výchozím nastavení, což znamená, že jste jediná osoba, která můžete zobrazit.  Chcete-li vidět další uživatelé, použijte **sdílenou složku** tlačítko, které se zobrazí spolu s jinými příkazy řídicího panelu.

![sdílení řídicího panelu.](./media/azure-portal-dashboards/share-dashboard.png)

Zobrazí se výzva vybrat skupinu prostředků pro řídicí panel publikování a odběru. Řídicí panely se bezproblémově integruje do ekosystému, implementovali jsme sdílené řídicí panely jako prostředky Azure (tak nemůžete sdílet zadáním e-mailovou adresu).  Přístup k informacím zobrazit většinou dlaždice na portálu se řídí [řízení přístupu na základě Role Azure](../role-based-access-control/role-assignments-portal.md). Sdílené řídicí panely jsou z hlediska řízení k přístupu, nijak neliší od virtuálního počítače nebo účtu úložiště.  

Řekněme, že máte předplatné Azure a členy týmu přiřazení rolí **vlastníka**, **Přispěvatel**, nebo **čtečky** předplatného.  Uživatelé, kteří jsou vlastníci a přispěvatelé dokážou seznamu, zobrazení, vytvořit, upravit nebo odstranit řídicí panely v rámci tohoto předplatného.  Uživatelé, kteří jsou čtečky dokážou seznamu a zobrazení řídicích panelů, ale nelze upravit nebo odstranit je.  Uživatelé s přístupem čtečky jsou možnost provádět místní úpravy sdílené řídicího panelu, ale nejsou možné publikovat tyto změny zpět na server.  Ale udělat privátní kopie řídicího panelu pro vlastní použití.  Jednotlivé dlaždice na řídicím panelu jako vždy vynutit svoje vlastní pravidla pro řízení přístupu na základě prostředků, které odpovídají.  

Pro usnadnění práce je portálu publikování prostředí příručky směrem vzor umístění řídicí panely ve skupině prostředků jste volali metodu **řídicí panely**.  

![publikovat řídicí panel](./media/azure-portal-dashboards/publish-dashboard.png)

Můžete také publikovat na řídicí panel do určité skupiny prostředků.  Řízení přístupu pro tento řídicí panel odpovídá řízení přístupu pro skupinu prostředků.  Uživatelé, kteří můžou spravovat prostředky v příslušné skupině prostředků také mít přístup k řídicí panely.

![publikování řídicího panelu do skupiny prostředků](./media/azure-portal-dashboards/publish-to-resource-group.png)

Po publikování řídicího panelu **sdílení + přístup** ovládacího prvku podokno bude aktualizovat a můžete zobrazit informace o publikovaných řídicího panelu, včetně odkaz na spravovat přístup uživatelů k řídicímu panelu.  Tento odkaz spustí standardní okno řízení přístupu na základě rolí používá k řízení přístupu pro jakýmikoli prostředky Azure.  Vždy získáte zpět na toto zobrazení tak, že vyberete **sdílené složky**.

![Správa řízení přístupu](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Další postup
* Ke správě prostředků, najdete v části [Azure spravovat prostředky prostřednictvím portálu](../azure-resource-manager/resource-group-portal.md).
* Nasazení prostředků najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a portálu Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

