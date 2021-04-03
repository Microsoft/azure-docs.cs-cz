---
title: Aktualizace zařízení StorSimple | Microsoft Docs
description: Vysvětluje, jak pomocí funkce aktualizace StorSimple instalovat běžné aktualizace a opravy hotfix v režimu údržby.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956836"
---
# <a name="update-your-storsimple-8000-series-device"></a>Aktualizace zařízení řady StorSimple 8000
> [!NOTE]
> Portál Classic pro StorSimple je zastaralý. Vaši Správci zařízení StorSimple se automaticky přesunou na nový Azure Portal podle plánu ukončování používání. O tomto přesunu vás budeme informovat prostřednictvím e-mailu a oznámení na portálu. Tento dokument zanedlouho také nebude k dispozici. Pokud máte jakékoli dotazy k tomuto přesunu, přečtěte si [Nejčastější dotazy: Přesun na Azure Portal](./index.yml).

## <a name="overview"></a>Přehled
Funkce StorSimple Updates umožňují snadno udržovat vaše zařízení StorSimple v aktuálním stavu. V závislosti na typu aktualizace můžete aktualizace zařízení provádět pomocí portálu Azure Classic nebo pomocí rozhraní Windows PowerShell. Tento kurz popisuje typy aktualizací a jejich instalaci.

Můžete použít dva typy aktualizací zařízení: 

* Aktualizace v normálním (nebo normálním režimu)
* Aktualizace režimu údržby

Pravidelné aktualizace můžete instalovat prostřednictvím portálu Azure Classic nebo Windows PowerShellu. k instalaci aktualizací režimu údržby je však nutné použít prostředí Windows PowerShell. 

Každý typ aktualizace je popsaný samostatně, níže.

### <a name="regular-updates"></a>Pravidelné aktualizace
Pravidelné aktualizace jsou nerušivé aktualizace, které je možné nainstalovat, když je zařízení v normálním režimu. Tyto aktualizace se používají prostřednictvím Microsoft Update webu na každém řadiči zařízení. 

> [!IMPORTANT]
> Během procesu aktualizace může dojít k převzetí služeb při selhání řadiče. To ale nebude mít vliv na dostupnost systému ani na jeho provoz.
> 
> 

