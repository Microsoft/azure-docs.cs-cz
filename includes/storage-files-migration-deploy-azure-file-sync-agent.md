---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7c1ea3fbf8e5cef4b1e8f8a2b0963fdd66584f5a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075554"
---
V této části nainstalujete agenta Synchronizace souborů Azure do instance Windows serveru.

[Průvodce nasazením](../articles/storage/files/storage-sync-files-deployment-guide.md) znázorňuje, že je nutné vypnout **konfiguraci rozšířeného zabezpečení aplikace Internet Explorer**. Tato míra zabezpečení není k dispozici u Synchronizace souborů Azure. Vypnutím IT můžete provádět ověřování v Azure bez jakýchkoli problémů.

Otevřete PowerShell a nainstalujte požadované moduly PowerShellu pomocí následujících příkazů. Po zobrazení výzvy nezapomeňte nainstalovat úplný modul a poskytovatele NuGet.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Pokud máte nějaké problémy, které se doblíží k Internetu z vašeho serveru, teď je čas k jejich řešení. Synchronizace souborů Azure používá jakékoli dostupné síťové připojení k Internetu. Podporuje se taky proxy server pro přístup k Internetu. V průběhu instalace agenta můžete buď nakonfigurovat proxy server v rámci místního počítače, nebo zadat proxy server, který bude používat pouze Synchronizace souborů Azure.

Pokud konfigurujete proxy server, budete muset otevřít brány firewall pro tento server, tento přístup může být pro vás přijatelný. Po dokončení instalace serveru vám v sestavě připojení k síti zobrazíte přesnou adresu URL koncových bodů v Azure, které Synchronizace souborů Azure musí komunikovat s pro vybranou oblast. Tato sestava také vysvětluje důvod, proč je komunikace nutná. Sestavu můžete použít k uzamknutí bran firewall kolem tohoto serveru na konkrétní adresy URL.

Můžete také postupovat podle konzervativního přístupu, ve kterém neotevíráte brány firewall, ale místo toho omezit server tak, aby komunikoval s obory názvů DNS vyšší úrovně. Další informace najdete v tématu [nastavení proxy serveru a brány firewall pro synchronizace souborů Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Řiďte se doporučenými osvědčenými postupy pro sítě.

Na konci Průvodce instalací serveru se otevře Průvodce registrací serveru. Zaregistrujte server do prostředku Azure služby synchronizace úložiště ze starší verze.

Tyto kroky jsou podrobněji popsané v Průvodci nasazením, který zahrnuje moduly PowerShellu, které byste měli nejdřív nainstalovat: [synchronizace souborů Azure instalaci agenta](../articles/storage/files/storage-sync-files-deployment-guide.md).

Použijte nejnovějšího agenta. Můžete si ho stáhnout z webu Microsoft Download Center: [Agent synchronizace souborů Azure](https://aka.ms/AFS/agent "Stažení agenta Synchronizace souborů Azure").

Po úspěšné instalaci a registraci serveru můžete ověřit, že jste úspěšně dokončili tento krok. V Azure Portal přejít na prostředek služby synchronizace úložiště. V nabídce vlevo přejděte na **registrované servery**. Zobrazí se váš server v seznamu.
