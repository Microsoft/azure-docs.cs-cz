---
title: App Service ve službě Azure Stack aktualizace 2 poznámky k verzi | Dokumentace Microsoftu
description: Další informace o tom, co je v aktualizaci 2 pro App Service ve službě Azure Stack, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: ec0c80925770c75ee1f23df29f1724444c1e7337
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249659"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service v Azure stacku zpráva k vydání verze update 2

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a oprav ve službě Azure App Service v Azure stacku Update 2 a známých problémech. Známé problémy jsou rozděleny do týkající se přímo k nasazení, aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Aktualizace 1804 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Referenční informace o buildu

App Service na číslo sestavení aktualizace 2 Azure Stack je **72.0.13698.10**

### <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Nyní vyžadují nové nasazení služby Azure App Service ve službě Azure Stack [certifikát se zástupným znakem tři subjektu](azure-stack-app-service-before-you-get-started.md#get-certificates) z důvodu vylepšení způsobem, ve kterém je jednotné přihlašování pro Kudu teď provádí ve službě Azure App Service. Je nový předmět  **\*. sso.appservice.\< oblast\>.\< domainname\>.\< rozšíření\>**
>
>

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure stacku Update 2 zahrnuje následující vylepšení a opravy:

- Aktualizace **aplikace služeb pro klienty, Admin, portály funkce a nástroje Kudu**. Konzistentní s verzí sady SDK portálu Azure Stack.

- Aktualizace základní službu, ke zlepšení spolehlivosti a chybových zpráv umožňuje snazší Diagnostika běžných problémů.

- **Aktualizace následujících aplikační architektury a nástroje**:
  - Přidání rozhraní .net Framework 4.7.1
  - Přidání **Node.JS** verze:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Přidání **NPM** verze:
    - 5.6.0
  - Aktualizace.Net Core součásti bylo v souladu s Azure App Service ve veřejném cloudu.
  - Aktualizované Kudu

- Nasazení automatické prohození slotů povolena – funkce [konfiguraci automatického prohození](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- Testování v produkčním funkce povoleny – [Úvod do testování v produkčním prostředí](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Proxy služby Azure Functions povoleny – [pracovat s proxy služby Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- Rozšíření správy služby App Service podporují UX přidány pro:
  - Otočení tajného kódu
  - Obměna certifikátů
  - Otočení přihlašovacích údajů systému
  - Připojovací řetězec otočení

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pracovní procesy se nám kontaktovat souborového serveru při nasazení služby App Service v existující virtuální sítě a souborový server je k dispozici v privátní síti.

Pokud jste se rozhodli nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, je nutné přidat odchozí pravidlo zabezpečení, povolení provozu SMB mezi podsítě pracovního procesu a souborový server. Chcete-li to provést, přejděte na WorkersNsg v portálu pro správu a přidat odchozí pravidlo zabezpečení s následujícími vlastnostmi:
 * Zdroj: Všechny
 * Zdrojový rozsah portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: Rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol: TCP
 * Akce: Povolit
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure App Service ve službě Azure Stack

Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack 1804](azure-stack-update-1804.md)

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na Přehled služby Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service ve službě Azure Stack najdete v tématu [před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md).
