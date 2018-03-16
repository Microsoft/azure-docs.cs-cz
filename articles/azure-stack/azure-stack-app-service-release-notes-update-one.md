---
title: "Aplikační služby na Azure zásobníku aktualizace jeden | Microsoft Docs"
description: "Další informace o tom, co je v aktualizaci, jeden pro službu App Service v Azure zásobníku známé problémy a toho, kde se stáhnout aktualizaci."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>Služby App Service v Azure zásobníku aktualizovat jeden poznámky k verzi

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service na Azure zásobníku Update 1 a všech známých problémů. Známé problémy se dál dělí na problémy, které jsou přímo týkající se nasazení, proces aktualizace a problémy s sestavení (po instalaci).

> [!IMPORTANT]
> Použitím 1802 aktualizace v zásobníku Azure integrované systému nebo nasadit nejnovější development kit zásobník Azure před nasazením služby Azure App Service.
>
>

## <a name="build-reference"></a>Odkaz na sestavení

App Service na číslo sestavení Azure zásobníku Update 1 je **69.0.13698.9**

### <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Aplikační služba Azure v zásobníku Azure nyní vyžaduje [certifikát se zástupným znakem tři subjektu](azure-stack-app-service-before-you-get-started.md#get-certificates) z důvodu vylepšení způsobem, ve kterém se nyní zpracovává jednotného přihlašování pro Kudu ve službě Azure App Service.  Nové subjektem je ** *. sso.appservice.<region>. <domainname>.<extension>**
>
>

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service na Azure zásobníku Update 1 obsahuje následující vylepšení a opravy:

- **Vysoká dostupnost služby Azure App Service** -poruch 1802 zásobníku Azure povolená aktualizace úloh k nasazení napříč doménami.  Infrastruktura služby App Service je proto může být odolné proti chybám, jak bude nasazen napříč doménami selhání.  Ve výchozím nastavení všechna nová nasazení služby Azure App Service bude mít tato funkce ale nasazení před 1802 zásobník Azure dokončil aktualizace, které bylo použito najdete [dokumentace doména selhání aplikace služby](azure-stack-app-service-fault-domain-update.md)

- **Nasazení v existující virtuální síť** -můžou nyní zákazníci nasadit služby App Service v zásobníku Azure v rámci existující virtuální síť.  Nasazení v existující virtuální síť umožňuje zákazníkům pro připojení k systému SQL Server a souborový Server, vyžaduje se pro Azure App Service přes privátní porty.  Během nasazování můžete vybrat zákazníků k nasazení v existující virtuální síť, ale [musíte vytvořit podsítě pro použití službou App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) před jejich nasazením.

- Aktualizace **klienta služby aplikace, správce, portálů funkce a nástroje Kudu**.  Konzistentní s verzí Azure SDK portálu zásobníku.

- **Aktualizace na tyto architektury aplikace a nástroje pro**:
    - Přidat **základní rozhraní .net 2.0** podporu
    - Přidat **Node.JS** verze:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Přidat **NPM** verze:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Přidat **PHP** aktualizace:
        - 5.6.32
        - 7.0.26 (x86 a x64)
        - 7.1.12 (x86 a x64)
    - Aktualizovat **Git pro Windows** až v 2.14.1
    - Aktualizovat **Mercurial** k v4.5.0

  - Přidání podpory pro **pouze HTTPS** funkce v rámci vlastní domény funkce na portálu aplikace služby klienta. 

  - Přidání ověření připojení úložiště v dialogu pro výběr vlastní úložiště pro Azure Functions 

#### <a name="fixes"></a>Opravy

- Když vytváříte balíček offline nasazení, zákazníků se už nebude přístup odepřen chybová zpráva při otevření složky z instalačního programu služby App Service

- Vyřešení problémů při práci ve funkci vlastní domény na portálu aplikace služby klienta.

- Zabránit zákazníky používající názvy vyhrazené správce během instalace

- Povoleno nasazení aplikace služby s **připojený k doméně** souborového serveru

- Vylepšené načítání zásobník Azure kořenových certifikátů ve skriptu a nyní ověřit kořenového certifikátu v instalačním programu služby App Service.

- Pevný nesprávný stav nevrátila do Azure Resource Manageru, pokud je předplatné odstranit tento obsažených prostředků v oboru názvů Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Známé problémy v procesu nasazení

- Nejsou žádné známé problémy pro nasazení služby Azure App Service na Azure zásobníku Update 1.

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Nejsou žádné známé problémy pro aktualizaci služby Azure App Service na Azure zásobníku Update 1.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Nejsou žádné známé problémy pro instalaci služby Azure App Service na Azure zásobníku Update 1.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu operační Azure App Service v Azure zásobníku

V dokumentaci v [poznámky k verzi 1802 zásobník Azure](azure-stack-update-1802.md)

## <a name="see-also"></a>Další informace najdete v tématech

- Přehled služby Azure App Service najdete v tématu [Azure App Service na přehled Azure zásobníku](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service v zásobníku Azure najdete v tématu [před zahájením práce s App Service v Azure zásobníku](azure-stack-app-service-before-you-get-started.md).
