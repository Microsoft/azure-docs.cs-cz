---
title: "Vytvoření, odkaz, odstranit nebo přesunout integrace účtu v Azure logic apps | Microsoft Docs"
description: "Postup vytvoření účtu integrace a propojit s logic apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; divswa
ms.openlocfilehash: fb1d0ceb26c5ed792f22051e2af10a7572200bdc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="what-is-an-integration-account"></a>Co je integrace účet?

Účet integrace poskytuje způsob pro váš podnik integrace aplikací, konkrétně logiku aplikace přistupovat ke a spravovat artefaktů B2B, například obchodní partnery, smlouvy, map, schémata, certifikáty a tak dále. Pokud chcete poskytnout tento přístup, propojte si účet integraci do aplikace logiky po a ujistěte se, že mají obě integrace účet a logiku aplikace *stejného umístění Azure*.

## <a name="create-an-integration-account"></a>Vytvoření účtu pro integraci

1. Přihlaste se na web [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. Z hlavní nabídky Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Vytvoření účtu integrace](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. V horní části stránky, zvolte **přidat**.

   ![Vyberte Přidat](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Název účtu integrace a vyberte předplatné Azure, kterou chcete použít. Můžete buď vytvořit novou **skupiny prostředků** nebo vyberte existující skupinu prostředků. Vyberte **umístění** k hostování účtu integrace a **cenová úroveň**. Až budete připraveni, zvolte **vytvořit**.

   ![Zadejte podrobnosti pro váš účet integrace](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure zřídí účtu integrace ve vybraném umístění, které by se měla dokončit během jedné minuty.

5. Aktualizujte stránku. Zobrazí váš nový účet integrace, které jsou uvedené.

   ![Zobrazí se váš nový účet integrace](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

V dalším kroku propojte integrace účet, který jste vytvořili pro svou aplikaci logiky. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Účet integrace propojit aplikaci logiky

Umožnit logiky aplikace přístup k artefaktům B2B, jako je například obchodní partnery, smlouvy, mapy a schémata ve vašem účtu integrace propojit účet integrace do aplikace logiky. 

1. Na portálu Azure vyberte svou aplikaci logiky a zkontrolujte umístění svou aplikaci logiky.

   ![Vyberte svou aplikaci logiky, zkontrolujte umístění](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. V části **nastavení**, vyberte **integrace účet**.

   ![Vyberte "Účet integrace"](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Z **vyberte účet, integrace** seznamu, vyberte integrace účet, který chcete propojit do aplikace logiky. K dokončení propojení, zvolte **Uložit**.

   ![Vyberte svůj účet integrace](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   Zobrazí se oznámení, že zobrazuje svoji integraci do aplikace logiky je propojený účet a všechny artefakty ve vašem účtu integrace jsou nyní k dispozici pro svou aplikaci logiky.

   ![Aplikace logiky je propojený s vaším účtem integrace](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Teď, když je propojené účtu integrace aplikace logiky, můžete použít konektory B2B ve vaší aplikaci logiky. Některé běžné konektory B2B zahrnují ověření XML a plochý soubor kódování nebo dekódování.  

## <a name="delete-your-integration-account"></a>Odstranění účtu integrace

1. Z hlavní nabídky Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Najít váš účet integrace](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Vyberte integrační účet, který chcete odstranit.

    ![Vyberte účet integrace odstranit](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. V nabídce zvolte **odstranit**.

    ![Zvolte "Odstranit"](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. Potvrďte volbu Odstranit účet integrace.

## <a name="move-your-integration-account"></a>Přesunutí účtu integrace

Pokud chcete přesunout účet integrace Azure jiné předplatné nebo prostředek skupiny, postupujte podle těchto kroků. Po přesunutí účtu integrace, ujistěte se, aktualizovat všechny skripty používat nové ID prostředku.

1. Z hlavní nabídky Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Najít váš účet integrace](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Vyberte integrační účet, který chcete přesunout. V části **nastavení**, zvolte **vlastnosti**.

   ![Vyberte účet integrace přesunout. V části Nastavení vyberte možnost Vlastnosti](./media/logic-apps-enterprise-integration-accounts/move.png)

3. Změňte skupinu prostředků nebo předplatného Azure, který je spojen s vaším účtem integrace.

   ![Vyberte skupiny prostředků změnu nebo změnu předplatného](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Další postup

* [Další informace o smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md "Další informace o integraci smlouvy enterprise")  

