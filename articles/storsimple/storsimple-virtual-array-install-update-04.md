---
title: Instalaci aktualizací do StorSimple Virtual Array | Dokumentace Microsoftu
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: cd3fdec0e50d64af28306034e6e1086e67526b7d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251049"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Instalace aktualizace 0.4 na StorSimple Virtual Array

## <a name="overview"></a>Přehled

Tento článek popisuje kroky potřebné k instalaci aktualizace 0.4 StorSimple Virtual Array prostřednictvím místního webového uživatelského rozhraní a prostřednictvím webu Azure portal. Budete muset použít aktualizace softwaru nebo oprav hotfix k udržování aktuálnosti StorSimple Virtual Array. 

Mějte na paměti, který instaluje aktualizaci nebo opravu hotfix restartuje vaše zařízení. Vzhledem k tomu, že StorSimple Virtual Array se zařízením s jedním uzlem, naruší se všechny vstupně-výstupních operací v průběhu a vaše zařízení dojde k výpadku. 

Před instalací aktualizace, doporučujeme vám věnovat svazky nebo sdílené složky offline na hostiteli první a pak zařízení. Minimalizuje se tak možnost poškození dat.

> [!IMPORTANT]
> Pokud používáte aktualizace 0.1 nebo verze GA softwaru, musíte použít opravu hotfix metodu prostřednictvím místního webového uživatelského rozhraní instalace aktualizace 0.3. Pokud používáte aktualizace 0.2 nebo později, doporučujeme nainstalovat aktualizace přes Azure portal.
 

## <a name="use-the-local-web-ui"></a>Použití místního webového uživatelského rozhraní

Existují dva kroky, při použití místního webového uživatelského rozhraní:

* Stáhnout aktualizaci nebo opravu hotfix
* Nainstalujte aktualizaci nebo opravu hotfix

### <a name="download-the-update-or-the-hotfix"></a>Stáhnout aktualizaci nebo opravu hotfix

Provedením následujících kroků si stáhněte aktualizace softwaru z Katalogu služby Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Chcete-li stáhnout aktualizace nebo opravy hotfix

1. Spustit aplikaci Internet Explorer a přejděte do [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Pokud na tomto počítači používáte Katalog služby Microsoft Update poprvé, po zobrazení výzvy k instalaci doplňku Katalog služby Microsoft Update klikněte na **Nainstalovat**.

3. Do vyhledávacího pole katalogu služby Microsoft Update zadejte číslo znalostní báze Knowledge Base (KB) opravy hotfix, které chcete stáhnout. Zadejte **3216577** aktualizace 0.4 a potom klikněte na tlačítko **hledání**.
   
    Zobrazí se výpis opravy hotfix, například **StorSimple Virtual Array aktualizace 0.4**.
   
    ![Prohledávání katalogu](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Klikněte na tlačítko **Přidat**. Aktualizace se přidá do košíku.

5. Klikněte na **Zobrazit košík**.

6. Klikněte na **Stáhnout**. Zadejte místní umístění, do kterého chcete aktualizace stáhnout, nebo do něj přejděte pomocí tlačítka **Procházet**. Aktualizace se stáhnou do zadaného umístění do podsložky se stejným názvem, jako má aktualizace. Složku je také možné zkopírovat do sdílené síťové složky dostupné ze zařízení.

7. Otevření složky zkopírovaný, měli byste vidět soubor Microsoft Update samostatného balíčku `WindowsTH-KB3011067-x64`. Tento soubor se používá k instalaci, aktualizaci nebo opravu hotfix.

### <a name="install-the-update-or-the-hotfix"></a>Nainstalujte aktualizaci nebo opravu hotfix

Před instalací aktualizace nebo opravy hotfix Přesvědčte se, že máte aktualizaci nebo opravu hotfix stažené buď místně na vašem hostiteli nebo přístupné přes síťové sdílené složky. 

Tuto metodu použijte k instalaci aktualizací do zařízení se systémem GA nebo aktualizace 0.1 verze softwaru. Tento postup trvá méně než 2 minut. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

#### <a name="to-install-the-update-or-the-hotfix"></a>K instalaci aktualizace nebo opravy hotfix

1. V místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update1m.png)

2. V **cesta k souboru aktualizace**, zadejte název souboru pro aktualizaci nebo opravu hotfix. Můžete také přejít k instalačnímu souboru aktualizace nebo opravy hotfix Pokud umístěn ve sdílené síťové složce. Klikněte na tlačítko **Použít**.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update2m.png)

3. Zobrazí se upozornění. Zadaný to je jeden uzel zařízení, až tuto aktualizaci nenainstalujete, zařízení se restartuje a nedochází k výpadkům. Kliknutím na ikonu zaškrtnutí.
   
   ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update3m.png)

4. Spustí se aktualizace. Po úspěšné aktualizaci zařízení restartuje. Místní uživatelské rozhraní není dostupný v této hodnotě duration.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update5m.png)

5. Po dokončení restartování, budete přesměrováni na **přihlášení** stránky. Chcete-li ověřit, že zařízení má aktualizovat, v místním webovém uživatelském rozhraní, přejděte na **údržby** > **aktualizace softwaru**. Zobrazené software verze by měla být **10.0.0.0.0.10289.0** aktualizace 0.4.
   
   > [!NOTE]
   > V trochu jinak v místním webovém uživatelském rozhraní a webu Azure portal vytvoříme sestavy verzí softwaru. Například místního webového uživatelského rozhraní sestavy **10.0.0.0.0.10289** a Azure portal sestavy **10.0.10289.0** pro stejnou verzi.
   
    ![aktualizace zařízení](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud aktualizace s 0,2 nebo novějším, doporučujeme, abyste instalaci aktualizace na webu Azure portal. Portálu postup vyžaduje, aby uživatel kontrolovat, stáhněte si a nainstalujte aktualizace. Tento postup trvá asi 7 minut na dokončení. Proveďte následující kroky k instalaci, aktualizaci nebo opravu hotfix.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

Po dokončení instalace (jak je uvedeno ve stavu úlohy na 100 %), přejděte do služby Správce zařízení StorSimple. Vyberte **zařízení** a potom klikněte na zařízení, které chcete aktualizovat ze seznamu zařízení připojených k této službě. V **nastavení** okno, přejděte na **spravovat** a vyberte **aktualizace zařízení**. Zobrazené software verze by měla být **10.0.10289.0**.


## <a name="next-steps"></a>Další postup

Další informace o [Správa StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

