---
title: Konfigurace nastavení web Windows Update pro práci s Azure Update Management
description: Tento článek popisuje nastavení web Windows Update, která konfigurujete pro práci s Update Management
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377561"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurace nastavení web Windows Update pro Update Management

Update Management spoléhá na web Windows Update stažení a instalaci aktualizací Windows. V důsledku toho respektuje mnohá nastavení, která používá web Windows Update. Pokud použijete nastavení k povolení aktualizací jiných než Windows, Update Management bude tyto aktualizace spravovat taky. Pokud chcete povolit stahování aktualizací ještě před tím, než dojde k nasazení aktualizace, nasazení aktualizací může rychlejší a méně pravděpodobně překročit časový interval pro správu a údržbu.

## <a name="pre-download-updates"></a>Předběžné stažení aktualizací

Pokud chcete konfigurovat automatické stahování aktualizací v Zásady skupiny, můžete nastavit [nastavení konfigurace automatických aktualizací](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. Tím se stáhnou aktualizace potřebné na pozadí, ale neinstalují se. Tím se zachovává Update Management kontroly plánů, ale aktualizace se můžou stahovat mimo Update Management časový interval pro správu a údržbu. To může zabránit tomu, aby časový **interval pro správu a údržbu překročil** chyby v Update Management.

Můžete ho také nastavit pomocí PowerShellu, spustit následující PowerShell v systému, ve kterém chcete automaticky stahovat aktualizace.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Zakázat automatickou instalaci

Virtuální počítače Azure mají automatickou instalaci aktualizací, které jsou ve výchozím nastavení povolené. To může způsobit, že se aktualizace nainstalují předtím, než je naplánujete jejich instalaci pomocí Update Management. Toto chování můžete zakázat nastavením klíče registru `NoAutoUpdate` na `1`. Následující fragment kódu prostředí PowerShell vám ukáže jeden ze způsobů, jak to provést.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Konfigurace nastavení restartování

Klíče registru uvedené v části [Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) a [klíčů registru, které slouží ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , můžou způsobit, že se počítače restartují i v případě, že jste v nastavení nasazení aktualizace nezadali **nikdy restart** . . Tyto klíče registru byste měli nakonfigurovat podle potřeby pro vaše prostředí.

## <a name="enable-updates-for-other-microsoft-products"></a>Povolit aktualizace pro ostatní produkty Microsoftu

Ve výchozím nastavení web Windows Update poskytuje pouze aktualizace pro Windows. Pokud při **aktualizaci systému Windows povolíte aktualizace pro další produkty společnosti Microsoft**, máte k dispozici aktualizace pro další produkty, včetně oprav zabezpečení pro SQL Server nebo jiný software první strany. Tato možnost se nedá nakonfigurovat pomocí Zásady skupiny. Spusťte následující PowerShell v systémech, u kterých chcete povolit opravy na základě první strany, a Update Management bude toto nastavení akceptovat.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Nastavení konfigurace služby WSUS

**Update Management** ctí nastavení konfigurace služby WSUS. Seznam nastavení služby WSUS, která můžete nakonfigurovat pro práci s Update Management, je uvedený níže.

### <a name="intranet-microsoft-update-service-location"></a>Umístění intranetové služby Microsoft Update

Můžete určit zdroje pro vyhledávání a stahování aktualizací v [umístění intranetové Microsoft Update služby](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování nastavení web Windows Update můžete naplánovat nasazení aktualizací podle pokynů v části [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md) .