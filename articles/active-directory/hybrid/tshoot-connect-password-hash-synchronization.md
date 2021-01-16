---
title: Řešení potíží se synchronizací hodnot hash hesel pomocí Azure AD Connect synchronizace | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak řešit potíže se synchronizací hodnot hash hesel.
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
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c6ec162ceb51c3bf19be42219b054d8371ff221
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247348"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží se synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect

V tomto tématu najdete postup řešení potíží se synchronizací hodnot hash hesel. Pokud se hesla nesynchronizují podle očekávání, může to být buď pro podmnožinu uživatelů, nebo pro všechny uživatele.

Pokud chcete Azure Active Directory (Azure AD) připojit nasazení s verzí 1.1.614.0 nebo novější, použijte v průvodci úlohu řešení potíží k řešení potíží se synchronizací hodnot hash hesel:

* Pokud máte problém s tím, že se žádná hesla nesynchronizují, Projděte si informace o tom, že se nesynchronizují [žádná hesla: řešení potíží pomocí oddílu úlohy řešení potíží](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) .

* Pokud máte problém s jednotlivými objekty, Projděte si odkaz na [jeden objekt nesynchronizující hesla: Poradce při potížích pomocí oddílu úlohy řešení potíží](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) .

Pro nasazení s verzí 1.1.524.0 nebo novější je k dispozici diagnostická rutina, kterou můžete použít k řešení potíží se synchronizací hodnot hash hesel:

* Pokud máte problém s tím, že se žádná hesla nesynchronizují, Projděte si téma [žádná hesla nejsou synchronizovaná: řešení potíží pomocí diagnostické rutiny](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) .

* Pokud máte problém s jednotlivými objekty, Projděte si odkaz na [jeden objekt nesynchronizující hesla: řešení potíží pomocí části diagnostické rutiny](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) .

Pro starší verze Azure AD Connect nasazení:

* Pokud máte problém s tím, že se žádná hesla nesynchronizují, přečtěte si téma [nesynchronizovaná hesla: oddíl ručních kroků při odstraňování potíží](#no-passwords-are-synchronized-manual-troubleshooting-steps) .

* Pokud máte problém s jednotlivými objekty, přečtěte si informace o tom, že [jeden objekt nesynchronizuje hesla: oddíl ručních kroků při odstraňování potíží](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) .



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Žádná hesla nejsou synchronizovaná: řešení potíží pomocí úlohy řešení potíží.

Pomocí úlohy řešení potíží můžete zjistit, proč nejsou synchronizována žádná hesla.

> [!NOTE]
> Úloha řešení potíží je k dispozici pouze pro Azure AD Connect verze 1.1.614.0 nebo novější.

### <a name="run-the-troubleshooting-task"></a>Spuštění úlohy odstraňování potíží

Řešení potíží, při kterých se nesynchronizují žádná hesla:

1. Na serveru Azure AD Connect otevřete novou relaci prostředí Windows PowerShell s možností **Spustit jako správce** .

2. Spusťte `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted` .

3. Spusťte Průvodce Azure AD Connect.

4. Přejděte na stránku **další úlohy** , vyberte možnost **řešení potíží** a klikněte na tlačítko **Další**.

5. Na stránce Poradce při potížích klikněte na **Spustit** a spusťte v PowerShellu nabídku pro odstraňování potíží.

6. V hlavní nabídce vyberte **řešit potíže se synchronizací hodnot hash hesel**.

7. V podnabídce vyberte možnost **synchronizace hodnot hash hesel nefunguje vůbec**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledků úlohy odstraňování potíží

Úloha řešení potíží provádí následující kontroly:

* Ověří, jestli je pro vašeho tenanta Azure AD povolená funkce synchronizace hodnoty hash hesla.

* Ověří, zda Azure AD Connect Server není v pracovním režimu.

* Pro každý existující místní konektor služby Active Directory (který odpovídá stávající doménové struktuře služby Active Directory):

   * Ověří, jestli je povolená funkce synchronizace hodnot hash hesel.
   
   * Vyhledá události prezenčního signálu synchronizace hodnot hash hesel v protokolech událostí aplikace systému Windows.

   * Pro každou doménu služby Active Directory v rámci místního konektoru služby Active Directory:

      * Ověří, jestli je doména dosažitelná z Azure AD Connectho serveru.

      * Ověří, jestli účty Active Directory Domain Services (služba AD DS) používané místním konektorem služby Active Directory mají správné uživatelské jméno, heslo a oprávnění, která se vyžadují pro synchronizaci hodnot hash hesel.

Následující diagram znázorňuje výsledky rutiny pro jednu doménu místní topologie služby Active Directory:

![Výstup diagnostiky pro synchronizaci hodnot hash hesel](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

Zbytek této části popisuje konkrétní výsledky, které jsou vráceny úlohou a související problémy.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>funkce synchronizace hodnot hash hesel není povolená.

Pokud jste nepovolili synchronizaci hodnot hash hesel pomocí Průvodce Azure AD Connect, vrátí se následující chyba:

![synchronizace hodnot hash hesel není povolená.](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Server Azure AD Connect je v pracovním režimu.

Pokud je server Azure AD Connect v pracovním režimu, synchronizace hodnot hash hesel je dočasně zakázaná a vrátí se následující chyba:

![Server Azure AD Connect je v pracovním režimu.](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Události prezenčního signálu synchronizace hodnoty hash hesla

Každý místní konektor služby Active Directory má svůj vlastní kanál synchronizace hodnot hash hesel. Když se vytvoří kanál synchronizace hodnot hash hesel a neexistují žádné změny hesla, které by se měly synchronizovat, vygeneruje se událost prezenčního signálu (ID události 654) každých 30 minut v protokolu událostí aplikace systému Windows. Pro každý místní konektor služby Active Directory vyhledá rutiny odpovídající události prezenčního signálu za poslední tři hodiny. Pokud se nenajde žádná událost prezenčního signálu, vrátí se následující chyba:

![Žádná událost synchronizace hodnot hash hesel – srdcový ráz](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Účet služba AD DS nemá správná oprávnění.

Pokud účet služba AD DS, který používá místní konektor služby Active Directory k synchronizaci hodnot hash hesel, nemá příslušná oprávnění, vrátí se následující chyba:

![Snímek obrazovky zobrazující chybu, která se vrátí, když služba AD DS účet obsahuje nesprávné uživatelské jméno nebo heslo.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nesprávné uživatelské jméno nebo heslo účtu služba AD DS

Pokud účet služba AD DS, který používá místní konektor služby Active Directory k synchronizaci hodnot hash hesel, má nesprávné uživatelské jméno nebo heslo, vrátí se následující chyba:

![Nesprávné pověření](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Jeden objekt nesynchronizuje hesla: řešení potíží pomocí úlohy odstraňování potíží

Pomocí úlohy řešení potíží můžete určit, proč jeden objekt nesynchronizuje hesla.

> [!NOTE]
> Úloha řešení potíží je k dispozici pouze pro Azure AD Connect verze 1.1.614.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spuštění diagnostické rutiny

Řešení potíží s určitým objektem uživatele:

1. Na serveru Azure AD Connect otevřete novou relaci prostředí Windows PowerShell s možností **Spustit jako správce** .

2. Spusťte `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted` .

3. Spusťte Průvodce Azure AD Connect.

4. Přejděte na stránku **další úlohy** , vyberte možnost **řešení potíží** a klikněte na tlačítko **Další**.

5. Na stránce Poradce při potížích klikněte na **Spustit** a spusťte v PowerShellu nabídku pro odstraňování potíží.

6. V hlavní nabídce vyberte **řešit potíže se synchronizací hodnot hash hesel**.

7. V podnabídce vyberte možnost **heslo není synchronizováno pro konkrétní uživatelský účet**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledků úlohy odstraňování potíží

Úloha řešení potíží provádí následující kontroly:

* Kontroluje stav objektu Active Directory v prostoru konektoru služby Active Directory, úložišti metaverse a v prostoru konektoru služby Azure AD.

* Ověřuje, že existují synchronizační pravidla se zapnutou synchronizací hodnot hash hesel a že se aplikuje na objekt služby Active Directory.

* Pokusí se načíst a zobrazit výsledky posledního pokusu o synchronizaci hesla pro daný objekt.

Následující diagram ilustruje výsledky rutiny při řešení potíží se synchronizací hodnot hash hesel pro jeden objekt:

![Výstup diagnostiky pro synchronizaci hodnot hash hesel – jeden objekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

Zbytek této části popisuje konkrétní výsledky vrácené rutinou a odpovídající problémy.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Objekt služby Active Directory není exportován do služby Azure AD.

synchronizace hodnot hash hesel pro tento místní účet Active Directory se nezdařila, protože v tenantovi služby Azure AD není žádný odpovídající objekt. Vrátí se následující chyba:

![Chybí objekt Azure AD.](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Uživatel má dočasné heslo.

V současné době Azure AD Connect nepodporuje synchronizaci dočasných hesel se službou Azure AD. Heslo je považováno za dočasné, pokud je možnost **změnit heslo při příštím přihlášení** nastavena u místního uživatele služby Active Directory. Vrátí se následující chyba:

![Dočasné heslo není exportováno.](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Výsledky posledního pokusu o synchronizaci hesla nejsou k dispozici.

Ve výchozím nastavení Azure AD Connect ukládá výsledky pokusů o synchronizaci hodnot hash hesel po dobu sedmi dnů. Pokud pro vybraný objekt služby Active Directory nejsou k dispozici žádné výsledky, vrátí se následující upozornění:

![Výstup diagnostiky pro jeden objekt – žádná historie synchronizace hesel](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Žádná hesla nejsou synchronizovaná: řešení potíží pomocí diagnostické rutiny

Pomocí `Invoke-ADSyncDiagnostics` rutiny můžete zjistit, proč nejsou synchronizována žádná hesla.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Rutina je k dispozici pouze pro Azure AD Connect verze 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spuštění diagnostické rutiny

Řešení potíží, při kterých se nesynchronizují žádná hesla:

1. Na serveru Azure AD Connect otevřete novou relaci prostředí Windows PowerShell s možností **Spustit jako správce** .

2. Spusťte `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted` .

3. Spusťte `Import-Module ADSyncDiagnostics`.

4. Spusťte `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Jeden objekt nesynchronizuje hesla: řešení potíží pomocí diagnostické rutiny

Pomocí `Invoke-ADSyncDiagnostics` rutiny můžete určit, proč jeden objekt nesynchronizuje hesla.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Rutina je k dispozici pouze pro Azure AD Connect verze 1.1.524.0 nebo novější.

### <a name="run-the-diagnostics-cmdlet"></a>Spuštění diagnostické rutiny

Řešení potíží, které nesynchronizují žádná hesla pro uživatele:

1. Na serveru Azure AD Connect otevřete novou relaci prostředí Windows PowerShell s možností **Spustit jako správce** .

2. Spusťte `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted` .

3. Spusťte `Import-Module ADSyncDiagnostics`.

4. Spusťte následující rutinu:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Příklad:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Žádná hesla nejsou synchronizovaná: ruční kroky při řešení potíží

Pokud chcete zjistit, proč nejsou synchronizovaná žádná hesla, postupujte podle těchto kroků:

1. Je server Connect v [pracovním režimu](how-to-connect-sync-staging-server.md)? Server v pracovním režimu nesynchronizuje žádná hesla.

2. Spusťte skript v části [získání stavu nastavení synchronizace hesel](#get-the-status-of-password-sync-settings) . Poskytuje přehled o konfiguraci synchronizace hesel.  

    ![Výstup skriptu PowerShellu z nastavení synchronizace hesel](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Pokud tato funkce není ve službě Azure AD povolená nebo pokud není stav synchronizace kanálu povolený, spusťte Průvodce připojit instalaci. Vyberte možnost **přizpůsobit možnosti synchronizace** a zrušte výběr možnosti synchronizace hesel. Tato změna dočasně zakáže funkci. Pak spusťte průvodce znovu a znovu povolte synchronizaci hesel. Spusťte skript znovu a ověřte, zda je konfigurace správná.

4. Vyhledejte chyby v protokolu událostí. Vyhledejte následující události, což znamená problém:
    * Zdroj: "synchronizace adresáře" ID: 0, 611, 652, 655 Pokud vidíte tyto události, máte problém s připojením. Zpráva protokolu událostí obsahuje informace o doménové struktuře, ve kterých došlo k problému. Další informace najdete v tématu [problém s připojením](#connectivity problem).

5. Pokud se nezobrazí žádný prezenční signál nebo pokud nic jiného nezpracováváte, spusťte [aktivaci úplné synchronizace všech hesel](#trigger-a-full-sync-of-all-passwords). Spusťte skript jenom jednou.

6. Podívejte se na téma Poradce při potížích s jedním objektem, který nesynchronizuje hesla.

### <a name="connectivity-problems"></a>Problémy s připojením

Máte připojení ke službě Azure AD?

Má účet potřebná oprávnění ke čtení hodnot hash hesel ve všech doménách? Pokud jste nainstalovali připojení pomocí expresního nastavení, měla by být tato oprávnění správná. 

Pokud jste použili vlastní instalaci, nastavte oprávnění ručně pomocí následujícího postupu:
    
1. Pokud chcete najít účet, který používá konektor služby Active Directory, spusťte **Synchronization Service Manager**. 
 
2. Přejít na **konektory** a vyhledat místní doménovou strukturu služby Active Directory, kterou řešíte. 
 
3. Vyberte spojnici a klikněte na tlačítko **vlastnosti**. 
 
4. Přejít na **připojení k doménové struktuře služby Active Directory**.  
    
    ![Účet používaný konektorem služby Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Poznamenejte si uživatelské jméno a doménu, ve které se účet nachází.
    
5. Spusťte modul **Uživatelé a počítače služby Active Directory** a ověřte, že účet, který jste dříve našli, má v kořenu všech domén v doménové struktuře nastavená oprávnění dodržovat:
    * Replikovat změny adresáře
    * Replikovat všechny změny adresáře

6. Jsou řadiče domény dostupné pomocí Azure AD Connect? Pokud se server Connect nemůže připojit ke všem řadičům domény, nakonfigurujte **pouze upřednostňovaný řadič domény**.  
    
    ![Řadič domény používaný konektorem služby Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Vraťte se na **Synchronization Service Manager** a **nakonfigurujte oddíl adresáře**. 
 
8. V části **Vybrat oddíly adresáře** vyberte svoji doménu, zaškrtněte políčko **použít pouze upřednostňované řadiče domény** a pak klikněte na **Konfigurovat**. 

9. V seznamu zadejte řadiče domény, které se mají připojit, aby je bylo nutné použít ke synchronizaci hesel. Stejný seznam se používá i pro import a export. Proveďte tyto kroky pro všechny domény.

> [!NOTE]
> Chcete-li tyto změny použít, restartujte službu **Microsoft Azure AD Sync** (AdSync).

10. Pokud skript ukáže, že není k dispozici žádný prezenční signál, spusťte skript v [aktivační události Úplná synchronizace všech hesel](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Jeden objekt nesynchronizuje hesla: ruční kroky při odstraňování potíží

Můžete snadno řešit potíže se synchronizací hodnot hash hesel tak, že zkontrolujete stav objektu.

1. V modulu **Uživatelé a počítače služby Active Directory** vyhledejte uživatele a ověřte, zda je zaškrtnuto políčko **při dalším přihlášení musí uživatel změnit heslo** .  

    ![Produktivní hesla služby Active Directory](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Pokud je zaškrtnuté políčko, požádejte uživatele, aby se přihlásil a změnil heslo. Dočasná hesla nejsou synchronizovaná se službou Azure AD.

2. Pokud heslo vypadá ve službě Active Directory správně, postupujte podle uživatele v synchronizačním modulu. Když použijete uživatele z místní služby Active Directory do služby Azure AD, můžete zjistit, jestli je v objektu popisná chyba.

    a. Spusťte [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Klikněte na **konektory**.

    c. Vyberte **konektor služby Active Directory** , kde se nachází uživatel.

    d. Vyberte možnost **Hledat místo v konektoru**.

    e. V poli **obor** vyberte **rozlišující název nebo kotva** a pak zadejte úplný rozlišující název uživatele, kterého se chystáte řešit.

    ![Hledat uživatele v prostoru konektoru s rozlišujícím názvem](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Najděte hledaného uživatele a kliknutím na **vlastnosti** zobrazíte všechny atributy. Pokud uživatel není ve výsledku hledání, ověřte [pravidla filtrování](how-to-connect-sync-configure-filtering.md) a ujistěte se, že jste spustili příkaz [použít a ověřit změny](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) pro uživatele, který se zobrazí v okně připojit.

    například Pokud chcete zobrazit podrobnosti o synchronizaci hesel pro daný objekt za minulý týden, klikněte na **protokol**.  

    ![Podrobnosti protokolu objektů](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Pokud je protokol objektu prázdný, Azure AD Connect nemohl přečíst hodnotu hash hesla ze služby Active Directory. Pokračujte v řešení potíží s chybami připojení. Pokud se zobrazí jakákoli jiná hodnota než **úspěch**, přečtěte si tabulku v [protokolu synchronizace hesel](#password-sync-log).

    h. Vyberte kartu **čára** a ujistěte se, že nejméně jedno pravidlo synchronizace ve sloupci **PasswordSync** má **hodnotu true**. Ve výchozí konfiguraci se název synchronizačního pravidla nachází **v rámci služby AD-User AccountEnabled**.  

    ![Informace o řádku uživatele](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Kliknutím na **vlastnosti objektu úložiště metaverse** zobrazte seznam atributů uživatele.  

    ![Snímek obrazovky, který zobrazuje seznam atributů uživatele pro vlastnosti objektu Metaverse.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Ověřte, že není přítomen žádný atribut **cloudFiltered** . Ujistěte se, že atributy domény (domainFQDN a domainNetBios) mají očekávané hodnoty.

    j. Klikněte na kartu **konektory** . Ujistěte se, že se zobrazují konektory pro místní službu Active Directory a Azure AD.

    ![Informace v úložišti Metaverse](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Vyberte řádek, který představuje Azure AD, klikněte na **vlastnosti** a potom klikněte na kartu vydaná **čára** . Objekt prostoru konektoru by měl mít odchozí pravidlo, které je ve sloupci **PasswordSync** nastaveno na **hodnotu true**. Ve výchozí konfiguraci je název synchronizačního pravidla **pro AAD – připojení uživatele**.  

    ![Dialogové okno Vlastnosti objektu prostoru konektoru](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Protokol synchronizace hesel

Sloupec status (stav) může obsahovat následující hodnoty:

| Status | Popis |
| --- | --- |
| Success |Heslo bylo úspěšně synchronizováno. |
| FilteredByTarget |Heslo je nastavené na hodnotu **uživatel musí změnit heslo při příštím přihlášení**. Heslo nebylo synchronizováno. |
| NoTargetConnection |V úložišti metaverse nebo v prostoru konektoru služby Azure AD není žádný objekt. |
| SourceConnectorNotPresent |V prostoru konektoru služby Active Directory nebyl nalezen žádný objekt. |
| TargetNotExportedToDirectory |Objekt v prostoru konektoru služby Azure AD ještě není exportovaný. |
| MigratedCheckDetailsForMoreInfo |Záznam v protokolu byl vytvořen před sestavením 1.0.9125.0 a je zobrazen ve starším stavu. |
| Chybová |Služba vrátila neznámou chybu. |
| Neznámý |Při pokusu o zpracování dávky hodnot hash hesel došlo k chybě.  |
| MissingAttribute |Konkrétní atributy (například hash protokolu Kerberos) vyžadované Azure AD Domain Services nejsou k dispozici. |
| RetryRequestedByTarget |Konkrétní atributy (například hash protokolu Kerberos) vyžadované Azure AD Domain Services nebyly dříve k dispozici. Pokus o opětovnou synchronizaci hodnoty hash hesla uživatele je proveden. |

## <a name="scripts-to-help-troubleshooting"></a>Skripty, které vám pomůžou při řešení potíží

### <a name="get-the-status-of-password-sync-settings"></a>Získat stav nastavení synchronizace hesel

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Aktivovat úplnou synchronizaci všech hesel

> [!NOTE]
> Spusťte tento skript jenom jednou. Pokud je potřebujete spustit více než jednou, problém je něco jiného. Pokud chcete tento problém vyřešit, obraťte se na podporu Microsoftu.

Úplnou synchronizaci všech hesel můžete aktivovat pomocí následujícího skriptu:

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

* [Implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect synchronizace: přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
