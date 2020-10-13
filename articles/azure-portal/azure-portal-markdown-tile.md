---
title: Použití vlastní dlaždice Markdownu na řídicích panelech Azure
description: Přečtěte si, jak přidat dlaždici Markdownu na řídicí panel Azure pro zobrazení statického obsahu.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: how-to
ms.custom: devx-track-js
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 4e8d7054fb0df265285bda223e360ea61612aaf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263942"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Použití dlaždice Markdownu na řídicích panelech Azure pro zobrazení vlastního obsahu

Dlaždici Markdownu můžete přidat do řídicích panelů Azure a zobrazit tak vlastní, statický obsah. Můžete například zobrazit základní pokyny, obrázek nebo sadu hypertextových odkazů na dlaždici Markdownu.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Přidání dlaždice Markdownu do řídicího panelu

1. Vyberte **řídicí panel** na bočním panelu Azure Portal.

   ![Snímek obrazovky s postranním panelem portálu](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Pokud jste vytvořili nějaké vlastní řídicí panely, v zobrazení řídicího panelu použijte rozevírací seznam a vyberte řídicí panel, ve kterém se má vlastní dlaždice Markdownu zobrazit. Výběrem ikony Upravit otevřete **galerii dlaždic**.

   ![Snímek obrazovky zobrazující zobrazení pro úpravy řídicího panelu](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. V **galerii dlaždic**Najděte dlaždici s názvem **Markdownu** a vyberte **Přidat**. Dlaždice se přidá na řídicí panel a otevře se podokno **Upravit Markdownu** .

1. Zadejte hodnoty pro **název** a **podnadpis**, které se zobrazí na dlaždici po přesunu do jiného pole.

   ![Snímek obrazovky s výsledky vstupu nadpisu a podnadpisu](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Vyberte jednu z možností pro zahrnutí obsahu Markdownu: **vložené úpravy** nebo **vložení obsahu pomocí adresy URL**.

   - Pokud chcete zadat Markdownu přímo, vyberte **vložené úpravy** .

      ![Snímek obrazovky zobrazující vložení vloženého obsahu](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Pokud chcete používat stávající Markdownu obsah, který je hostovaný online, vyberte **Vložit obsah pomocí adresy URL** .

      ![Snímek obrazovky ukazující zadání adresy URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Pro zvýšení zabezpečení můžete vytvořit soubor Markdownu a uložit ho do [objektu BLOB účtu úložiště Azure, kde je šifrování povolené](../storage/common/storage-service-encryption.md), a pak na soubor odkazovat pomocí možnosti adresa URL. Obsah Markdownu je zašifrovaný prostřednictvím možností šifrování účtu úložiště. Obsah Markdownu na řídicím panelu můžou zobrazit jenom uživatelé s oprávněními k tomuto souboru.

1. Výběrem **Hotovo** zavřete podokno **Upravit Markdownu** . Obsah se zobrazí na dlaždici Markdownu, kterou můžete změnit přetažením úchytu v pravém dolním rohu.

   ![Snímek obrazovky zobrazující vlastní dlaždici Markdownu](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdownu možnosti obsahu a omezení

Na dlaždici Markdownu můžete použít libovolnou kombinaci prostého textu, syntaxe Markdownu a obsahu HTML. Azure Portal používá open source knihovnu s názvem _označenou_ k transformaci vašeho obsahu na HTML, který se zobrazí na dlaždici. KÓD HTML vytvořený pomocí _označení_ je před jeho vykreslením předem zpracován portálem. Tento krok vám pomůže zajistit, aby vaše přizpůsobení neovlivnilo zabezpečení nebo rozložení portálu. Během tohoto předběžného zpracování se odeberou všechny součásti HTML, které představují potenciální hrozbu. Portál nepovoluje následující typy obsahu:

* JavaScript – `<script>` značky a vložená hodnocení JavaScriptu se odeberou.
* prvky IFrame – `<iframe>` značky budou odebrány.
* Styly – `<style>` značky budou odebrány. Vložené atributy stylu u elementů HTML nejsou oficiálně podporovány. Může dojít k tomu, že některé vložené prvky stylu fungují za vás, ale pokud jsou v konfliktu s rozložením portálu, mohou přestat fungovat kdykoli. Dlaždice Markdownu je určená pro základní a statický obsah, který používá výchozí styly portálu.

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit vlastní řídicí panel, přečtěte si téma [Vytvoření a sdílení řídicích panelů v Azure Portal](../azure-portal/azure-portal-dashboards.md) .
