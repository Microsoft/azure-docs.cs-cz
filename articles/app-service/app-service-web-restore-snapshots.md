---
title: Obnovení aplikace ze snímku
description: Přečtěte si, jak obnovit aplikaci ze snímku. Obnovte z neočekávané ztráty dat na úrovni Premium pomocí automatických stínových kopií.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: f7edb632559dc8da2de32c58d994a7c51b1b09e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169966"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Obnovení aplikace v Azure ze snímku
V tomto článku se dozvíte, jak obnovit aplikaci v [Azure App Service](../app-service/overview.md) ze snímku. Můžete obnovit aplikaci do předchozího stavu, a to na základě jedné z snímků vaší aplikace. Není nutné povolit zálohování snímků, platforma automaticky uloží snímek všech aplikací pro účely obnovení dat.

Snímky jsou přírůstkové stínové kopie a nabízejí několik výhod oproti pravidelným [zálohám](manage-backup.md):
- Kvůli zámkům souborů se nenašly žádné chyby kopírování souborů.
- Žádné omezení velikosti úložiště
- Není nutná žádná konfigurace.

Obnovení ze snímků je k dispozici pro aplikace běžící na úrovni **Premium** nebo vyšší. Informace o škálování aplikace najdete v tématu [horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md).

## <a name="limitations"></a>Omezení

- Tato funkce je aktuálně ve verzi Preview.
- Můžete obnovit pouze do stejné aplikace nebo do slotu patřícího k dané aplikaci.
- Při obnovení App Service zastaví cílovou aplikaci nebo cílovou pozici.
- App Service zachovává tři měsíce na snímky pro účely obnovení dat platformy.
- Snímky můžete obnovit jenom za posledních 30 dní.
- App Services spuštěné v App Service Environment nepodporují snímky.
 

## <a name="restore-an-app-from-a-snapshot"></a>Obnovení aplikace ze snímku

1. Na stránce **Nastavení** aplikace v [Azure Portal](https://portal.azure.com)klikněte na **zálohování** . zobrazí se stránka **zálohy** . Pak klikněte na **obnovit** v části **snímek (Preview)** .
   
    ![Snímek obrazovky, který ukazuje, jak obnovit aplikaci ze zálohy snímku.](./media/app-service-web-restore-snapshots/1.png)

2. Na stránce **obnovení** vyberte snímek, který chcete obnovit.
   
    ![Snímek obrazovky, který ukazuje, jak vybrat snímek, který se má obnovit ](./media/app-service-web-restore-snapshots/2.png)
   
3. Zadejte cíl pro obnovení aplikace v umístění pro **obnovení**.
   
    ![Snímek obrazovky, který ukazuje, jak určit cíl obnovení.](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Pokud zvolíte možnost **přepsat**, všechna stávající data v aktuálním systému souborů vaší aplikace budou smazána a přepsána. Než kliknete na **OK**, ujistěte se, že to je to, co chcete udělat.
   > 
   > 
      
   > [!Note]
   > Kvůli aktuálním technickým omezením můžete jenom obnovit aplikace jenom ve stejné jednotce škálování. Toto omezení bude v budoucí verzi odebráno.
   > 
   > 
   
    Můžete vybrat **existující aplikaci** , která se má obnovit do slotu. Než použijete tuto možnost, měli byste už ve své aplikaci vytvořit slot.

4. Můžete zvolit obnovení konfigurace lokality.
   
    ![Snímek obrazovky, který ukazuje, jak obnovit konfiguraci lokality.](./media/app-service-web-restore-snapshots/4.png)

5. Klikněte na **OK**.
