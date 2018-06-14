---
title: Aktualizace zařízení StorSimple | Microsoft Docs
description: Vysvětluje, jak používat funkci aktualizace zařízení StorSimple k instalaci aktualizací režimu regular a údržbu a opravy hotfix.
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
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2018
ms.locfileid: "28103604"
---
# <a name="update-your-storsimple-8000-series-device"></a>Aktualizace zařízení StorSimple řady 8000
> [!NOTE]
> Portál Classic pro StorSimple je zastaralý. Vaši Správci zařízení StorSimple se automaticky přesunou na nový Azure Portal podle plánu ukončování používání. O tomto přesunu vás budeme informovat prostřednictvím e-mailu a oznámení na portálu. Tento dokument zanedlouho také nebude k dispozici. Pokud máte jakékoli dotazy k tomuto přesunu, přečtěte si [Nejčastější dotazy: Přesun na Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Přehled
Funkce aktualizace zařízení StorSimple umožňují snadno zachovat aktuální zařízení StorSimple. V závislosti na daný typ aktualizace můžete použít aktualizace na zařízení prostřednictvím portálu Azure classic nebo prostřednictvím rozhraní Windows PowerShell. Tento kurz popisuje typy aktualizace a postup instalace, každý z nich.

Můžete použít dva typy aktualizace zařízení: 

* Regulární (nebo v normálním režimu) aktualizací
* Aktualizace režimu údržby

Můžete nainstalovat pravidelné aktualizace prostřednictvím portálu Azure classic nebo prostředí Windows PowerShell; však musíte použít prostředí Windows PowerShell k instalaci aktualizací režimu údržby. 

Každý typ aktualizace je popsána samostatně, níže.

### <a name="regular-updates"></a>Pravidelné aktualizace
Pravidelné aktualizace jsou omezovaly aktualizace, které je možné nainstalovat, když je zařízení v normálním režimu. Tyto aktualizace se použijí prostřednictvím webu Microsoft Update pro každý řadič zařízení. 

> [!IMPORTANT]
> Během procesu aktualizace může dojít k selhání řadiče. Ale to nebude mít vliv na dostupnost systému nebo operace.
> 
> 

* Podrobnosti o tom, jak nainstalovat pravidelné aktualizace prostřednictvím portálu Azure classic najdete v tématu [nainstalovat pravidelné aktualizace prostřednictvím portálu Azure classic](#install-regular-updates-via-the-azure-classic-portal).
* Můžete také nainstalovat pravidelné aktualizace pomocí prostředí Windows PowerShell pro StorSimple. Podrobnosti najdete v tématu [instalace pravidelné aktualizace prostřednictvím Windows Powershellu pro StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizace režimu údržby
Aktualizace režimu údržby jsou rušivý aktualizace, jako je například upgrade firmwaru disku. Tyto aktualizace vyžaduje zařízení uvést do režimu údržby. Podrobnosti najdete v tématu [krok 2: Zadejte údržby režimu](#step2). Portál Azure classic nelze použít k instalaci aktualizací režimu údržby. Místo toho musíte použít Windows PowerShell pro StorSimple. 

Podrobnosti o tom, jak nainstalovat aktualizace režimu údržby najdete v tématu [aktualizace režimu údržby nainstalovat prostřednictvím Windows Powershellu pro StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizace režimu údržby, je nutné použít samostatně na každém řadiči. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Nainstalujte pravidelné aktualizace prostřednictvím portálu Azure classic
Portál Azure classic můžete použít aktualizace na zařízení StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalace pravidelné aktualizace prostřednictvím Windows Powershellu pro StorSimple
Alternativně můžete použít prostředí Windows PowerShell pro StorSimple na aktualizace regular (normální režim).

> [!IMPORTANT]
> I když můžete nainstalovat pravidelné aktualizace pomocí Windows Powershellu pro StorSimple, důrazně doporučujeme nainstalovat pravidelné aktualizace prostřednictvím portálu Azure classic. Počínaje Update 1, bude předběžné kontroly provést před instalací aktualizací z portálu. Tyto předběžné kontroly se mají prioritu před selhání a zajistěte hladší prostředí. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalovat aktualizace režimu údržby pomocí prostředí Windows PowerShell pro StorSimple
Pomocí Windows Powershellu pro StorSimple na aktualizace režimu údržby pro zařízení StorSimple. V tomto režimu jsou pozastavena všechny vstupně-výstupní požadavky. Také se zastaví službám, jako je paměť s náhodným přístupem stálé (paměti NVRAM) nebo službu clusteringu. Oba řadiče se restartují, když zadáte nebo ukončit tento režim. Při ukončení tohoto režimu se všechny služby bude pokračovat a musí být v pořádku. (To může trvat několik minut.)

Pokud potřebujete aktualizace režimu údržby, zobrazí se výstraha prostřednictvím portálu Azure classic, která obsahuje aktualizace, které je třeba nainstalovat. Tato výstraha bude obsahovat pokyny pro instalaci aktualizací pomocí Windows Powershellu pro StorSimple. Po aktualizaci zařízení, použijte stejný postup a změňte zařízení do režimu regulární. Podrobné pokyny najdete v tématu [krok 4: režim údržby ukončení](#step4).

> [!IMPORTANT]
> * Před přechodem do režimu údržby, ověřte, zda jsou oba řadiče zařízení v pořádku kontrolou **stavu hardwaru** na **údržby** na portálu Azure classic. Pokud je řadič není v pořádku, požádejte o další kroky Microsoft Support. Další informace najdete v tématu kontaktujte podporu Microsoftu. 
> * Pokud jste v režimu údržby, musíte nejprve použít aktualizaci na jednom řadiči a poté na jiný řadič.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Krok 1: Připojení ke konzole sériového portu<a name="step1">
Nejprve pomocí aplikace, jako je například PuTTY přístup ke konzole sériového portu. Následující postup vysvětluje, jak použití klienta PuTTY k připojení ke konzole sériového portu.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Krok 2: Zadejte režimu údržby<a name="step2">
Po připojení ke konzole, zjistěte, jestli jsou aktualizace nainstalovat, a zadejte režim údržby k instalaci.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Krok 3: Instalace aktualizace<a name="step3">
Dále nainstalujte vaše aktualizace.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Krok 4: Režim údržby ukončení<a name="step4">
Nakonec ukončení režimu údržby.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalaci oprav hotfix prostřednictvím Windows Powershellu pro StorSimple
Na rozdíl od aktualizace pro Microsoft Azure StorSimple jsou nainstalované opravy hotfix ze sdílené složky. Jako s aktualizacemi, existují dva typy opravy hotfix: 

* Regulární opravy hotfix 
* Opravy hotfix režimu údržby  

Následující postupy popisují, jak pomocí prostředí Windows PowerShell pro StorSimple nainstalovat regular a opravy hotfix režimu údržby.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co se stane aktualizace, pokud provádíte obnovení továrního nastavení zařízení?
Pokud je zařízení obnovit tovární nastavení, všechny aktualizace budou ztraceny. Po obnovení továrního nastavení zařízení je registrované a konfiguraci, musíte ručně nainstalovat aktualizace prostřednictvím portálu Azure classic nebo prostředí Windows PowerShell pro StorSimple. Další informace o obnovení továrního nastavení najdete v tématu [zařízení resetovat výchozí tovární nastavení](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Další postup
* Další informace o [pomocí Windows Powershellu pro StorSimple ke správě zařízení StorSimple](storsimple-windows-powershell-administration.md).
* Další informace o [pomocí služby StorSimple Manager ke správě zařízení StorSimple](storsimple-manager-service-administration.md).

