---
title: Automatické škálování hostitelé systému Windows virtuální plochy Preview relace – Azure
description: Popisuje, jak nastavit automatické škálování skriptu pro hostitele relace Windows virtuální plochy, ve verzi Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 379e73c33aa4570c3e56f902b011d75944c94a8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870719"
---
# <a name="automatically-scale-session-hosts"></a>Automatické škálování hostitelů relace

Pro mnoho nasazení virtuální Preview Desktop Windows v Azure náklady na virtuální počítač představují významnou část celkové náklady na nasazení virtuální plochy Windows. Pokud chcete snížit náklady, je nejlepší vypnout a uvolnit relace hostování virtuálních počítačů (VM) během hodin mimo špičku využití a pak je restartujte během špičky využití.

Tento článek používá jednoduchý skript škálování pro automatické škálování relace hostování virtuálních počítačů v prostředí virtuálního klienta Windows. Další informace o tom, jak škálování skriptu funguje, najdete v článku [fungování škálování skriptu](#how-the-scaling-script-works) oddílu.

## <a name="prerequisites"></a>Požadavky

Prostředí, ve kterém jste spustili skript musíte mít následující věci:

- Tenant virtuální plochy Windows a účet nebo instančního objektu s oprávněními k dotazu tohoto tenanta (třeba Přispěvatel vzdálené plochy).
- Relace hostitele fondu virtuálních počítačů nakonfigurované a registrovaný ve službe virtuální plochy Windows.
- Další scaler virtuální počítač, který spustí naplánovanou úlohu prostřednictvím a plánování úloh, který má přístup k síti pro hostitele relace.
- Modul Powershellu pro Microsoft Azure Resource Manager nainstalovaná na virtuálním počítači naplánované úlohy.
- Modul Windows Powershellu virtuální plochy nainstalovala do virtuálního počítače s naplánovanou úlohu.

## <a name="recommendations-and-limitations"></a>Doporučení a omezení

Při škálování skriptu, vezměte v úvahu následující věci:

- Tento skript škálování může zpracovat pouze jeden fond hostitele na instanci naplánované úlohy, na kterém běží škálování skriptu.
- Naplánované úlohy, na kterých běží škálování skripty musí být na virtuálním počítači, který je vždycky aktivní.
- Vytvořte samostatnou složku pro každou instanci sady škálování skriptu a jeho konfigurace.
- Tento skript nepodporuje účty pomocí služby Multi-Factor authentication. Doporučujeme že použít instanční objekty pro přístup k virtuálnímu klientovi Windows service a Azure.
- Azure SLA se zárukou platí jenom pro virtuální počítače ve skupině dostupnosti. Aktuální verzi dokumentu popisuje prostředí pro jeden virtuální počítač způsobem škálování, který nemusí splňovat požadavky na dostupnost.

## <a name="deploy-the-scaling-script"></a>Škálování skriptu nasazení

Následující postupy vám sdělí škálování skriptu nasazení.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Příprava prostředí pro škálování skriptu

Nejprve připravte vaše prostředí na škálování skriptu:

1. Přihlaste se k virtuálnímu počítači (**škálování virtuálních počítačů**) pomocí účtu správce domény, který se spustí naplánovaná úloha.
2. Vytvořte složku na škálování virtuálních počítačů pro uložení škálování skriptu a jeho konfigurace (například **C:\\škálování HostPool1**).
3. Stáhněte si **basicScaler.ps1**, **Config.xml**, a **funkce PSStoredCredentials.ps1** soubory a **PowershellModules** ze složky [škálování úložiště skriptů](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) a zkopírujte je do složky, kterou jste vytvořili v kroku 2.

### <a name="create-securely-stored-credentials"></a>Vytvořte bezpečně uložené přihlašovací údaje

V dalším kroku budete muset vytvořit bezpečně uložené přihlašovací údaje:

1. Otevřete prostředí PowerShell ISE jako správce.
2. Otevřete podokno úprav a zatížení **funkce PSStoredCredentials.ps1** souboru.
3. Spusťte následující rutinu:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Například **proměnná-sady - název KeyPath – globální obor – hodnota "c:\\škálování HostPool1"**
4. Spustit **New StoredCredential - KeyPath \$KeyPath** rutiny. Po zobrazení výzvy zadejte svoje přihlašovací údaje Windows virtuálního klienta s oprávněními k dotazování fondu hostitele (hostitele fondu je zadán v **config.xml**).
    - Pokud používáte jiný instančních objektů nebo standardního účtu, spusťte **New StoredCredential - KeyPath \$KeyPath** rutina jednou pro každý účet k vytvoření místního uložené přihlašovací údaje.
5. Spustit **Get StoredCredentials-seznamu** potvrďte přihlašovací údaje byly úspěšně vytvořeny.

### <a name="configure-the-configxml-file"></a>Konfigurace v souboru config.xml

Do následujících polí se aktualizovat nastavení škálování skript v souboru config.xml zadejte příslušné hodnoty:

| Pole                     | Popis                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Spusťte Azure AD Tenant ID, které přidružuje předplatné, ve kterém relace hostování virtuálních počítačů     |
| AADApplicationId              | ID aplikace instančního objektu služby                                                       |
| AADServicePrincipalSecret     | To může uživatel zadat během fáze testování, ale je udržovat prázdné po vytvoření přihlašovacích údajů pomocí **PSStoredCredentials.ps1 – funkce**    |
| currentAzureSubscriptionId    | ID předplatného Azure, kde spustit relaci hostitele virtuálních počítačů                        |
| tenantName                    | Název tenanta virtuálního klienta Windows                                                    |
| hostPoolName                  | Název fondu hostitele virtuálního klienta Windows                                                 |
| RDBroker                      | Adresa URL ke službě WVD výchozí hodnotu https:\//rdbroker.wvd.microsoft.com             |
| Uživatelské jméno                      | ID aplikace instančního objektu služby (je možné mít instanční objekt stejný jako v AADApplicationId) nebo standardního uživatele bez ověřování Multi-Factor Authentication |
| isServicePrincipal            | Platné hodnoty jsou **true** nebo **false**. Určuje, zda je druhou sadu pověření používá objekt služby nebo standardní účet. |
| BeginPeakTime                 | Kdy začíná špičky využití                                                            |
| EndPeakTime                   | Po ukončení špičky využití                                                              |
| TimeDifferenceInHours         | Časový rozdíl mezi místním časem a UTC, v hodinách                                   |
| SessionThresholdPerCPU        | Maximální počet relací na prahová hodnota využití procesoru umožňuje určit, kdy nového Hostitelského serveru musí být spuštěna během špičky.  |
| MinimumNumberOfRDSH           | Minimální počet hostitelů fondu virtuálních počítačů běžela v době mimo špičku využití             |
| LimitSecondsToForceLogOffUser | Počet sekund se má čekat, než se rozhodnete Vynutit odhlášení uživatele. Pokud je nastaveno na 0, uživatelé nejsou nuceni Odhlásit se.  |
| LogOffMessageTitle            | Nadpis zprávy pro uživatele předtím, než budete muset odhlásit se                  |
| LogOffMessageBody             | Tělo zprávy odeslané uživatelům předtím, než se odhlásili. Například "odpojí tento počítač dolů v X minut. Uložte svou práci a odhlásit se." |

### <a name="configure-the-task-scheduler"></a>Konfigurace služby Plánovač úloh

Po nakonfigurování konfiguračního souboru .xml, budete potřebovat ke konfiguraci plánovače úloh ke spuštění souboru basicScaler.ps1 v pravidelných intervalech.

1. Spustit **Plánovač úloh**.
2. V **Plánovač úloh** okně **vytvořit úlohu...**
3. V **vytvořit úlohu** dialogového okna, vyberte **Obecné** kartu, zadejte **název** (například "Dynamické RDSH"), vyberte **spouštění, jestli je uživatel přihlášen, nebo Ne** a **spustit s nejvyšším oprávněním**.
4. Přejděte **triggery** kartu a potom vyberte **nový...**
5. V **Nová aktivační událost** dialogového okna, v části **upřesňující nastavení**, zkontrolujte **opakování úlohy každý** a vyberte příslušnou období a doba trvání (například **15 minut** nebo **po neomezenou dobu**).
6. Vyberte **akce** kartu a **nový...**
7. V **novou akci** dialogové okno, zadejte **powershell.exe** do **programu nebo skriptu** pole, a pak zadejte **C:\\škálování\\ RDSScaler.ps1** do **argumenty (volitelné) přidejte** pole.
8. Přejděte **podmínky** a **nastavení** karet a vyberte **OK** přijměte výchozí nastavení pro každý.
9. Zadejte heslo pro účet správce, kde plánujete provozovat škálování skriptu.

## <a name="how-the-scaling-script-works"></a>Jak funguje škálování skriptu

Tento skript škálování načte nastavení ze souboru config.xml, včetně počáteční a koncové období špičky využití během dne.

Během doby využití ve špičce tento skript zkontroluje aktuální počet relací a aktuální kapacita RDSH spuštěný pro každou kolekci. Vypočítá Pokud spuštěné servery RDSH mít dostatečnou kapacitu pro podporu stávající relace na základě parametru SessionThresholdPerCPU definované v souboru config.xml. Pokud ne, skript se spustí další servery RDSH v kolekci.

V době mimo špičku využití skript určí, které servery RDSH měli vypínat na základě MinimumNumberOfRDSH parametru v souboru config.xml. Skript nastaví servery RDSH, které chcete vyprázdnit režim, který zabrání nové relace připojení k hostiteli. Pokud jste nastavili **LimitSecondsToForceLogOffUser** parametru v souboru config.xml kladná hodnota nulová, skript vás upozorní, všechny aktuálně přihlášení uživatele k ukládání, počkejte nakonfigurovaného množství času a pak vynutit Uživatelé se odhlásit. Po všechny uživatelské relace na serveru RDSH, skript bude vypnutí serveru.

Pokud jste nastavili **LimitSecondsToForceLogOffUser** parametru v souboru config.xml na nulu, tento skript vám umožní nastavení konfigurace relace v kolekci vlastností pro zpracování přihlašování vypnout uživatelských relací. Pokud nejsou žádné relace na serveru RDSH, ponechá Hostitelského serveru spuštěna. Pokud nejsou k dispozici žádné relace, skript se vypne Hostitelského serveru.

Skript je navržen pro spouštění pravidelně na serveru scaler virtuálního počítače pomocí plánovače úloh. Vyberte vhodný časový interval na základě velikosti vašeho prostředí služby Vzdálená plocha a mějte na paměti, že spouštění a vypínání virtuálních počítačů může nějakou dobu trvat. Doporučujeme spustit skript škálování každých 15 minut.

## <a name="log-files"></a>Soubory protokolu

Škálování skript vytvoří dva soubory protokolu, **WVDTenantScale.log** a **WVDTenantUsage.log**. **WVDTenantScale.log** souboru se budou protokolovat události a chyby (pokud existuje) během každé spuštění skriptu škálování.

**WVDTenantUsage.log** souboru zaznamená aktivní počtu jader a počty aktivních virtuálních počítačů pokaždé, když provedete škálování skriptu. Tyto informace můžete použít k odhadu ze skutečného využití virtuálních počítačů Microsoft Azure a náklady. Soubor je formátován jako textový soubor s oddělovači, s každou položku, který obsahuje následující informace:

>čas, kolekce, počet jader, virtuální počítače

Název souboru můžete upravit také mít příponu CSV, načte do aplikace Microsoft Excel a analyzovat.