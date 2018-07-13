---
title: Instalace aktualizace 0.5 na StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje způsob použití StorSimple Virtual Array webového uživatelského rozhraní aktualizací pomocí Azure portal a opravy hotfix – metoda
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
ms.openlocfilehash: c47da5b90c16e2d5b5709e2a6affc026238b9468
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704517"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0.5 na StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0.5 StorSimple Virtual Array prostřednictvím místního webového uživatelského rozhraní a prostřednictvím webu Azure portal. Budete muset použít aktualizace softwaru nebo oprav hotfix k udržování aktuálnosti StorSimple Virtual Array.

Před instalací aktualizace, doporučujeme vám věnovat svazky nebo sdílené složky offline na hostiteli první a pak zařízení. Tím se minimalizují možnost poškození dat. Jakmile svazky nebo sdílené složky jsou offline, byste měli také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 0.5 odpovídá **10.0.10290.0** verze softwaru na vašem zařízení. Informace o novinkách v této aktualizaci najdete v části [zpráva k vydání verze pro aktualizace 0.5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Pokud používáte aktualizace 0.2 nebo později, doporučujeme nainstalovat aktualizace přes Azure portal. Pokud používáte aktualizace 0.1 nebo verze GA softwaru, musíte použít opravu hotfix metodu prostřednictvím místního webového uživatelského rozhraní instalace aktualizace 0.5.
>
> - Mějte na paměti, který instaluje aktualizaci nebo opravu hotfix restartuje vaše zařízení. Vzhledem k tomu, že StorSimple Virtual Array se zařízením s jedním uzlem, naruší se všechny vstupně-výstupních operací v průběhu a vaše zařízení dojde k výpadku.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud aktualizace s 0,2 nebo novějším, doporučujeme, abyste instalaci aktualizace na webu Azure portal. Portálu postup vyžaduje, aby uživatel kontrolovat, stáhněte si a nainstalujte aktualizace. Tento postup trvá asi 7 minut na dokončení. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a potom klikněte na zařízení, které jste právě aktualizovali. Přejděte na **Nastavení > Správa > aktualizace zařízení**. Zobrazené software verze by měla být **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky, při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Nainstalujte aktualizaci nebo opravu hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Chcete-li stáhnout aktualizace nebo opravy hotfix

1. Spustit aplikaci Internet Explorer a přejděte do [ http://catalog.update.microsoft.com ](http://catalog.update.microsoft.com).

2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

3. Do vyhledávacího pole katalogu služby Microsoft Update zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout. Zadejte **4021576** pro aktualizace 0.5 a potom klikněte na tlačítko **hledání**.
   
    Zobrazí se výpis opravy hotfix, například **StorSimple Virtual Array aktualizace 0.5**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Klikněte na **Stáhnout**. 

5. Měli byste vidět dva soubory ke stažení, *MSU* a *.cab* souboru. Stáhněte si každý z těchto souborů do složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, ve kterém se soubory nacházejí.
    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Zobrazí:
    -  Microsoft Update samostatného balíčku souboru `WindowsTH-KB3011067-x64`. Tento soubor slouží k aktualizaci softwaru zařízení.
    - Soubor balíčku agenta monitorování Geneva `GenevaMonitoringAgentPackageInstaller`. Tento soubor slouží k aktualizaci agenta monitorování a Diagnostika služby (MDS). Poklikejte na soubor cab. Zobrazí se MSI. Vyberte soubor, klikněte pravým tlačítkem a pak **extrahovat** souboru. Budete používat _MSI_ souboru aktualizace agenta.

        ![Extrahujte soubor aktualizace pro agenta služby MDS](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Nainstalujte aktualizaci nebo opravu hotfix

Před instalací aktualizace nebo opravy hotfix Přesvědčte se, že máte aktualizaci nebo opravu hotfix stažené buď místně na vašem hostiteli nebo přístupné přes síťové sdílené složky.

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo aktualizace 0.1 verze softwaru. Tento postup trvá méně než 2 minut. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>K instalaci aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. V **cesta k souboru aktualizace**, zadejte název souboru pro aktualizaci nebo opravu hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix Pokud umístěn ve sdílené síťové složce. Klikněte na tlačítko **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zobrazí se upozornění. Zadaný to je jeden uzel zařízení, až tuto aktualizaci nenainstalujete, zařízení se restartuje a nedochází k výpadkům. Kliknutím na ikonu zaškrtnutí.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení restartuje. Místní uživatelské rozhraní není dostupný v této hodnotě duration.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po dokončení restartování, budete přesměrováni na **přihlášení** stránky. Chcete-li ověřit, že zařízení má aktualizovat, v místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Zobrazené software verze by měla být **10.0.0.0.0.10290.0** pro aktualizace 0.5.
   
   > [!NOTE]
   > V trochu jinak v místním webovém uživatelském rozhraní a webu Azure portal vytvoříme sestavy verzí softwaru. Například místního webového uživatelského rozhraní sestavy **10.0.0.0.0.10290** a Azure portal sestavy **10.0.10290.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. Dalším krokem je aktualizace agenta služby MDS. V **aktualizace softwaru** stránky, přejděte na **cesta k souboru aktualizace** a přejděte do `GenevaMonitoringAgentPackageInstaller.msi` souboru. Opakujte kroky 2 až 4. Po restartování virtuálního pole přihlášení do místního webového uživatelského rozhraní.

Aktualizace je nyní dokončena.

## <a name="next-steps"></a>Další postup

Další informace o [Správa StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

