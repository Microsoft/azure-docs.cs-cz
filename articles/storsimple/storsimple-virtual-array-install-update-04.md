---
title: Instalace aktualizací na virtuálním poli StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak pomocí webového uživatelského uživatelského prostředí StorSimple Virtual Array použít aktualizace pomocí portálu Azure a metody opravy hotfix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: b67fcb82bdcc94d7faeceedb7420a869e6578cad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436310"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0.4 na virtuální morovém poli StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0.4 na virtuální pole StorSimple prostřednictvím místního webového uživatelského prostředí a prostřednictvím portálu Azure. Chcete-li, aby bylo virtuální pole StorSimple aktuální, je třeba použít aktualizace softwaru nebo opravy hotfix. 

Nezapomeňte, že instalace aktualizace nebo opravy hotfix restartuje zařízení. Vzhledem k tomu, že virtuální pole StorSimple je zařízení s jedním uzly, je přerušeno jakékoli probíhající vstupně-up a prostoje zařízení. 

Před aktualizací doporučujeme nejprve převzít svazky nebo sdílené složky do offline na hostiteli a potom na zařízení. Minimalizuje se tak možnost poškození dat.

> [!IMPORTANT]
> Pokud používáte verzi softwaru Aktualizace 0.1 nebo GA, je nutné k instalaci aktualizace 0.3 použít metodu opravy hotfix prostřednictvím místního webového uživatelského prostředí. Pokud používáte aktualizaci 0.2 nebo novější, doporučujeme nainstalovat aktualizace přes portál Azure.
 

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského prostředí

Při použití místního webového uživatelského prostředí existují dva kroky:

* Stažení aktualizace nebo opravy hotfix
* Instalace aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte aplikaci [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer a přejděte na .

2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo opravy hotfix, kterou chcete stáhnout, do znalostní báze Knowledge Base (KB). Zadejte **3216577** pro aktualizaci 0.4 a klepněte na tlačítko **Hledat**.
   
    Zobrazí se výpis opravy hotfix, například **Aktualizace virtuálního pole StorSimple 0.4**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Klikněte na **Přidat**. Aktualizace se přidá do košíku.

5. Klikněte na **Zobrazit košík**.

6. Klepněte na tlačítko **Stáhnout**. Zadejte místní umístění, do kterého chcete aktualizace stáhnout, nebo do něj přejděte pomocí tlačítka **Procházet**. Aktualizace se stáhnou do zadaného umístění do podsložky se stejným názvem, jako má aktualizace. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

7. Otevřete zkopírovanou složku, měli byste vidět `WindowsTH-KB3011067-x64`soubor samostatného balíčku Microsoft Update . Tento soubor slouží k instalaci aktualizace nebo opravy hotfix.

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo opravy hotfix se ujistěte, že je aktualizace nebo oprava hotfix stažena místně na hostiteli nebo dostupná prostřednictvím sdílené síťové složky. 

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo Update 0.1 verze softwaru. Tento postup trvá méně než 2 minuty. Chcete-li nainstalovat aktualizaci nebo opravu hotfix, proveďte následující kroky.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském prostředí přejděte na**stránku Aktualizace softwaru** **údržby** > .
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update1m.png)

2. V **části Aktualizovat cestu k souboru**zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít na instalační soubor aktualizace nebo opravy hotfix, pokud je umístěn do sdílené síťové složky. Klikněte na **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tomu, že se jedná o zařízení s jedním uzlovým uzly, po instalaci aktualizace se zařízení restartuje a dojde k prostojům. Klikněte na ikonu šeku.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update3m.png)

4. Aktualizace se spustí. Po úspěšné aktualizaci zařízení se restartuje. Místní uI není přístupné v této době trvání.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po dokončení restartování se dostanete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, přejděte v místním webovém uživatelském rozhraní na**stránku** **Maintenance** > Software Update . Zobrazená verze softwaru by měla být **10.0.0.0.0.10289.0** pro aktualizaci 0.4.
   
   > [!NOTE]
   > Verze softwaru hlásíme trochu jiným způsobem v místním webovém uživatelském prostředí a na portálu Azure. Například místní webové uživatelské prostředí hlásí **10.0.0.0.0.10289** a portál Azure hlásí **10.0.10289.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud se sem schovali aktualizace 0.2 a novější, doporučujeme nainstalovat aktualizace prostřednictvím portálu Azure. Postup portálu vyžaduje, aby uživatel prohledává, stahuje a poté aktualizuje aktualizace. Tento postup trvá přibližně 7 minut. Chcete-li nainstalovat aktualizaci nebo opravu hotfix, proveďte následující kroky.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace (jak je uvedeno stav úlohy na 100 %), přejděte do služby StorSimple Device Manager. Vyberte **Zařízení** a pak vyberte a klikněte na zařízení, které chcete aktualizovat ze seznamu zařízení připojených k této službě. V okně **Nastavení** přejděte do části **Spravovat** a vyberte **Aktualizace zařízení**. Zobrazená verze softwaru by měla být **10.0.10289.0**.


## <a name="next-steps"></a>Další kroky

Další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

