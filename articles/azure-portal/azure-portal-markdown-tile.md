---
title: Použití vlastní dlaždice Markdownu na řídicích panelech Azure
description: Přečtěte si, jak přidat dlaždici Markdownu na řídicí panel Azure pro zobrazení statického obsahu.
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774452"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Použití dlaždice Markdownu na řídicích panelech Azure pro zobrazení vlastního obsahu

Dlaždici Markdownu můžete přidat do řídicích panelů Azure a zobrazit tak vlastní, statický obsah. Můžete například zobrazit základní pokyny, obrázek nebo sadu hypertextových odkazů na dlaždici Markdownu.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Přidání dlaždice Markdownu do řídicího panelu

1. Vyberte **řídicí panel** na bočním panelu Azure Portal.

   ![Snímek obrazovky s postranním panelem portálu](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. V zobrazení řídicího panelu vyberte řídicí panel, kde se má zobrazit vlastní dlaždice Markdownu, a pak vyberte **Upravit**.

   ![Snímek obrazovky zobrazující zobrazení pro úpravy řídicího panelu](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. V **galerii dlaždic** Najděte dlaždici s názvem **Markdownu** a vyberte **Přidat**. Dlaždice se přidá na řídicí panel a otevře se podokno **Upravit Markdownu** .

1. Zadejte hodnoty pro **název** a **podnadpis**, které se zobrazí na dlaždici po přesunu do jiného pole.

   ![Snímek obrazovky s výsledky vstupu nadpisu a podnadpisu](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Vyberte jednu z možností pro zahrnutí obsahu Markdownu: **vložené úpravy** nebo **vložení obsahu pomocí adresy URL**.

   - Pokud chcete zadat Markdownu přímo, vyberte **vložené úpravy** .

      ![Snímek obrazovky zobrazující vložení vloženého obsahu](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Pokud chcete používat stávající Markdownu obsah, který je hostovaný online, vyberte **Vložit obsah pomocí adresy URL** .

      ![Snímek obrazovky ukazující zadání adresy URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Pro zvýšení zabezpečení můžete vytvořit soubor Markdownu a uložit ho do [objektu BLOB účtu úložiště Azure, kde je šifrování povolené](../storage/common/storage-service-encryption.md), a pak na soubor odkazovat pomocí možnosti adresa URL. Obsah Markdownu je zašifrovaný prostřednictvím možností šifrování účtu úložiště. Obsah Markdownu na řídicím panelu můžou zobrazit jenom uživatelé s oprávněními k tomuto souboru. V účtu úložiště možná budete muset nastavit pravidlo [sdílení prostředků mezi zdroji (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) , aby Azure Portal ( _https://portal.azure.com/_ ) mohl získat přístup k souboru Markdownu v objektu BLOB.

1. Výběrem **Hotovo** zavřete podokno **Upravit Markdownu** . Obsah se zobrazí na dlaždici Markdownu, kterou můžete změnit přetažením úchytu v pravém dolním rohu.

   ![Snímek obrazovky zobrazující vlastní dlaždici Markdownu](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdownu možnosti obsahu a omezení

Na dlaždici Markdownu můžete použít libovolnou kombinaci prostého textu, syntaxe Markdownu a obsahu HTML. Azure Portal používá open source knihovnu s názvem _označenou_ k transformaci vašeho obsahu na HTML, který se zobrazí na dlaždici. KÓD HTML vytvořený pomocí _označení_ je před jeho vykreslením předem zpracován portálem. Tento krok vám pomůže zajistit, aby vaše přizpůsobení neovlivnilo zabezpečení nebo rozložení portálu. Během tohoto předběžného zpracování se odeberou všechny součásti HTML, které představují potenciální hrozbu. Portál nepovoluje následující typy obsahu:

* JavaScript – `<script>` značky a vložená hodnocení JavaScriptu se odeberou.
* prvky IFrame – `<iframe>` značky se odeberou.
* Styly – `<style>` značky se odeberou. Vložené atributy stylu u elementů HTML nejsou oficiálně podporovány. Může dojít k tomu, že některé vložené prvky stylu fungují za vás, ale pokud jsou v konfliktu s rozložením portálu, mohou přestat fungovat kdykoli. Dlaždice Markdownu je určená pro základní a statický obsah, který používá výchozí styly portálu.

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit vlastní řídicí panel, přečtěte si téma [Vytvoření a sdílení řídicích panelů v Azure Portal](../azure-portal/azure-portal-dashboards.md) .
