---
title: Poradce při potížích se synchronizací hash hesla pomocí synchronizace Azure AD Connect | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o řešení problémů se synchronizací hodnot hash hesel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253543"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží se synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect

Toto téma obsahuje postup řešení problémů se synchronizací hodnot hash hesel. Pokud hesla nejsou synchronizovány podle očekávání, může být buď pro podmnožinu uživatelů nebo pro všechny uživatele.

Pro Azure Active Directory (Azure AD) Připojte nasazení s verzí 1.1.614.0 nebo po, použijte úlohu řešení potíží v průvodci k řešení problémů se synchronizací hodnot hash hesla:

* Pokud máte problém, kdy nejsou synchronizována žádná hesla, podívejte se na [seznam Žádná hesla jsou synchronizovány: řešení potíží pomocí části úlohy řešení potíží.](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task)

* Pokud máte problém s jednotlivými objekty, podívejte se na [jeden objekt není synchronizace hesla: řešení potíží pomocí části úlohy řešení potíží.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task)

Pro nasazení s verzí 1.1.524.0 nebo novější je diagnostická rutina, kterou můžete použít k řešení problémů se synchronizací hash hesel:

* Pokud máte problém, kdy nejsou synchronizována žádná hesla, podívejte se na [nehesla jsou synchronizovány: řešení potíží pomocí diagnostické rutiny](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) části.

* Pokud máte problém s jednotlivými objekty, podívejte se na [jeden objekt není synchronizace hesla: řešení potíží pomocí diagnostické rutiny](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) části.

Pro starší verze nasazení Azure AD Connect:

