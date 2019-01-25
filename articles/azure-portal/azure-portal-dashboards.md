---
title: Vytvoření a sdílení řídicích panelů Azure portal | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak vytvářet a upravovat řídicí panely na webu Azure Portal.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: kfollis
ms.openlocfilehash: 4a2dee4ad90fb269268ec7d3e86def58f2444e3a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884724"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Vytvářejte a sdílejte řídicí panely na webu Azure Portal
Můžete vytvořit více řídicích panelů a sdílet je s ostatními uživateli, kteří mají přístup k předplatným Azure.  Tento článek probírá základní informace o vytváření, úpravy, publikování a správa přístupu k řídicím panelům.

## <a name="create-a-dashboard"></a>Vytvoření řídicího panelu
Řídicí panel vytvoříte výběrem tlačítka **Nový řídicí panel** vedle názvu aktuálního řídicího panelu.  

![Vytvoření řídicího panelu](./media/azure-portal-dashboards/new-dashboard.png)

Tato akce vytvoří nový, prázdný, privátní řídicí panel a přepne vás do režimu přizpůsobení, ve kterém můžete řídicí panel pojmenovat a přidat na něj dlaždice nebo změnit jejich uspořádání.  V tomto režimu má Galerie dlaždic sbalitelné levé navigační nabídce.  Galerie dlaždic vám umožní najít dlaždice pro prostředky Azure různými způsoby: můžete procházet podle [skupiny prostředků](../azure-resource-manager/resource-group-overview.md#resource-groups), typ prostředku, za [značka](../azure-resource-manager/resource-group-using-tags.md), nebo tak, že váš prostředek podle názvu.  

![přizpůsobení řídicího panelu](./media/azure-portal-dashboards/customize-dashboard.png)

Přidáte dlaždice přetahováním myší kdekoli budete chtít na plochu řídicího panelu.

Existuje nová kategorie **Obecné** pro dlaždice, které nejsou přidružené k určitému prostředku.  V tomto příkladu jsme připínat dlaždice Markdown.  Tato dlaždice vám přidat na řídicí panel vlastní obsah.  Dlaždice podporuje prostý text, [syntaxe Markdownu](https://daringfireball.net/projects/markdown/syntax)a omezenou sadu HTML.  (Pro zabezpečení, nemůžete provádět akce, třeba vložit `<script>` značky nebo použití některých stylu elementu šablon stylů CSS, který může zasahovat na portálu.) 

![Přidat markdownu](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Upravit řídicí panel
Po vytvoření řídicího panelu, můžete připnout dlaždice z Galerie dlaždic nebo dlaždici reprezentace oken. Pojďme připnout reprezentace skupiny prostředků. Můžete buď PIN kód při procházení položky, nebo v okně skupiny prostředků. Oba přístupy za následek připínat dlaždice reprezentace skupinu prostředků.

![Připnout na řídicí panel](./media/azure-portal-dashboards/pin-to-dashboard.png)

Po Připnutí položky, se zobrazí na řídicím panelu.

![Zobrazit řídicí panel](./media/azure-portal-dashboards/view-dashboard.png)

Teď, když máme dlaždicí Markdown a skupinu prostředků připnuté na řídicí panel, můžeme změnit velikost a přeuspořádat dlaždice do vhodný rozložení.

Najetí myší a výběrem "..." nebo pravým tlačítkem myši na dlaždici zobrazíte všechny kontextové příkazy pro tuto dlaždici. Ve výchozím nastavení existují dvě položky:

1. **Odepnout z řídicího panelu** – odebere dlaždici na řídicím panelu
2. **Přizpůsobení** – přejde do režimu přizpůsobení,

![přizpůsobení dlaždice](./media/azure-portal-dashboards/customize-tile.png)

Výběrem přizpůsobit, můžete změnit velikost a přeuspořádat dlaždice. Pokud chcete změnit velikost dlaždice, vyberte novou velikost z kontextové nabídky, jak je znázorněno na následujícím obrázku.

![Změna velikosti dlaždice](./media/azure-portal-dashboards/resize-tile.png)

Nebo, pokud dlaždice podporuje libovolné velikosti, můžete přetáhnout pravém dolním rohu na požadovanou velikost.

![Změna velikosti dlaždice](./media/azure-portal-dashboards/resize-corner.png)

Po změně velikosti dlaždice, zobrazte si řídicí panel.

![zobrazení dlaždice](./media/azure-portal-dashboards/view-tile.png)

Jakmile budete hotovi, přizpůsobení řídicího panelu, stačí vybrat **přizpůsobení dokončeno** pro ukončení režimu přizpůsobení, nebo klikněte pravým tlačítkem a vyberte **přizpůsobení dokončeno** v místní nabídce.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publikování řídicího panelu a správě řízení přístupu
Když vytvoříte řídicí panel, ve výchozím nastavení je privátní. To znamená, že se zobrazí pouze vám.  Pokud ho chcete zpřístupnit ostatním, použijte tlačítko **Sdílet**, které se zobrazí vedle dalších příkazů řídicího panelu.

![sdílení řídicího panelu](./media/azure-portal-dashboards/share-dashboard.png)

Zobrazí se výzva k výběru předplatného a skupiny prostředků, do kterých se má řídicí panel publikovat. Bez problémů integrovat řídicí panely do ekosystému, implementovali jsme sdílené řídicí panely jako prostředky Azure (takže nemůžete sdílet tak, že zadáte e-mailovou adresu).  Přístup k informacím zobrazeným většina dlaždic na portálu se řídí [řízení přístupu na základě Role Azure](../role-based-access-control/role-assignments-portal.md). Sdílené řídicí panely jsou z hlediska řízení přístupu, nijak neliší od virtuálního počítače nebo účet úložiště.  

Řekněme, že máte předplatné Azure a členy týmu mít byla přiřazena role **vlastníka**, **Přispěvatel**, nebo **čtečky** předplatného.  Uživatelé, kteří jsou vlastníky nebo přispěvateli budou moct seznamu, zobrazit, vytvořit, upravit nebo odstranit řídicí panely v tomto předplatném.  Uživatelé, kteří jsou čtenáři budou moct vypisování a zobrazování řídicích panelů, ale nemůžete upravovat ani odstranit je.  Uživatelé s přístupem čtečky dokáží provádět místní úpravy na sdílený řídicí panel, ale nejsou k publikování tyto změny zpět na server.  Ale budou mít vlastní kopii řídicího panelu pro vlastní použití.  Jako vždy jednotlivým dlaždicím na řídicím panelu vynutit vlastní pravidla pro řízení přístupu na základě prostředků, které odpovídají.  

Pro usnadnění práce vás prostředí pro publikování na portálu navede k umístění řídicích panelů do skupiny prostředků **dashboards** (řídicí panely).  

![Publikování řídicího panelu](./media/azure-portal-dashboards/publish-dashboard.png)

Můžete také publikovat řídicí panel do konkrétní skupiny prostředků.  Řízení přístupu k tomuto řídicímu panelu odpovídá řízení přístupu pro skupinu prostředků.  Uživatelé, kteří můžou spravovat prostředky v příslušné skupině prostředků mít také přístup k řídicím panelům.

![publikování řídicího panelu do skupiny prostředků](./media/azure-portal-dashboards/publish-to-resource-group.png)

Po publikování řídicího panelu **sdílení a přístup** ovládací panel bude aktualizovat a zobrazit informace o řídicím panelu publikované, včetně odkazů ke správě přístupu uživatelů k řídicímu panelu.  Tento odkaz spustí standardní okno řízení přístupu na základě rolí používá k řízení přístupu pro libovolné prostředky Azure.  Vždy získáte zpět na toto zobrazení tak, že vyberete **sdílené složky**.

![Správa řízení přístupu](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Další postup
* Pokud chcete spravovat prostředky, najdete v článku [Správa prostředků Azure prostřednictvím portálu](../azure-resource-manager/resource-group-portal.md).
* Nasazení prostředků najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

