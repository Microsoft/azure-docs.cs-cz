---
title: Pomocí privilegovaných koncového bodu ve službě Azure Stack | Dokumentace Microsoftu
description: Ukazuje, jak použít privilegovaný koncový bod (období) ve službě Azure Stack (pro operátory Azure stacku).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: fiseraci
ms.openlocfilehash: df1f8d805c950bdfbe2c18f365a450a6d630891b
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300434"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Pomocí privilegovaných koncového bodu ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Jako operátory Azure stacku by měl použít správce portálu, Powershellu nebo rozhraní API Azure Resource Manageru pro nejvíce každodenní úlohy správy. Však pro některé méně běžných operací, budete muset použít *privilegovaných koncový bod* (období). OBDOBÍ je předem nakonfigurované vzdálené konzoly Powershellu, poskytující s právě dostatečnou funkcemi, které vám pomohou provést požadované úlohy. Koncový bod používá [Powershellu JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) vystavit pouze omezenou sadu rutin. K přístupu období a vyvolání omezenou sadu rutin, se používá účet s nízkým oprávněním. Jsou vyžadovány žádné účty správce. Za účelem zvýšení zabezpečení není povoleno skriptování.

OBDOBÍ můžete použít k provádění následujících úloh:

- Při zpracování úloh nízké úrovně, jako například [shromažďování diagnostických protokolů](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Provádět mnoho úkolů integrace datových Center po nasazení pro integrované systémy, jako je například přidávání serverů pro předávání systému DNS (Domain Name) po nasazení, nastavení integrace Microsoft Graphu, integrace služby Active Directory Federation Services (AD FS) obměna certifikátů, atd.
- Pro práci s podporou získat dočasné, vysoké úrovně přístupu pro komplexní řešení potíží integrovaný systém.

OBDOBÍ zaznamenává všechny akce (a její odpovídající výstup), které můžete provádět v relaci Powershellu. To poskytuje plnou průhlednost a kompletní auditování operací. Můžete zachovat tyto soubory protokolu pro budoucí audity.

> [!NOTE]
> V Azure Stack Development Kit (ASDK), můžete spustit několik příkazů dostupných v období přímo z relace prostředí PowerShell na hostiteli development kit. Můžete však test některé operace pomocí období, jako je například shromažďování protokolů, protože to je jedinou metodou dostupnou k provádění určitých operací v integrovaných systémech prostředí.

## <a name="access-the-privileged-endpoint"></a>Přístup k privilegovaným koncového bodu

OBDOBÍ přistupujete prostřednictvím vzdálené relace prostředí PowerShell na virtuálním počítači, který je hostitelem období. V ASDK, je tento virtuální počítač s názvem **AzS-ERCS01**. Pokud používáte integrovaný systém, existují tři instance období, každé spuštění uvnitř virtuálního počítače (*předpony*-ERCS01, *předpony*-ERCS02, nebo *předpony*- ERCS03) na různých hostitelích pro odolnost proti chybám. 

Před zahájením tohoto postupu pro integrovaný systém, ujistěte se, že období můžete přístup podle IP adresy nebo prostřednictvím DNS. Po počátečním nasazení služby Azure Stack mít přístup období pouze podle IP adresy, protože integrace DNS ještě není nastavený. Výrobce OEM dodavatele hardwaru vám poskytne soubor JSON s názvem **AzureStackStampDeploymentInfo** , který obsahuje období IP adresy.


> [!NOTE]
> Z důvodů zabezpečení vyžadujeme, že připojíte k období pouze z posílené virtuální počítač se systémem na hostitelský hardware životního cyklu, nebo na počítači zabezpečené, vyhrazené, jako [Privileged Access pracovní stanice](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). Původní konfiguraci hostitelského hardwaru životního cyklu nesmí změnit z jeho původní konfigurace, včetně instalace nového softwaru, ani by měla sloužit k připojení období.

1. Vytvořte vztah důvěryhodnosti.

    - Na integrovaný systém spusťte následující příkaz z relace prostředí Windows PowerShell se zvýšenými oprávněními pro přidání období jako důvěryhodného hostitele, na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo Privileged Access pracovní stanice.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Pokud spouštíte ASDK, přihlaste se k hostiteli development kit.

2. Na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo Privileged Access pracovní stanice otevřete relaci Windows Powershellu. Spusťte následující příkazy k vytvoření vzdálené relace na virtuálním počítači, který je hostitelem období:
 
    - Na integrovaný systém:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` Parametr může být IP adresa nebo název DNS některého z virtuálních počítačů, jejichž hostitelem období. 
    - Pokud spouštíte ASDK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Po zobrazení výzvy použijte následující pověření:

      - **Uživatelské jméno**: Zadejte účet CloudAdmin ve formátu  **&lt; *doméně služby Azure Stack*&gt;\cloudadmin**. (ASDK, uživatelské jméno je **azurestack\cloudadmin**.)
      - **Heslo**: Zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

    > [!NOTE]
    > Pokud se nemůžete připojit ke koncovému bodu ERCS, opakujte kroky 1 a 2 opakujte s IP adresou ERCS virtuálního počítače do které jste už nevyzkoušeli pro připojení.

3.  Až se připojíte, příkazovém řádku se změní na **[*IP adresa nebo virtuální počítač ERCS pojmenujte*]: PS >** nebo **[azs-ercs01]: PS >**, v závislosti na prostředí. Z tohoto místa spuštění `Get-Command` zobrazíte seznam dostupných rutin.

    Mnohé z těchto rutin jsou určena pouze pro prostředí integrovaného systému (například rutiny související s integrací datového centra). V ASDK ověřily následující rutiny:

    - Clear hostitel
    - Zavřít PrivilegedEndpoint
    - Ukončení relace PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Objekt míry
    - Nové CloudAdminUser
    - Výchozí out-Buffer:
    - Odebrat CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tipy pro používání privilegovaných koncového bodu 

Jak je uvedeno výše, je období [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) koncového bodu. Poskytuje silné zabezpečení vrstvy, snižuje koncového bodu JEA některé základní funkce prostředí PowerShell, jako jsou dokončení skriptování, nebo kartu. Pokud se pokusíte jakýkoli typ operace skriptu, se nezdaří s chybou **ScriptsNotAllowed**. Toto je očekávané chování.

Ano například zobrazíte seznam parametrů pro danou rutinu spustíte následující příkaz:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Alternativně můžete použít [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) rutinu, která importuje všechny rutiny období do aktuální relace v místním počítači. Díky tomu všechny rutiny a funkce období jsou nyní k dispozici na místním počítači, společně s tab k dokončování příkazů a další obecně skriptování. 

Chcete-li importovat období relaci na místním počítači, proveďte následující kroky:

1. Vytvořte vztah důvěryhodnosti.

    -Na integrovaný systém spusťte následující příkaz z relace prostředí Windows PowerShell se zvýšenými oprávněními pro přidání období jako důvěryhodného hostitele, na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo Privileged Access pracovní stanice.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Pokud spouštíte ASDK, přihlaste se k hostiteli development kit.

2. Na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo Privileged Access pracovní stanice otevřete relaci Windows Powershellu. Spusťte následující příkazy k vytvoření vzdálené relace na virtuálním počítači, který je hostitelem období:
 
    - Na integrovaný systém:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` Parametr může být IP adresa nebo název DNS některého z virtuálních počítačů, jejichž hostitelem období. 
    - Pokud spouštíte ASDK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Po zobrazení výzvy použijte následující pověření:

      - **Uživatelské jméno**: Zadejte účet CloudAdmin ve formátu  **&lt; *doméně služby Azure Stack*&gt;\cloudadmin**. (ASDK, uživatelské jméno je **azurestack\cloudadmin**.)
      - **Heslo**: Zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

3. Importovat relace období do místního počítače
    ````PowerShell 
        Import-PSSession $session
    ````
4. Nyní jste – používají dokončování pomocí tabulátoru a provádět obvyklým skriptování v místní relaci Powershellu s funkcemi a rutiny období, bez snižuje stav zabezpečení služby Azure Stack. Užijte si ji!


## <a name="close-the-privileged-endpoint-session"></a>Ukončete relaci privileged koncového bodu

 Jak už bylo zmíněno dříve, období protokoluje všechny akce (a její odpovídající výstup), které můžete provádět v relaci Powershellu. Relace musí zavřete stisknutím kombinace kláves `Close-PrivilegedEndpoint` rutiny. Tato rutina správně koncový bod se zavře a přenese soubory protokolů do externí sdílené složky pro uchování.

Ukončit relaci koncový bod:

1. Vytvoření externí sdílené složky, který je přístupný období. Ve vývojovém prostředí kit můžete pouze vytvoření sdílené složky na hostiteli development kit.
2. Spustit `Close-PrivilegedEndpoint` rutiny. 
3. Zobrazí se výzva pro cestu, na které se má uložit soubor protokolu přepisu. Určete sdílenou složku souboru, který jste vytvořili dříve, ve formátu &#92; &#92; *servername*&#92;*sharename*. Pokud nezadáte cestu, rutina selže a relace zůstane otevřená. 

    ![Zavřít PrivilegedEndpoint rutiny výstup, který ukazuje, kde zadáte cestu k cílovému přepisu](media/azure-stack-privileged-endpoint/closeendpoint.png)

Po přepisu protokolové soubory jsou úspěšně převedena do sdílené složky, se automaticky odstraní z období. 

> [!NOTE]
> Pokud zavřete relaci období pomocí rutin `Exit-PSSession` nebo `Exit`, nebo pouze zavřete konzolu Powershellu, protokoly přepisu se nepřevádějí do sdílené složky. Zůstanou v období. Při příštím spuštění `Close-PrivilegedEndpoint` a zahrnují sdílené složky, přepisu protokoly z předchozí relace také přenášet. Nepoužívejte `Exit-PSSession` nebo `Exit` ukončit relaci období; použijte `Close-PrivilegedEndpoint` místo.


## <a name="next-steps"></a>Další postup
[Azure Stack diagnostické nástroje](azure-stack-diagnostics.md)
