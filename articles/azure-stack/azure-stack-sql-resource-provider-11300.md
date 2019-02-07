---
title: Azure Stack SQL prostředků poskytovatele 1.1.30.0 poznámky k verzi | Dokumentace Microsoftu
description: Další informace o novinky v nejnovější Azure Stack prostředků poskytovatele aktualizaci SQL, včetně všech známých problémů a kde ho můžete stáhnout.
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
ms.date: 1/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 1/09/2019
ms.openlocfilehash: aaa4dc48f237a4647cfde330e40eda91eeb1388d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768728"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Poznámky k verzi 1.1.30.0 poskytovatele prostředků SQL

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známých problémech ve verzi poskytovatele prostředků 1.1.30.0 SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhnout poskytovatele prostředků SQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure Stack sestavení. Minimální verze služby Azure Stack požadovaná k instalaci této verze poskytovatele prostředků SQL jsou uvedeny níže:

> |Minimální verze služby Azure Stack|Verze poskytovatele prostředků SQL|
> |-----|-----|
> |Verze. 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Platí minimální podporované aktualizace služby Azure Stack pro Azure Stack integrované systému nebo nasadit nejnovější Azure Stack Development Kit (ASDK) před nasazením nejnovější verzi poskytovatele prostředků SQL.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack SQL zahrnuje následující vylepšení a opravy:

- **Telemetrie pro nasazení poskytovatele prostředků SQL povoleno**. Nasazení poskytovatele prostředků SQL je povoleno shromažďování telemetrie. Shromažďovat telemetrii zahrnuje nasazení poskytovatele prostředků, spuštění a zastavení časy, ukončete stavové zprávy o ukončení a podrobnosti o chybě (pokud existuje).

- **Aktualizace šifrování TLS 1.2**. Povoleno jen 1.2 podpora TLS pro prostředek zprostředkovatele komunikace s interní komponenty služby Azure Stack. 

### <a name="fixes"></a>Opravy

- **Kompatibilita Azure Stack Powershellu poskytovatele prostředků SQL**. Poskytovatele prostředků SQL byla aktualizována na pracovním profilem Azure Stack 2018-03-01hybridní prostředí PowerShell a k zajištění kompatibility se AzureRM 1.3.0 a novější.

- **Okno heslo změnit přihlašovací jméno SQL**. Opravili jsme problém, kde heslo nelze změnit, v okně změnit heslo. Upozornění na změnu odstraněny odkazy z hesla.

- **Hostování aktualizovat okno nastavení serveru SQL**. Opravili jsme problém, kde v okně nastavení byla správně s názvem jako "Password".

## <a name="known-issues"></a>Známé problémy 

- **SKU SQL může trvat až hodinu, uvidí na portálu**. Může trvat až hodinu nově vytvořený skladová jednotka byla viditelná jenom pro použití při vytváření nové databáze SQL. 

    **Alternativní řešení**: Žádné.

- **Znovu použít přihlášeních SQL**. Pokus o vytvoření nové SQL přihlásit se pomocí stejné uživatelské jméno jako stávající přihlašovací údaje v rámci stejného předplatného způsobí opětovné použití stejné přihlašovací údaje a stávající heslo. 

    **Alternativní řešení**: Použít různá uživatelská jména, při vytváření nové přihlašovací údaje v rámci stejného předplatného nebo vytvářet přihlášení se stejným uživatelským jménem v rámci různých předplatných.

- **Sdílené přihlašovací údaje SQL způsobovat datové nekonzistence**. Pokud je přihlašovací jméno SQL sdílí pro více databází SQL v rámci stejného předplatného, změna hesla přihlášení způsobí nekonzistenci dat.

    **Alternativní řešení**: Vždy používejte jiné přihlašovací údaje pro různé databáze v rámci stejného předplatného.

- **Požadavek na podporu protokolu TLS 1.2**. Pokud se pokusíte nasadit nebo aktualizovat poskytovatele prostředků SQL na počítači, kde není povolený protokol TLS 1.2, může operace selhat. Spusťte následující příkaz Powershellu na počítači, který se použije k nasazení nebo aktualizujte zprostředkovatele prostředků k ověření, že se vrátí TLS 1.2, podporuje:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Pokud **Tls12** není součástí výstupu příkazu v počítači není povolená TLS 1.2.

    **Alternativní řešení**: Spusťte následující příkaz Powershellu k povolení protokolu TLS 1.2 a spusťte nasazení poskytovatele prostředků nebo aktualizujte skript v téže relaci prostředí PowerShell:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **Poskytovatele prostředků SQL se nepodařilo přidat naslouchací proces SQL serveru Always On**. Pokud používáte SQL Server vždy na naslouchací proces IP adresu naslouchacího procesu, poskytovatele prostředků SQL virtuálního počítače nemůže přeložit název hostitele naslouchacího procesu.

    **Alternativní řešení**: Ujistěte se, že DNS funguje správně přeložit IP adresu naslouchacího procesu na název naslouchacího procesu hostitele.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure Stack
Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další postup
[Další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgrade z předchozí verze poskytovatele prostředků SQL](azure-stack-sql-resource-provider-update.md). 