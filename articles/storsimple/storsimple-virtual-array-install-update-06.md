---
title: Instalace aktualizace 0.6 na virtuálním poli StorSimple | Dokumenty společnosti Microsoft
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 5f0be5d8378cd1640d3052f2e56c8161e2c0b203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116887"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0.6 na virtuální morovém poli StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0.6 na virtuální pole StorSimple prostřednictvím místního webového uživatelského prostředí a prostřednictvím portálu Azure. Aktualizace softwaru nebo opravy hotfix použijete, aby vaše virtuální pole StorSimple bylo aktuální.

Před aktualizací doporučujeme nejprve převzít svazky nebo sdílené složky do offline na hostiteli a potom na zařízení. Minimalizuje se tak možnost poškození dat. Po offline svazky nebo sdílené složky byste měli také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 0.6 odpovídá verzi softwaru **10.0.10293.0** v zařízení. Informace o novince v této aktualizaci naleznete v [poznámkách k verzi aktualizace 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Pokud používáte aktualizaci 0.2 nebo novější, doporučujeme nainstalovat aktualizace přes portál Azure. Pokud používáte verzi softwaru Aktualizace 0.1 nebo GA, je nutné k instalaci aktualizace 0.6 použít metodu opravy hotfix prostřednictvím místního webového uživatelského prostředí.
>
> - Nezapomeňte, že instalace aktualizace nebo opravy hotfix restartuje zařízení. Vzhledem k tomu, že virtuální pole StorSimple je zařízení s jedním uzly, je přerušeno jakékoli probíhající vstupně-up a prostoje zařízení.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud se sem schovali aktualizace 0.2 a novější, doporučujeme nainstalovat aktualizace prostřednictvím portálu Azure. Postup portálu vyžaduje, aby uživatel prohledává, stahuje a poté aktualizuje aktualizace. Tento postup trvá přibližně 7 minut. Chcete-li nainstalovat aktualizaci nebo opravu hotfix, proveďte následující kroky.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace přejděte do služby Správce zařízení StorSimple. Vyberte **Zařízení** a pak vyberte a klikněte na zařízení, které jste právě aktualizovali. Přejděte **na nastavení > Správa aktualizací > zařízení**. Zobrazená verze softwaru by měla být **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského prostředí

Při použití místního webového uživatelského prostředí existují dva kroky:

* Stažení aktualizace nebo opravy hotfix
* Instalace aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Stažení aktualizace nebo opravy hotfix

1. Spusťte aplikaci [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer a přejděte na .

2. Pokud v tomto počítači používáte katalog Microsoft Update poprvé, klepněte po zobrazení výzvy k instalaci doplňku Katalogu microsoft update na **tlačítko Nainstalovat.**

3. Do vyhledávacího pole katalogu Microsoft Update zadejte číslo opravy hotfix, kterou chcete stáhnout, do znalostní báze Knowledge Base (KB). Zadejte **hodnotu 4023268** pro aktualizaci 0.6 a klepněte na tlačítko **Hledat**.
   
    Zobrazí se výpis opravy hotfix, například **Aktualizace virtuálního pole StorSimple 0.6**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klepněte na tlačítko **Stáhnout**.

5. Měli byste vidět pět souborů ke stažení. Stáhněte si každý z těchto souborů do složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, ve které jsou soubory umístěny.
    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Zobrazí se následující:
    -  Soubor `WindowsTH-KB3011067-x64`samostatného balíčku společnosti Microsoft Update . Tento soubor se používá k aktualizaci softwaru zařízení.
    - Ženeva Monitoring `GenevaMonitoringAgentPackageInstaller`Agent package file . Tento soubor se používá k aktualizaci agenta služby Monitorování a diagnostika (MDS). Poklepejte na soubor kabiny. Zobrazí se _soubor MSI._ Vyberte soubor, klikněte pravým tlačítkem myši a potom soubor **extrahujte.** Soubor _MSI_ slouží k aktualizaci agenta.

        ![Extrahovat soubor aktualizace agenta MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Není nutné aktualizovat agenta MDS, pokud používáte StorSimple Update 0.5 (0.0.10293.0).

    - Tři soubory, které obsahují `windows8.1-kb4012213-x64`důležité`windows8.1-kb3205400-x64`aktualizace `windows8.1-kb4019213-x64`zabezpečení systému Windows , a .


### <a name="install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

Před instalací aktualizace nebo opravy hotfix se ujistěte, že je aktualizace nebo oprava hotfix stažena místně na hostiteli nebo dostupná prostřednictvím sdílené síťové složky.

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo Update 0.1 verze softwaru. Tento postup trvá přibližně 12 minut. Chcete-li nainstalovat aktualizaci nebo opravu hotfix, proveďte následující kroky.

#### <a name="to-install-the-update-or-the-hotfix"></a>Instalace aktualizace nebo opravy hotfix

1. V místním webovém uživatelském prostředí přejděte na**stránku Aktualizace softwaru** **údržby** > . Poznamenejte si verzi softwaru, kterou používáte. Pokud používáte **10.0.10290.0**, není nutné aktualizovat agenta MDS v kroku 6.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. V **části Aktualizovat cestu k souboru**zadejte název souboru aktualizace nebo opravy hotfix. Můžete také přejít na instalační soubor aktualizace nebo opravy hotfix, pokud je umístěn do sdílené síťové složky. Klikněte na **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zobrazí se upozornění. Vzhledem k tomu, virtuální pole je jeden uzel zařízení, po instalaci aktualizace, zařízení restartuje a je prostoje. Klikněte na ikonu šeku.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Aktualizace se spustí. Po úspěšné aktualizaci zařízení se restartuje. Místní uI není přístupné v této době trvání.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po dokončení restartování se dostanete na **přihlašovací** stránku. Chcete-li ověřit, zda byl software zařízení aktualizován, přejděte v místním webovém uživatelském rozhraní na**stránku** **Maintenance** > Software Update . Zobrazená verze softwaru by měla být **10.0.0.0.0.10293** pro aktualizaci 0.6.
   
   > [!NOTE]
   > Verze softwaru hlásíme trochu jiným způsobem v místním webovém uživatelském prostředí a na portálu Azure. Například místní webové uživatelské prostředí hlásí **10.0.0.0.10293** a portál Azure hlásí **10.0.10293.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Tento krok přeskočte, pokud jste před použitím této aktualizace spouštěli aktualizaci StorSimple Virtual Array Update 0.5 **(10.0.10290.0).** Před zahájením aktualizace jste si v kroku 1 poznamenejte verzi softwaru. Pokud jste spouštěli aktualizaci 0.5, je váš agent MDS již aktuální .

    Pokud používáte verzi softwaru před aktualizací 0.5, dalším krokem je aktualizace agenta MDS. Na stránce **Aktualizace softwaru** přejděte na cestu k `GenevaMonitoringAgentPackageInstaller.msi` **aktualizaci souboru** a vyhledejte soubor. Opakujte kroky 2-4. Po restartování virtuálního pole se přihlaste do místního webového uživatelského prostředí.

7. Opakováním kroku 2-4 nainstalujte opravy `windows8.1-kb4012213-x64``windows8.1-kb3205400-x64`zabezpečení `windows8.1-kb4019213-x64`systému Windows pomocí souborů , a . Virtuální pole se restartuje po každé instalaci a je třeba se přihlásit do místního webového uživatelského prostředí.

## <a name="next-steps"></a>Další kroky

Další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

