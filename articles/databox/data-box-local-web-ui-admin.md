---
title: Správa místního webového uživatelského rozhraní Microsoft Azure Data Box | Microsoft Docs v datech
description: Popisuje, jak používat místní webové uživatelské rozhraní při správě zařízení Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 09/11/2018
ms.author: alkohli
ms.openlocfilehash: 9dd519f8efc9700f7a747aa37a9c02414f3e1865
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093880"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>Použití místního webového uživatelského rozhraní při správě Data Boxu

Článek popisuje část konfigurace a úlohy správy, které se dají provádět v Data Boxu. Data Box můžete spravovat přes uživatelské rozhraní webu Azure Portal a místní webové uživatelské rozhraní pro zařízení. Tento článek se zaměřuje na úlohy, které můžete provádět pomocí místního webového uživatelského rozhraní.

Místní webové uživatelské rozhraní pro Data Box se používá pro počáteční konfiguraci zařízení. Místní webové uživatelské rozhraní můžete také použít k vypnutí nebo restartování Data Boxu, spuštění diagnostických testů, aktualizaci softwaru, zobrazení protokolů kopírování a generování balíčku protokolů pro podporu Microsoftu.

Tento článek obsahuje následující návody:

- Generování balíčku pro podporu
- Vypnutí nebo restartování zařízení
- Bezpečné vymazání dat ze zařízení
- Monitorování dostupné kapacity zařízení
- Přeskočení ověření kontrolního součtu 

## <a name="generate-support-package"></a>Generování balíčku pro podporu

Pokud budete mít se zařízením jakékoliv problémy, můžete vytvořit ze systémových protokolů balíček pro podporu. Podpora Microsoftu používá tento balíček k řešení příslušných potíží. Pokud chcete vygenerovat balíček pro podporu, postupujte takto:

1. V místním webovém uživatelském rozhraní přejděte na **Kontaktovat podporu** a klikněte na **Vytvořit balíček pro podporu**.

    ![Vytvoření balíčku pro podporu 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Shromáždí se položky balíčku pro podporu. Tato operace trvá několik minut.

    ![Vytvoření balíčku pro podporu 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Po dokončení vytváření balíčku pro podporu klikněte na **Stáhnout balíček pro podporu**. 

    ![Vytvoření balíčku pro podporu 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Přejděte k umístění pro stahování a vyberte ho. Otevřete složku a zobrazte obsah.

    ![Vytvoření balíčku pro podporu 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Vypnutí nebo restartování zařízení

Data Box můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme před restartováním přepnout sdílené složky na hostiteli a potom na zařízení do offline režimu. Minimalizuje se tak možnost poškození dat. Při vypnutí zařízení zkontrolujte, že se nekopírují data.

Při vypnutí Data Boxu postupujte podle následujících pokynů.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
2. Klikněte na **Vypnout**.

    ![Vypnutí Data Boxu 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

    ![Vypnutí Data Boxu 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Zařízení po vypnutí zapněte tlačítkem napájení na předním panelu.

Při restartování Data Boxu postupujte podle následujících pokynů.

1. V místním webovém uživatelském rozhraní přejděte na **Vypnout nebo restartovat**.
2. Klikněte na **Restartovat**.

    ![Restartování Data Boxu 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte kliknutím na **OK**.

   Zařízení se vypne a restartuje.

## <a name="view-available-capacity-of-the-device"></a>Zobrazení dostupné kapacity zařízení

Dostupnou a využitou kapacitu zařízení můžete zobrazit na řídicím panelu zařízení. 

1. V místním webovém uživatelském rozhraní přejděte na **Zobrazit řídicí panel**.
2. V části **Připojit a kopírovat** se zobrazí volné a využité místo zařízení.

    ![Zobrazení dostupné kapacity](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Přeskočení ověření kontrolního součtu

Při přípravě k odeslání se pro data standardně generují kontrolní součty. V některých výjimečných případech může být v závislosti na typu dat (malé velikosti souborů) výkon velmi pomalý. V takových případech můžete kontrolní součet přeskočit. 

Důrazně doporučujeme kontrolní součet nezakazovat, pokud to výrazně neovlivňuje výkon.

1. V pravém horním rohu místního webového uživatelského rozhraní zařízení přejděte na Nastavení.

    ![Zakázání kontrolních součtů](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Zakázání** ověření kontrolního součtu
3. Klikněte na tlačítko **Použít**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat Data Box prostřednictvím webu Azure Portal](data-box-portal-admin.md).

