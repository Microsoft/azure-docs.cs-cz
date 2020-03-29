---
title: Instalace aktualizace 0.5 na virtuálním poli StorSimple | Dokumenty společnosti Microsoft
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
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: e09ff4bcbc141b1a1f80bc278918a291639c1885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61445299"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0.5 na virtuální morovém poli StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0.5 na virtuální pole StorSimple prostřednictvím místního webového uživatelského prostředí a prostřednictvím portálu Azure. Chcete-li, aby bylo virtuální pole StorSimple aktuální, je třeba použít aktualizace softwaru nebo opravy hotfix.

Před aktualizací doporučujeme nejprve převzít svazky nebo sdílené složky do offline na hostiteli a potom na zařízení. Minimalizuje se tak možnost poškození dat. Po offline svazky nebo sdílené složky byste měli také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 0.5 odpovídá verzi softwaru **10.0.10290.0** v zařízení. Informace o novince v této aktualizaci naleznete v [poznámkách k verzi aktualizace 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Pokud používáte aktualizaci 0.2 nebo novější, doporučujeme nainstalovat aktualizace přes portál Azure. Pokud používáte verzi softwaru Aktualizace 0.1 nebo GA, je nutné k instalaci aktualizace 0.5 použít metodu opravy hotfix prostřednictvím místního webového uživatelského prostředí.
>
> - Nezapomeňte, že instalace aktualizace nebo opravy hotfix restartuje zařízení. Vzhledem k tomu, že virtuální pole StorSimple je zařízení s jedním uzly, je přerušeno jakékoli probíhající vstupně-up a prostoje zařízení.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud se sem schovali aktualizace 0.2 a novější, doporučujeme nainstalovat aktualizace prostřednictvím portálu Azure. Postup portálu vyžaduje, aby uživatel prohledává, stahuje a poté aktualizuje aktualizace. Tento postup trvá přibližně 7 minut. Chcete-li nainstalovat aktualizaci nebo opravu hotfix, proveďte následující kroky.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace přejděte do služby Správce zařízení StorSimple. Vyberte **Zařízení** a pak vyberte a klikněte na zařízení, které jste právě aktualizovali. Přejděte **na nastavení > Správa aktualizací > zařízení**. Zobrazená verze softwaru by měla být **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského prostředí

Při použití místního webového uživatelského prostředí existují dva kroky:

* Stažení aktualizace nebo opravy hotfix
* Instalace aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte aplikaci [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer a přejděte na .

2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo opravy hotfix, kterou chcete stáhnout, do znalostní báze Knowledge Base (KB). Zadejte **hodnotu 4021576** pro aktualizaci 0.5 a klepněte na tlačítko **Hledat**.
   
    Zobrazí se výpis opravy hotfix, například **Aktualizace virtuálního pole StorSimple 0.5**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Klepněte na tlačítko **Stáhnout**. 

5. Měli byste vidět dva soubory ke stažení, *.msu* a *.cab* soubor. Stáhněte si každý z těchto souborů do složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, ve které jsou soubory umístěny.
    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Zobrazí se následující:
    -  Soubor `WindowsTH-KB3011067-x64`samostatného balíčku společnosti Microsoft Update . Tento soubor se používá k aktualizaci softwaru zařízení.
    - Ženeva Monitoring `GenevaMonitoringAgentPackageInstaller`Agent package file . Tento soubor se používá k aktualizaci agenta služby Monitorování a diagnostika (MDS). Poklepejte na soubor kabiny. Zobrazí se soubor MSI. Vyberte soubor, klikněte pravým tlačítkem myši a potom soubor **extrahujte.** K aktualizaci agenta použijete soubor _MSI._

        ![Extrahovat soubor aktualizace agenta MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo opravy hotfix se ujistěte, že je aktualizace nebo oprava hotfix stažena místně na hostiteli nebo dostupná prostřednictvím sdílené síťové složky.

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo Update 0.1 verze softwaru. Tento postup trvá méně než 2 minuty. Chcete-li nainstalovat aktualizaci nebo opravu hotfix, proveďte následující kroky.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském prostředí přejděte na**stránku Aktualizace softwaru** **údržby** > .
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. V **části Aktualizovat cestu k souboru**zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít na instalační soubor aktualizace nebo opravy hotfix, pokud je umístěn do sdílené síťové složky. Klikněte na **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tomu, že se jedná o zařízení s jedním uzlovým uzly, po instalaci aktualizace se zařízení restartuje a dojde k prostojům. Klikněte na ikonu šeku.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Aktualizace se spustí. Po úspěšné aktualizaci zařízení se restartuje. Místní uI není přístupné v této době trvání.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po dokončení restartování se dostanete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, přejděte v místním webovém uživatelském rozhraní na**stránku** **Maintenance** > Software Update . Zobrazená verze softwaru by měla být **10.0.0.0.0.10290.0** pro aktualizaci 0.5.
   
   > [!NOTE]
   > Verze softwaru hlásíme trochu jiným způsobem v místním webovém uživatelském prostředí a na portálu Azure. Například místní webové uživatelské prostředí hlásí **10.0.0.0.10290** a portál Azure hlásí **10.0.10290.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. Dalším krokem je aktualizace agenta MDS. Na stránce **Aktualizace softwaru** přejděte na cestu k `GenevaMonitoringAgentPackageInstaller.msi` **aktualizaci souboru** a vyhledejte soubor. Opakujte kroky 2-4. Po restartování virtuálního pole se přihlaste do místního webového uživatelského prostředí.

Aktualizace je nyní dokončena.

## <a name="next-steps"></a>Další kroky

Další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

