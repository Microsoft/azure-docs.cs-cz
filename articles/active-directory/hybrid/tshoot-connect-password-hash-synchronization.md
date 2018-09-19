---
title: Řešení potíží s synchronizaci hodnot hash hesel pomocí synchronizace Azure AD Connect | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak řešit potíže se synchronizací hodnot hash hesel.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 036933c6b6e86856871c5f59f08fea20a0343ad7
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314843"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží s synchronizaci hodnot hash hesel pomocí synchronizace Azure AD Connect
Toto téma popisuje kroky pro řešení potíží s synchronizaci hodnot hash hesel. Pokud hesla se nesynchronizují podle očekávání, může být pro podmnožinu uživatelů nebo pro všechny uživatele.

Pro nasazení služby Azure Active Directory (Azure AD) Connect s verzí 1.1.614.0 nebo po pomocí úlohy řešení potíží v Průvodci potíží se synchronizací hodnot hash hesel:

* Pokud máte potíže, kde hesla se nesynchronizují, přečtěte si [hesla se nesynchronizují: řešení potíží pomocí úlohy řešení potíží](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) oddílu.

* Pokud máte potíže s jednotlivé objekty, přečtěte si [jeden objekt nesynchronizuje hesla: řešení potíží pomocí úlohy řešení potíží](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) oddílu.

Pro nasazení s verze 1.1.524.0 nebo novější, je k dispozici diagnostických rutina, která vám umožní potíží se synchronizací hodnot hash hesel:

* Pokud máte potíže, kde hesla se nesynchronizují, přečtěte si [hesla se nesynchronizují: řešení potíží pomocí rutiny diagnostických](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) oddílu.

* Pokud máte potíže s jednotlivé objekty, přečtěte si [jeden objekt nesynchronizuje hesla: řešení potíží pomocí rutiny diagnostických](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) oddílu.

Pro starší verze nasazení Azure AD Connect:

