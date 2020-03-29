---
title: Konfigurace pravidel a správa upozornění
description: Popisuje, jak konfigurovat pravidla a spravovat výstrahy v FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482982"
---
# <a name="configure-rules-and-manage-alerts"></a>Konfigurace pravidel a správa upozornění

Azure FarmBeats umožňuje vytvářet pravidla založená na obchodní logice, kromě dat senzorů, která proudí ze senzorů a zařízení nasazených ve vaší farmě. Pravidla aktivují výstrahy v systému vždy, když hodnoty senzoru překročí prahovou hodnotu. Zobrazením a analýzou výstrah vytvořených po prahových hodnotách můžete rychle pracovat s případnými problémy a získat požadovaná řešení.

## <a name="create-rule"></a>Vytvořit pravidlo

1. Na domovské stránce přejděte na **Pravidla**.
2. Vyberte **nové pravidlo**. Zobrazí se okno Nové pravidlo.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Zadejte **název pravidla** a popis **pravidla** a pak vyberte farmu z rozevírací nabídky **Vybrat farmu.**
4. Zadejte název farmy a vyberte oddíl Farma a ve stejném okně se zobrazí oddíl **Podmínky.**  

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Do **pole Podmínky**zadejte hodnoty pro **Měření**, **Operátor** a **Hodnota**.
6. Do rozevírací nabídky **Míra** zadejte název míry.
7. Výběrem **možnosti +Přidat podmínku** přidáte do pravidla další podmínky.
8. Vyberte **úroveň závažnosti**.
9. V **akci**zapněte přepínač **E-mail,** který povolí e-mailová upozornění.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Zadejte **e-mailové adresy,** na které chcete e-mailovou výstrahu odeslat, spolu s **předmětem e-mailu** a **dalšími poznámkami**.  
11. Ve **stavu pravidla**zapněte přepínač **povoleno,** abyste pravidlo povolili nebo zakázali.
    Můžete zobrazit počet zařízení, která budou mít vliv na pravidlo.
12. Chcete-li vytvořit pravidlo, vyberte **Použít.**

## <a name="view-rule"></a>Zobrazit pravidlo

Na stránce **Farm** a zobrazí se seznam dostupných pravidel. Vyberte **název pravidla**. V okně se zobrazí následující podrobnosti, které jsou použitelné pro vybrané pravidlo:
 - Název pravidla
 - Odkaz na farmu, ke které je pravidlo přidruženo
 - Datum vytvoření
 - Datum poslední aktualizace
 - Úroveň závažnosti
 - Stav pravidla
 - Seznam podmínek  
 - Počet zařízení ovlivněných pravidlem

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Úprava pravidla

Chcete-li pravidlo upravit, postupujte takto:

1. Na domovské stránce vyberte **Pravidla** v levé navigační nabídce.
   Zobrazí se okno pravidel.
2. Vyberte pravidlo, pro které chcete upravit.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Z panelu akcí vyberte **Upravit,** zobrazí se okno **Upravit pravidlo.**

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Změňte **název pravidla**a popis **pravidla** a vyberte farmu z rozevírací nabídky **Vybrat farmu.**
5. Zadejte název farmy a vyberte farmu a **podmínky** se zobrazí ve stejném okně.  
6. V **podmínkách**upravte **measure**, **Operator** a **Value**.
7. Do rozevírací nabídky **Míra** zadejte název míry.
8. Vyberte **+Přidat podmínku,** chcete-li do pravidel přidat nebo upravit podmínky.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Vyberte **úroveň závažnosti**.  
10. V **akci**zapněte přepínač **E-mail,** který povolí e-mailová upozornění.
11. Upravte **e-mailové adresy,** které chcete odeslat e-mailové upozornění, spolu s **předmětem e-mailu** a **dalšími poznámkami**.  
12. Ve **stavu pravidla**zapněte přepínač **povoleno,** abyste pravidlo povolili nebo zakázali.
Můžete zobrazit počet zařízení, která budou tímto pravidlem ovlivněna.
13. Vyberte **Použít,** chcete-li pravidlo upravit.

## <a name="change-rule-status"></a>Změnit stav pravidla

Chcete-li změnit stav pravidla, postupujte takto:

1. Na domovské stránce vyberte **Pravidla** v levé navigační nabídce. Zobrazí se okno pravidel.
2. Vyberte pravidlo, u kterého chcete změnit stav.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Na panelu akcí vyberte **Změnit stav.** Zobrazí se okno **Změnit stav.**

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Změňte stav pravidla pomocí přepínače **Změnit stav.**
   Můžete zobrazit počet zařízení, která budou ovlivněny pravidlo.
4. Vyberte **Použít,** chcete-li změnit stav pravidla.

## <a name="delete-rule"></a>Odstranění pravidla

Chcete-li pravidlo odstranit, postupujte takto:

1. Na domovské stránce vyberte **Pravidla** v levé navigační nabídce. Zobrazí se okno pravidel.
2. Vyberte pravidlo, pro které chcete odstranit.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Na panelu akcí vyberte **Odstranit.**

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Zobrazí se dialogové okno **Odstranit pravidlo.** Vyberte **Odstranit**.
