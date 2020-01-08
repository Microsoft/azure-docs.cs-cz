---
title: Použití vlastní dlaždice Markdownu na řídicích panelech Azure
description: Přečtěte si, jak přidat dlaždici Markdownu na řídicí panel Azure pro zobrazení statického obsahu.
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640138"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Použití dlaždice Markdownu na řídicích panelech Azure pro zobrazení vlastního obsahu

Dlaždici Markdownu můžete přidat do řídicích panelů Azure a zobrazit tak vlastní, statický obsah. Můžete například zobrazit základní pokyny, obrázek nebo sadu hypertextových odkazů s dlaždicí Markdownu.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Přidání dlaždice Markdownu do řídicího panelu

1. Vyberte **řídicí panel** na bočním panelu Azure Portal. Pokud jste vytvořili nějaké vlastní řídicí panely, v zobrazení řídicího panelu použijte rozevírací seznam a vyberte řídicí panel, ve kterém se má vlastní dlaždice Markdownu zobrazit. Výběrem ikony Upravit otevřete **galerii dlaždic**.

   ![Snímek obrazovky zobrazující zobrazení pro úpravy řídicího panelu](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. V **galerii dlaždic**Najděte dlaždici s názvem **Markdownu** a klikněte na **Přidat**. Dlaždice se přidá na řídicí panel a otevře se podokno **Upravit Markdownu** .

1. Upravte pole **název**, **podnadpis**a **obsah** , abyste dlaždici přizpůsobili. V následujícím příkladu se upraví dlaždice Markdownu, aby se zobrazily vlastní informace o centru pro technickou podporu.

   ![Snímek obrazovky zobrazující zobrazení pro úpravy dlaždice Markdownu](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Výběrem **Hotovo** zavřete podokno **Upravit Markdownu** . Obsah se zobrazí na dlaždici Markdownu, která se pak dá změnit tak, že se táhlo přetáhne v pravém dolním rohu.

   ![Snímek obrazovky zobrazující vlastní dlaždici Markdownu](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdownu možnosti obsahu a omezení

Na dlaždici Markdownu můžete použít libovolnou kombinaci prostého textu, syntaxe Markdownu a obsahu HTML. Azure Portal používá open source knihovnu s názvem _označenou_ k transformaci vašeho obsahu na HTML, který se zobrazí na dlaždici. KÓD HTML vytvořený pomocí _označení_ je před jeho vykreslením předem zpracován portálem. Tento krok vám pomůže zajistit, aby vaše přizpůsobení neovlivnilo zabezpečení nebo rozložení portálu. Během tohoto předběžného zpracování se odeberou všechny součásti HTML, které představují potenciální hrozbu. Portál nepovoluje následující typy obsahu:

* JavaScript – `<script>` značky a vložená hodnocení JavaScriptu se odeberou.
* prvky IFrame – značky `<iframe>` budou odebrány.
* Styly – `<style>` značky budou odebrány. Vložené atributy stylu u elementů HTML nejsou oficiálně podporovány. Může dojít k tomu, že některé vložené prvky stylu fungují za vás, ale pokud jsou v konfliktu s rozložením portálu, mohou přestat fungovat kdykoli. Dlaždice Markdownu je určená pro základní a statický obsah, který používá výchozí styly portálu.

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit vlastní řídicí panel, přečtěte si téma [Vytvoření a sdílení řídicích panelů v Azure Portal](../azure-portal/azure-portal-dashboards.md) .
