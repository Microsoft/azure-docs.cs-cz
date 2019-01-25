---
title: Použít vlastní markdown dlaždice na řídicích panelů Azure
description: Zjistěte, jak přidat markdownu dlaždici na řídicí panel Azure zobrazíte statického obsahu
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 29c6dc0f7e71508e7b4ecb4e0f1d5c849df7a68c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904844"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Zobrazit vlastní obsah pomocí markdownu dlaždice na řídicích panelů Azure

Dlaždice markdown můžete přidat na řídicí panely Azure k zobrazení vlastní, statický obsah. Můžete například zobrazit základní pokyny, image nebo sadu hypertextové odkazy s dlaždicí markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Přidat na řídicí panel dlaždicí markdown

1. Vyberte **řídicí panel** postranním panelu Azure portal. Pokud žádné vlastní řídicí panely, jste vytvořili v zobrazení řídicího panelu, pomocí rozevírací nabídky vyberte řídicí panel, kde by se měla zobrazit dlaždice vlastní markdown. Vyberte ikonu úprav otevřete **Galerie dlaždic**.

  ![Zobrazení pro úpravy snímek obrazovky zobrazující řídicí panel](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. V **Galerie dlaždic**, vyhledejte dlaždici volá **Markdownu** a klikněte na tlačítko **přidat**. Přidání dlaždice na řídicí panel a **upravit Markdown** se otevře podokno.

1. Upravit **Title**, **titulek**, a **obsahu** polí pro přizpůsobení na dlaždici. V tomto příkladu byl upraven dlaždicí markdown pro zobrazení informací helpdesku vlastní nápovědy.

  ![Snímek obrazovky zobrazení pro úpravy dlaždice markdown](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Vyberte **provádí** zrušíte **upravit Markdown** podokně. Váš obsah se zobrazí na dlaždici Markdown, který se potom dá změnit přetažením úchytu v pravém dolním rohu.

  ![Snímek obrazovky znázorňující vlastní markdown dlaždice](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Možnosti obsahu markdownu a omezení

Na dlaždici markdownu můžete použít libovolnou kombinaci prostý text, syntaxe Markdownu a obsah HTML. Na webu Azure portal využívá open source knihovnu volá _označené_ získat obsah HTML, který se zobrazí na dlaždici. Kód HTML vytvořený _označené_ předem zpracovává portálu před vykreslením. Tento krok vám pomůže, ujistěte se, že vaše přizpůsobení neovlivní zabezpečení nebo rozložení na portálu. Během předběžného zpracování, odeberou se některá část kódu HTML, který představuje potenciální ohrožení. Na portálu nejsou povoleny následující typy obsahu:

* JavaScript – `<script>` značky a hodnocení vložený JavaScript se odeberou.
* prvky IFRAME - `<iframe>` značky se odeberou.
* Style - `<style>` značky se odeberou. Atributy stylu vložené na prvcích HTML nejsou oficiálně podporována. Může se stát, že některé prvky stylu vložené pracují pro vás, ale pokud jsou v konfliktu s rozložením portálu, se může přestat fungovat v každém okamžiku. Dlaždice Markdown je určená pro basic, statický obsah, který používá výchozí stylů na portálu.

## <a name="next-steps"></a>Další postup

* Pokud chcete vytvořit vlastní řídicí panel, naleznete v tématu [vytváření a sdílení řídicích panelů na webu Azure Portal](../azure-portal/azure-portal-dashboards.md)
