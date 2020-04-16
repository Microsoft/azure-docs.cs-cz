---
title: Správa aplikací logiky na webu Azure Portal
description: Upravujte, povolte, zakažte nebo odstraňte aplikace logiky pomocí portálu Azure.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415981"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Správa aplikací logiky na webu Azure Portal

Aplikace logiky můžete spravovat pomocí [portálu Azure nebo](https://portal.azure.com) [Visual Studia](manage-logic-apps-with-visual-studio.md). Tento článek ukazuje, jak upravit, zakázat, povolit nebo odstranit aplikace logiky na webu Azure Portal. Pokud s Azure Logic Apps teču, přečtěte [si, co je Azure Logic Apps?](logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Existující aplikace logiky. Informace o tom, jak vytvořit aplikaci logiky na webu Azure Portal, najdete v [tématu Úvodní příručka: Vytvoření prvního pracovního postupu pomocí Azure Logic Apps – portál Azure](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>Vyhledání aplikací logiky

Pokud chcete najít a otevřít aplikaci logiky, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

1. Na panelu hledání `logic apps`Azure zadejte a vyberte **Logic Apps**.

   ![Vyhledání a výběr "Logic Apps"](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. Na stránce **Logic Apps** najděte a vyberte aplikaci logiky, kterou chcete spravovat.

   Po otevření podokna **Přehled** aplikace logiky můžete filtrovat seznam, který se zobrazí na stránce **Aplikace logiky,** těmito způsoby:

   * Hledání aplikací logiky podle názvu
   * Filtrování aplikací logiky podle předplatného, skupiny prostředků, umístění a značek
   * Seskupení aplikací logiky podle skupiny prostředků, typu, předplatného a umístění

## <a name="view-logic-app-properties"></a>Zobrazení vlastností aplikace logiky

1. Na webu Azure Portal [najděte a otevřete aplikaci logiky](#find-logic-app).

1. V nabídce aplikace logiky vyberte v části **Nastavení** **položku Vlastnosti**.

1. V podokně **Vlastnosti** můžete zobrazit a zkopírovat následující informace o aplikaci logiky:

   * **Název**
   * **ID prostředku**
   * **Skupina prostředků**
   * **Umístění**
   * **Typ** 
   * **Název předplatného**
   * **ID předplatného**
   * **Koncový bod aplikace Access**
   * **Odchozí IP adresy runtime**
   * **Ip adresy koncového bodu přístupu**
   * **Konektor odchozích IP adres**

## <a name="disable-or-enable-logic-apps"></a>Zakázání nebo povolení aplikací logiky

Můžete povolit nebo zakázat [jednu aplikaci logiky](#disable-enable-single-logic-app) nebo [více aplikací logiky současně](#disable-or-enable-multiple-logic-apps) na webu Azure Portal. Můžete také [povolit nebo zakázat aplikace logiky v sadě Visual Studio](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app).

Zakázání aplikace logiky ovlivní instance pracovního postupu a spustí se následujícími způsoby:

* Všechna probíhající a čekající spuštění pokračují až do dokončení. V závislosti na počtu těchto spuštění může tento proces nějakou dobu trvat.

* Modul Logic Apps nevytvoří ani nespustí nové instance pracovního postupu.

* Spoušť se nespustí, až bude příště splněna její podmínky.

* Stav aktivační události si pamatuje bod, ve kterém byla zastavena aplikace logiky. Takže pokud znovu povolit aplikaci logiky, aktivační událost aktivuje pro všechny nezpracované položky od posledního spuštění.

  Chcete-li zastavit spuštění aplikace logiky na nezpracované položky od posledního spuštění, zrušte stav aktivační události před opětovným povolením aplikace logiky:

  1. Na webu Azure Portal [najděte a otevřete aplikaci logiky](#find-logic-app).

  1. Upravte libovolnou část aktivační události aplikace logiky.

  1. Uložte provedené změny. Tento krok obnoví aktuální stav aktivační události.

  1. [Znovu povolte aplikaci logiky](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Zakázat nebo povolit jednu aplikaci logiky

1. Na webu Azure Portal [najděte a otevřete aplikaci logiky](#find-logic-app).

1. V nabídce aplikace logiky vyberte **Přehled**. Vyberte si z těchto možností:

   * Na panelu nástrojů vyberte **Zakázat**.

     ![Zakázání jedné aplikace logiky na webu Azure Portal](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Pokud je aplikace logiky již zakázána, zobrazí se pouze možnost **Povolit.**

   * Na panelu nástrojů vyberte **Povolit**.

     ![Povolení jediné aplikace logiky na webu Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Pokud je aplikace logiky již povolena, zobrazí se pouze možnost **Zakázat.** 

   Portál Azure zobrazuje oznámení na hlavním panelu nástrojů Azure, které potvrzuje, zda byla vaše operace úspěšná nebo neúspěšná.

   ![Oznámení o potvrzení stavu operace](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Zakázání nebo povolení více aplikací logiky

1. Na webu Azure Portal [najděte aplikace logiky,](#find-logic-app) které chcete zakázat nebo povolit.

1. Chcete-li zkontrolovat, zda je aplikace logiky aktuálně povolená nebo zakázaná, zkontrolujte na stránce **Logic Apps** sloupec **Stav** pro tuto aplikaci logiky. 

   ![Sloupec stavu aplikace logiky](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Pokud sloupec **Stav** není viditelný, na panelu nástrojů **Logic Apps** vyberte **Vyzkoušet náhled**.

   ![Zapnutí náhledu](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. Ve sloupci zaškrtávacího políčka vyberte aplikace logiky, které chcete zakázat nebo povolit. Na panelu nástrojů vyberte **Zakázat** nebo **Povolit**.

   ![Povolení nebo zakázání více aplikací logiky na webu Azure Portal](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Po přepychu vyberte **Ano,** chcete-li pokračovat.

   Portál Azure zobrazuje oznámení na hlavním panelu nástrojů Azure, které potvrzuje, zda byla vaše operace úspěšná nebo neúspěšná.

## <a name="delete-logic-apps"></a>Odstranění aplikací logiky

Můžete [odstranit jednu aplikaci logiky](#delete-single-logic-app) nebo [odstranit více aplikací logiky současně](#delete-multiple-logic-apps) na webu Azure Portal. Aplikaci [logiky](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)můžete také odstranit v sadě Visual Studio .

Odstranění aplikace logiky ovlivní instance pracovního postupu těmito způsoby:

* Všechna probíhající a čekající spuštění pokračují až do dokončení. V závislosti na počtu těchto spuštění může tento proces nějakou dobu trvat.

* Modul Logic Apps nevytvoří ani nespustí nové instance pracovního postupu.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Odstranit jednu aplikaci logiky

1. Na webu Azure Portal [najděte a otevřete aplikaci logiky](#find-logic-app).

1. V nabídce aplikace logiky vyberte **Přehled**. Na panelu nástrojů aplikace logiky vyberte **Odstranit**.

   ![Na panelu nástrojů aplikace logiky vyberte "Odstranit"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Po zobrazení potvrzovacího pole zadejte název aplikace logiky a vyberte **Odstranit**.

   ![Potvrzení odstranění aplikace logiky](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Portál Azure zobrazuje oznámení na hlavním panelu nástrojů Azure, které potvrzuje, zda byla vaše operace úspěšná nebo neúspěšná.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Odstranění více aplikací logiky

1. Na webu Azure Portal [najděte aplikace logiky, které chcete odstranit](#find-logic-app).

1. Ve sloupci zaškrtávacího políčka vyberte aplikace logiky, které chcete odstranit. Na panelu nástrojů vyberte **Odstranit**.

   ![Odstranění více aplikací logiky](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Po otevření potvrzovacího pole zadejte `yes`a vyberte **Odstranit**.

   ![Potvrzení odstranění aplikací logiky](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Portál Azure zobrazuje oznámení na hlavním panelu nástrojů Azure, které potvrzuje, zda byla vaše operace úspěšná nebo neúspěšná.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Správa verzí aplikací logiky

Portál Azure můžete použít pro správu verzí aplikací logiky. Můžete najít historii verzí aplikace logiky a propagovat předchozí verze.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Hledání a zobrazení předchozích verzí

1. Na webu Azure Portal [najděte aplikaci logiky, kterou chcete spravovat](#find-logic-app).

1. V nabídce aplikace logiky vyberte v části **Vývojové nástroje** **možnost Verze**.

   ![V nabídce aplikace logiky vyberte "Verze" v části "Vývojové nástroje"](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Vyberte **verzi** aplikace logiky, kterou chcete spravovat ze seznamu. Chcete-li seznam filtrovat, můžete do vyhledávacího řádku zadat identifikátor **verze.**

1. Na stránce **Verze historie** se podrobnosti o předchozí verzi zobrazí v režimu jen pro čtení. Můžete vybrat mezi **návrhářem** aplikací logiky a režimy **zobrazení kódu.**

   ![Stránka verze historie pro aplikaci logiky se zobrazením kódu a zobrazením návrháře aplikací logiky](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Povýšení předchozích verzí

1. V historii verzí aplikace logiky [najděte a vyberte verzi, kterou chcete propagovat](#find-version-history).

1. Na stránce **Verze historie** vyberte **Propagovat**.

   ![Tlačítko Povýšit v historii verzí aplikace logiky](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Na stránce **Návrhář epo-aplikací,** která se otevře, upravte verzi, kterou propagujete podle potřeby. Můžete přepínat mezi režimy zobrazení **Návrhářa** a **Kódu.** Můžete také aktualizovat **parametry**, **šablony**a **konektory**.

   ![Stránka Aplikace logiky pro propagaci předchozí verze](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Chcete-li uložit všechny aktualizace a dokončit propagaci předchozí verze, vyberte **uložit**. (Chcete-li zrušit změny, vyberte **možnost Zahodit**.) 

   Při [zobrazení historie verzí aplikace logiky](#find-version-history) znovu, povýšená verze se zobrazí v horní části seznamu a má nový identifikátor.

## <a name="next-steps"></a>Další kroky

* [Monitorování aplikací logiky](monitor-logic-apps.md)
