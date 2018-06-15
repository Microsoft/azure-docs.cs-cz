---
title: Služby App Service v Azure zásobníku aktualizace 2 poznámky k verzi | Microsoft Docs
description: Další informace o tom, co je v aktualizaci dva jsou pro aplikaci služby v Azure zásobníku, známé problémy a kde se stáhnout aktualizaci.
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
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360373"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Aplikační služby na Azure zásobníku poznámky k verzi update 2

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service na Azure zásobníku Update 2 a všech známých problémů. Známé problémy se dál dělí na problémy, které jsou přímo týkající se nasazení, proces aktualizace a problémy s sestavení (po instalaci).

> [!IMPORTANT]
> Použitím 1804 aktualizace v zásobníku Azure integrované systému nebo nasadit nejnovější development kit zásobník Azure před nasazením Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Odkaz na sestavení

App Service na číslo sestavení Azure zásobníku Update 2 je **72.0.13698.10**

### <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Nová nasazení služby Azure App Service v zásobníku Azure nyní vyžadují [certifikát se zástupným znakem tři subjektu](azure-stack-app-service-before-you-get-started.md#get-certificates) z důvodu vylepšení způsobem, ve kterém se nyní zpracovává jednotného přihlašování pro Kudu ve službě Azure App Service. Nové subjektem je  **\*. sso.appservice.\< oblast\>.\< domainname\>.\< rozšíření\>**
>
>

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service na Azure zásobníku Update 2 obsahuje následující vylepšení a opravy:

- Aktualizace **klienta služby aplikace, správce, portálů funkce a nástroje Kudu**. Konzistentní s verzí Azure SDK portálu zásobníku.

- Aktualizace základní služby ke zlepšení spolehlivosti a chybových zpráv, povolení jednodušší diagnóza běžné problémy.

- **Aktualizace na tyto architektury aplikace a nástroje pro**:
  - Přidání rozhraní .net Framework 4.7.1
  - Přidat **Node.JS** verze:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Přidat **NPM** verze:
    - 5.6.0
  - Aktualizovat .net základní součásti, které bylo v souladu s Azure App Service ve veřejném cloudu.
  - Aktualizované Kudu

- Swap automatické nasazení přihrádek povolena – funkce [konfigurace automatického odkládacího souboru](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- Testování v produkčním funkce povoleny – [Úvod k testování v produkčním prostředí](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure proxy funkce povoleny – [pracovat s funkcí proxy Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- Rozšíření služby App Service správce UX podporují zahrnout:
  - Tajný otočení
  - Otočení certifikátu
  - Otočení přihlašovací údaje systému
  - Otočení řetězec připojení

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Jsou možné vás zastihnout souborového serveru při nasazení aplikace služby v existující virtuální síť a souborový server je k dispozici v soukromé síti pouze zaměstnanci.

Pokud jste vybrali k nasazení do existující virtuální síť a interní IP adresu připojit k souborovému serveru, je nutné přidat pravidlo odchozí zabezpečení povolení přenosy SMB mezi podsíť pracovního procesu a souborový server. Chcete-li to provést, přejděte do WorkersNsg v portálu pro správu a přidejte pravidlo odchozí zabezpečení s následujícími vlastnostmi:
 * Zdroj: žádné
 * Zdroj rozsah portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol: TCP
 * Akce: Povolit
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu operační Azure App Service v Azure zásobníku

V dokumentaci v [poznámky k verzi 1804 zásobník Azure](azure-stack-update-1804.md)

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na přehled Azure zásobníku](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service v zásobníku Azure najdete v tématu [před zahájením práce s App Service v Azure zásobníku](azure-stack-app-service-before-you-get-started.md).
