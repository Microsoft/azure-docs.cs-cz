---
title: 'Synchronizace služby Azure AD Connect: Provozní úkoly a důležité informace | Dokumenty společnosti Microsoft'
description: Toto téma popisuje provozní úlohy pro synchronizaci Azure AD Connect a jak se připravit na provoz této součásti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc88640cdff4f716902a80bb149913b961d40ae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261018"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Pracovní server a zotavení po havárii
Server v pracovním režimu můžete před uzastavením serveru provést změny v konfiguraci a zobrazit náhled změn. Umožňuje také spustit úplný import a úplnou synchronizaci a ověřit, zda jsou před prováděním těchto změn do produkčního prostředí očekávány všechny změny.

## <a name="staging-mode"></a>Pracovní režim
Pracovní režim lze použít pro několik scénářů, včetně:

* Vysoká dostupnost
* Otestujte a nasaďte nové změny konfigurace.
* Zavést nový server a vyřadit z provozu staré.

Během instalace můžete vybrat server, který má být v **pracovním režimu**. Tato akce způsobí, že server bude aktivní pro import a synchronizaci, ale nespustí žádné exporty. Server v pracovním režimu nespouštějí synchronizaci hesel nebo zpětný zápis hesla, a to ani v případě, že jste tyto funkce během instalace vybrali. Když zakážete pracovní režim, server začne exportovat, povolí synchronizaci hesel a povolí zpětný zápis hesla.

> [!NOTE]
> Předpokládejme, že máte povolenou funkci synchronizace hash služby Azure AD Connect with Password Hash. Když povolíte pracovní režim, server přestane synchronizovat změny hesla z místní služby AD. Pokud zakážete pracovní režim, server obnoví synchronizaci změn hesla z místa, kde naposledy skončil. Pokud je server ponechán v pracovním režimu delší dobu, může chvíli trvat, než server synchronizuje všechny změny hesla, ke kterým došlo během časového období.
>
>

Export můžete vynutit pomocí správce synchronizačních služeb.

Server v pracovním režimu nadále přijímat změny ze služby Active Directory a Azure AD a můžete rychle převzít odpovědnosti jiného serveru v případě selhání. Pokud provedete změny konfigurace primárního serveru, je vaší odpovědností provést stejné změny na serveru v pracovním režimu.

Pro ty z vás, kteří mají znalosti o starších synchronizačních technologiích, je pracovní režim odlišný, protože server má vlastní databázi SQL. Tato architektura umožňuje server pracovního režimu, který má být umístěn v jiném datovém centru.

### <a name="verify-the-configuration-of-a-server"></a>Ověření konfigurace serveru
Chcete-li použít tuto metodu, postupujte takto:

