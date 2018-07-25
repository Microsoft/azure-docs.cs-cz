---
title: Správa verzí služby LUIS aplikací v Azure | Dokumentace Microsoftu
description: Zjistěte, jak spravovat verze v aplikacích Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226590"
---
# <a name="manage-versions"></a>Správa verzí

Pokaždé, když pracujete na modelu, vytvořit jiný [verze](luis-concept-version.md) aplikace. 

## <a name="set-active-version"></a>Nastavit aktivní verzi
Pro práci s verzí, otevřete aplikaci tak, že vyberete jeho název na **Moje aplikace** stránce a pak vyberte **nastavení** v horním panelu.

![Verze stránky](./media/luis-how-to-manage-versions/settings.png)

**Nastavení** stránce umožňuje nakonfigurovat nastavení pro celou aplikaci, včetně verzí a spolupracovníky. 

## <a name="clone-a-version"></a>Klonovat verze
1. Na **nastavení** stránky, po nastavení aplikací a spolupracovníci oddíly, vyhledejte řádek s verzí, které chcete naklonovat. Vyberte tři tečky (***...*** ) tlačítko v pravém rohu. 

    ![Vlastnosti verze řádku](./media/luis-how-to-manage-versions/version-section.png)

2. Vyberte **klonování** ze seznamu.

    ![Volba vlastnosti verze řádku](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. V **klonování verze** dialogové okno, zadejte název pro novou verzi, jako je například "0.2".

   ![Dialogové okno verze klonování](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Verze ID může obsahovat pouze znaky, číslice nebo '.' a nesmí být delší než 10 znaků.
 
 Nová verze se zadaným názvem je vytvořen a nastavit jako aktivní verze.
 
  ![Verze je vytvořen a přidán do seznamu](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Jak je znázorněno na předchozím obrázku, publikovanou verzi souvisí s barevné značky, určující typ slot, kde se publikoval: produkčního prostředí (zelená), pracovní (červený) a obě (černá). Školení a publikování kalendářní data se zobrazí pro každou publikovanou verzi.

## <a name="set-active-version"></a>Nastavit aktivní verzi
1. Na **nastavení** stránku, **verze** seznamu, vyberte tři tečky (***...*** ) tlačítko úplně vpravo.

2. V rozevíracím seznamu vyberte **nastavit jako aktivní**.

    ![Nastavit aktivní verzi](./media/luis-how-to-manage-versions/set-active-version.png)

    Aktivní verze je zvýrazněná světle modrou barvou, jak je znázorněno na následujícím snímku obrazovky:

    ![Aktivní verze](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Import verze
Verze můžete importovat ze souboru JSON. Jakmile dokončíte import verze, nová verze stane aktivní verze.

**Chcete-li importovat verze:**

1. Na **nastavení** stránce **Import nové verze** tlačítko.

    ![Tlačítko Import](./media/luis-how-to-manage-versions/import-version.png)

2. Vyberte **Procházet** a zvolte soubor JSON.

    ![Dialogové okno importu verze](./media/luis-how-to-manage-versions/import-version-dialog.png)

Stačí nastavit ID verze, pokud už existuje verze v souboru JSON v aplikaci.

## <a name="export-version"></a>Export verze
Verze můžete exportovat do souboru JSON.

**Chcete-li exportovat verze:**

1. Na **nastavení** stránku, **verze** seznamu, vyberte tři tečky (***...*** ) tlačítko úplně vpravo.

2. Vyberte **exportovat** v rozbalovací seznam akcí a vyberte, kam chcete soubor uložit.

## <a name="delete-a-version"></a>Odstranit verzi
Můžete odstranit verze, ale budete muset mít alespoň jednu verzi aplikace. Můžete odstranit všechny verze, s výjimkou aktivní verze. 

1. Na **nastavení** stránku, **verze** seznamu, vyberte tři tečky (***...*** ) tlačítko úplně vpravo.

2. Vyberte **odstranit** v rozbalovací seznam akcí a vyberte, kam chcete soubor uložit.

    ![Verze potvrzení odstranění](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Přejmenovat na verzi
Verze můžete přejmenovat, jako název verze již není používán.  

1. Na **nastavení** stránku, **verze** seznamu, vyberte tři tečky (***...*** ) tlačítko úplně vpravo.

2. Vyberte **přejmenovat** v rozbalovací seznam akcí.

3. Zadejte název nové verze a vyberte **provádí**.

    ![Přejmenovat potvrzení verze](./media/luis-how-to-manage-versions/rename-popup.png) 
