---
title: Použití vlastní dlaždice markdownu na řídicích panelech Azure
description: Přečtěte si, jak přidat dlaždici markdown na řídicí panel Azure pro zobrazení statického obsahu.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310709"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Zobrazení vlastního obsahu pomocí dlaždice markdownu na řídicích panelech Azure

Do řídicích panelů Azure můžete přidat dlaždici markdown, která zobrazí vlastní statický obsah. Můžete například zobrazit základní pokyny, obrázek nebo sadu hypertextových odkazů na dlaždici značky.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Přidání dlaždice markdown na řídicí panel

1. Na postranním panelu portálu Azure vyberte **řídicí panel.**

   ![Snímek obrazovky s postranním panelem portálu](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Pokud jste vytvořili vlastní řídicí panely, v zobrazení řídicího panelu vyberte pomocí rozevíracího panelu řídicí panel, kde by se měla zobrazit vlastní dlaždice značky. Vyberte ikonu úprav a otevřete **Galerii dlaždic**.

   ![Snímek obrazovky s zobrazením úprav řídicího panelu](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. V **Galerii dlaždic**vyhledejte dlaždici nazvanou **Markdown** a vyberte **Přidat**. Dlaždice se přidá na řídicí panel a otevře se podokno **Upravit značky.**

1. Zadejte hodnoty pro **Název** a **Titulky**, které se zobrazí na dlaždici po přesunutí do jiného pole.

   ![Snímek obrazovky s výsledky zadávání nadpisu a titulků](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Vyberte jednu z možností zahrnutí obsahu značky: **Vložené úpravy** nebo **vložení obsahu pomocí adresy URL**.

   - Vyberte **Vstřikovací úpravy,** pokud chcete zadat markdown přímo.

      ![Snímek obrazovky s zadáváním vobsahového obsahu](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Pokud chcete použít existující obsah značky hostovaný online, vyberte **Vložit obsah pomocí adresy URL.**

      ![Snímek obrazovky s adresou URL pro zadání](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Pro zvýšení zabezpečení můžete vytvořit soubor markdown a uložit jej do [objektu blob účtu úložiště Azure, kde je povoleno šifrování](../storage/common/storage-service-encryption.md), a pak na soubor přejděte pomocí možnosti URL. Obsah značky je šifrován prostřednictvím možností šifrování účtu úložiště. Obsah značky na řídicím panelu mohou zobrazit pouze uživatelé s oprávněními k souboru.

1. Výběrem **možnosti Hotovo** zavřete podokno **Upravit značky.** Váš obsah se zobrazí na dlaždici Markdown, kterou můžete změnit přetažením úchytu v pravém dolním rohu.

   ![Snímek obrazovky s vlastní dlaždicí markdown](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funkce a omezení obsahu značky

Na dlaždici markdown umíte libovolnou kombinaci prostého textu, syntaxe Markdownu a obsahu HTML. Portál Azure používá knihovnu s otevřeným zdrojovým kódem _označenou_ k transformaci obsahu do html, který se zobrazuje na dlaždici. Kód HTML vytvořený _označeným je_ před vykreslením předem zpracován portálem. Tento krok pomáhá zajistit, aby vaše vlastní nastavení neovlivnilo zabezpečení nebo rozložení portálu. Během tohoto předběžného zpracování bude odebrána jakákoli část kódu HTML, která představuje potenciální hrozbu. Portál nepovoluje následující typy obsahu:

* JavaScript `<script>` – budou odstraněny značky a inline vyhodnocení JavaScriptu.
* iframe `<iframe>` - značky budou odstraněny.
* Styl `<style>` - značky budou odstraněny. Atributy vložených stylů u prvků HTML nejsou oficiálně podporovány. Možná zjistíte, že některé prvky inline stylu pracují za vás, ale pokud narušují rozložení portálu, mohou kdykoli přestat pracovat. Dlaždice Markdown je určena pro základní statický obsah, který používá výchozí styly portálu.

## <a name="next-steps"></a>Další kroky

* Pokud chcete vytvořit vlastní řídicí panel, přečtěte si část [Vytvoření a sdílení řídicích panelů na webu Azure Portal.](../azure-portal/azure-portal-dashboards.md)
