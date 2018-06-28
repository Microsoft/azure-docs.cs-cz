---
title: 'Synchronizace Azure AD Connect: provozní úlohy a důležité informace | Microsoft Docs'
description: Toto téma popisuje provozní úlohy synchronizace Azure AD Connect a postup přípravy operační této součásti.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 363b76c3a28b15bc612a34531ceb162b76621672
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050460"
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Synchronizace Azure AD Connect: provozní úlohy a zvážení
Cílem tohoto tématu je k popisu provozní úlohy pro synchronizaci Azure AD Connect.

## <a name="staging-mode"></a>Pracovní režim
Pracovní režim lze použít pro několik scénářů, včetně:

* Vysoká dostupnost
* Testování a nasazení nové změny konfigurace.
* Zavedení nového serveru a vyřadit z provozu starý.

S server v pracovní režimu můžete provést změny v konfiguraci a zobrazit náhled změn, před provedením server aktivní. Také umožňuje spustit úplný import a úplnou synchronizaci za účelem ověření, že všechny změny budou před provedením změn do vašeho provozního prostředí.

Během instalace, můžete vybrat serveru, aby se v **pracovním režimu**. Tato akce aktivuje serveru pro import a synchronizaci, ale nespustí žádné export. Server v pracovní režimu není spuštěna synchronizace hesel nebo zpětný zápis hesla, i v případě, že jste vybrali tyto funkce během instalace. Při zakázání pracovní režim serveru spustí, export, umožňuje synchronizaci hesel a umožňuje zpětný zápis hesla.

> [!NOTE]
> Předpokládejme, že máte Azure AD Connect s povolenou funkcí synchronizaci hodnoty Hash hesla. Když povolíte pracovní režim, zastaví serveru, který synchronizace hesel se změní z místní AD. Pokud zakážete pracovní režim, server obnoví synchronizaci hesla změny od poslední místa. Pokud server zůstane v pracovním režimu pro delší dobu, může trvat nějakou dobu serveru synchronizovat všechny změny hesla, které měl došlo k chybě během časového období.
>
>

Můžete vynutit exportu pomocí synchronization service manager.

Server v pracovní režimu i nadále přijímat změny ze služby Active Directory a Azure AD. Má vždy kopii poslední změny a může trvat velmi rychlé přes odpovědnosti jiného serveru. Pokud provedete změny konfigurace primárního serveru, je vaší povinností provést stejné změny na server v pracovním režimu.

Pro těch, které můžete s znalostmi starší technologie synchronizace se liší pracovní režim, protože má svou vlastní databázi SQL server. Tato architektura umožňuje pracovní režim server umístěný v jiném datovém centru.

### <a name="verify-the-configuration-of-a-server"></a>Ověření konfigurace serveru
Chcete-li použít tuto metodu, postupujte takto:

