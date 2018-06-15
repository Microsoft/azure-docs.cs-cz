---
title: Pomocí privilegované koncový bod v zásobníku Azure | Microsoft Docs
description: Ukazuje, jak používat privilegované koncový bod (období) v zásobníku Azure (pro operátor zásobník Azure).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: fiseraci
ms.openlocfilehash: 9fb928b7cb8e1a83734b64a8b9c19bc3cf3203ba
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153180"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Pomocí privilegované koncový bod v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Jako operátor zásobník Azure měli byste použít správce portálu, prostředí PowerShell nebo rozhraní API Správce Azure Resource Manageru pro nejvíce každodenní úlohy správy. Ale pro některé méně běžné operace, budete muset použít *privilegované koncový bod* (období). OBDOBÍ je předem nakonfigurovaný vzdálené konzoly prostředí PowerShell, který poskytuje akorát, funkce, které vám pomohou provést požadované úlohy. Koncový bod používá [JEA prostředí PowerShell (jen dostatečně správy)](https://docs.microsoft.com/powershell/jea/overview) vystavit jenom omezenou sadu rutin. Pro přístup období a vyvolání omezenou sadu rutin, se používá účet s nízkou úrovní oprávnění. Jsou vyžadovány žádné účty správce. Pro dodatečné zabezpečení není povoleno skriptování.

OBDOBÍ můžete použít k provedení následujících úloh:

- Úkoly nízké úrovně, jako například [shromažďování diagnostických protokolů](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- K provádění úloh po nasazení datacenter integrace pro integrované systémy, jako je například přidávání serverů pro předávání systému DNS (Domain Name) po nasazení nastavení integrace Microsoft Graph, integrace služby Active Directory Federation Services (AD FS) certifikát otočení atd.
- Pro práci s podporou získat dočasný vysoké úrovně přístup pro odstraňování potíží integrovaného systému.

OBDOBÍ zaznamená každou akci (a její výstup. odpovídající), které můžete provádět v relaci prostředí PowerShell. To poskytuje plnou průhlednost a dokončení auditování operací. Tyto soubory protokolu pro budoucí audity můžete zachovat.

> [!NOTE]
> V Azure zásobníku Development Kit (ASDK), můžete spustit některé příkazy, které jsou k dispozici v období přímo z relace prostředí PowerShell na hostiteli development kit. Můžete však testování některé operace pomocí období, jako je například kolekce protokolu, protože to je jedinou metodou dostupnou k provádění některých operací v prostředí, integrované systémy.

## <a name="access-the-privileged-endpoint"></a>Přístup k privilegované koncový bod

OBDOBÍ přistupujete prostřednictvím vzdálené relace prostředí PowerShell na virtuálním počítači, který je hostitelem období. V ASDK, je tento virtuální počítač s názvem **AzS ERCS01**. Pokud používáte integrovaný systém, existují tři instance období, každý běží uvnitř virtuálního počítače (*předpony*-ERCS01, *předpony*-ERCS02, nebo *předpony*- ERCS03) na různých hostitelích pro odolnost proti chybám. 

Před zahájením tohoto postupu pro integrovaný systém, ujistěte se, že dostanete období podle IP adresy nebo přes DNS. Po počátečním nasazení Azure zásobníku mít přístup období jen pomocí IP adresy, protože integrace DNS se ještě není nastavený. Dodavatele hardwaru, od výrobců OEM vám poskytne soubor JSON s názvem **AzureStackStampDeploymentInfo** obsahující období IP adresy.


> [!NOTE]
> Z bezpečnostních důvodů jsme nutné připojit k období pouze posílené virtuální počítač se systémem na hostiteli životního cyklu hardwaru, nebo z počítače vyhrazené, zabezpečení, například [pracovní stanice privilegovaného přístupu](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). Původní konfiguraci hostitelského hardwaru životního cyklu nesmí změnit z původní konfigurace, včetně instalace nového softwaru, ani by být používána pro připojení k období.

1. Vytvořit vztah důvěryhodnosti.

    - Integrovaný systém spusťte následující příkaz z relace prostředí Windows PowerShell zvýšenými přidejte období jako důvěryhodného hostitele na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo pracovní stanici k privilegovaný přístup.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Pokud spouštíte ADSK, přihlaste se k hostiteli development kit.

2. Na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo privilegovaného přístupu pracovní stanice otevřete relaci prostředí Windows PowerShell. Spusťte následující příkazy k vytvoření vzdálené relace na virtuálním počítači, který je hostitelem období:
 
    - Integrované systému:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` Parametr může být IP adresa nebo název DNS jednoho z virtuálních počítačů, které hostuje období. 
    - Pokud spouštíte ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Pokud budete vyzváni, použijte následující pověření:

      - **Uživatelské jméno**: Zadejte účet CloudAdmin ve formátu  **&lt; *zásobník Azure domény*&gt;\cloudadmin**. (Pro ASDK, uživatelské jméno je **azurestack\cloudadmin**.)
      - **Heslo**: Zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

    > [!NOTE]
    > Pokud není možné se připojit ke koncovému bodu ERCS, opakujte kroky 1 a 2 znovu s adresou IP ERCS virtuálního počítače, do které jste nebyly již byl proveden pokus o připojení.

3.  Když se připojíte, řádku se změní na **[*ERCS virtuálního počítače nebo IP adresu název*]: PS >** nebo **[azs-ercs01]: PS >**, v závislosti na prostředí. Odtud spustit `Get-Command` zobrazíte seznam dostupných rutin.

    Řadu tyto rutiny jsou určena pouze pro prostředí integrovaný systém (například rutiny související s integrací datacenter). V ASDK ověření následující rutiny:

    - Clear hostitel
    - Zavřít PrivilegedEndpoint
    - Ukončení PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get – příkaz
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Objekt míry
    - Nové CloudAdminUser
    - Odchozí výchozí
    - Odebrat CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tipy pro používání privilegovaných koncový bod 

Jak je uvedeno nahoře, je období [JEA prostředí PowerShell](https://docs.microsoft.com/powershell/jea/overview) koncový bod. Při současném poskytování silné zabezpečení vrstvy, snižuje koncový bod JEA některé základní funkce prostředí PowerShell, jako je například skriptování nebo kartě dokončení. Pokud se pokusíte žádný druh operace skriptu, operace selže s chybou **ScriptsNotAllowed**. Toto je očekávané chování.

Ano například pokud chcete získat seznam parametrů pro danou rutinu, můžete spusťte následující příkaz:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Alternativně můžete použít [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) rutiny Import všechny rutiny období do aktuální relace v místním počítači. Díky tomu všechny rutiny a funkce období jsou nyní k dispozici na místním počítači, společně s dokončování pomocí tabulátorů a další obecně skriptování. 

Chcete-li importovat období relaci na místním počítači, proveďte následující kroky:

1. Vytvořit vztah důvěryhodnosti.

    -Na integrovaný systém spusťte následující příkaz z relace prostředí Windows PowerShell zvýšenými přidejte období jako důvěryhodného hostitele na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo pracovní stanici k privilegovaný přístup.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Pokud spouštíte ADSK, přihlaste se k hostiteli development kit.

2. Na posílené virtuálního počítače spuštěného na hostiteli životního cyklu hardwaru nebo privilegovaného přístupu pracovní stanice otevřete relaci prostředí Windows PowerShell. Spusťte následující příkazy k vytvoření vzdálené relace na virtuálním počítači, který je hostitelem období:
 
    - Integrované systému:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` Parametr může být IP adresa nebo název DNS jednoho z virtuálních počítačů, které hostuje období. 
    - Pokud spouštíte ADSK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Pokud budete vyzváni, použijte následující pověření:

      - **Uživatelské jméno**: Zadejte účet CloudAdmin ve formátu  **&lt; *zásobník Azure domény*&gt;\cloudadmin**. (Pro ASDK, uživatelské jméno je **azurestack\cloudadmin**.)
      - **Heslo**: Zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

3. Importovat období relace do místního počítače
    ````PowerShell 
        Import-PSSession $session
    ````
4. Teď můžete použít kartu dokončení a udělat skriptování obvyklým na místní relace prostředí PowerShell s funkcí a rutiny období, bez snížení postavení zabezpečení zásobník Azure. Užijte si ji!


## <a name="close-the-privileged-endpoint-session"></a>Ukončení relace privilegované koncový bod

 Jak už bylo zmíněno dříve, období zaznamená každou akci (a její výstup. odpovídající), které můžete provádět v relaci prostředí PowerShell. Relace je třeba nejprve zavřít pomocí `Close-PrivilegedEndpoint` rutiny. Tato rutina správně koncový bod se zavře a přenese soubory protokolů do externí sdílené složky pro uchovávání informací.

K ukončení relace koncového bodu:

1. Vytvoření externí sdílené složky, která je přístupná pomocí období. Ve vývojovém prostředí sady právě vytvoříte sdílenou složku na hostiteli development kit.
2. Spustit `Close-PrivilegedEndpoint` rutiny. 
3. Se zobrazí výzva k zadání cesty na které chcete uložit soubor protokolu přepis. Zadejte sdílené složky, kterou jste vytvořili dříve, ve formátu &#92; &#92; *servername*&#92;*sharename*. Pokud nezadáte cestu, rutina selže a relace zůstane otevřená. 

    ![Zavřít PrivilegedEndpoint výstupu rutiny, který ukazuje, kde zadáte cílovou cestu přepis](media/azure-stack-privileged-endpoint/closeendpoint.png)

Po přepis protokolové soubory jsou úspěšně přenést do sdílené složky, se automaticky odstraní z období. 

> [!NOTE]
> Pokud zavřete relaci období pomocí rutin `Exit-PSSession` nebo `Exit`, nebo jenom zavřete konzolu prostředí PowerShell, protokoly přepis nemáte přenést do sdílené složky. Zůstanou v období. Při příštím spuštění `Close-PrivilegedEndpoint` a zahrnují sdílené složky, přepis protokoly z předchozí relací se také přenosu. Nepoužívejte `Exit-PSSession` nebo `Exit` k ukončení relace období; použít `Close-PrivilegedEndpoint` místo.


## <a name="next-steps"></a>Další postup
[Azure zásobníku diagnostické nástroje](azure-stack-diagnostics.md)
