---
title: Zpráva k vydání verze Azure Stack ověření jako služba | Dokumentace Microsoftu
description: Zpráva k vydání verze Azure Stack ověření jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 5252eed66018cd2028545567dfe62ca7ba17be7e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247812"
---
# <a name="release-notes-for-validation-as-a-service"></a>Zpráva k vydání verze pro ověření jako služba

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Tento článek obsahuje poznámky k verzi pro Azure Stack ověření jako služba.

## <a name="version-405"></a>Verze 4.0.5
2019 ledna 17

-  Test identifikace disku aktualizovat, aby adresa nekonzistence fondu úložiště. Verze: 5.1.14.0  -> 5.1.15.0
-  Azure Stack měsíční aktualizace ověření aktualizovat na adresu schválený software a obsah ověření nekonzistence. Verze: 5.1.14.0  -> 5.1.15.0
-  Ověření balíčku výrobce OEM rozšíření aktualizovat, aby provést nezbytné kontroly *před* krok aktualizace služby Azure Stack. Verze: 5.1.14.0  -> 5.1.15.0
-  Interní opravy chyb



## <a name="version-402"></a>Verze 4.0.2

2019 dne 7

Pokud používáte Azure Stack měsíční aktualizace ověření pracovního postupu a verze pro aktualizace balíčku výrobce OEM není 1810 nebo vyšší, obdržíte chybu po načtení krok aktualizace výrobce OEM. Jedná se o chybu. Oprava se vyvíjejí. Kroky pro zmírnění rizika jsou následující:

1.  Výrobce OEM aktualizaci spusťte jako za normálních okolností.
2.  Spustit Test AzureStack po úspěšném použití balíčku a uložte si výstup.
3.  Zrušení testu.
4.  Odeslat uložený výstup do VaaSHelp@microsoft.com přijímat předávání výsledky spuštění.

## <a name="version-402"></a>Verze 4.0.2

30. listopadu 2018

- Interní opravy chyb

## <a name="version-401"></a>Verze 4.0.1

8. října 2018

- VaaS požadavky

    `Install-VaaSPrerequisites` už vyžadují přihlašovací údaje správce cloudu. Pokud používáte nejnovější verzi této rutiny, přečtěte si téma [stáhnout a nainstalovat agenta](azure-stack-vaas-local-agent.md#download-and-install-the-agent) pro revidovaný příkazy pro instalaci požadavků. Tady jsou příkazy:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Verze 4.0.0

29. srpna 2018

- Aktualizuje VaaS požadavky a virtuálního pevného disku

    `Install-VaaSPrerequisites` nyní vyžadují přihlašovací údaje správce cloudových řešení problémů při ověřování řešení. V dokumentaci na [stáhnout a nainstalovat agenta](azure-stack-vaas-local-agent.md#download-and-install-the-agent) má aktualizované a přinášejí následující:

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > `$CloudAdminCreds` Vyžaduje skript pro službu Azure Stack instanci Probíhá ověřování. Nejsou používané tenanta VaaS přihlašovací údaje Azure Active Directory.

- Aktualizace místního agenta

    Předchozí verze místní agent není kompatibilní s aktuální 4.0.0 vydání služby. Všichni uživatelé musí aktualizovat svá místní agenti. Zobrazit [stáhnout a nainstalovat agenta](azure-stack-vaas-local-agent.md#download-and-install-the-agent) pokyny k instalaci nejnovějšího agenta.

- Aktualizace automatizace Powershellu

    Provedly se změny `LaunchVaaSTests` Powershellové skripty, které vyžadují nejnovější verzi skriptování balíčků. Zobrazit [spuštění pracovního postupu průchodu testů](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) pokyny k instalaci nejnovější verze skriptovací balíček.

- Ověření jako portál služby

  - Podepisování oznámení balíčku

    Při odeslání balíčku výrobce OEM přizpůsobení jako součást pracovního postupu řešení ověřování se ověří formát balíčku k zajištění, že splňuje specifikaci publikovaná. Pokud balíček není v souladu, se nezdaří spuštění. E-mailová oznámení se pošle e-mailovou adresu registrované kontaktování Azure Active Directory pro příslušného tenanta.

  - Kategorie interaktivní testu

    **Interaktivní** byla přidána kategorie testu. Tyto testy umožňují partneři vykonávat interaktivní, neautomatický scénáře služby Azure Stack.

  - Interaktivní funkce ověřování

    Schopnost poskytnout zpětnou vazbu cílené pro určité funkce je teď k dispozici v pracovním postupu průchodu testu. `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` Test zkontroluje, pokud byly správně použili konkrétní aktualizace a pak shromažďuje zpětnou vazbu.

## <a name="next-steps"></a>Další postup

- [Řešení potíží s ověření jako služba](azure-stack-vaas-troubleshoot.md)