* Podrobnosti o tom, jak nainstalovat pravidelné aktualizace prostřednictvím portálu Azure Classic, najdete v tématu [instalace pravidelných aktualizací prostřednictvím portálu Azure Classic](#install-regular-updates-via-the-azure-classic-portal).
* Pravidelné aktualizace můžete nainstalovat také prostřednictvím Windows PowerShell pro StorSimple. Podrobnosti najdete v tématu [instalace pravidelných aktualizací prostřednictvím Windows PowerShell pro StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizace režimu údržby
Aktualizace režimu údržby jsou rušivé aktualizace, například upgrady firmwaru disku. Tyto aktualizace vyžadují, aby bylo zařízení přepnuto do režimu údržby. Podrobnosti najdete v části [Krok 2: Zadejte režim údržby](#step2). K instalaci aktualizací režimu údržby nemůžete použít portál Azure Classic. Místo toho je nutné použít Windows PowerShell pro StorSimple. 

Podrobnosti o instalaci aktualizací režimu údržby najdete v tématu [instalace aktualizací režimu údržby prostřednictvím Windows PowerShell pro StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizace režimu údržby se musí použít samostatně pro každý kontroler. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalace pravidelných aktualizací prostřednictvím portálu Azure Classic
K instalaci aktualizací na zařízení StorSimple můžete použít portál Azure Classic.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalace pravidelných aktualizací prostřednictvím Windows PowerShell pro StorSimple
Alternativně můžete použít Windows PowerShell pro StorSimple k použití normální aktualizace (normální režim).

> [!IMPORTANT]
> I když můžete instalovat pravidelné aktualizace pomocí Windows PowerShell pro StorSimple, důrazně doporučujeme instalovat pravidelné aktualizace prostřednictvím portálu Azure Classic. Od verze Update 1 se před instalací aktualizací z portálu provede předběžné kontroly. Tyto předběžné kontroly zabrání selhání a zajišťují plynulejší prostředí. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Nainstalovat aktualizace režimu údržby prostřednictvím Windows PowerShell pro StorSimple
Pomocí Windows PowerShell pro StorSimple aplikujete aktualizace režimu údržby na zařízení StorSimple. V tomto režimu jsou pozastaveny všechny požadavky na vstupně-výstupní operace. Také se zastaví služby, jako je například nestálá paměť s náhodným přístupem (NVRAM) nebo služba clusteringu. Oba řadiče se při zadání nebo ukončení tohoto režimu restartují. Po ukončení tohoto režimu budou všechny služby obnoveny a měly by být v pořádku. (Může to trvat několik minut.)

Pokud potřebujete použít aktualizace režimu údržby, obdržíte upozornění prostřednictvím portálu Azure Classic, na kterém je nutné nainstalovat aktualizace. Tato výstraha bude obsahovat pokyny k instalaci aktualizací pomocí Windows PowerShell pro StorSimple. Po aktualizaci zařízení použijte stejný postup ke změně zařízení do normálního režimu. Podrobné pokyny najdete v části [Krok 4: ukončení režimu údržby](#step4).

> [!IMPORTANT]
> * Před přechodem do režimu údržby ověřte, že oba řadiče zařízení jsou v pořádku, a to tak, že zkontroluje **stav hardwaru** na stránce **Údržba** na portálu Azure Classic. Pokud kontroler není v pořádku, kontaktujte podpora Microsoftu pro další kroky. Další informace najdete v podpora Microsoftu kontaktů. 
> * Když jste v režimu údržby, musíte nejdřív nainstalovat aktualizaci na jeden kontroler a pak na druhý kontroler.
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>Krok 1: připojení ke konzole sériového portu <a name="step1"></a>
Nejprve k přístupu ke konzole sériového rozhraní použijte aplikaci, jako je například výstup. Následující postup vysvětluje, jak pomocí výstupu připojit ke konzole sériového připojení.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>Krok 2: Zadejte režim údržby <a name="step2"></a>
Po připojení ke konzole nástroje Zjistěte, zda jsou k dispozici aktualizace pro instalaci, a zadejte režim údržby pro jejich instalaci.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>Krok 3: instalace aktualizací <a name="step3"></a>
Dále nainstalujte své aktualizace.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>Krok 4: ukončení režimu údržby <a name="step4"></a>
Nakonec ukončete režim údržby.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalace oprav hotfix prostřednictvím Windows PowerShell pro StorSimple
Na rozdíl od aktualizací Microsoft Azure StorSimple se opravy hotfix instalují ze sdílené složky. Stejně jako u aktualizací existují dva typy oprav hotfix: 

* Běžné opravy hotfix 
* Opravy hotfix v režimu údržby  

Následující postupy vysvětlují, jak použít Windows PowerShell pro StorSimple k instalaci běžných oprav hotfix a v režimu údržby.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co se stane s aktualizacemi, pokud provedete obnovení továrního nastavení zařízení?
Pokud se zařízení obnoví do továrního nastavení, všechny aktualizace se ztratí. Po registraci a konfiguraci zařízení pro resetování továrního nastavení budete muset aktualizace nainstalovat ručně prostřednictvím portálu Azure Classic nebo Windows PowerShell pro StorSimple. Další informace o obnovení továrního nastavení najdete v tématu [resetování zařízení do výchozího továrního nastavení](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [použití Windows PowerShell pro StorSimple ke správě zařízení StorSimple](./storsimple-8000-windows-powershell-administration.md).
* Přečtěte si další informace o [používání služby StorSimple Manager ke správě zařízení StorSimple](./storsimple-8000-manager-service-administration.md).