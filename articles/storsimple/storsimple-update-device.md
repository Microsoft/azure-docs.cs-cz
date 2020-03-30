---
title: Aktualizace zařízení StorSimple | Dokumenty společnosti Microsoft
description: Vysvětluje, jak pomocí funkce aktualizace StorSimple nainstalovat pravidelné aktualizace a opravy hotfix v režimu údržby.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: twooley
ms.openlocfilehash: c9451afaefdd220b5f87d4650c7844f06926b03a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933422"
---
# <a name="update-your-storsimple-8000-series-device"></a>Aktualizace zařízení řady StorSimple 8000
> [!NOTE]
> Portál Classic pro StorSimple je zastaralý. Vaši Správci zařízení StorSimple se automaticky přesunou na nový Azure Portal podle plánu ukončování používání. O tomto přesunu vás budeme informovat prostřednictvím e-mailu a oznámení na portálu. Tento dokument zanedlouho také nebude k dispozici. Pokud máte jakékoli dotazy k tomuto přesunu, přečtěte si [Nejčastější dotazy: Přesun na Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Přehled
Funkce aktualizací StorSimple umožňují snadno udržovat zařízení StorSimple aktuální. V závislosti na typu aktualizace můžete použít aktualizace zařízení prostřednictvím klasického portálu Azure nebo prostřednictvím rozhraní Prostředí Windows PowerShell. Tento kurz popisuje typy aktualizací a jak nainstalovat každý z nich.

Můžete použít dva typy aktualizací zařízení: 

* Pravidelné (nebo normální režim) aktualizace
* Aktualizace režimu údržby

Můžete nainstalovat pravidelné aktualizace prostřednictvím klasického portálu Azure nebo Windows PowerShell; K instalaci aktualizací režimu údržby je však nutné použít prostředí Windows PowerShell. 

Každý typ aktualizace je popsán samostatně, níže.

### <a name="regular-updates"></a>Pravidelné aktualizace
Pravidelné aktualizace jsou nerušivé aktualizace, které lze nainstalovat, když je zařízení v normálním režimu. Tyto aktualizace jsou použity prostřednictvím webu Microsoft Update pro každý řadič zařízení. 

> [!IMPORTANT]
> Během procesu aktualizace může dojít k převzetí služeb při selhání řadiče. To však nebude mít vliv na dostupnost systému nebo provoz.
> 
> 

* Podrobnosti o tom, jak nainstalovat pravidelné aktualizace prostřednictvím klasického portálu Azure, najdete [v tématu Instalace pravidelných aktualizací prostřednictvím klasického portálu Azure](#install-regular-updates-via-the-azure-classic-portal).
* Můžete také nainstalovat pravidelné aktualizace prostřednictvím prostředí Windows PowerShell pro StorSimple. Podrobnosti naleznete v [tématu Instalace pravidelných aktualizací prostřednictvím prostředí Windows PowerShell pro StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizace režimu údržby
Aktualizace režimu údržby jsou rušivé aktualizace, jako je například aktualizace firmwaru disku. Tyto aktualizace vyžadují, aby zařízení bylo přeptono do režimu údržby. Podrobnosti naleznete [v kroku 2: Předejte režim údržby](#step2). Klasický portál Azure nelze použít k instalaci aktualizací režimu údržby. Místo toho je nutné použít prostředí Windows PowerShell pro StorSimple. 

Podrobnosti o instalaci aktualizací režimu údržby naleznete v [tématu Instalace aktualizací režimu údržby prostřednictvím prostředí Windows PowerShell for StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizace režimu údržby musí být použity samostatně pro každý řadič. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalace pravidelných aktualizací prostřednictvím klasického portálu Azure
Klasický portál Azure můžete použít aktualizace zařízení StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalace pravidelných aktualizací prostřednictvím prostředí Windows PowerShell pro StorSimple
Alternativně můžete použít prostředí Windows PowerShell pro StorSimple použít pravidelné (normální režim) aktualizace.

> [!IMPORTANT]
> Přestože můžete nainstalovat pravidelné aktualizace pomocí prostředí Windows PowerShell pro StorSimple, důrazně doporučujeme instalovat pravidelné aktualizace prostřednictvím klasického portálu Azure. Počínaje aktualizací 1 budou před instalací aktualizací z portálu provedeny předběžné kontroly. Tyto předběžné kontroly zabrání selháním a zajistí hladší zážitek. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalace aktualizací režimu údržby prostřednictvím prostředí Windows PowerShell pro StorSimple
Pomocí prostředí Windows PowerShell pro StorSimple použít aktualizace režimu údržby zařízení StorSimple. V tomto režimu jsou pozastaveny všechny vstupně-v.i. Zastaveny jsou také služby, jako je energeticky nezávislá paměť nvram (Random Access Memory) nebo clustering ová služba. Oba řadiče jsou restartovány při vstupu nebo ukončení tohoto režimu. Po ukončení tohoto režimu budou všechny služby obnoveny a měly by být v pořádku. (To může trvat několik minut.)

Pokud potřebujete použít aktualizace režimu údržby, obdržíte výstrahu prostřednictvím klasického portálu Azure, který máte aktualizace, které musí být nainstalovány. Tato výstraha bude obsahovat pokyny pro použití prostředí Windows PowerShell pro StorSimple k instalaci aktualizací. Po aktualizaci zařízení použijte stejný postup pro změnu zařízení do normálního režimu. Podrobné pokyny naleznete v [tématu Krok 4: Ukončení režimu údržby](#step4).

> [!IMPORTANT]
> * Před vstupem do režimu údržby ověřte, že oba řadiče zařízení jsou v pořádku kontrolou **stavu hardwaru** na stránce **Údržba** na klasickém portálu Azure. Pokud řadič není v pořádku, obraťte se na podporu společnosti Microsoft pro další kroky. Další informace naleznete v odborné mši společnosti Microsoft. 
> * Když jste v režimu údržby, je třeba nejprve nainstalovat aktualizaci na jeden řadič a potom na druhý řadič.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Krok 1: Připojení k sériové konzoli<a name="step1">
Nejprve použijte aplikaci, jako je PuTTY pro přístup k konzole sériového portu. Následující postup vysvětluje, jak používat PuTTY pro připojení k konzole sériového zařízení.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Krok 2: Vstup do režimu údržby<a name="step2">
Po připojení ke konzoli určete, zda jsou k dispozici aktualizace k instalaci, a předejte režim údržby, který je chcete nainstalovat.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Krok 3: Instalace aktualizací<a name="step3">
Dále nainstalujte aktualizace.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Krok 4: Ukončení režimu údržby<a name="step4">
Nakonec ukončete režim údržby.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalace oprav hotfix v prostředí Windows PowerShell pro službu StorSimple
Na rozdíl od aktualizací pro Microsoft Azure StorSimple jsou opravy hotfix nainstalované ze sdílené složky. Stejně jako u aktualizací existují dva typy oprav hotfix: 

* Běžné opravy hotfix 
* Opravy hotfix v režimu údržby  

Následující postupy vysvětlují použití prostředí Windows PowerShell pro StorSimple k instalaci pravidelných oprav hotfix v režimu údržby.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co se stane s aktualizacemi, pokud provedete obnovení továrního nastavení zařízení?
Pokud je zařízení resetováno do továrního nastavení, dojde ke ztrátě všech aktualizací. Po registraci a konfiguraci zařízení obnovení továrního nastavení budete muset ručně nainstalovat aktualizace prostřednictvím klasického portálu Azure a/nebo prostředí Windows PowerShell pro StorSimple. Další informace o obnovení továrního nastavení naleznete [v tématu Obnovení výchozího nastavení zařízení z výroby](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Další kroky
* Další informace o [použití prostředí Windows PowerShell pro StorSimple ke správě zařízení StorSimple](storsimple-windows-powershell-administration.md).
* Další informace o [použití služby StorSimple Manager ke správě zařízení StorSimple](storsimple-manager-service-administration.md).