* Pokud máte potíže, kde hesla se nesynchronizují, přečtěte si [hesla se nesynchronizují: ruční kroky pro řešení potíží](#no-passwords-are-synchronized-manual-troubleshooting-steps) oddílu.

* Pokud máte potíže s jednotlivé objekty, přečtěte si [jeden objekt nesynchronizuje hesla: ruční kroky pro řešení potíží](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) oddílu.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Hesla se nesynchronizují: řešení potíží pomocí úlohy řešení potíží
Úlohy řešení potíží můžete zjistit, proč hesla se nesynchronizují.

> [!NOTE]
> Úlohy řešení potíží je dostupná jenom pro verzi služby Azure AD Connect 1.1.614.0 nebo novější.

### <a name="run-the-troubleshooting-task"></a>Spuštění úlohy řešení potíží
Řešení potíží, kde hesla se nesynchronizují:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru s Azure AD Connect **spustit jako správce** možnost.

2. Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.

3. Spusťte Průvodce Azure AD Connect.

4. Přejděte **další úkoly** stránce **Poradce při potížích**a klikněte na tlačítko **Další**.

5. Klikněte na stránce Poradce při potížích s **spuštění** do řešení problémů s nabídky start v prostředí PowerShell.

6. V hlavní nabídce vyberte **řešit synchronizaci hodnot hash hesel**.

7. V nabídce sub vyberte **synchronizaci hodnot hash hesel nefunguje vůbec**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledky úlohy řešení potíží
Řešení potíží úloha provede následující kontroly:

* Ověřuje, že funkce synchronizace hodnot hash hesel je povolený pro vašeho tenanta Azure AD.

* Ověřuje, že server Azure AD Connect není v pracovním režimu.

* Pro každý existující konektor pro místní služby Active Directory (což odpovídá existující doménové struktury služby Active Directory):

   * Ověří, zda je povolena funkce synchronizace hodnot hash hesel.
   
   * Vyhledá heslo hash synchronizace prezenční signál události v protokolu událostí Windows.

   * Pro každou doménu služby Active Directory v části místní konektor služby Active Directory:

      * Ověří, že doménu je dostupný ze serveru, Azure AD Connect.

      * Ověřuje, že účty Active Directory Domain Services (AD DS) používají místní konektor služby Active Directory má správné uživatelské jméno, heslo a oprávněních pro synchronizaci hodnot hash hesel.

Následující diagram znázorňuje výsledky rutiny pro topologie s jednou doménou, v místním Active Directory:

![Diagnostický výstup pro synchronizaci hodnot hash hesel](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

Zbytek tohoto oddílu popisuje konkrétní výsledky, které jsou vrácené úlohy a odpovídající problémy.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Funkce synchronizace hodnot hash hesla není povoleno.
Pokud nepovolíte synchronizaci hodnot hash hesel pomocí Průvodce Azure AD Connect, je vrácena následující chyba:

![není-li povolena synchronizace hodnot hash hesel](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Server Azure AD Connect je v pracovním režimu
Pokud je server Azure AD Connect v pracovním režimu, synchronizaci hodnot hash hesel se dočasně zakáže a vrátil následující chybu:

![Server Azure AD Connect je v pracovním režimu](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Žádné události prezenčního signálu synchronizace hodnoty hash hesla
Každý konektor pro místní služby Active Directory má svůj vlastní kanál synchronizace hodnoty hash hesla. Pokud je vytvořit kanál synchronizace hodnot hash hesel a nejsou k dispozici žádné změny hesla se dá provést synchronizace, se vygeneruje událost prezenčního signálu (EventId 654) každých 30 minut v protokolu událostí aplikace Windows. Pro každý konektor pro místní služby Active Directory rutina vyhledá odpovídající události prezenčního signálu během posledních tří hodin. Pokud se nenajde žádný prezenční signál události, je vrácena následující chyba:

![Žádné srdce synchronizace hodnoty hash hesla beat událostí](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Účet AD DS nemá správná oprávnění
Pokud účet služby AD DS, který se používá konektorem místní služby Active Directory k synchronizaci hodnot hash hesel nemá příslušná oprávnění, je vrácena následující chyba:

![Nesprávné přihlašovací údaje](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nesprávné uživatelské jméno účtu služby AD DS nebo heslo
Pokud účet služby AD DS používají místní konektor služby Active Directory k synchronizaci hodnot hash hesel má nesprávné uživatelské jméno nebo heslo, je vrácena následující chyba:

![Nesprávné přihlašovací údaje](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden objekt nesynchronizuje hesla: řešení potíží pomocí úlohy řešení potíží

Chcete-li zjistit, proč se jeden objekt nesynchronizuje hesla můžete úlohy řešení potíží.

> [!NOTE]
> Úlohy řešení potíží je dostupná jenom pro verzi služby Azure AD Connect 1.1.614.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spusťte rutinu diagnostiky
Řešení potíží pro objekt konkrétního uživatele:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru s Azure AD Connect **spustit jako správce** možnost.

2. Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.

3. Spusťte Průvodce Azure AD Connect.

4. Přejděte **další úkoly** stránce **Poradce při potížích**a klikněte na tlačítko **Další**.

5. Klikněte na stránce Poradce při potížích s **spuštění** do řešení problémů s nabídky start v prostředí PowerShell.

6. V hlavní nabídce vyberte **řešit synchronizaci hodnot hash hesel**.

7. V nabídce sub vyberte **pro konkrétní uživatelský účet není synchronizován heslo**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledky úlohy řešení potíží
Řešení potíží úloha provede následující kontroly:

* Zkontroluje stav objektu služby Active Directory v prostoru konektoru Active Directory, úložiště Metaverse a Azure AD prostoru konektoru.

* Ověřuje, že jsou synchronizační pravidla, jejichž synchronizace hodnot hash hesel povolená a použije objekt služby Active Directory.

* Pokusí se načíst a zobrazit výsledky poslední pokus o synchronizaci hesla pro objekt.

Následující diagram znázorňuje výsledky rutiny při řešení potíží s synchronizaci hodnot hash hesel pro jediný objekt:

![Diagnostický výstup pro synchronizaci hodnot hash hesel – jeden objekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

Zbytek tohoto oddílu popisuje konkrétní výsledky vrácené rutiny a odpovídající problémy.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Objekt služby Active Directory není exportovány do služby Azure AD
synchronizace hodnot hash hesel pro tento účet místní služby Active Directory se nezdaří, protože neexistuje žádný odpovídající objekt v tenantovi Azure AD. Vrátil následující chybu:

![Chybí objekt Azure AD](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Uživatel má dočasné heslo
Azure AD Connect v současné době nepodporuje synchronizaci dočasná hesla s Azure AD. Heslo se považuje za dočasné Pokud **změnit heslo při příštím přihlášení** je možnost nastavená na uživatele v místním Active Directory. Vrátil následující chybu:

![Neexportoval dočasné heslo](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Nejsou k dispozici, výsledky poslední pokus o synchronizaci hesla
Azure AD Connect ve výchozím nastavení, uloží výsledky pokusů o synchronizaci hodnoty hash hesla po dobu sedmi dní. Pokud nebyly nalezeny žádné výsledky k dispozici pro vybraný objekt služby Active Directory, vrátí se následující upozornění:

![Diagnostický výstup pro jeden objekt - žádná historie synchronizace hesla](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Hesla se nesynchronizují: řešení potíží pomocí rutiny diagnostiky
Můžete použít `Invoke-ADSyncDiagnostics` rutiny zjistit, proč hesla se nesynchronizují.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Rutina je k dispozici pouze pro služby Azure AD Connect verze 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spusťte rutinu diagnostiky
Řešení potíží, kde hesla se nesynchronizují:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru s Azure AD Connect **spustit jako správce** možnost.

2. Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.

3. Spusťte `Import-Module ADSyncDiagnostics`.

4. Spusťte `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden objekt nesynchronizuje hesla: řešení potíží pomocí rutiny diagnostiky
Můžete použít `Invoke-ADSyncDiagnostics` rutiny určit, proč se jeden objekt nesynchronizuje hesla.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Rutina je k dispozici pouze pro služby Azure AD Connect verze 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spusťte rutinu diagnostiky
Řešení potíží, kde hesla se nesynchronizují pro uživatele:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru s Azure AD Connect **spustit jako správce** možnost.

2. Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.

3. Spusťte `Import-Module ADSyncDiagnostics`.

4. Spusťte následující rutinu:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   Příklad:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Hesla se nesynchronizují: ruční kroky pro řešení potíží
Postupujte podle následujících kroků zjistěte, proč hesla se nesynchronizují:

1. Je server Connect v [pracovním režimu](how-to-connect-sync-operations.md#staging-mode)? Server v pracovní režimu nesynchronizuje hesla.

2. Spusťte skript [získat stav nastavení synchronizace hesel](#get-the-status-of-password-sync-settings) oddílu. Poskytuje přehled o konfiguraci synchronizace hesel.  

    ![Výstup skriptu prostředí PowerShell z nastavení synchronizace hesel](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Pokud funkce není povolena ve službě Azure AD nebo pokud stav kanálu synchronizace není povolená, spusťte Průvodce instalací služby Connect. Vyberte **přizpůsobit možnosti synchronizace**a zrušte výběr synchronizaci hesel. Tato změna se dočasně zakáže funkci. Potom spusťte průvodce znovu a znovu povolit synchronizaci hesel. Spuštění skriptu znovu k ověření, zda je konfigurace správná.

4. Vyhledejte v protokolu událostí pro chyby. Vyhledejte následující události, které by mohly ukazovat na problém:
    * Zdroje: ID "Synchronizace adresářů": 0, 611, 652, pokud se zobrazí tyto události 655 máte problém připojením. Zprávy protokolu událostí obsahuje informace o doménové struktuře, kde máte problém. Další informace najdete v tématu [problému s připojením](#connectivity problem).

5. Pokud se žádný prezenční signál nebo pokud nic jiného nepracovali, spusťte [aktivovat úplná synchronizace všech hesel](#trigger-a-full-sync-of-all-passwords). Spusťte skript pouze jednou.

6. Zobrazit [Poradce při potížích s jeden objekt, který se nesynchronizuje hesla](#one-object-is-not-synchronizing-passwords) části.

### <a name="connectivity-problems"></a>Problémy s připojením

Máte připojení k Azure AD?

Má účet požadovaná oprávnění ke čtení hodnot hash hesel ve všech doménách? Pokud jste nainstalovali připojit s použitím expresního nastavení, by měly být oprávnění již správné. 

Pokud jste použili vlastní instalace, nastavte oprávnění ručně následujícím způsobem:
    
1. Účet používaný službou konektoru služby Active Directory spusťte **Synchronization Service Manager**. 
 
2. Přejděte na **konektory**a poté vyhledejte místní doménové struktuře služby Active Directory se řešení potíží. 
 
3. Vyberte konektor a potom klikněte na tlačítko **vlastnosti**. 
 
4. Přejděte na **připojit k doménové struktuře služby Active Directory**.  
    
    ![Účet používaný službou konektoru služby Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Všimněte si uživatelské jméno a doménu, ve kterém je umístěn účet.
    
5. Spustit **Active Directory Users and Computers**a potom ověřte, zda má účet, jste našli dříve následující oprávnění, nastavte v kořenovém adresáři všechny domény v doménové struktuře:
    * Replikace změn adresáře
    * Replikace adresáře se změní všechny

6. Řadiče domény jsou dostupná z Azure AD Connect? Pokud je Connect server nemůže připojit do všech řadičů domény, nakonfigurujte **použít pouze upřednostňované řadič**.  
    
    ![Řadič domény se používá konektorem služby Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Přejděte zpět na **Synchronization Service Manager** a **konfigurace oddíl adresáře**. 
 
8. Zadejte název domény v **Vybrat oddíly adresářů**, vyberte **použít pouze upřednostňované řadiče domény** zaškrtněte políčko a potom klikněte na tlačítko **konfigurovat**. 

9. V seznamu zadejte řadičů domény, které by měly používat Connect pro synchronizaci hesel. Stejného seznamu se používá pro import a export také. Proveďte tyto kroky pro všechny domény.

10. Pokud tento skript ukazuje, že neexistuje žádný prezenční signál, spusťte skript [aktivovat úplná synchronizace všech hesel](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden objekt nesynchronizuje hesla: ruční kroky pro řešení potíží
Snadno můžete řešit problémy synchronizace hodnoty hash hesla kontrolou stavu objektu.

1. V **Active Directory Users and Computers**, vyhledejte uživatele a potom ověřte, že **musí uživatel změnit heslo při příštím přihlášení** zrušení zaškrtnutí políčka.  

    ![Produktivní hesla Active Directory](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Pokud zaškrtávací políčko je zaškrtnuto, požádejte uživatele o přihlášení a změnit heslo. Dočasná hesla nejsou synchronizované s Azure AD.

2. Pokud ve službě Active Directory správné heslo, postupujte podle uživatele v synchronizační modul. Pomocí následujícího uživatele z místní služby Active Directory do služby Azure AD, zobrazí se, zda je u objektu popisná chybová.

    a. Spustit [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Klikněte na tlačítko **konektory**.

    c. Vyberte **konektor služby Active Directory** ve kterém se uživatel zdržuje.

    d. Vyberte **hledání prostoru konektoru**.

    e. V **oboru** vyberte **rozlišující název nebo ukotvení**a pak zadejte úplný název DN uživatele řešení potíží.

    ![Hledat uživatele v prostoru konektoru s rozlišujícím názvem](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Vyhledejte uživatele, kterou hledáte a klikněte na **vlastnosti** zobrazíte všechny atributy. Pokud uživatel není ve výsledcích hledání, ověřte váš [pravidla filtrování](how-to-connect-sync-configure-filtering.md) a ujistěte se, že spustíte [použít a ověřit změny](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) pro uživatele se zobrazí ve službě Connect.

    g. Chcete-li zobrazit podrobnosti synchronizace hesla objektu za minulý týden, klikněte na tlačítko **protokolu**.  

    ![Podrobnosti protokolu objektu](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Pokud objekt protokolu je prázdný, Azure AD Connect nelze číst hodnotu hash hesla ze služby Active Directory. Pokračovat v odstraňování problémů s [k chybám připojení](#connectivity-errors). Pokud se zobrazí jakákoli jiná hodnota než **úspěch**, naleznete v tabulce v [protokol synchronizace hesla](#password-sync-log).

    h. Vyberte **rodokmenu** kartu a ujistěte se, že tento aspoň jedna synchronizační pravidlo v **PasswordSync** sloupec je **True**. Ve výchozím nastavení je název pravidla synchronizace **v ze služby AD - uživatel AccountEnabled**.  

    ![Rodokmenu informací o uživateli](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klikněte na tlačítko **vlastnosti objektu úložiště Metaverse** zobrazíte seznam atributů.  

    ![Informace v úložišti Metaverse](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Ověřte, že neexistuje žádná **cloudFiltered** atribut. Ujistěte se, že očekávané hodnoty atributů domény (domainFQDN a domainNetBios).

    j. Klikněte na tlačítko **konektory** kartu. Ujistěte se, jestli konektory, které se v místní službě Active Directory a Azure AD.

    ![Informace v úložišti Metaverse](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Vyberte řádek, který představuje Azure AD, klikněte na tlačítko **vlastnosti**a potom klikněte na tlačítko **rodokmenu** kartu. Objekt prostoru konektoru by mělo mít odchozí pravidlo v **PasswordSync** sloupec nastaven na **True**. Ve výchozím nastavení je název pravidla synchronizace **na AAD – uživatel připojit**.  

    ![Dialogové okno Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Protokol synchronizace hesla
Ve sloupci stav může mít následující hodnoty:

| Status | Popis |
| --- | --- |
| Úspěch |Heslo bylo úspěšně synchronizováno. |
| FilteredByTarget |Heslo je nastaveno na **musí uživatel změnit heslo při příštím přihlášení**. Hesla nebyl synchronizován. |
| NoTargetConnection |Objekt nebyl nalezen v úložišti metaverse nebo v prostoru konektoru Azure AD. |
| SourceConnectorNotPresent |Objekt nebyl nalezen v prostoru konektoru v místním Active Directory. |
| TargetNotExportedToDirectory |Objekt v prostoru konektoru Azure AD ještě nebyla exportována. |
| MigratedCheckDetailsForMoreInfo |Deníkový záznam vytvořil před sestavením 1.0.9125.0 a je zobrazena ve stavu starší verze. |
| Chyba |Služba vrátila neznámou chybu. |
| Neznámé |Došlo k chybě při pokusu o zpracování dávky hodnot hash hesel.  |
| MissingAttribute |Konkrétní atributy (například Kerberos hash) vyžaduje služby Azure AD Domain Services nejsou k dispozici. |
| RetryRequestedByTarget |Konkrétní atributy (například Kerberos hash) vyžaduje služby Azure AD Domain Services nebyly k dispozici dříve. Provádí se pokus o opětovnou synchronizaci hodnoty hash hesla uživatele. |

## <a name="scripts-to-help-troubleshooting"></a>Skripty, které pomůžou Poradce při potížích

### <a name="get-the-status-of-password-sync-settings"></a>Získat stav nastavení synchronizace hesel
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Spustit úplnou synchronizaci všech hesel
> [!NOTE]
> Tento skript spusťte jenom jednou. Pokud je potřeba spustit více než jednou, je něco jiného problému. Chcete-li potíže vyřešit, obraťte se na podporu Microsoftu.

Úplná synchronizace všech hesel můžete aktivovat pomocí následujícího skriptu:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Další postup
* [Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect Sync: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
