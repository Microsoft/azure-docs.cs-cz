---
title: Instalace aktualizace 0.6 na StorSimple Virtual Array | Dokumentace Microsoftu
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 89ca7a5fa13696856bed108e4cbf7462b536b4bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246612"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0.6 na StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0.6 StorSimple Virtual Array prostřednictvím místního webového uživatelského rozhraní a prostřednictvím webu Azure portal. Použití aktualizací softwaru nebo oprav hotfix k udržování aktuálnosti StorSimple Virtual Array.

Před instalací aktualizace, doporučujeme vám věnovat svazky nebo sdílené složky offline na hostiteli první a pak zařízení. Minimalizuje se tak možnost poškození dat. Jakmile svazky nebo sdílené složky jsou offline, byste měli také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 0.6 odpovídá **10.0.10293.0** verze softwaru na vašem zařízení. Informace o novinkách v této aktualizaci najdete v části [zpráva k vydání verze pro aktualizace 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Pokud používáte aktualizace 0.2 nebo později, doporučujeme nainstalovat aktualizace přes Azure portal. Pokud používáte aktualizace 0.1 nebo verze GA softwaru, musíte použít opravu hotfix metodu prostřednictvím místního webového uživatelského rozhraní instalace aktualizace 0.6.
>
> - Mějte na paměti, který instaluje aktualizaci nebo opravu hotfix restartuje vaše zařízení. Vzhledem k tomu, že StorSimple Virtual Array se zařízením s jedním uzlem, naruší se všechny vstupně-výstupních operací v průběhu a vaše zařízení dojde k výpadku.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud aktualizace s 0,2 nebo novějším, doporučujeme, abyste instalaci aktualizace na webu Azure portal. Portálu postup vyžaduje, aby uživatel kontrolovat, stáhněte si a nainstalujte aktualizace. Tento postup trvá asi 7 minut na dokončení. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a potom klikněte na zařízení, které jste právě aktualizovali. Přejděte na **Nastavení > Správa > aktualizace zařízení**. Zobrazené software verze by měla být **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky, při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Nainstalujte aktualizaci nebo opravu hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Chcete-li stáhnout aktualizace nebo opravy hotfix

1. Spustit aplikaci Internet Explorer a přejděte do [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Pokud používáte katalog služby Microsoft Update na tomto počítači poprvé, klikněte na tlačítko **nainstalovat** po zobrazení výzvy k instalaci doplňku katalog služby Microsoft Update.

3. Do vyhledávacího pole katalogu služby Microsoft Update zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout. Zadejte **4023268** aktualizace 0.6 a potom klikněte na tlačítko **hledání**.
   
    Zobrazí se výpis opravy hotfix, například **StorSimple Virtual Array aktualizace 0.6**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klikněte na **Stáhnout**.

5. Měli byste vidět pět souborů ke stažení. Stáhněte si každý z těchto souborů do složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, ve kterém se soubory nacházejí.
    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Zobrazí se následující:
    -  Microsoft Update samostatného balíčku souboru `WindowsTH-KB3011067-x64`. Tento soubor slouží k aktualizaci softwaru zařízení.
    - Soubor balíčku agenta monitorování Geneva `GenevaMonitoringAgentPackageInstaller`. Tento soubor slouží k aktualizaci agenta monitorování a Diagnostika služby (MDS). Poklikejte na soubor cab. A _MSI_ se zobrazí. Vyberte soubor, klikněte pravým tlačítkem a pak **extrahovat** souboru. Můžete použít _MSI_ souboru aktualizace agenta.

        ![Extrahujte soubor aktualizace pro agenta služby MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Není nutné aktualizovat agenta služby MDS, pokud používáte verzi StorSimple Update 0,5 (0.0.10293.0).

    - Tři soubory, které obsahují důležité aktualizace zabezpečení Windows `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, a `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Nainstalujte aktualizaci nebo opravu hotfix

Před instalací aktualizace nebo opravy hotfix Přesvědčte se, že máte aktualizaci nebo opravu hotfix stažené buď místně na vašem hostiteli nebo přístupné přes síťové sdílené složky.

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo aktualizace 0.1 verze softwaru. Tento postup trvá přibližně 12 minut. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>K instalaci aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte. Pokud používáte **10.0.10290.0**, není potřeba aktualizovat agenta služby MDS v kroku 6.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. V **cesta k souboru aktualizace**, zadejte název souboru pro aktualizaci nebo opravu hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix Pokud umístěn ve sdílené síťové složce. Klikněte na tlačítko **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zobrazí se upozornění. Poté, co tuto aktualizaci nenainstalujete, zařízení se restartuje a nedochází k výpadkům je uvedený virtuální pole zařízením s jedním uzlem. Kliknutím na ikonu zaškrtnutí.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení restartuje. Místní uživatelské rozhraní není dostupný v této hodnotě duration.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po dokončení restartování, budete přesměrováni na **přihlášení** stránky. Chcete-li ověřit, že zařízení má aktualizovat, v místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Zobrazené software verze by měla být **10.0.0.0.0.10293** aktualizace 0.6.
   
   > [!NOTE]
   > V trochu jinak v místním webovém uživatelském rozhraní a webu Azure portal vytvoříme sestavy verzí softwaru. Například místního webového uživatelského rozhraní sestavy **10.0.0.0.0.10293** a Azure portal sestavy **10.0.10293.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Tento krok přeskočte, pokud jste používali StorSimple Virtual Array aktualizace 0.5 (**10.0.10290.0**) před použitím této aktualizace. Jste si poznamenali verze softwaru v kroku 1 předtím, než začal aktualizovat. Pokud jste používali aktualizace 0.5, agenta služby MDS už je aktuální.

    Pokud používáte verzi softwaru před aktualizací 0,5, dalším krokem pro vás je aktualizace agenta služby MDS. V **aktualizace softwaru** stránky, přejděte na **cesta k souboru aktualizace** a přejděte do `GenevaMonitoringAgentPackageInstaller.msi` souboru. Opakujte kroky 2 až 4. Po restartování virtuálního pole přihlášení do místního webového uživatelského rozhraní.

7. Opakujte krok 2 – 4 k instalaci Windows zabezpečení řeší pomocí souborů `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, a `windows8.1-kb4019213-x64`. Po každé instalaci restartuje virtuální pole a potřebujete se přihlásit do místního webového uživatelského rozhraní.

## <a name="next-steps"></a>Další postup

Další informace o [Správa StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

