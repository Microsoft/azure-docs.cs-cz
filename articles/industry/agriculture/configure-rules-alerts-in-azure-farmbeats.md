---
title: Konfigurace pravidel a správa upozornění
description: Popisuje, jak nakonfigurovat pravidla a spravovat výstrahy v FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75482982"
---
# <a name="configure-rules-and-manage-alerts"></a>Konfigurace pravidel a správa upozornění

Azure FarmBeats umožňuje vytvářet pravidla založená na obchodní logice kromě dat senzorů, která se předávají ze senzorů a zařízení nasazených ve vaší farmě. Pravidla aktivují výstrahy v systému, kdykoli hodnoty snímačů překračují prahovou hodnotu. Zobrazením a analýzou výstrah vytvořených po prahových hodnotách můžete rychle působit na jakékoli problémy a získat požadovaná řešení.

## <a name="create-rule"></a>Vytvořit pravidlo

1. Na domovské stránce přejít na **pravidla**.
2. Vyberte **nové pravidlo**. Zobrazí se okno nové pravidlo.

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Zadejte **název pravidla** a **Popis pravidla** a potom vyberte farmu z rozevírací nabídky **Vybrat farmu** .
4. Zadáním názvu farmy vyberte oddíl farmy a **podmínky** , který se zobrazí ve stejném okně.  

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. V části **podmínky**zadejte hodnoty pro **míry**, **operátor** a **hodnotu**.
6. Do rozevírací nabídky **míra** zadejte název míry.
7. Vyberte **+ Přidat podmínku** a přidejte do pravidla další podmínky.
8. Vyberte **úroveň závažnosti**.
9. V **akci**přepněte na přepínací tlačítko s **povoleným e** -mailem a povolte e-mailová upozornění.

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Zadejte e-mailové **adresy** , na které chcete e-mailové upozornění poslat, a také **Předmět e-mailu** a **Další poznámky**.  
11. V poli **stav pravidla**přepněte na přepínací tlačítko **povoleno** a povolte nebo zakažte pravidlo.
    Můžete zobrazit počet zařízení, na která bude pravidlo ovlivněno.
12. Pro vytvoření pravidla vyberte **použít** .

## <a name="view-rule"></a>Zobrazit pravidlo

Na stránce **farma** se zobrazí seznam dostupných pravidel. Vyberte **název pravidla**. V okně se zobrazí následující podrobnosti, které se vztahují k vybranému pravidlu:
 - Název pravidla
 - Odkaz na farmu, ke které je pravidlo přidruženo
 - Datum vytvoření
 - Datum poslední aktualizace
 - Úroveň závažnosti
 - Stav pravidla
 - Seznam podmínek  
 - Počet zařízení ovlivněných pravidlem

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Úprava pravidla

Pravidlo upravíte pomocí následujících kroků:

1. Na domovské stránce vyberte **pravidla** z levé navigační nabídky.
   Zobrazí se okno pravidla.
2. Vyberte pravidlo, pro které chcete upravit.

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Na panelu akcí vyberte **Upravit** . otevře se okno **Upravit pravidlo** .

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Změňte **název pravidla**a **Popis pravidla** a potom vyberte farmu z rozevírací nabídky **Vybrat farmu** .
5. Zadáním názvu farmy vyberte farmu a **podmínky** se zobrazí ve stejném okně.  
6. V **podmínkách**upravte **míru**, **operátor** a **hodnotu**.
7. Do rozevírací nabídky **míra** zadejte název míry.
8. Vyberte **+ Přidat podmínku** a přidejte nebo upravte podmínky pravidel.

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Vyberte **úroveň závažnosti**.  
10. V **akci**přepněte na přepínací tlačítko s **povoleným e** -mailem a povolte e-mailová upozornění.
11. Upravte e-mailové **adresy** , které chcete odeslat e-mailové upozornění, spolu s **subjektem E-mail** a **dalšími poznámkami**.  
12. V poli **stav pravidla**přepněte na přepínací tlačítko **povoleno** a povolte nebo zakažte pravidlo.
Můžete zobrazit počet zařízení, u kterých bude toto pravidlo ovlivněno.
13. Pokud chcete pravidlo upravit, vyberte **použít** .

## <a name="change-rule-status"></a>Změnit stav pravidla

Chcete-li změnit stav pravidla, použijte následující postup:

1. Na domovské stránce vyberte **pravidla** z levé navigační nabídky. Zobrazí se okno pravidla.
2. Vyberte pravidlo, pro které chcete změnit stav.

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Na panelu akcí vyberte **změnit stav** . Zobrazí se okno **stav změny** .

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Změňte stav pravidla pomocí přepínacího tlačítka **změnit stav** .
   Můžete zobrazit počet zařízení, na která bude pravidlo ovlivněno.
4. Chcete-li změnit stav pravidla, vyberte možnost **použít** .

## <a name="delete-rule"></a>Odstranění pravidla

Pravidlo odstraníte pomocí následujících kroků:

1. Na domovské stránce vyberte **pravidla** z levé navigační nabídky. Zobrazí se okno pravidla.
2. Vyberte pravidlo, pro které chcete odstranit.

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Na panelu akcí vyberte **Odstranit** .

    ![Beats farmy projektu](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Zobrazí se dialogové okno **Odstranit pravidlo** . Vyberte **Odstranit**.