1. [Příprava](#prepare)
2. [Konfigurace](#configuration)
3. [Import a synchronizaci](#import-and-synchronize)
4. [Ověření](#verify)
5. [Přepínač aktivní server](#switch-active-server)

#### <a name="prepare"></a>Příprava
1. Nainstalujte Azure AD Connect, vyberte **pracovním režimu**a zrušte výběr **spustit synchronizaci** na poslední stránce Průvodce instalací. Tento režim umožňuje spustit synchronizační modul ručně.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Přihlašovací vypnout nebo přihlášení do a z nabídky vyberte možnost Spustit **synchronizační služba**.

#### <a name="configuration"></a>Konfigurace
Pokud jste provedli vlastní změny na primární server a chcete porovnání konfigurace se serverem pracovní, použijte [nástroje Azure AD Connect konfigurace dokumentace](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Import a synchronizaci
1. Vyberte **konektory**a vyberte první konektor s typem **Active Directory Domain Services**. Klikněte na tlačítko **spustit**, vyberte **úplný import**, a **OK**. Proveďte tyto kroky pro všechny konektory tohoto typu.
2. Vyberte konektor s typem **Azure Active Directory (Microsoft)**. Klikněte na tlačítko **spustit**, vyberte **úplný import**, a **OK**.
3. Ujistěte se, že je stále vybraná karta konektory. Pro každý konektor s typem **Active Directory Domain Services**, klikněte na tlačítko **spustit**, vyberte **rozdílová synchronizace**, a **OK**.
4. Vyberte konektor s typem **Azure Active Directory (Microsoft)**. Klikněte na tlačítko **spustit**, vyberte **rozdílová synchronizace**, a **OK**.

Nyní máte připravené export změny do Azure AD a místní AD (Pokud používáte hybridní nasazení systému Exchange). Následující kroky umožňují kontrolovat, co se má změnit před zahájením ve skutečnosti exportu do adresáře.

#### <a name="verify"></a>Ověřit
1. Spusťte příkazový řádek a přejděte na `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Spustit: `csexport "Name of Connector" %temp%\export.xml /f:x` název konektoru najdete v synchronizační služba. Je podobná "contoso.com – AAD" název pro Azure AD.
3. Spustit: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` soubor v adresáři % temp % s názvem export.csv, který může být prověřen v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
4. Proveďte potřebné změny na data nebo konfigurace a spusťte tyto kroky opakujte (Import a synchronizaci a ověřte, zda) dokud změny, které mají být exportovány se očekává.

**Principy soubor export.csv** většinu souboru je není potřeba vysvětlovat. Některé zkratky pochopit obsahu:
* OMODT – Změna typu objektu. Určuje, zda je operace na úrovni objektu přidat, aktualizace nebo odstranění.
* AMODT – typ atributu úpravy. Určuje, zda je operace na úrovni atribut přidat, aktualizace nebo odstranění.

**Načtení běžné identifikátorů** soubor export.csv obsahuje všechny změny, které mají být exportovány. Každý řádek odpovídá na změnu pro objekt v prostoru konektoru a objekt je identifikována atribut rozlišující název. Atribut rozlišující název je jedinečný identifikátor přidružených k objektu v prostoru konektoru. Pokud máte mnoho řádků nebo změny v export.csv analyzovat, může být obtížné pro můžete zjistit, které objekty jsou změny pro založená na samostatné rozlišující název atributu. Pokud chcete zjednodušit proces analyzování změny, použijte csanalyzer.ps1 skript prostředí PowerShell. Skript načte běžné identifikátory objektů (například displayName, userPrincipalName). Použití skriptu:
1. Zkopírujte skript prostředí PowerShell z části [CSAnalyzer](#appendix-csanalyzer) do souboru s názvem `csanalyzer.ps1`.
2. Otevřete okno prostředí PowerShell a přejděte do složky, kde můžete vytvořit skript prostředí PowerShell.
3. Spustit: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Nyní máte soubor s názvem **processedusers1.csv** , může být prověřen v aplikaci Microsoft Excel. Všimněte si, že soubor poskytuje mapování z rozlišující název atributu pro běžné identifikátory (například displayName a userPrincipalName). Aktuálně neobsahoval skutečné atribut změny, které mají být exportovány.

#### <a name="switch-active-server"></a>Přepínač aktivní server
1. Na serveru aktuálně aktivní vypnout server (DirSync nebo FIM nebo Azure AD Sync), není export do služby Azure AD nebo ho nastavit v pracovním režimu (Azure AD Connect).
2. Spusťte Průvodce instalací na serveru v **pracovním režimu** a zakázat **pracovním režimu**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Zotavení po havárii
Část návrhu implementace je naplánovat co dělat v případě, že dojde k havárii, kde ztratíte synchronizační server. Existují různé modely, které používá a které z nich používat závisí na několika faktorech včetně:

* Co je tolerance pro nebude možné provádět změny objektů ve službě Azure AD během výpadek?
* Pokud používáte synchronizace hesel, uživatelé přijímat mají používat staré heslo ve službě Azure AD, v případě, že se ho změnit na místě?
* Máte závislost na v reálném čase operace, jako je zpětný zápis hesla?

V závislosti na odpovědi na tyto otázky a zásad vaší organizace může být implementováno jednu z následujících strategií:

* Znovu sestavte v případě potřeby.
* K výměně za chodu pohotovostní server, označuje jako **pracovním režimu**.
* Virtuální počítače použijte.

Pokud použijete integrovanou databází SQL Express, tak také byste měli revidovat [vysoké dostupnosti SQL](#sql-high-availability) části.

### <a name="rebuild-when-needed"></a>Opětovné sestavení v případě potřeby
Vhodným strategie je naplánovat opětovném sestavení serveru v případě potřeby. Obvykle instalace synchronizačního modulu a proveďte počáteční import a synchronizace může být dokončena v rámci několik hodin. Pokud není k dispozici náhradní server, je možné k hostování synchronizační modul dočasně použít řadič domény.

Synchronizační modul server nejsou uložené žádné stav o objektech tak databázi můžete znovu sestavit z dat v Active Directory a Azure AD. **SourceAnchor** atribut se používá pro připojení objekty z místní a cloudem. Pokud jste znovu sestavit server pomocí existující objekty místní a cloudu, pak synchronizační modul odpovídá tyto objekty společně znovu na přeinstalování. Jsou věci, které potřebujete k dokumentu a uložit změny konfigurace provedené na serveru, například pravidla filtrování a synchronizace. Před zahájením synchronizace ho znovu použít tyto vlastní konfigurace.

### <a name="have-a-spare-standby-server---staging-mode"></a>K výměně za chodu pohotovostní server - pracovní režim
Pokud máte složitější prostředí, pak má jeden nebo více pohotovostní serverů doporučujeme. Během instalace, můžete povolit serveru, aby se v **pracovním režimu**.

Další informace najdete v tématu [pracovním režimu](#staging-mode).

### <a name="use-virtual-machines"></a>Použijte virtuální počítače
Metoda běžných a podporovanou je ke spuštění synchronizační modul ve virtuálním počítači. V případě, že hostitel má problém, můžete k migraci bitová kopie se synchronizační modul server na jiný server.

### <a name="sql-high-availability"></a>Vysoká dostupnost SQL
Pokud nepoužíváte SQL Server Express, která se dodává s Azure AD Connect, pak vysoká dostupnost pro SQL Server měli byste také zvážit. Řešení vysoké dostupnosti podporovaná zahrnují clusteringu SQL a AOA (skupiny dostupnosti Always On). Nepodporované řešení zahrnují zrcadlení.

K Azure AD Connect v verze 1.1.524.0 byla přidána podpora pro SQL AOA. Před instalací Azure AD Connect je nutné povolit SQL AOA. Při instalaci Azure AD Connect zjistí, zda zadaná instance SQL je povoleno pro SQL AOA, nebo ne. Pokud je povoleno SQL AOA, Azure AD Connect další hodnoty, pokud SQL AOA je konfigurovaná pro použití replikace synchronní nebo asynchronní replikaci. Při nastavování naslouchacího procesu skupiny dostupnosti, se doporučuje nastavit vlastnost RegisterAllProvidersIP na 0. Je to proto, že Azure AD Connect aktuálně používá SQL Native Client k připojení k SQL a nativní klient SQL nepodporuje použití vlastnosti MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Příloha CSAnalyzer
Najdete v části [ověřte](#verify) o tom, jak pomocí tohoto skriptu.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Další postup
**Témata s přehledem**  

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)  
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)  