1. [Příprava](#prepare)
2. [Konfigurace](#configuration)
3. [Import a synchronizace](#import-and-synchronize)
4. [Ověřit](#verify)
5. [Přepnutí aktivního serveru](#switch-active-server)

#### <a name="prepare"></a>Příprava
1. Nainstalujte Azure AD Connect, vyberte **pracovní režim**a zrušte výběr **spuštění synchronizace** na poslední stránce průvodce instalací. Tento režim umožňuje spustit synchronizační modul ručně.
   ![Připraveno konfigurace](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Odhlásit/přihlásit a v nabídce Start vyberte **možnost Synchronizační služba**.

#### <a name="configuration"></a>Konfigurace
Pokud jste provedli vlastní změny na primárním serveru a chcete porovnat konfiguraci s pracovní server, použijte [Azure AD Connect konfigurace documenter](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Import a synchronizace
1. Vyberte **konektory**a první konektor s typem **Služby Active Directory Domain Services**. Klepněte na **tlačítko Spustit**, vyberte **možnost Úplné importace**a **OK**. Proveďte tyto kroky pro všechny konektory tohoto typu.
2. Vyberte konektor s typem **Azure Active Directory (Microsoft)**. Klepněte na **tlačítko Spustit**, vyberte **možnost Úplné importace**a **OK**.
3. Zkontrolujte, zda je stále vybraná karta Konektory. U každého konektoru s typem **Služby Active Directory Domain Services**klepněte na tlačítko **Spustit**, vyberte **možnost Rozdílová synchronizace**a **OK**.
4. Vyberte konektor s typem **Azure Active Directory (Microsoft)**. Klepněte na tlačítko **Spustit**, vyberte **možnost Rozdílová synchronizace**a **OK**.

Nyní máte připravené změny exportu do Azure AD a místní služby AD (pokud používáte hybridní nasazení Exchange). Další kroky umožňují zkontrolovat, co se chystá změnit před skutečně spustit export do adresářů.

#### <a name="verify"></a>Ověřit
1. Spuštění výzvy cmd a přejděte na`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Spustit: `csexport "Name of Connector" %temp%\export.xml /f:x` Název konektoru lze nalézt v synchronizační službě. Má název podobný "contoso.com – AAD" pro Azure AD.
3. Spustit: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Máte soubor v %temp% s názvem export.csv, který lze zkontrolováno v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
4. Proveďte nezbytné změny dat nebo konfigurace a spusťte tyto kroky znovu (Import a synchronizace a ověření), dokud nebudou očekávány změny, které mají být exportovány.

**Principy souboru export.csv** Většina souboru je samozřejmá. Některé zkratky k pochopení obsahu:
* OMODT – typ modifikace objektu. Označuje, zda je operace na úrovni objektu Přidat, Aktualizovat nebo Odstranit.
* AMODT – typ modifikace atributu. Označuje, zda je operace na úrovni atributu Přidat, Aktualizovat nebo odstranit.

**Načtení běžných identifikátorů** Soubor export.csv obsahuje všechny změny, které mají být exportovány. Každý řádek odpovídá změně objektu v prostoru spojnice a objekt je identifikován atributem DN. Atribut DN je jedinečný identifikátor přiřazený objektu v prostoru spojnice. Pokud máte mnoho řádků nebo změn v export.csv analyzovat, může být obtížné zjistit, které objekty změny jsou pro na základě atributu DN sám. Chcete-li zjednodušit proces analýzy změn, použijte skript prostředí PowerShell csanalyzer.ps1. Skript načte běžné identifikátory (například displayName, userPrincipalName) objektů. Použití skriptu:
1. Zkopírujte skript prostředí PowerShell z oddílu `csanalyzer.ps1` [CSAnalyzer](#appendix-csanalyzer) do souboru s názvem .
2. Otevřete okno PowerShellu a přejděte do složky, do které jste vytvořili skript PowerShellu.
3. Spusťte `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Nyní máte soubor s názvem **zpracovanéusers1.csv,** které lze zkontrolovány v aplikaci Microsoft Excel. Všimněte si, že soubor poskytuje mapování z atributu DN na běžné identifikátory (například displayName a userPrincipalName). V současné době nezahrnuje skutečné změny atributů, které mají být exportovány.

#### <a name="switch-active-server"></a>Přepnutí aktivního serveru
1. Na aktuálně aktivním serveru buď vypněte server (DirSync/FIM/Azure AD Sync), aby se neexportoval do Azure AD, nebo ho nastavte v pracovním režimu (Azure AD Connect).
2. Spusťte průvodce instalací na serveru v **pracovním režimu** a zakažte **pracovní režim**.
   ![Připraveno konfigurace](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Zotavení po havárii
Součástí návrhu implementace je naplánovat, co dělat v případě, že dojde k havárii, kdy ztratíte synchronizační server. Existují různé modely, které chcete použít a který z nich použít, závisí na několika faktorech, včetně:

* Jaká je vaše tolerance pro není možné provádět změny objektů ve službě Azure AD během prostojů?
* Pokud používáte synchronizaci hesel, uživatelé akceptují, že musí používat staré heslo ve službě Azure AD v případě, že ho změní místně?
* Máte závislost na operacích v reálném čase, jako je například zpětný zápis hesla?

V závislosti na odpovědích na tyto otázky a zásady vaší organizace lze implementovat jednu z následujících strategií:

* V případě potřeby znovu sestavte.
* Mít náhradní pohotovostní server, známý jako **pracovní režim**.
* Používejte virtuální počítače.

Pokud nepoužíváte vestavěnou databázi SQL Express, měli byste také zkontrolovat část [Vysoká dostupnost SQL.](#sql-high-availability)

### <a name="rebuild-when-needed"></a>Znovu sestavit v případě potřeby
Životaschopnou strategií je naplánovat opětovné sestavení serveru v případě potřeby. Obvykle, instalace synchronizačního modulu a provést počáteční import a synchronizaci lze dokončit během několika hodin. Pokud není k dispozici náhradní server, je možné dočasně použít řadič domény k hostování synchronizačního modulu.

Server synchronizačního modulu neukládá žádný stav o objektech, takže databázi lze znovu sestavit z dat ve službě Active Directory a Azure AD. **SourceAnchor** atribut se používá k připojení k objektům z místní a cloud. Pokud znovu sestavujete server s existujícími objekty v místním prostředí a v cloudu, synchronizační modul tyto objekty při přeinstalaci opět spojí. Věci, které potřebujete dokumentovat a uložit, jsou změny konfigurace provedené na serveru, jako je například filtrování a synchronizace pravidel. Tyto vlastní konfigurace musí být znovu použity před zahájením synchronizace.

### <a name="have-a-spare-standby-server---staging-mode"></a>Mít náhradní pohotovostní server - pracovní režim
Pokud máte složitější prostředí, doporučujeme mít jeden nebo více pohotovostních serverů. Během instalace můžete povolit, aby byl server v **pracovním režimu**.

Další informace naleznete v [tématu pracovní režim](#staging-mode).

### <a name="use-virtual-machines"></a>Používání virtuálních počítačů
Běžnou a podporovanou metodou je spuštění synchronizačního modulu ve virtuálním počítači. V případě, že hostitel má problém, může být bitová kopie se serverem synchronizačního motoru migrována na jiný server.

### <a name="sql-high-availability"></a>Vysoká dostupnost SQL
Pokud nepoužíváte SQL Server Express, který je dodáván s Azure AD Connect, pak vysoká dostupnost pro SQL Server by také měly být považovány za. Podporovaná řešení s vysokou dostupností zahrnují clustering SQL a AOA (vždy zapnuté skupiny dostupnosti). Nepodporovaná řešení zahrnují zrcadlení.

Podpora sql AOA byla přidána do služby Azure AD Connect ve verzi 1.1.524.0. Před instalací služby Azure AD Connect je nutné povolit SQL AOA. Během instalace Azure AD Connect zjistí, zda je poskytnutá instance SQL povolená pro SQL AOA nebo ne. Pokud je povolena SQL AOA, Azure AD Connect dále zjistí, pokud SQL AOA je nakonfigurovaný pro použití synchronní replikace nebo asynchronní replikace. Při nastavování naslouchací proces skupiny dostupnosti se doporučuje nastavit vlastnost RegisterAllProvidersIP na 0. Důvodem je, že Azure AD Connect aktuálně používá nativního klienta SQL pro připojení k NAtivnímu klientovi SQL a SQL nepodporuje použití vlastnosti MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Dodatek CSAnalyzer
Podívejte se na [informace](#verify) o způsobu použití tohoto skriptu v části.

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
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
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

## <a name="next-steps"></a>Další kroky
**Přehledná témata**  

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)  
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)  
