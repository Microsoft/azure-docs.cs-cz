---
title: Nasazení agenta Azure File Sync
description: Nasazení agenta Azure File Sync. Společný textový blok, který je sdílen mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209412"
---
V této části nainstalujete agenta Azure File Sync na Windows Server.
[Průvodce nasazením](../articles/storage/files/storage-sync-files-deployment-guide.md) znázorňuje, že je nutné vypnout **Rozšířené zabezpečení aplikace Internet Explorer**. Rozšířené zabezpečení IE je bezpečnostní opatření, které se nedá použít Azure File Sync a jeho vypnutí vám umožňuje provádět ověřování v Azure bez jakýchkoli problémů.

Otevřete PowerShell a nainstalujte požadované moduly PowerShellu s následujícími příkazy. Po zobrazení výzvy nezapomeňte nainstalovat úplný modul a poskytovatele NuGet:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Pokud máte nějaké problémy, které se doblíží k Internetu z vašeho serveru, teď je čas k jejich řešení. Azure File Sync používá jakékoli dostupné síťové připojení k Internetu.
Podporuje se taky proxy server pro přístup k Internetu. V průběhu instalace agenta můžete buď nakonfigurovat proxy server v rámci místního počítače, nebo zadat proxy server, který bude používat jenom synchronizace souborů.

Pokud to znamená, musíte pro tento server otevřít brány firewall a pak to může být přijatelný přístup. Po dokončení instalace serveru bude po dokončení registrace serveru zobrazena sestava připojení k síti s přesnými adresami URL koncových bodů v Azure. Tato synchronizace souborů musí komunikovat s pro vybranou oblast. Tato sestava také vysvětluje důvod, proč je komunikace nutná. Pomocí této sestavy můžete na konkrétní adresy URL uzamknout brány firewall kolem tohoto serveru.

Můžete také použít lépe konzervativní přístup, ve kterém nemusíte otevírat brány firewall, ale místo toho omezit server tak, aby komunikoval s obory názvů DNS vyšší úrovně – k dispozici je další dokumentace a podrobnosti v článku [Azure File Sync proxy a nastavení brány firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) . Řiďte se doporučenými osvědčenými postupy pro sítě.

Na konci průvodce *instalací* serveru se automaticky otevře průvodce *registrací* serveru.
Zaregistrujte server do služby synchronizace úložiště v prostředku Azure ze starší verze.

Tyto kroky jsou podrobněji popsané v Průvodci nasazením, včetně výše uvedených modulů PowerShellu, které byste měli nejdřív nainstalovat: [Azure File Sync instalaci agenta](../articles/storage/files/storage-sync-files-deployment-guide.md).

Nejnovějšího agenta by se měl použít a stáhnout ho z webu Microsoft Download Center: [Azure File Sync-agent](https://aka.ms/AFS/agent "Stažení agenta Azure File Sync").

Po úspěšné instalaci a registraci serveru můžete ověřit, že jste úspěšně dokončili tento krok: přejděte do prostředku služby synchronizace úložiště v Azure Portal a pak postupujte podle levé nabídky na registrované servery. Váš server bude hned zobrazen.
