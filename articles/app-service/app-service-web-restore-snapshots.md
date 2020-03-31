---
title: Obnovení aplikace ze snímku
description: Přečtěte si, jak obnovit aplikaci ze snímku. Obnovte se z neočekávané ztráty dat na úrovni Premium pomocí automatických stínových kopií.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: b17a49535541c8f75f65cdbe9986a895427f3877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255153"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Obnovení aplikace v Azure ze snímku
Tento článek ukazuje, jak obnovit aplikaci ve [službě Azure App Service](../app-service/overview.md) ze snímku. Aplikaci můžete obnovit do předchozího stavu na základě jednoho ze snímků aplikace. Není nutné povolit zálohování snímků, platforma automaticky uloží snímek všech aplikací pro účely obnovení dat.

Snímky jsou přírůstkové stínové kopie a nabízejí několik výhod oproti pravidelným [zálohováním](manage-backup.md):
- Žádné chyby kopírování souborů z důvodu uzamčení souborů.
- Žádné omezení velikosti úložiště.
- Není nutná žádná konfigurace.

Obnovení ze snímků je k dispozici pro aplikace spuštěné na úrovni **Premium** nebo vyšší. Informace o škálování aplikace najdete [v tématu Vertika navýšit kapacitu aplikace v Azure](manage-scale-up.md).

## <a name="limitations"></a>Omezení

- Funkce je aktuálně ve verzi Preview.
- Můžete obnovit pouze do stejné aplikace nebo do slotu, který patří do této aplikace.
- Služba App Service zastaví cílovou aplikaci nebo cílový slot při obnovení.
- Služba App Service uchovává snímky v hodnotě tří měsíců pro účely obnovení dat platformy.
- Snímky lze obnovit pouze za posledních 30 dní.
- Služby aplikací spuštěné v prostředí služby App Service nepodporují snímky.
 

## <a name="restore-an-app-from-a-snapshot"></a>Obnovení aplikace ze snímku

1. Na stránce **Nastavení** aplikace na [webu Azure Portal](https://portal.azure.com)klikněte na **Zálohy** a zobrazte stránku **Zálohy.** Potom klikněte na **Obnovit** v části **Snímek(Náhled).**
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Na stránce **Obnovení** vyberte snímek, který chcete obnovit.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Zadejte cíl pro obnovení aplikace v **cíli obnovení**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Pokud zvolíte **Přepsat**, všechna existující data v aktuálním systému souborů aplikace se smaže a přepíší. Než klepnete na tlačítko **OK**, ujistěte se, že je to, co chcete udělat.
   > 
   > 
      
   > [!Note]
   > Vzhledem k aktuálním technickým omezením můžete obnovit pouze aplikace ve stejné jednotce škálování. Toto omezení bude odebráno v budoucí verzi.
   > 
   > 
   
    Můžete vybrat **existující aplikace** obnovit do patice. Než použijete tuto možnost, měli jste už v aplikaci vytvořit patici.

4. Můžete obnovit konfiguraci webu.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klikněte na tlačítko **OK**.
