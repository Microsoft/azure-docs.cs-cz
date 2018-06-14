---
title: Instalaci aktualizací do pole virtuální zařízení StorSimple | Microsoft Docs
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: 3fb246b1515e7a637e6cff6499bf324c3f80dd45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875778"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Nainstalujte aktualizace 0.4 na pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0.4 pole virtuální zařízení StorSimple prostřednictvím místního webového uživatelského rozhraní a prostřednictvím portálu Azure. Budete muset použít aktualizace softwaru nebo opravy hotfix, které k zachování aktualizovaného stavu pole virtuální zařízení StorSimple. 

Mějte na paměti, který instaluje aktualizaci nebo opravu hotfix restartuje vaše zařízení. Vzhledem k tomu, že pole virtuální zařízení StorSimple je jeden uzel zařízení, dojde k narušení všechny vstupně-výstupních operací v průběhu a zařízení dojde k výpadku. 

Než použijete aktualizaci, doporučujeme vám věnovat svazky nebo sdílené složky do offline režimu na hostiteli první a pak zařízení. Tím se minimalizují možnost poškození dat.

> [!IMPORTANT]
> Pokud používáte Update 0.1 nebo verze GA softwaru, musíte použít metodu opravu hotfix prostřednictvím místního webového uživatelského rozhraní pro instalaci aktualizace 0.3. Pokud používáte verzi Update 0,2 nebo novější, doporučujeme, aby instalujete aktualizace prostřednictvím portálu Azure.
 

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizace nebo opravy hotfix
* Instalovat aktualizace nebo opravy hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizace nebo opravy hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Chcete-li stáhnout aktualizace nebo opravy hotfix

1. Spusťte Internet Explorer a přejděte na [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

3. Do vyhledávacího pole katalogu služby Microsoft Update zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout. Zadejte **3216577** pro aktualizace 0.4 a pak klikněte na tlačítko **vyhledávání**.
   
    Zobrazí se seznam oprav hotfix, například **aktualizace pole virtuálního zařízení StorSimple 0.4**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Klikněte na tlačítko **Přidat**. Aktualizace se přidá do košíku.

5. Klikněte na **Zobrazit košík**.

6. Klikněte na **Stáhnout**. Zadejte místní umístění, do kterého chcete aktualizace stáhnout, nebo do něj přejděte pomocí tlačítka **Procházet**. Aktualizace se stáhnou do zadaného umístění do podsložky se stejným názvem, jako má aktualizace. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

7. Otevřete složku, zkopírovaný, měli byste vidět soubor balíčku samostatné aktualizace Microsoft `WindowsTH-KB3011067-x64`. Tento soubor se používá k instalaci, aktualizaci nebo opravu hotfix.

### <a name="install-the-update-or-the-hotfix"></a>Instalovat aktualizace nebo opravy hotfix

Před instalací aktualizace nebo opravy hotfix ověřte, že aktualizaci nebo opravu hotfix stažené buď místně na vašem hostiteli nebo přístupné přes síťové sdílené složky. 

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo aktualizovat 0,1 verze softwaru. Tento postup trvá méně než 2 minut. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>K instalaci aktualizace nebo opravy hotfix

1. V místní webového uživatelského rozhraní, přejděte do **údržby** > **aktualizace softwaru**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update1m.png)

2. V **cesta k souboru aktualizace**, zadejte název souboru pro aktualizaci nebo opravu hotfix. Můžete také procházet k instalačnímu souboru aktualizaci nebo opravu hotfix Pokud umístěn ve sdílené síťové složce. Klikněte na tlačítko **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update2m.png)

3. Zobrazí se upozornění. Zadaný to je jeden uzel zařízení, když tuto aktualizaci nenainstalujete, restartování zařízení a dojde k výpadku. Klikněte na ikonu zaškrtnutí.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update3m.png)

4. Aktualizace spustí. Po úspěšné aktualizaci zařízení restartuje. Místní uživatelské rozhraní není dostupný v této hodnotě duration.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po dokončení restartování se otevře **přihlášení** stránky. Chcete-li ověřit, že zařízení software byl aktualizován, v místní webového uživatelského rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Zobrazit software verze musí být **10.0.0.0.0.10289.0** pro aktualizaci 0.4.
   
   > [!NOTE]
   > Verze softwaru jsme sestavu mírně odlišné způsobem místního webového uživatelského rozhraní a portálu Azure. Například místní webového uživatelského rozhraní sestavy **10.0.0.0.0.10289** a Azure portálu sestavy **10.0.10289.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud aktualizace 0,2 a novější, doporučujeme instalovat aktualizace prostřednictvím portálu Azure. Portál postup vyžaduje, aby uživatel ke kontrole, stáhněte a nainstalujte aktualizace. Tento postup trvá asi 7 minut. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace (jako je označené podle stavu úlohy na 100 %), přejděte do služby StorSimple Manager zařízení. Vyberte **zařízení** a poté vyberte a klikněte na zařízení, které chcete aktualizovat ze seznamu zařízení připojená k této službě. V **nastavení** okno, přejděte na **spravovat** a vyberte **aktualizace zařízení**. Zobrazit software verze musí být **10.0.10289.0**.


## <a name="next-steps"></a>Další kroky

Další informace o [Správa pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