* Pokud máte problém, kdy nejsou synchronizována žádná hesla, podívejte se do části [Žádná hesla: Pokyny pro ruční řešení potíží.](#no-passwords-are-synchronized-manual-troubleshooting-steps)

* Pokud máte problém s jednotlivými objekty, podívejte se na [jeden objekt není synchronizace hesla: ruční řešení potíží kroky](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) části.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Nejsou synchronizována žádná hesla: řešení potíží pomocí úlohy řešení potíží

Pomocí úlohy řešení potíží můžete zjistit, proč nejsou synchronizována žádná hesla.

> [!NOTE]
> Úloha řešení potíží je k dispozici jenom pro Azure AD Connect verze 1.1.614.0 nebo novější.

### <a name="run-the-troubleshooting-task"></a>Spuštění úlohy řešení potíží

Postup řešení problémů, kdy nejsou synchronizována žádná hesla:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru Azure AD Connect s možností **Spustit jako správce.**

2. Spustit `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`nebo .

3. Spusťte Průvodce připojením Azure AD Connect.

4. Přejděte na stránku **Další úkoly,** vyberte **Poradce při potížích**a klepněte na tlačítko **Další**.

5. Na stránce Poradce při potížích klikněte na **Spustit** a spusťte nabídku řešení potíží v PowerShellu.

6. V hlavní nabídce vyberte **Poradce při potížích s synchronizací hash hesel**.

7. V dílčí nabídce vyberte **synchronizace hodnot hash hesla vůbec nefunguje**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledků úlohy řešení potíží

Úloha řešení potíží provádí následující kontroly:

* Ověří, že funkce synchronizace hodnot hash hesla je povolená pro vašeho klienta Azure AD.

* Ověří, že server Azure AD Connect není v pracovním režimu.

* Pro každý existující místní konektor služby Active Directory (který odpovídá existující doménové struktuře služby Active Directory):

   * Ověří, zda je povolena funkce synchronizace hodnot hash hesla.
   
   * Vyhledá události prezenčního signálu synchronizace hodnot hash hesla v protokolech událostí aplikace systému Windows.

   * Pro každou doménu služby Active Directory v místním konektoru služby Active Directory:

      * Ověří, že doména je dosažitelná ze serveru Azure AD Connect.

      * Ověří, zda účty služby AD DS používané místním konektorem služby Active Directory mají správné uživatelské jméno, heslo a oprávnění vyžadovaná pro synchronizaci hodnot hash hesel.

Následující diagram znázorňuje výsledky rutiny pro topologii služby Active Directory s jednou doménou:

![Diagnostický výstup pro synchronizaci hodnot hash hesel](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

Zbývající část této části popisuje konkrétní výsledky, které jsou vráceny úkol a odpovídající problémy.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Funkce synchronizace hodnot hash hesel není povolena

Pokud jste nepovolili synchronizaci hash hesel pomocí Průvodce azure ad připojení, je vrácena následující chyba:

![Synchronizace hodnot hash hesel není povolena](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Server Azure AD Connect je v pracovním režimu

Pokud je server Azure AD Connect v pracovním režimu, synchronizace hodnot hash hesel je dočasně zakázána a je vrácena následující chyba:

![Server Azure AD Connect je v pracovním režimu](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Žádné události prezenčního signálu synchronizace hodnot hash hesel

Každý místní konektor služby Active Directory má vlastní kanál synchronizace hodnot hash hesel. Když je vytvořen kanál synchronizace hash hesla a neexistují žádné změny hesla, které mají být synchronizovány, událost prezenčního signálu (EventId 654) je generována jednou za 30 minut v protokolu událostí aplikace systému Windows. Pro každý místní konektor služby Active Directory rutina vyhledá odpovídající události prezenčního signálu za poslední tři hodiny. Pokud není nalezena žádná událost prezenčního signálu, je vrácena následující chyba:

![Žádná událost synchronizace hash hesla srdeční hod](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Účet ad DS nemá správná oprávnění

Pokud účet služby AD DS používaný místním konektorem služby Active Directory k synchronizaci hashe hesel nemá příslušná oprávnění, vrátí se následující chyba:

![Nesprávné pověření](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nesprávné uživatelské jméno nebo heslo účtu ad ds

Pokud má účet služby AD DS používaný místním konektorem služby Active Directory k synchronizaci hodnoty hashe hesel nesprávné uživatelské jméno nebo heslo, vrátí se následující chyba:

![Nesprávné pověření](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden objekt nesynchronizuje hesla: řešení potíží pomocí úlohy řešení potíží

Pomocí úlohy řešení potíží můžete určit, proč jeden objekt nesynchronizuje hesla.

> [!NOTE]
> Úloha řešení potíží je k dispozici jenom pro Azure AD Connect verze 1.1.614.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spuštění rutiny diagnostiky

Postup řešení problémů s určitým objektem uživatele:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru Azure AD Connect s možností **Spustit jako správce.**

2. Spustit `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`nebo .

3. Spusťte Průvodce připojením Azure AD Connect.

4. Přejděte na stránku **Další úkoly,** vyberte **Poradce při potížích**a klepněte na tlačítko **Další**.

5. Na stránce Poradce při potížích klikněte na **Spustit** a spusťte nabídku řešení potíží v PowerShellu.

6. V hlavní nabídce vyberte **Poradce při potížích s synchronizací hash hesel**.

7. V dílčí nabídce není pro **konkrétní uživatelský účet synchronizováno políčko Heslo**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledků úlohy řešení potíží

Úloha řešení potíží provádí následující kontroly:

* Zkontroluje stav objektu služby Active Directory v prostoru konektoru služby Active Directory, Metaverse a Azure AD konektor prostoru.

* Ověří, zda existují pravidla synchronizace s povolenou synchronizací hodnot hash hesel a použitou u objektu služby Active Directory.

* Pokusí se načíst a zobrazit výsledky posledního pokusu o synchronizaci hesla pro objekt.

Následující diagram znázorňuje výsledky rutiny při řešení potíží se synchronizací hash hesel pro jeden objekt:

![Diagnostický výstup pro synchronizaci hodnot hash hesel - jeden objekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

Zbývající část této části popisuje konkrétní výsledky vrácené rutinou a odpovídající problémy.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Objekt služby Active Directory se neexportuje do služby Azure AD.

Synchronizace hodnot hash hesla pro tento místní účet služby Active Directory se nezdaří, protože v tenantovi Azure AD neexistuje žádný odpovídající objekt. Je vrácena následující chyba:

![Chybí objekt Azure AD](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Uživatel má dočasné heslo

V současné době Azure AD Connect nepodporuje synchronizaci dočasných hesel s Azure AD. Heslo se považuje za dočasné, pokud je u místního uživatele služby Active Directory nastavena možnost Změnit heslo při **příštím přihlášení.** Je vrácena následující chyba:

![Dočasné heslo není exportováno.](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Výsledky posledního pokusu o synchronizaci hesla nejsou k dispozici.

Ve výchozím nastavení Azure AD Connect ukládá výsledky pokusů o synchronizaci hodnot hash hesla po dobu sedmi dnů. Pokud pro vybraný objekt služby Active Directory nejsou k dispozici žádné výsledky, bude vráceno následující upozornění:

![Diagnostický výstup pro jeden objekt - bez historie synchronizace hesel](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nejsou synchronizována žádná hesla: řešení potíží pomocí diagnostické rutiny

Pomocí rutiny `Invoke-ADSyncDiagnostics` můžete zjistit, proč nejsou synchronizována žádná hesla.

> [!NOTE]
> Rutina `Invoke-ADSyncDiagnostics` je k dispozici pouze pro Azure AD Connect verze 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spuštění rutiny diagnostiky

Postup řešení problémů, kdy nejsou synchronizována žádná hesla:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru Azure AD Connect s možností **Spustit jako správce.**

2. Spustit `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`nebo .

3. Spusťte `Import-Module ADSyncDiagnostics`.

4. Spusťte `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden objekt nesynchronizuje hesla: řešení potíží pomocí diagnostické rutiny

Pomocí rutiny `Invoke-ADSyncDiagnostics` můžete určit, proč jeden objekt nesynchronizuje hesla.

> [!NOTE]
> Rutina `Invoke-ADSyncDiagnostics` je k dispozici pouze pro Azure AD Connect verze 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spuštění rutiny diagnostiky

Postup řešení problémů, kdy nejsou pro uživatele synchronizována žádná hesla:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru Azure AD Connect s možností **Spustit jako správce.**

2. Spustit `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`nebo .

3. Spusťte `Import-Module ADSyncDiagnostics`.

4. Spusťte následující rutinu:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Například:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Nejsou synchronizována žádná hesla: postup ručního řešení potíží

Chcete-li zjistit, proč nejsou synchronizována žádná hesla, postupujte takto:

1. Je server Connect v [pracovním režimu](how-to-connect-sync-staging-server.md)? Server v pracovním režimu nesynchronizuje žádná hesla.

2. Spusťte skript v části [Získat stav nastavení synchronizace hesla.](#get-the-status-of-password-sync-settings) Poskytuje přehled konfigurace synchronizace hesel.  

    ![Výstup skriptu PowerShellu z nastavení synchronizace hesel](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Pokud tato funkce není povolena ve službě Azure AD nebo pokud není povolen stav synchronizačního kanálu, spusťte Průvodce instalací připojení. Vyberte **Přizpůsobit možnosti synchronizace**a zrušte výběr synchronizace hesel. Tato změna dočasně zakáže funkci. Potom průvodce spusťte znovu a znovu povolte synchronizaci hesel. Spusťte skript znovu a ověřte, zda je konfigurace správná.

4. Vyhledejte v protokolu událostí chyby. Vyhledejte následující události, které by naznačovaly problém:
    * Zdroj: ID synchronizace adresářů: 0, 611, 652, 655 Pokud se zobrazí tyto události, máte potíže s připojením. Zpráva protokolu událostí obsahuje informace o doménové struktuře, kde máte potíže. Další informace naleznete v tématu [Problém s připojením](#connectivity problem).

5. Pokud nevidíte žádný prezenční signál nebo pokud nic jiného nefungovalo, [spusťte Spustit úplnou synchronizaci všech hesel](#trigger-a-full-sync-of-all-passwords). Spusťte skript pouze jednou.

6. Viz část Poradce při potížích s jedním objektem, který nesynchronizuje hesla.

### <a name="connectivity-problems"></a>Problémy s připojením

Máte připojení k Azure AD?

Má účet požadovaná oprávnění ke čtení hashe hesel ve všech doménách? Pokud jste aplikaci Connect nainstalovali pomocí nastavení Express, měla by být oprávnění již správná. 

Pokud jste použili vlastní instalaci, nastavte oprávnění ručně následujícím způsobem:
    
1. Chcete-li najít účet používaný konektorem služby Active Directory, **spusťte Správce synchronizačních služeb**. 
 
2. Přejděte do **polepy a**vyhledejte místní doménovou strukturu služby Active Directory, kterou řešíte. 
 
3. Vyberte spojnici a klepněte na příkaz **Vlastnosti**. 
 
4. Přejděte na **odkaz Připojit k doménové struktuře služby Active Directory**.  
    
    ![Účet používaný konektorem služby Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Poznamenejte si uživatelské jméno a doménu, ve které je účet umístěn.
    
5. Spusťte **službu Uživatelé a počítače služby Active Directory a**ověřte, zda dříve nalezený účet má oprávnění pro sledování nastavená v kořenovém adresáři všech domén v doménové struktuře:
    * Replikovat změny adresáře
    * Replikovat všechny změny adresáře

6. Jsou řadiče domény dostupné pomocí služby Azure AD Connect? Pokud se server Connect nemůže připojit ke všem řadičům domény, nakonfigurujte **pouze upřednostňovaný řadič domény**.  
    
    ![Řadič domény používaný konektorem služby Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Vraťte se do **správce synchronizační služby** a **konfigurace oddílu adresáře**. 
 
8. Zaškrtněte doménu v **části Vybrat oddíly adresáře**, zaškrtněte políčko **Pouze upřednostňované řadiče domény** a klepněte na tlačítko **Konfigurovat**. 

9. V seznamu zadejte řadiče domény, které by měl použít connect pro synchronizaci hesel. Stejný seznam se používá také pro import a export. Proveďte tyto kroky pro všechny domény.

10. Pokud skript ukazuje, že není prezenční signál, spusťte skript v [aktivovat úplnou synchronizaci všech hesel](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden objekt nesynchronizuje hesla: ruční postup řešení potíží

Můžete snadno řešit problémy se synchronizací hodnot hash hesla kontrolou stavu objektu.

1. Ve **službě Uživatelé a počítače služby Active Directory**vyhledejte uživatele a ověřte, zda je zaškrtnuto políčko Uživatel změnit heslo při příštím **přihlášení.**  

    ![Produktivní hesla služby Active Directory](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Pokud je toto políčko zaškrtnuto, požádejte uživatele, aby se přihlásil a změnil heslo. Dočasná hesla nejsou synchronizovány s Azure AD.

2. Pokud heslo vypadá ve službě Active Directory správně, postupujte podle uživatele v synchronizačním modulu. Sledováním uživatele z místní služby Active Directory do služby Azure AD můžete zjistit, zda je na objektu popisná chyba.

    a. Spusťte [Správce synchronizačních služeb](how-to-connect-sync-service-manager-ui.md).

    b. Klepněte na **položku Konektory**.

    c. Vyberte **konektor služby Active Directory,** ve kterém je uživatel umístěn.

    d. Vyberte **možnost Hledat mezeru v spojnici**.

    e. V poli **Obor** vyberte **DN nebo Anchor**a zadejte úplný dn uživatele, kterému řešíte potíže.

    ![Hledání uživatele v prostoru konektoru pomocí dn](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Vyhledejte hledavého uživatele a kliknutím na **Vlastnosti** zopřete všechny atributy. Pokud uživatel není ve výsledku hledání, ověřte [pravidla filtrování](how-to-connect-sync-configure-filtering.md) a ujistěte se, že spustíte [Použít a ověřte změny,](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) aby se uživatel zobrazil v aplikaci Připojit.

    g. Chcete-li zobrazit podrobnosti o synchronizaci hesla objektu za poslední týden, klepněte na tlačítko **Protokolovat**.  

    ![Podrobnosti protokolu objektů](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Pokud je protokol objektů prázdný, azure ad connect nebyl schopen číst hash hesla ze služby Active Directory. Pokračujte v řešení potíží s chybami připojení. Pokud se zobrazí jiná hodnota než **úspěch**, podívejte se do tabulky v [protokolu synchronizace hesel](#password-sync-log).

    h. Vyberte kartu **linie** a ujistěte se, že alespoň jedno pravidlo synchronizace ve sloupci **PasswordSync** je **True**. Ve výchozí konfiguraci je název pravidla synchronizace **V ze služby AD – User AccountEnabled**.  

    ![Informace o uživateli](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klepnutím na **položku Vlastnosti objektu Metaverse** zobrazíte seznam atributů uživatele.  

    ![Metaverse informace](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Ověřte, zda není k dispozici žádný atribut **cloudFiltered.** Ujistěte se, že atributy domény (domainFQDN a domainNetBios) mají očekávané hodnoty.

    j. Klikněte na kartu **Konektory.**

    ![Metaverse informace](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Vyberte řádek, který představuje Azure AD, klikněte na **vlastnosti**a potom klikněte na kartu **Lineage** . Objekt prostoru konektoru by měl mít odchozí pravidlo ve sloupci **PasswordSync** nastavené na **hodnotu True**. Ve výchozí konfiguraci je název pravidla synchronizace **Out to AAD - User Join**.  

    ![Dialogové okno Vlastnosti objektu prostoru spojnice](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Protokol synchronizace hesel

Sloupec stavu může mít následující hodnoty:

| Status | Popis |
| --- | --- |
| Úspěch |Heslo bylo úspěšně synchronizováno. |
| FilteredByTarget |Heslo je nastaveno na **uživatel musí změnit heslo při příštím přihlášení**. Heslo nebylo synchronizováno. |
| NoTargetConnection |Žádný objekt v metaverse nebo v prostoru konektoru Azure AD. |
| SourceConnectorNotPresent |V místním prostoru konektoru služby Active Directory nebyl nalezen žádný objekt. |
| CílNotExportedToDirectory |Objekt v prostoru konektoru Azure AD ještě nebyl exportován. |
| MigratedCheckDetailsForMoreInfo |Položka protokolu byla vytvořena před sestavením 1.0.9125.0 a je zobrazena v jeho starším stavu. |
| Chyba |Služba vrátila neznámou chybu. |
| Není známo |Při pokusu o zpracování dávky hashe hesel došlo k chybě.  |
| Chybějící atribut |Konkrétní atributy (například hodnota hash protokolu Kerberos) vyžadované službou Azure AD Domain Services nejsou k dispozici. |
| Opakovat CílOvá skupina |Konkrétní atributy (například hodnota hash protokolu Kerberos) vyžadované službou Azure AD Domain Services nebyly dříve k dispozici. Je proveden pokus o synchronizaci hash hesla uživatele. |

## <a name="scripts-to-help-troubleshooting"></a>Skripty, které vám pomohou při řešení potíží

### <a name="get-the-status-of-password-sync-settings"></a>Získání stavu nastavení synchronizace hesel

```powershell
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Aktivace úplné synchronizace všech hesel

> [!NOTE]
> Spusťte tento skript pouze jednou. Pokud potřebujete spustit více než jednou, něco jiného je problém. Chcete-li problém vyřešit, obraťte se na podporu společnosti Microsoft.

Úplnou synchronizaci všech hesel můžete spustit pomocí následujícího skriptu:

```powershell
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

## <a name="next-steps"></a>Další kroky

* [Implementace synchronizace hash hesel se synchronizací Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Synchronizace připojení Azure AD: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)