---
title: Azure Stack MySQL prostředků poskytovatele 1.1.30.0 poznámky k verzi | Dokumentace Microsoftu
description: Další informace o novinky v nejnovější Azure Stack MySQL prostředků poskytovatele aktualizaci, včetně všech známých problémů a kde ho můžete stáhnout.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: 0b8bf94ba99f36662f03a48ca1f09366ddf2aa40
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243504"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>Poznámky k verzi poskytovatele 1.1.30.0 prostředků MySQL

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy v MySQL verze zprostředkovatele prostředků 1.1.30.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhnout poskytovatele prostředků MySQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure Stack sestavení. Minimální verze služby Azure Stack požadovaná k instalaci této verze poskytovatele prostředků MySQL jsou uvedeny níže:

> |Minimální verze služby Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Aktualizace Azure Stack. 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Platí minimální podporované aktualizace služby Azure Stack pro Azure Stack integrované systému nebo nasadit nejnovější Azure Stack Development Kit (ASDK) před nasazením nejnovější verzi poskytovatele prostředků MySQL.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack MySQL zahrnuje následující vylepšení a opravy:

- **Telemetrická data povolená pro nasazení poskytovatele prostředků MySQL**. Nasazení poskytovatele prostředků MySQL je povoleno shromažďování telemetrie. Shromažďovat telemetrii zahrnuje nasazení poskytovatele prostředků, spuštění a zastavení časy, ukončete stavové zprávy o ukončení a podrobnosti o chybě (pokud existuje).

- **Aktualizace šifrování TLS 1.2**. Povoleno jen 1.2 podpora TLS pro prostředek zprostředkovatele komunikace s interní komponenty služby Azure Stack. 

### <a name="fixes"></a>Opravy

- **Kompatibilita Azure Stack Powershellu poskytovatele prostředků MySQL**. Poskytovatele prostředků MySQL byla aktualizována na pracovním profilem Azure Stack 2018-03-01hybridní prostředí PowerShell a k zajištění kompatibility se AzureRM 1.3.0 a novější.

- **MySQL přihlášení změnit heslo okno**. Opravili jsme problém, kde heslo nelze změnit, v okně změnit heslo. Upozornění na změnu odstraněny odkazy z hesla.

## <a name="known-issues"></a>Známé problémy 

- **SKU MySQL může trvat až hodinu, uvidí na portálu**. Může trvat až hodinu nově vytvořený skladová jednotka byla viditelná jenom pro použití při vytváření nových databází MySQL. 

    **Alternativní řešení**: Žádné.

- **Znovu použít přihlašovací údaje MySQL**. Při pokusu o vytvoření nové databáze MySQL přihlášení pomocí stejné uživatelské jméno jako stávající přihlašovací údaje v rámci stejného předplatného způsobí opětovné použití stejné přihlašovací údaje a stávající heslo. 

    **Alternativní řešení**: Použít různá uživatelská jména, při vytváření nové přihlašovací údaje v rámci stejného předplatného nebo vytvářet přihlášení se stejným uživatelským jménem v rámci různých předplatných.

- **Požadavek na podporu protokolu TLS 1.2**. Pokud se pokusíte nasadit nebo aktualizovat poskytovatele prostředků MySQL z počítače, kde není povolený protokol TLS 1.2, může operace selhat. Spusťte následující příkaz Powershellu na počítači, který se použije k nasazení nebo aktualizujte zprostředkovatele prostředků k ověření, že se vrátí TLS 1.2, podporuje:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Pokud **Tls12** není součástí výstupu příkazu v počítači není povolená TLS 1.2.

    **Alternativní řešení**: Spusťte následující příkaz Powershellu k povolení protokolu TLS 1.2 a spusťte nasazení poskytovatele prostředků nebo aktualizujte skript v téže relaci prostředí PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure Stack
Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další postup
[Další informace o poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgrade z předchozí verze poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-update.md). 