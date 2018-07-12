---
title: Aktualizace zařízení StorSimple | Dokumentace Microsoftu
description: Vysvětluje, jak používat funkci StorSimple update k instalaci aktualizací režimu běžných a údržba a opravy hotfix.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: v-sharos
ms.openlocfilehash: 412978d2c343394f295e336690ec72153dda4b79
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452613"
---
# <a name="update-your-storsimple-8000-series-device"></a>Aktualizace zařízení StorSimple řady 8000
> [!NOTE]
> Portál Classic pro StorSimple je zastaralý. Vaši Správci zařízení StorSimple se automaticky přesunou na nový Azure Portal podle plánu ukončování používání. O tomto přesunu vás budeme informovat prostřednictvím e-mailu a oznámení na portálu. Tento dokument zanedlouho také nebude k dispozici. Pokud máte jakékoli dotazy k tomuto přesunu, přečtěte si [Nejčastější dotazy: Přesun na Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Přehled
Funkce aktualizace StorSimple umožňují snadno aktualizovat zařízení StorSimple. V závislosti na typu aktualizací můžete použít aktualizace na zařízení prostřednictvím portálu Azure classic nebo prostřednictvím rozhraní Windows PowerShell. Tento kurz popisuje typy aktualizací a jak nainstalovat každou z nich.

Můžete použít dva typy aktualizace zařízení: 

* Pravidelné (nebo normální režim) aktualizace
* Aktualizace režimu údržby

Můžete nainstalovat pravidelné aktualizace prostřednictvím portálu Azure classic nebo prostředí Windows PowerShell; však musíte použít Windows PowerShell k instalaci aktualizací režimu údržby. 

Každý typ aktualizace je popsána samostatně, níže.

### <a name="regular-updates"></a>Pravidelné aktualizace
Pravidelné aktualizace jsou nenarušující aktualizace, které mohou být nainstalovány, když je zařízení v normálním režimu. Tyto aktualizace se použijí prostřednictvím webu Microsoft Update na obou kontrolerech zařízení. 

> [!IMPORTANT]
> Během procesu aktualizace může dojít k převzetí služeb kontroleru. Nicméně to nebude mít vliv na dostupnost systému nebo operace.
> 
> 

* Podrobnosti o tom, jak nainstalovat pravidelné aktualizace přes portál Azure classic najdete v tématu [nainstalovat pravidelné aktualizace přes portál Azure classic](#install-regular-updates-via-the-azure-classic-portal).
* Můžete také nainstalovat pravidelné aktualizace přes Windows PowerShell pro StorSimple. Podrobnosti najdete v tématu [instalace pravidelné aktualizace přes Windows PowerShell pro StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizace režimu údržby
Aktualizace režimu údržby se o narušující aktualizace, jako je například upgrady firmwaru disku. Tyto aktualizace vyžadují zařízení uvést do režimu údržby. Podrobnosti najdete v tématu [krok 2: Zadejte údržbu](#step2). Na portálu Azure classic nelze použít k instalaci aktualizací režimu údržby. Místo toho musíte použít Windows PowerShell pro StorSimple. 

Podrobnosti o tom, jak nainstalovat aktualizace režimu údržby, najdete v článku [aktualizace režimu údržby nainstalovat přes Windows PowerShell pro StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizace režimu údržby musí použít samostatně pro každý kontroler. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Pravidelné aktualizace přes portál Azure classic
Na portálu Azure classic můžete použít aktualizace na zařízení StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalace pravidelné aktualizace přes Windows PowerShell pro StorSimple
Alternativně můžete použít Windows PowerShell pro StorSimple na standardní (běžný režim) aktualizace.

> [!IMPORTANT]
> Ačkoli je možné nainstalovat pravidelné aktualizace pomocí prostředí Windows PowerShell pro StorSimple, důrazně doporučujeme nainstalovat pravidelné aktualizace prostřednictvím portálu Azure classic. Od verze Update 1, předběžné kontroly se provede před instalací aktualizací z portálu. Tyto předběžné kontroly se vyřizuje selhání a zajistit plynulost. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Nainstalovat aktualizace režimu údržby pomocí prostředí Windows PowerShell pro StorSimple
Nainstalovat aktualizace režimu údržby na zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple. V tomto režimu jsou pozastaveny všechny vstupně-výstupní požadavky. Zastaví se také služby, jako je paměť s náhodným přístupem stálé (paměti NVRAM) nebo službu clusteringu. Oba kontrolery se restartují, když zadáte nebo tento režim ukončit. Při ukončení tento režim všechny služby bude pokračovat a musí být v pořádku. (To může trvat několik minut.)

Pokud je potřeba nainstalovat aktualizace režimu údržby, dostanete upozornění, že máte aktualizace, které musí být nainstalován prostřednictvím portálu Azure classic. Tato výstraha bude obsahovat pokyny k instalaci aktualizací pomocí Windows Powershellu pro StorSimple. Po aktualizaci vašich zařízení, použijte stejný postup a změňte zařízení do normálního režimu. Podrobné pokyny najdete v tématu [krok 4: režim údržby ukončovací](#step4).

> [!IMPORTANT]
> * Před přechod do režimu údržby, ověřte, že oba kontrolery zařízení jsou v dobrém stavu tak, že zkontrolujete **stav hardwaru** na **údržby** stránky na portálu Azure classic. Pokud kontroler není v pořádku, obraťte se na Microsoft Support pro další kroky. Další informace přejděte na kontaktujte podporu Microsoftu. 
> * Pokud jste v režimu údržby, budete muset nejdřív použít aktualizaci na jeden kontroler a pak na druhý kontroler.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Krok 1: Připojení ke konzole sériového portu <a name="step1">
Nejprve použijte aplikaci, jako je například PuTTY pro přístup ke konzole sériového portu. Následující postup vysvětluje, jak pomocí PuTTY k připojení ke konzole sériového portu.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Krok 2: Zadejte režim údržby <a name="step2">
Po připojení ke konzole, určete, jestli existují aktualizace nainstalovat a spustit režim údržby k instalaci.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Krok 3: Instalace aktualizace <a name="step3">
V dalším kroku nainstalujte aktualizace.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Krok 4: Režim údržby ukončení <a name="step4">
A konečně ukončete režim údržby.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalace opravy hotfix přes Windows PowerShell pro StorSimple
Na rozdíl od aktualizace pro Microsoft Azure StorSimple jsou nainstalované opravy hotfix ze sdílené složky. Stejně jako u aktualizací, existují dva druhy opravy hotfix: 

* Pravidelné opravy hotfix 
* Oprav hotfix režimu údržby  

Následující postupy vysvětlují, jak pomocí prostředí Windows PowerShell pro StorSimple k instalaci standardní a oprav hotfix režimu údržby.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co se stane aktualizace, pokud provádíte obnovení továrního nastavení zařízení?
Pokud se zařízení resetuje do továrního nastavení, všechny aktualizace budou ztraceny. Po obnovení továrního nastavení zařízení je zaregistrované a nakonfigurované, je potřeba ruční instalace aktualizací prostřednictvím portálu Azure classic nebo prostředí Windows PowerShell pro StorSimple. Další informace o obnovení do výrobního nastavení najdete v tématu [obnovíte výchozí tovární nastavení zařízení](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Další postup
* Další informace o [pomocí prostředí Windows PowerShell pro StorSimple ke správě zařízení StorSimple](storsimple-windows-powershell-administration.md).
* Další informace o [ke správě zařízení StorSimple pomocí služby StorSimple Manager](storsimple-manager-service-administration.md).

