---
title: Nasazení agenta Azure File Sync
description: Nasazení agenta Azure File Sync. Společný textový blok sdílený v rámci migračních dokumentů.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143614"
---
V této části nainstalujete agenta Azure File Sync do instance Windows serveru.

[Průvodce nasazením](../articles/storage/files/storage-sync-files-deployment-guide.md) znázorňuje, že je nutné vypnout **konfiguraci rozšířeného zabezpečení aplikace Internet Explorer**. Tato míra zabezpečení se nedá použít u Azure File Sync. Vypnutím IT můžete provádět ověřování v Azure bez jakýchkoli problémů.

Otevřete PowerShell a nainstalujte požadované moduly PowerShellu pomocí následujících příkazů. Po zobrazení výzvy nezapomeňte nainstalovat úplný modul a poskytovatele NuGet.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Pokud máte nějaké problémy, které se doblíží k Internetu z vašeho serveru, teď je čas k jejich řešení. Azure File Sync používá jakékoli dostupné síťové připojení k Internetu. Podporuje se taky proxy server pro přístup k Internetu. V průběhu instalace agenta můžete buď nakonfigurovat proxy server pro všechny počítače, nebo zadat proxy server, který bude používat pouze Azure File Sync.

Pokud konfigurujete proxy server, budete muset otevřít brány firewall pro tento server, což může být přijatelný přístup. Po dokončení instalace serveru vám v sestavě připojení k síti zobrazíte přesnou adresu URL koncových bodů v Azure, které Azure File Sync musí komunikovat s pro vybranou oblast. Tato sestava také vysvětluje důvod, proč je komunikace nutná. Sestavu můžete použít k uzamknutí bran firewall kolem tohoto serveru na konkrétní adresy URL.

Můžete také postupovat podle konzervativního přístupu, ve kterém neotevíráte brány firewall, ale místo toho omezit server tak, aby komunikoval s obory názvů DNS vyšší úrovně. Další informace najdete v tématu [nastavení proxy serveru a brány firewall pro Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Řiďte se doporučenými osvědčenými postupy pro sítě.

Na konci průvodce *instalací* serveru se otevře průvodce *registrací* serveru. Zaregistrujte server do prostředku Azure služby synchronizace úložiště ze starší verze.

Tyto kroky jsou podrobněji popsané v Průvodci nasazením, včetně modulů PowerShellu, které byste měli nejdřív nainstalovat: [Azure File Sync instalaci agenta](../articles/storage/files/storage-sync-files-deployment-guide.md).

Použijte nejnovějšího agenta. Můžete si ho stáhnout z webu Microsoft Download Center: [Agent Azure File Sync](https://aka.ms/AFS/agent "Stažení agenta Azure File Sync").

Po úspěšné instalaci a registraci serveru můžete ověřit, že jste úspěšně dokončili tento krok. Přejděte do prostředku služby synchronizace úložiště v Azure Portal a pak podle levé nabídky **Zaregistrujte servery**. Zobrazí se váš server v seznamu.
