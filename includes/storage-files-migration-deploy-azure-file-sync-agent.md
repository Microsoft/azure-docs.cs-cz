---
title: Nasazení agenta synchronizace souborů Azure
description: Nasazení agenta Azure File Sync. Běžný textový blok sdílený mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209412"
---
V této části nainstalujete agenta Azure File Sync na windows server.
[Průvodce nasazením](../articles/storage/files/storage-sync-files-deployment-guide.md) ukazuje, že je třeba vypnout **rozšířené zabezpečení aplikace IE**. Vylepšené zabezpečení IE je bezpečnostní opatření, které se nevztahuje na Azure File Sync a jeho vypnutí vám umožní ověření do Azure bez problémů.

Otevřete PowerShell a nainstalujte požadované moduly PowerShellu pomocí následujících příkazů. Ujistěte se, že nainstalujete úplný modul a zprostředkovatele NuGet po zobrazení výzvy:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Pokud máte nějaké problémy s dosažením internetu z vašeho serveru, nyní je čas je vyřešit. Azure File Sync používá jakékoli dostupné síťové připojení k internetu.
Vyžadování proxy serveru pro přístup k internetu je také podporováno. Můžete buď nakonfigurovat proxy pro celý počítač nyní, nebo určit proxy server, který pouze synchronizace souborů bude používat, během instalace agenta.

Pokud to znamená, že potřebujete otevřít brány firewall pro tento server, pak to může být přijatelný přístup k vám. Na konci instalace serveru po dokončení registrace serveru bude sestava připojení k síti zobrazující přesné adresy URL koncových bodů v Azure, se kterými synchronizace souborů musí komunikovat pro vybranou oblast. Zpráva také uvádí důvod, proč je komunikace potřebná. Pomocí sestavy můžete zamknout brány firewall kolem tohoto serveru na konkrétní adresy URL.

Můžete také postupovat konzervativnější přístup, ve kterém není otevřít firewally široké, ale místo toho omezit server komunikovat do vyšší úrovně DNS názvové prostory – je k dispozici další dokumentaci a podrobnosti k dispozici v [Azure File Sync proxy a nastavení brány firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) článku. Postupujte podle svých vlastních síťových doporučených postupů.

Na konci průvodce *instalací* serveru se zobrazí průvodce *registrací* serveru.
Zaregistrujte server do prostředků Azure služby Storage Sync Service z dřívějších.

Tyto kroky jsou podrobněji popsány v příručce pro nasazení, včetně výše uvedených modulů prostředí PowerShell, které byste měli nainstalovat jako první: [instalace agenta synchronizace souborů Azure](../articles/storage/files/storage-sync-files-deployment-guide.md).

Nejnovější agent by měl být použit a lze stáhnout z Microsoft Download Center: [Azure File Sync - agent](https://aka.ms/AFS/agent "Agent synchronizace souborů Azure ke stažení").

Po úspěšné instalaci a registraci serveru můžete zkontrolovat, zda jste úspěšně dokončili tento krok: Přejděte na prostředek služby Synchronizace úložiště na webu Azure portal a potom podle levé nabídky na "Registrované servery". Uvidíte váš server uvedeny tam hned.
