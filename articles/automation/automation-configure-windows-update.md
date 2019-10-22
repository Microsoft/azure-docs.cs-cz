---
title: Konfigurace nastavení web Windows Update pro práci s Azure Update Management
description: Tento článek popisuje nastavení web Windows Update, která konfigurujete pro práci s Azure Update Management.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 813d34f9c07e6c2909c483f040d4f3bf09b3ad24
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690842"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurace nastavení web Windows Update pro Update Management

Azure Update Management využívá web Windows Update ke stažení a instalaci aktualizací Windows. V důsledku toho Update Management respektovat mnoho nastavení používaných web Windows Update. Pokud použijete nastavení k povolení aktualizací jiných než Windows, Update Management tyto aktualizace taky spravovat. Pokud chcete povolit stahování aktualizací před tím, než dojde k nasazení aktualizace, může být nasazení aktualizace rychlejší, efektivnější a méně pravděpodobně větší než časový interval pro správu a údržbu.

## <a name="pre-download-updates"></a>Předběžné stažení aktualizací

Pokud chcete konfigurovat automatické stahování aktualizací v Zásady skupiny, nastavte [nastavení konfigurace automatických aktualizací](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. Toto nastavení umožňuje stáhnout požadované aktualizace na pozadí, ale nenainstaluje je. Tímto způsobem Update Management zůstane v řízení plánů, ale aktualizace se dají stáhnout mimo Update Management časový interval pro správu a údržbu. Toto chování brání tomu, aby se v Update Management překročila chyba časový interval pro správu a údržbu.

Toto nastavení můžete zapnout taky spuštěním následujícího příkazu PowerShellu v systému, který chcete nakonfigurovat pro automatické stahování aktualizací:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Zakázat automatickou instalaci

Ve výchozím nastavení se na virtuálních počítačích Azure povoluje Automatická instalace aktualizací. To může způsobit, že se aktualizace nainstalují předtím, než je naplánujete na instalaci pomocí Update Management. Toto chování můžete zakázat nastavením klíče registru `NoAutoUpdate` na `1`. Následující fragment kódu prostředí PowerShell ukazuje, jak to provést:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Konfigurace nastavení restartování

Klíče registru uvedené v [části Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) a [klíčů registru, které slouží ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , můžou způsobit, že se počítače restartují, i když v nastaveních **nasazení aktualizace** neurčíte **nikdy restartovat** . . Tyto klíče registru byste měli nakonfigurovat tak, aby nejlépe vyhovovaly vašemu prostředí.

## <a name="enable-updates-for-other-microsoft-products"></a>Povolit aktualizace pro ostatní produkty Microsoftu

Ve výchozím nastavení web Windows Update poskytuje aktualizace pouze pro systém Windows. Pokud **při aktualizaci nastavení systému Windows povolíte aktualizace pro další produkty společnosti Microsoft** , obdržíte také aktualizace pro další produkty, včetně oprav zabezpečení pro Microsoft SQL Server a další software společnosti Microsoft. Tato možnost se nedá nakonfigurovat pomocí Zásady skupiny. Spusťte následující příkaz prostředí PowerShell v systémech, na kterých chcete povolit další aktualizace společnosti Microsoft. Update Management bude toto nastavení dodržovat.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Nastavení konfigurace služby WSUS

Update Management je v souladu s nastavením služby Windows Server Update Services (WSUS). Nastavení služby WSUS, která můžete nakonfigurovat pro práci s Update Management, jsou uvedená níže.

### <a name="intranet-microsoft-update-service-location"></a>Umístění intranetové služby Microsoft Update

Můžete určit zdroje pro vyhledávání a stahování aktualizací v části [Zadejte umístění intranetové Microsoft Update služby](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Další kroky

Po konfiguraci nastavení web Windows Update můžete naplánovat nasazení aktualizací podle pokynů v tématu [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).