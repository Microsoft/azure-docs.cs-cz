---
title: Automatické škálování hostitelů relací virtuálních počítačů s Windows – Azure
description: Popisuje, jak nastavit skript automatického škálování pro hostitele relací virtuálních počítačů s Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 932fbe6814df8ec324dd3360bcacfcbcf1c19b62
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71842774"
---
# <a name="scale-session-hosts-dynamically"></a>Dynamické škálování hostitelů relací

Pro mnoho nasazení virtuálních klientských počítačů s Windows v Azure představuje náklady na virtuální počítač významnou část celkových nákladů na nasazení virtuálních počítačů s Windows. Aby se snížily náklady, je nejlepší vypnout a uvolnit virtuální počítače hostitele relace v době mimo špičku a pak je restartovat během špičky využití.

V tomto článku se používá jednoduchý skript pro škálování pro automatické škálování virtuálních počítačů hostitele relací v prostředí virtuálních počítačů s Windows. Další informace o tom, jak skript škálování funguje, najdete v části [Jak funguje skript pro škálování](#how-the-scaling-script-works) .

## <a name="prerequisites"></a>Předpoklady

Prostředí, ve kterém spouštíte skript, musí mít následující věci:

- Tenant a účet virtuálních klientů s Windows nebo instanční objekt s oprávněními pro dotazování tohoto tenanta (například Přispěvatel RDS).
- Virtuální počítače fondu hostitele relace jsou nakonfigurované a zaregistrované ve službě Virtual Desktop systému Windows.
- Další virtuální počítač, který spustí naplánovanou úlohu prostřednictvím Plánovač úloh a má síťový přístup k hostitelům relací. Bude se na něj později v dokumentu odkazovat jako na virtuální počítač pro horizontální navýšení kapacity.
- Na virtuálním počítači, na kterém běží naplánovaná úloha, je nainstalovaný [modul Microsoft Azure správce prostředků PowerShellu](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .
- Na virtuálním počítači, na kterém běží naplánovaná úloha, je nainstalovaný [modul PowerShellu virtuálního počítače s Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) .

## <a name="recommendations-and-limitations"></a>Doporučení a omezení

Při spuštění skriptu škálování mějte na paměti následující věci:

- Tento skript škálování může zpracovat pouze jeden fond hostitelů na instanci naplánované úlohy, která spouští skript škálování.
- Naplánované úlohy, které spouštějí skripty škálování, musí být na virtuálním počítači, který je vždycky zapnutý.
- Vytvořte samostatnou složku pro každou instanci skriptu škálování a jeho konfiguraci.
- Tento skript nepodporuje přihlašování jako správce k virtuálním plochám Windows s uživatelskými účty Azure AD, které vyžadují službu Multi-Factor Authentication. Pro přístup ke službě Windows Virtual Desktop a Azure doporučujeme používat instanční objekty. Podle [tohoto kurzu](create-service-principal-role-powershell.md) vytvořte instanční objekt a přiřazení role pomocí PowerShellu.
- Záruka SLA Azure platí jenom pro virtuální počítače ve skupině dostupnosti. Aktuální verze dokumentu popisuje prostředí s jedním virtuálním počítačem, které provádí škálování, což nemusí splňovat požadavky na dostupnost.

## <a name="deploy-the-scaling-script"></a>Nasazení skriptu škálování

Následující postupy vám posdělí, jak nasadit skript škálování.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Příprava prostředí pro skript škálování

Nejprve Připravte prostředí pro skript škálování:

1. Přihlaste se k virtuálnímu počítači (virtuální počítač pro horizontální navýšení kapacity), který spustí naplánovanou úlohu s účtem správce domény.
2. Vytvořte složku na virtuálním počítači pro škálování, která bude obsahovat skript škálování a jeho konfiguraci (například **C: \\scaling-HostPool1**).
3. Stažení souborů **basicScale. ps1**, **config. XML**a **Functions-PSStoredCredentials. ps1** a složky **PowershellModules** z [úložiště skriptu škálování](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) a jejich zkopírování do složky, kterou jste vytvořili v kroku 2. Existují dva základní způsoby, jak soubory získat před jejich zkopírováním do virtuálního počítače se škálováním na více počítačů:
    - Naklonujte úložiště Git do místního počítače.
    - Zobrazte **nezpracované** verze každého souboru, zkopírujte a vložte obsah každého souboru do textového editoru a pak soubory uložte s odpovídajícím názvem souboru a typem souboru. 

### <a name="create-securely-stored-credentials"></a>Vytvoření bezpečných uložených přihlašovacích údajů

V dalším kroku budete muset vytvořit bezpečně uložené přihlašovací údaje:

1. Otevřete PowerShell ISE jako správce.
2. Importujte modul PowerShellu služby RDS spuštěním následující rutiny:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Otevřete podokno úprav a načtěte soubor **Function-PSStoredCredentials. ps1** a pak spusťte celý skript (F5).
4. Spusťte následující rutinu:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Příklad **: set-Variable-Name cesta k nástroji – globální hodnota "c: \\scaling-HostPool1"**
5. Spusťte rutinu **New-StoredCredential-path \$KeyPath** . Po zobrazení výzvy zadejte přihlašovací údaje k virtuálnímu počítači s Windows s oprávněními pro dotazování fondu hostitelů (fond hostitelů je zadaný v **souboru config. XML**).
    - Pokud používáte jiné instanční objekty nebo standardní účet, spusťte rutinu **New-StoredCredential-path \$KeyPath** pro každý účet pro vytvoření místních uložených přihlašovacích údajů.
6. Spuštěním rutiny **Get-StoredCredential-list** potvrďte, že se přihlašovací údaje úspěšně vytvořily.

### <a name="configure-the-configxml-file"></a>Konfigurace souboru config. XML

Zadejte příslušné hodnoty do následujících polí pro aktualizaci nastavení skriptu škálování v souboru config. XML:

| Pole                     | Popis                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID tenanta Azure AD, které přidruží předplatné, ve kterém se spouštějí virtuální počítače hostitele relace     |
| AADApplicationId              | ID aplikace instančního objektu                                                       |
| AADServicePrincipalSecret     | Tato možnost se dá zadat během zkušební fáze, ale po vytvoření přihlašovacích údajů pomocí **Functions-PSStoredCredentials. ps1** se musí uchovávat prázdná.    |
| currentAzureSubscriptionId    | ID předplatného Azure, ve kterém se spouštějí virtuální počítače hostitele relace                        |
| tenantName                    | Název tenanta virtuálních klientů Windows                                                    |
| hostPoolName                  | Název fondu hostitelů virtuálních počítačů s Windows                                                 |
| RDBroker                      | Adresa URL služby WVD, výchozí hodnota https: \//rdbroker. WVD. Microsoft. com             |
| Uživatelské jméno                      | ID aplikace instančního objektu (je možné, že má stejný instanční objekt jako v AADApplicationId) nebo standardní uživatel bez služby Multi-Factor Authentication |
| isServicePrincipal            | Přijaté hodnoty jsou **true** nebo **false**. Určuje, jestli druhá sada přihlašovacích údajů používá instanční objekt nebo standardní účet. |
| BeginPeakTime                 | Čas zahájení špičky využití                                                            |
| EndPeakTime                   | Doba špičky využití na konci                                                              |
| TimeDifferenceInHours         | Časový rozdíl mezi místním časem a časem UTC v hodinách                                   |
| SessionThresholdPerCPU        | Maximální počet relací na prahovou hodnotu procesoru, který se používá k určení, kdy je potřeba spustit nový virtuální počítač hostitele relace během špičky.  |
| MinimumNumberOfRDSH           | Minimální počet virtuálních počítačů fondu hostitelů, které mají běžet v době mimo špičku             |
| LimitSecondsToForceLogOffUser | Počet sekund, po který se má počkat, než se uživatelé vynutí odhlášení. Pokud je nastavená hodnota 0, uživatelé se nebudou muset odhlásit.  |
| LogOffMessageTitle            | Název zprávy odeslané uživateli před jejich vynuceným odhlášením                  |
| LogOffMessageBody             | Text zprávy s upozorněním, která se uživateli pošle předtím, než se odhlásí. Například "Tento počítač se vypne během X minut. Uložte prosím svoji práci a odhlaste se. |

### <a name="configure-the-task-scheduler"></a>Nakonfigurovat Plánovač úloh

Po konfiguraci souboru Configuration. XML bude nutné nakonfigurovat Plánovač úloh, aby se soubor basicScaler. ps1 spouštěl v pravidelných intervalech.

1. Spusťte **Plánovač úloh**.
2. V okně **Plánovač úloh** vyberte **vytvořit úlohu...**
3. V dialogovém okně **vytvořit úlohu** vyberte kartu **Obecné** , zadejte **název** (například "dynamický hostitel vzdálené relace"), vyberte možnost spustit bez **ohledu na to, zda je uživatel přihlášen nebo nikoli** a **Spusťte s nejvyššími oprávněními**.
4. Otevřete kartu **triggery** a pak vyberte **nové...**
5. V dialogovém **okně Nová aktivační událost** v **části Upřesnit nastavení**zaškrtněte políčko **Opakovat úlohu každých** a vyberte příslušné období a dobu trvání (například **15 minut** nebo **neomezeně**).
6. Vyberte kartu **Akce** a **nové...**
7. V dialogovém okně **Nová akce** zadejte do pole **program/Script skript** **PowerShell. exe** a potom do pole **Přidat argumenty (volitelné)** zadejte **C: \\scaling @ no__t-5basicScale. ps1** .
8. Otevřete karty **podmínky** a **Nastavení** a výběrem **OK** potvrďte výchozí nastavení pro každou z nich.
9. Zadejte heslo pro účet správce, ve kterém chcete spustit skript škálování.

## <a name="how-the-scaling-script-works"></a>Jak funguje skript škálování

Tento skript pro škálování čte nastavení ze souboru config. XML, včetně začátku a konce období špičky využití během dne.

Během špičky běhu skript zkontroluje aktuální počet relací a aktuálně spuštěnou kapacitu vzdálené plochy pro každý fond hostitelů. Počítá se v případě, že virtuální počítače hostitele spuštěné relace mají dostatečnou kapacitu pro podporu stávajících relací na základě parametru SessionThresholdPerCPU definovaného v souboru config. XML. V takovém případě se spustí skript s dalšími virtuálními počítači hostitele relace ve fondu hostitelů.

V době mimo špičku bude skript určovat, které virtuální počítače hostitele relace by se měly vypnout na základě parametru MinimumNumberOfRDSH v souboru config. XML. Skript nastaví virtuální počítače hostitele relace na režim vyprázdnění, aby se zabránilo novým relacím, které se připojují k hostitelům. Pokud nastavíte parametr **LimitSecondsToForceLogOffUser** v souboru config. XML na nenulovou kladnou hodnotu, skript upozorní všechny aktuálně přihlášené uživatele, aby ušetřili práci, počkali nakonfigurovanou dobu a pak vynutí, aby se uživatelé odhlásili. Jakmile se všechny uživatelské relace odhlásily na virtuálním počítači hostitele relace, skript vypne server.

Pokud v souboru config. xml nastavíte parametr **LimitSecondsToForceLogOffUser** na hodnotu nula, skript umožní, aby nastavení konfigurace relace ve vlastnostech fondu hostitelů zpracovával podepisování uživatelských relací. Pokud se na virtuálním počítači hostitele relace nacházejí nějaké relace, ponechá se virtuální počítač hostitele relace spuštěný. Pokud se nevyskytly žádné relace, skript vypne virtuální počítač hostitele relace.

Skript je navržený tak, aby pravidelně běžel na serveru virtuálních počítačů se škálováním na více systému pomocí Plánovač úloh. Vyberte odpovídající časový interval na základě velikosti prostředí vzdálené plochy a nezapomeňte, že spouštění a vypínání virtuálních počítačů může nějakou dobu trvat. Doporučujeme spouštět skript škálování každých 15 minut.

## <a name="log-files"></a>Soubory protokolu

Skript škálování vytvoří dva soubory protokolu **WVDTenantScale. log** a **WVDTenantUsage. log**. Soubor **WVDTenantScale. log** bude protokolovat události a chyby (pokud existují) během každého spuštění skriptu škálování.

V souboru **WVDTenantUsage. log** se zaznamená aktivní počet jader a aktivních počet virtuálních počítačů pokaždé, když spustíte skript škálování. Tyto informace můžete použít k odhadu skutečného využití Microsoft Azure virtuálních počítačů a nákladů. Soubor je formátován jako hodnoty oddělené čárkami. Každá položka obsahuje následující informace:

>čas, fond hostitelů, jádra, virtuální počítače

Název souboru se taky dá změnit tak, aby měl rozšíření CSV, načtený do Microsoft Excelu a analyzuje.
