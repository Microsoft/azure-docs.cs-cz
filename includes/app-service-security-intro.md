---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83649051"
---
Komponenty platformy App Service, včetně virtuálních počítačů Azure, úložiště, síťových připojení, webových rozhraní, funkcí pro správu a integrace, jsou aktivně zabezpečeny a zpřísněny. App Service projde průběžnými kontrolami dodržování předpisů, aby se zajistilo, že:

- Prostředky vaší aplikace jsou [zabezpečené](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z prostředků Azure jiných zákazníků.
- [Instance virtuálních počítačů a běhový software se pravidelně aktualizují](../articles/app-service/overview-patch-os-runtime.md) a řeší nově zjištěná ohrožení zabezpečení. 
- Komunikace s tajnými kódy (jako jsou připojovací řetězce) mezi vaší aplikací a dalšími prostředky Azure (například [SQL Database](https://azure.microsoft.com/services/sql-database/)) zůstává v Azure a nepřekračuje žádné hranice sítě. Tajné kódy jsou při uložení vždy zašifrovány.
- Veškerá komunikace přes App Service funkce připojení, jako je [hybridní připojení](../articles/app-service/app-service-hybrid-connections.md), je zašifrovaná. 
- Připojení ke vzdáleným nástrojům pro správu, jako je Azure PowerShell, Azure CLI, sady SDK Azure a rozhraní REST API, jsou šifrovaná.
- 24 hodinová Správa hrozeb chrání infrastrukturu a platformu proti malwaru, distribuovaným útokům na DDoS (man-in-the-middle), MITM a dalším hrozbám.

Další informace o infrastruktuře a zabezpečení platforem v Azure najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/overview/trusted-cloud/).