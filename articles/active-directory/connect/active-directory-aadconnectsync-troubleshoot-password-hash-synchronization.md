---
title: Řešení potíží s synchronizaci hodnoty hash hesla s Azure AD Connect sync | Microsoft Docs
description: Tento článek obsahuje informace o tom, jak řešit potíže hodnoty hash hesla.
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
ms.author: billmath
ms.openlocfilehash: bcf266813476c682d47bfd483db77f5d8b73837a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží s synchronizaci hodnoty hash hesla s synchronizace Azure AD Connect
Toto téma popisuje kroky pro řešení potíží s synchronizaci hodnoty hash hesla. Pokud hesla se nesynchronizují podle očekávání, může být buď pro podmnožinu uživatelů, nebo pro všechny uživatele.

Pro nasazení služby Azure Active Directory (Azure AD) připojit s verzí 1.1.614.0 nebo po použijte úlohu řešení potíží v průvodci k řešení potíží synchronizace hodnoty hash hesla:

* Pokud máte potíže, kde jsou synchronizovány žádná hesla, přečtěte si [bez hesla se synchronizují: řešení potíží pomocí úloh odstraňování potíží](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) části.

* Pokud máte potíže s jednotlivé objekty, podívejte se na [jeden objekt není synchronizace hesel: řešení potíží pomocí řešení potíží úloh](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) části.

Pro nasazení s verzí 1.1.524.0 nebo novější, je k dispozici diagnostické rutina, můžete použít k řešení potíží synchronizace hodnoty hash hesla:

* Pokud máte potíže, kde jsou synchronizovány žádná hesla, podívejte se na [bez hesla se synchronizují: řešení potíží pomocí diagnostiky rutina](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) části.

* Pokud máte potíže s jednotlivé objekty, podívejte se na [jeden objekt není synchronizace hesel: řešení potíží pomocí diagnostiky rutina](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) části.

Pro starší verze aplikace Azure AD Connect nasazení:

* Pokud máte potíže, kde jsou synchronizovány žádná hesla, přečtěte si [bez hesla se synchronizují: Ruční postup řešení potíží](#no-passwords-are-synchronized-manual-troubleshooting-steps) části.

* Pokud máte potíže s jednotlivé objekty, podívejte se na [jeden objekt není synchronizace hesel: Ruční postup řešení potíží](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) části.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Žádná hesla se synchronizují: řešení potíží pomocí úlohu řešení potíží
Odstraňování úkolů můžete zjistit, proč jsou synchronizovány žádná hesla.

> [!NOTE]
> Řešení potíží úloha je k dispozici pouze pro verze služby Azure AD Connect 1.1.614.0 nebo novější.

### <a name="run-the-troubleshooting-task"></a>Spusťte úlohu řešení potíží
Řešení problémů, kde jsou synchronizovány žádná hesla:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru s Azure AD Connect **spustit jako správce** možnost.

2. Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.

3. Spusťte Průvodce službou Azure AD Connect.

4. Přejděte na **další úkoly** vyberte **Poradce při potížích**a klikněte na tlačítko **Další**.

5. Na stránce Poradce při potížích, klikněte na **spusťte** k řešení potíží nabídky start v prostředí PowerShell.

6. V hlavní nabídce vyberte **řešení potíží s synchronizaci hodnoty hash hesla**.

7. V nabídce dílčí vyberte **synchronizaci hodnoty hash hesla nefunguje vůbec**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Pochopení výsledky úlohy řešení potíží
Řešení potíží úloha provede následující kontroly:

* Ověří, zda je povoleno funkce synchronizace hodnoty hash hesla klientovi Azure AD.

* Ověří, zda není server Azure AD Connect v pracovním režimu.

* Pro každý existující místní konektor služby Active Directory (který odpovídá existující doménové struktury služby Active Directory):

   * Ověří, že je povolena funkce synchronizace hodnoty hash hesla.
   
   * Vyhledá heslo hash události prezenční signál synchronizace v protokolech událostí aplikace systému Windows.

   * Pro každou doménu služby Active Directory v rámci konektoru služby Active Directory v místě:

      * Ověří, že doména je dostupný ze serveru, Azure AD Connect.

      * Ověří, že má správné uživatelské jméno, heslo a oprávněních pro synchronizaci hodnoty hash hesla účtů služby Active Directory Domain Services (AD DS) používaných místní konektor služby Active Directory.

Následující diagram znázorňuje výsledky rutiny pro topologie s jednou doménou, místní služby Active Directory:

![Výstup diagnostiky pro synchronizaci hodnoty hash hesla](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalgeneral.png)

Zbývající část tohoto oddílu popisuje konkrétní výsledky, které se vrátí pomocí úloh a odpovídající problémy.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Funkce synchronizace hodnoty hash hesla není povoleno.
Pokud nepovolíte synchronizaci hodnoty hash hesla pomocí Průvodce službou Azure AD Connect, je vrácena následující chyba:

![synchronizaci hodnoty hash hesla není povoleno.](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Server Azure AD Connect je v pracovním režimu
Pokud je server Azure AD Connect v pracovním režimu, synchronizaci hodnoty hash hesla je dočasně zakázána a vrácena následující chyba:

![Server Azure AD Connect je v pracovním režimu](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Žádné události prezenční signál synchronizace hodnoty hash hesla
Každý místní konektor služby Active Directory má vlastní kanál synchronizace hodnoty hash hesla. Při vytvoření kanálu synchronizace hodnoty hash hesla a nejsou k dispozici žádné změny hesla k synchronizaci, prezenční signál události (EventId 654) generuje každých 30 minut v protokolu událostí aplikace systému Windows. Pro každý konektor služby Active Directory v místě rutina vyhledá odpovídající události prezenčního signálu v posledních tří hodin. Pokud se nenajde žádný prezenční signál události, je vrácena následující chyba:

![Žádné vysílat synchronizace hodnoty hash hesla porazit událostí](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS účet nemá správná oprávnění
Pokud účet služby AD DS, který se používá místní konektor služby Active Directory k synchronizaci hodnot hash hesel nemá příslušná oprávnění, je vrácena následující chyba:

![Nesprávná pověření](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nesprávné uživatelské jméno účtu služby AD DS nebo heslo
Pokud účet služby AD DS používá místní konektor služby Active Directory k synchronizaci hodnot hash hesel má nesprávné uživatelské jméno nebo heslo, je vrácena následující chyba:

![Nesprávná pověření](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden objekt není synchronizace hesel: řešení potíží pomocí úlohu řešení potíží

Řešení potíží úlohy vám pomohou zjistit, proč není jeden objekt synchronizace hesel.

> [!NOTE]
> Řešení potíží úloha je k dispozici pouze pro verze služby Azure AD Connect 1.1.614.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spusťte rutinu diagnostiky
Řešení problémů pro objekt konkrétní uživatele:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru s Azure AD Connect **spustit jako správce** možnost.

2. Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.

3. Spusťte Průvodce službou Azure AD Connect.

4. Přejděte na **další úkoly** vyberte **Poradce při potížích**a klikněte na tlačítko **Další**.

5. Na stránce Poradce při potížích, klikněte na **spusťte** k řešení potíží nabídky start v prostředí PowerShell.

6. V hlavní nabídce vyberte **řešení potíží s synchronizaci hodnoty hash hesla**.

7. V nabídce dílčí vyberte **pro konkrétní uživatelský účet není synchronizován heslo**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Pochopení výsledky úlohy řešení potíží
Řešení potíží úloha provede následující kontroly:

* Prozkoumá stav objektu služby Active Directory v prostoru konektoru služby Active Directory, úložiště Metaverse a Azure AD prostoru konektoru.

* Ověří, že jsou pravidla synchronizace s synchronizaci hodnoty hash hesla povolena a u objektu služby Active Directory.

* Pokusí se načíst a zobrazit výsledky poslední pokus o synchronizaci hesla pro objekt.

Následující diagram znázorňuje výsledky rutiny při řešení potíží s synchronizaci hodnoty hash hesla pro jediný objekt:

![Výstup diagnostiky pro synchronizaci hodnoty hash hesla - jednoho objektu.](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectgeneral.png)

Zbývající část tohoto oddílu popisuje konkrétní výsledky vrácené rutiny a odpovídající problémy.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Objekt služby Active Directory není exportovány do služby Azure AD
synchronizaci hodnoty hash hesla pro tento účet místní služby Active Directory se nezdaří, protože neexistuje žádný odpovídající objektů v klientovi Azure AD. Je vrácena následující chyba:

![Chybí objekt Azure AD](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Uživatel má dočasné heslo.
Azure AD Connect v současné době nepodporuje synchronizaci dočasná hesla s Azure AD. Heslo se považuje za dočasnou Pokud **při dalším přihlášení změnit heslo** je možnost nastavena na místní uživatele služby Active Directory. Je vrácena následující chyba:

![Dočasné heslo není exportovali.](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Výsledky poslední pokus o synchronizaci hesla nejsou k dispozici
Ve výchozím nastavení Azure AD Connect ukládá výsledky pokusy o synchronizaci hodnoty hash hesla sedm dní. Pokud žádné výsledky nejsou dostupné pro vybraný objekt služby Active Directory, se vrátí následující upozornění:

![Výstup diagnostiky pro jediný objekt - žádná historie synchronizace hesla](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Žádná hesla se synchronizují: řešení potíží pomocí rutiny diagnostiky
Můžete použít `Invoke-ADSyncDiagnostics` rutiny zjistěte, proč jsou synchronizovány žádná hesla.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Rutina je dostupné pouze pro verze služby Azure AD Connect 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spusťte rutinu diagnostiky
Řešení problémů, kde jsou synchronizovány žádná hesla:

1. Otevřete novou relaci prostředí Windows PowerShell na serveru s Azure AD Connect **spustit jako správce** možnost.

2. Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.

3. Spusťte `Import-Module ADSyncDiagnostics`.

4. Spusťte `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden objekt není synchronizace hesel: řešení potíží pomocí rutiny diagnostiky
Můžete použít `Invoke-ADSyncDiagnostics` rutiny zjistit, proč se jeden objekt synchronizace hesel.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` Rutina je dostupné pouze pro verze služby Azure AD Connect 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spusťte rutinu diagnostiky
Řešení problémů, které jsou synchronizovány žádná hesla pro uživatele:

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



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Žádná hesla se synchronizují: Ruční postup řešení potíží
Postupujte podle těchto kroků, chcete-li zjistit, proč jsou synchronizovány žádná hesla:

1. Je server Connect v [pracovním režimu](active-directory-aadconnectsync-operations.md#staging-mode)? Server v pracovní režimu nebyla synchronizována všechna hesla.

2. Spusťte skript [získat stav nastavení synchronizace hesla](#get-the-status-of-password-sync-settings) části. Poskytuje přehled o konfiguraci synchronizace hesel.  

    ![Výstup skriptu prostředí PowerShell z nastavení synchronizace hesla](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/psverifyconfig.png)  

3. Pokud není povolená funkce ve službě Azure AD, nebo pokud není povolené kanál stav synchronizace, spusťte Průvodce instalací služby Connect. Vyberte **přizpůsobit možnosti synchronizace**a zrušte výběr synchronizaci hesel. Tato změna dočasně zakáže funkci. Potom spusťte znovu Průvodce a znovu povolte synchronizaci hesla. Spuštění skriptu znovu k ověření, že konfigurace je správná.

4. Vyhledejte v protokolu událostí pro chyby. Podívejte se na následující události, které by mohly ukazovat na problém:
    * Zdroje: ID "Synchronizace adresáře": 0, 611, 652, 655 těchto událostí došlo k potížím. připojení. Zprávy protokolu událostí obsahuje informace doménové struktuře, kde došlo k potížím. Další informace najdete v tématu [problém s připojením k](#connectivity problem).

5. Pokud se neobjevil žádný prezenční signál nebo nic jiného fungovala, spusťte [spustit úplnou synchronizaci hesel všech](#trigger-a-full-sync-of-all-passwords). Skript spusťte jenom jednou.

6. Najdete v článku [řešení potíží s jeden objekt, který není synchronizace hesel](#one-object-is-not-synchronizing-passwords) části.

### <a name="connectivity-problems"></a>Potíže s připojením k

Máte připojení k Azure AD?

Má účet požadovaná oprávnění ke čtení hodnot hash hesel ve všech doménách? Pokud jste nainstalovali Connect pomocí expresního nastavení, oprávnění by měl být již správné. 

Pokud jste použili vlastní instalaci, nastavte oprávnění ručně následujícím způsobem:
    
1. Chcete-li najít má účet používaný službou konektoru služby Active Directory, spusťte **Synchronization Service Manager**. 
 
2. Přejděte na **konektory**a poté vyhledejte doménové struktury služby Active Directory místní řešení potíží. 
 
3. Vyberte konektor a potom klikněte na **vlastnosti**. 
 
4. Přejděte na **připojit k doménové struktuře služby Active Directory**.  
    
    ![Účet používaný službou konektoru služby Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/connectoraccount.png)  
    Všimněte si, uživatelské jméno a doménu, kde je umístěn účet.
    
5. Spustit **Active Directory Users and Computers**a potom ověřte, zda má účet jste našli dříve oprávnění postupujte podle kroků nastavte v kořenovém adresáři všechny domény v doménové struktuře:
    * Replikovat změny adresáře
    * Replikace adresáře všechny změny

6. Jsou dostupné přes Azure AD Connect řadiče domény? Pokud server připojit se nelze připojit na všechny řadiče domény, nakonfigurujte **používat jenom upřednostňovaného řadiče domény**.  
    
    ![Řadič domény používané konektorem služby Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/preferreddc.png)  
    
7. Přejděte zpět na **Synchronization Service Manager** a **konfigurace oddílu adresáře**. 
 
8. Vyberte doménu v **Vybrat oddíly adresářů**, vyberte **používat jenom řadiče domény upřednostňované** zaškrtněte políčko a potom klikněte na **konfigurace**. 

9. V seznamu zadejte řadiče domény, které by měly používat Connect pro synchronizaci hesel. Stejný seznam se používá pro import a export také. Proveďte tyto kroky pro všechny domény.

10. Pokud skript ukazuje, že neexistuje žádný prezenční signál, spusťte skript v [spustit úplnou synchronizaci hesel všech](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden objekt není synchronizace hesel: Ruční postup řešení potíží
Snadno můžete řešit problémy s synchronizací hodnoty hash hesla kontrolou stavu objektu.

1. V **Active Directory Users and Computers**, vyhledejte uživatele a ověřte, že **musí uživatel změnit heslo při příštím přihlášení** zaškrtnutí políčka.  

    ![Produktivní hesla služby Active Directory](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/adprodpassword.png)  

    Pokud je zaškrtnuté políčko, požádejte uživatele přihlásit a změnit heslo. Dočasná hesla nejsou synchronizovány s Azure AD.

2. Pokud ve službě Active Directory správné heslo, postupujte podle uživatele v synchronizační modul. Pomocí následujících uživatele z místní služby Active Directory do Azure AD, se zobrazí, zda je u objektu popisný chyba.

    a. Spuštění [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

    b. Klikněte na tlačítko **konektory**.

    c. Vyberte **konektor služby Active Directory** kde se uživatel nachází.

    d. Vyberte **hledání prostoru konektoru**.

    e. V **oboru** vyberte **rozlišující název nebo ukotvení**a pak zadejte úplný název DN uživatele řešení potíží.

    ![Vyhledejte uživatele v prostoru konektoru s rozlišujícím názvem](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/searchcs.png)  

    f. Vyhledejte uživatele, které hledáte a potom klikněte na **vlastnosti** zobrazíte všechny atributy. Pokud uživatel není ve výsledcích hledání, ověřte vaší [pravidla filtrování](active-directory-aadconnectsync-configure-filtering.md) a ujistěte se, že spustíte [použít a ověřte změny](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes) pro uživatele, než se objeví v připojení.

    g. Chcete-li zobrazit podrobnosti synchronizace hesla objektu za uplynulý týden, klikněte na tlačítko **protokolu**.  

    ![Podrobnosti protokolu objektu](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/csobjectlog.png)  

    Pokud protokol objektu je prázdná, Azure AD Connect nelze číst hodnotu hash hesla ze služby Active Directory. Pokračovat v odstraňování problémů s [k chybám připojení](#connectivity-errors). Pokud se zobrazí jakoukoli jinou hodnotu než **úspěch**, naleznete v tabulce v [protokolu synchronizace hesla](#password-sync-log).

    h. Vyberte **rodokmenu** kartě a ujistěte se, že aspoň jeden synchronizační pravidlo v **PasswordSync** sloupec je **True**. Ve výchozím nastavení je název pravidla synchronizace **v ze služby Active Directory - uživatele AccountEnabled**.  

    ![Rodokmenu informací o uživateli](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync.png)  

    i. Klikněte na tlačítko **vlastnosti objektu úložiště Metaverse** zobrazíte seznam atributů uživatele.  

    ![Informace o úložiště Metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvpasswordsync.png)  

    Ověřte, zda je žádné **cloudFiltered** atribut přítomen. Ujistěte se, že domény atributy (domainFQDN a domainNetBios) mají očekávaných hodnot.

    j. Klikněte **konektory** kartě. Ujistěte se, najdete v části konektory pro místní služby Active Directory a Azure AD.

    ![Informace o úložiště Metaverse](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvconnectors.png)  

    k. Vyberte řádek, který představuje Azure AD, klikněte na tlačítko **vlastnosti**a klikněte **rodokmenu** kartě. Objekt konektoru místa měli odchozí pravidlo **PasswordSync** sloupec nastavený na **True**. Ve výchozím nastavení je název pravidla synchronizace **Out aad - připojení uživatele k**.  

    ![Dialogové okno Vlastnosti objektu prostoru konektoru](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Protokol synchronizace hesla
Ve sloupci stav může mít následující hodnoty:

| Stav | Popis |
| --- | --- |
| Úspěšné |Heslo se úspěšně synchronizoval. |
| FilteredByTarget |Heslo je nastaveno na **musí uživatel změnit heslo při příštím přihlášení**. Heslo nebyl synchronizován. |
| NoTargetConnection |Žádný objekt v úložišti metaverse nebo v prostoru konektoru služby Azure AD. |
| SourceConnectorNotPresent |Žádný objekt v prostoru konektoru místní služby Active Directory nalezen. |
| TargetNotExportedToDirectory |Objekt v prostoru konektoru služby Azure AD nebyl dosud byla exportována. |
| MigratedCheckDetailsForMoreInfo |Položka protokolu byl vytvořen ještě před sestavení 1.0.9125.0 a je zobrazen v stav starší verze. |
| Chyba |Služba vrátila neznámou chybu. |
| Unknown |Došlo k chybě při pokusu o zpracování dávky hodnot hash hesel.  |
| MissingAttribute |Konkrétní atributy (například hodnota hash protokolu Kerberos) vyžaduje Azure AD Domain Services nejsou k dispozici. |
| RetryRequestedByTarget |Konkrétní atributy (například hodnota hash protokolu Kerberos) vyžaduje Azure AD Domain Services nebyly k dispozici dříve. Je proveden pokus o opakovanou synchronizaci hodnoty hash hesla uživatele. |

## <a name="scripts-to-help-troubleshooting"></a>Skripty, které pomůžou řešení potíží

### <a name="get-the-status-of-password-sync-settings"></a>Načíst stav nastavení synchronizace hesla
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
> Tento skript spusťte jenom jednou. Pokud potřebujete spustit více než jednou, je něco jiného problému. K vyřešení tohoto problému, kontaktujte podporu společnosti Microsoft.

Pomocí následujícího skriptu můžete aktivovat úplnou synchronizaci všech hesel:

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
* [Implementace synchronizaci hodnoty hash hesla s synchronizace Azure AD Connect](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
* [Azure AD Connect Sync: Přizpůsobení možnosti synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
