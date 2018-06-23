---
title: Správa verzí v aplikacích LEOŠ v Azure | Microsoft Docs
description: Informace o správě verzí v aplikacích jazyka Principy (LEOŠ).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: v-geberr
ms.openlocfilehash: 672f7991be0fc236e39daf7d1ce1d6080b31815b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342471"
---
# <a name="manage-versions"></a>Správa verzí

Pokaždé, když pracujete v modelu, vytvořit jinou [verze](luis-concept-version.md) aplikace. 

## <a name="set-active-version"></a>Nastavit verzi active
Pracovat s verzemi, otevřete aplikaci tak, že vyberete svůj název na **Moje aplikace** a pak vyberte **nastavení** na horním panelu.

![Verze stránky](./media/luis-how-to-manage-versions/settings.png)

**Nastavení** stránce umožňuje nakonfigurovat nastavení pro celou aplikaci, včetně verzí a spolupracovníky. 

## <a name="clone-a-version"></a>Klonování verze
1. Na **nastavení** stránky, po nastavení aplikace a spolupracovníci oddíly, najde řádek s verzí, které chcete klonovat. Vyberte se třemi tečkami (...) v pravém rohu. 

    ![Verze řádku vlastnosti](./media/luis-how-to-manage-versions/version-section.png)

2. Vyberte **klon** ze seznamu.

    ![Volba vlastnosti verze řádku](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. V **klon verze** dialogové okno, zadejte název pro novou verzi, jako je například "0,2".

   ![Dialogové okno verze klonování](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Verze ID může obsahovat pouze znaky, číslice nebo '.' a nesmí být delší než 10 znaků.
 
 Novou verzi se zadaným názvem je vytvořena a nastavena jako aktivní verze.
 
  ![Verze je vytvořen a přidán do seznamu](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Jak je vidět na předchozím obrázku, publikovanou verzi souvisí s barevnou značku, označující typ slotu, kde se publikoval: produkční (zelený), pracovní (červený), obě (černá). Zobrazí se data školení a publikování pro každou publikovanou verzi.

## <a name="set-active-version"></a>Nastavit verzi active
1. Na **nastavení** stránky v **verze** vyberte se třemi tečkami (...) v pravém horním.

2. V rozevíracím seznamu vyberte **nastaveno jako aktivní**.

    ![Nastavit verzi active](./media/luis-how-to-manage-versions/set-active-version.png)

    Aktivní verze je označený pomocí světla blue barev, jak je znázorněno na následujícím snímku obrazovky:

    ![Aktivní verze](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Import verze
Verze můžete importovat ze souboru JSON. Jakmile importujete na verzi, nové verze stane aktivní verze.

**Chcete-li importovat verze:**

1. Na **nastavení** vyberte **Import nové verze** tlačítko.

    ![Tlačítko Import](./media/luis-how-to-manage-versions/import-version.png)

2. Vyberte **Procházet** a vyberte soubor JSON.

    ![Dialogové okno importu verze](./media/luis-how-to-manage-versions/import-version-dialog.png)

Stačí nastavit ID verze, pokud již existuje verze v souboru JSON v aplikaci.

## <a name="export-version"></a>Export verze
Verze můžete exportovat do souboru JSON.

**Export z verze:**

1. Na **nastavení** stránky v **verze** vyberte se třemi tečkami (...) v pravém horním.

2. Vyberte **exportovat** rozbalovací seznam akcí a vyberte, kam chcete uložit soubor.

## <a name="delete-a-version"></a>Odstranění verze
Můžete odstranit verze, ale budete muset zachovat alespoň jednu verzi aplikace. Můžete odstranit všechny verze kromě aktivní verze. 

1. Na **nastavení** stránky v **verze** vyberte se třemi tečkami (...) v pravém horním.

2. Vyberte **odstranit** rozbalovací seznam akcí a vyberte, kam chcete uložit soubor.

    ![Odstranit potvrzení verze](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Přejmenovat verze
Verze můžete přejmenovat, dokud název verze není již používán.  

1. Na **nastavení** stránky v **verze** vyberte se třemi tečkami (...) v pravém horním.

2. Vyberte **přejmenovat** v rozbalovací seznam akcí.

3. Zadejte název nové verze a vyberte **provádí**.

    ![Přejmenujte potvrzení verze](./media/luis-how-to-manage-versions/rename-popup.png) 
