---
title: Instalaci aktualizace 0,6 v poli virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje způsob použití pole virtuální zařízení StorSimple webového uživatelského rozhraní na aktualizace pomocí metody Azure portal a opravy hotfix
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
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875904"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Nainstalujte aktualizace 0,6 na pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0,6 pole virtuální zařízení StorSimple prostřednictvím místního webového uživatelského rozhraní a prostřednictvím portálu Azure. Můžete použít aktualizace softwaru nebo opravy hotfix, které k zachování aktualizovaného stavu pole virtuální zařízení StorSimple.

Než použijete aktualizaci, doporučujeme vám věnovat svazky nebo sdílené složky do offline režimu na hostiteli první a pak zařízení. Tím se minimalizují možnost poškození dat. Jakmile svazky nebo sdílené složky jsou offline, můžete také provést ruční zálohování zařízení.

> [!IMPORTANT]
> - Aktualizace 0,6 odpovídá **10.0.10293.0** verze softwaru ve vašem zařízení. Informace na to, co je nového v této aktualizaci najdete na [poznámky k verzi pro aktualizaci 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Pokud používáte verzi Update 0,2 nebo novější, doporučujeme, aby instalujete aktualizace prostřednictvím portálu Azure. Pokud používáte Update 0.1 nebo verze GA softwaru, musíte použít metodu opravu hotfix prostřednictvím místního webového uživatelského rozhraní pro instalaci aktualizace 0,6.
>
> - Mějte na paměti, který instaluje aktualizaci nebo opravu hotfix restartuje vaše zařízení. Vzhledem k tomu, že pole virtuální zařízení StorSimple je jeden uzel zařízení, dojde k narušení všechny vstupně-výstupních operací v průběhu a zařízení dojde k výpadku.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud aktualizace 0,2 a novější, doporučujeme instalovat aktualizace prostřednictvím portálu Azure. Portál postup vyžaduje, aby uživatel ke kontrole, stáhněte a nainstalujte aktualizace. Tento postup trvá asi 7 minut. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace, přejděte do služby StorSimple Manager zařízení. Vyberte **zařízení** a poté vyberte a klikněte na zařízení k aktualizaci. Přejděte na **Nastavení > Správa > aktualizace zařízení**. Zobrazit software verze musí být **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizace nebo opravy hotfix
* Instalovat aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizace nebo opravy hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Chcete-li stáhnout aktualizace nebo opravy hotfix

1. Spusťte Internet Explorer a přejděte na [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Pokud používáte katalogu služby Microsoft Update na tomto počítači poprvé, klikněte na tlačítko **nainstalovat** po zobrazení výzvy k instalaci doplňku katalogu služby Microsoft Update.

3. Do vyhledávacího pole katalogu služby Microsoft Update zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout. Zadejte **4023268** pro aktualizace 0,6 a pak klikněte na tlačítko **vyhledávání**.
   
    Zobrazí se seznam oprav hotfix, například **aktualizace pole virtuálního zařízení StorSimple 0,6**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klikněte na **Stáhnout**.

5. Měli byste vidět pět souborů ke stažení. Stáhněte si každý z těchto souborů do složky. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

6. Otevřete složku, kde jsou umístěny soubory.
    ![Soubory v balíčku](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Zobrazí:
    -  Soubor balíčku samostatné aktualizace Microsoft `WindowsTH-KB3011067-x64`. Tento soubor se používá k aktualizaci softwaru zařízení.
    - Soubor balíčku agenta monitorování Genevy `GenevaMonitoringAgentPackageInstaller`. Tento soubor se používá k aktualizaci agenta monitorovací a diagnostické služby (MDS). Poklikejte na soubor cab. A _.msi_ se zobrazí. Vyberte soubor, klikněte pravým tlačítkem a potom **extrahovat** soubor. Můžete použít _.msi_ soubor aktualizace agenta.

        ![Extrahujte soubor aktualizace agenta služby MDS](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Není nutné aktualizovat agenta MDS, pokud používáte StorSimple Update 0,5 (0.0.10293.0).

    - Tři soubory, které obsahují důležité aktualizace zabezpečení systému Windows, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, a `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Instalovat aktualizace nebo opravy hotfix

Před instalací aktualizace nebo opravy hotfix ověřte, že aktualizaci nebo opravu hotfix stažené buď místně na vašem hostiteli nebo přístupné přes síťové sdílené složky.

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo aktualizovat 0,1 verze softwaru. Tento postup trvá přibližně 12 minut na dokončení. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>K instalaci aktualizace nebo opravy hotfix

1. V místní webového uživatelského rozhraní, přejděte do **údržby** > **aktualizace softwaru**. Poznamenejte si verzi softwaru, kterou používáte. Pokud používáte **10.0.10290.0**, není nutné aktualizovat agenta MDS v kroku 6.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. V **cesta k souboru aktualizace**, zadejte název souboru pro aktualizaci nebo opravu hotfix. Můžete také procházet k instalačnímu souboru aktualizaci nebo opravu hotfix Pokud umístěn ve sdílené síťové složce. Klikněte na tlačítko **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. Zobrazí se upozornění. Zadané virtuální pole je jeden uzel zařízení, po tuto aktualizaci nenainstalujete, restartování zařízení a dojde k výpadku. Klikněte na ikonu zaškrtnutí.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Aktualizace spustí. Po úspěšné aktualizaci zařízení restartuje. Místní uživatelské rozhraní není dostupný v této hodnotě duration.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Po dokončení restartování se otevře **přihlášení** stránky. Chcete-li ověřit, že zařízení software byl aktualizován, v místní webového uživatelského rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Zobrazit software verze musí být **10.0.0.0.0.10293** pro aktualizaci 0,6.
   
   > [!NOTE]
   > Verze softwaru jsme sestavu mírně odlišné způsobem místního webového uživatelského rozhraní a portálu Azure. Například místní webového uživatelského rozhraní sestavy **10.0.0.0.0.10293** a Azure portálu sestavy **10.0.10293.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Tento krok přeskočte, pokud byly spuštěny aktualizace pole virtuálního zařízení StorSimple 0,5 (**10.0.10290.0**) před instalací této aktualizace. Před započetím aktualizace provedené v kroku 1 poznamenejte si verzi softwaru. Pokud byly spuštěny aktualizace 0,5, vaše MDS agent je již aktuální.

    Pokud používáte verzi softwaru před aktualizací 0,5, je dalším krokem pro vás aktualizace agenta služby MDS. V **aktualizace softwaru** stránky, přejděte na **cesta k souboru aktualizace** a přejděte do `GenevaMonitoringAgentPackageInstaller.msi` souboru. Opakujte kroky 2 – 4. Po restartování virtuální pole, přihlaste se k místní webového uživatelského rozhraní.

7. Opakujte krok 2 – 4 k instalaci zabezpečení systému Windows opravy pomocí souborů `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, a `windows8.1-kb4019213-x64`. Pole virtuální restartuje po každé instalaci a je třeba se přihlásit do místního webového uživatelského rozhraní.

## <a name="next-steps"></a>Další kroky

Další informace o [Správa pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

