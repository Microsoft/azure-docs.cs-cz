---
title: 'Azure AD Connect synchronizace: provozní úlohy a požadavky | Microsoft Docs'
description: Toto téma popisuje provozní úlohy pro Azure AD Connect synchronizaci a postup přípravy na provoz této součásti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48584fa4042cf53fa1084e519dca0e64f530ca59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090121"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: přípravný Server a zotavení po havárii
U serveru v pracovním režimu můžete změnit konfiguraci a zobrazit náhled změn před tím, než provedete server aktivní. Umožňuje taky spustit úplnou synchronizaci a úplnou synchronizaci a ověřit, jestli jsou všechny změny očekávané, než provedete tyto změny v produkčním prostředí.

## <a name="staging-mode"></a>Pracovní režim
Pracovní režim lze použít pro několik scénářů, včetně:

* Vysoká dostupnost
* Otestujte a nasaďte nové změny konfigurace.
* Zaveďte nový server a vyřaďte ho do provozu staré.

Během instalace můžete vybrat server, který bude v **pracovním režimu**. Tato akce zpřístupní Server jako aktivní pro import a synchronizaci, ale nespustí žádné exporty. Server v pracovním režimu nespouští synchronizaci hesla ani zpětný zápis hesla, i když jste tyto funkce při instalaci vybrali. Když zakážete pracovní režim, Server spustí export, povolí synchronizaci hesla a povolí zpětný zápis hesla.

> [!NOTE]
> Předpokládejme, že máte Azure AD Connect s povolenou funkcí synchronizace hodnot hash hesel. Když povolíte pracovní režim, server přestane synchronizovat změny hesel z místní služby AD. Když zakážete pracovní režim, server obnoví synchronizaci změn hesel z místa, kde naposledy skončil. Pokud je server v pracovním režimu po delší dobu, může chvíli trvat, než server synchronizuje všechny změny hesel, ke kterým došlo během tohoto časového období.
>
>

Můžete přesto vynutit export pomocí synchronizace Service Manageru.

Server v pracovním režimu nadále přijímá změny ze služby Active Directory a Azure AD a může v případě selhání rychle převzít zodpovědnosti jiného serveru. Pokud provedete změny konfigurace primárního serveru, je vaše zodpovědností udělat stejné změny serveru v pracovním režimu.

Pro vás se znalostí starších technologií synchronizace se pracovní režim liší od chvíle, kdy server má svou vlastní databázi SQL. Tato architektura umožňuje, aby se server pracovního režimu nacházel v jiném datovém centru.

### <a name="verify-the-configuration-of-a-server"></a>Ověření konfigurace serveru
Chcete-li použít tuto metodu, postupujte podle následujících kroků:

1. [Příprava](#prepare)
2. [Konfigurace](#configuration)
3. [Import a synchronizace](#import-and-synchronize)
4. [Ověřit](#verify)
5. [Přepnout aktivní server](#switch-active-server)

#### <a name="prepare"></a>Příprava
1. Nainstalujte Azure AD Connect, vyberte **pracovní režim** a zrušte výběr **Spustit synchronizaci** na poslední stránce Průvodce instalací. Tento režim umožňuje spustit modul synchronizace ručně.
   ![Snímek obrazovky se stránkou připravenou ke konfiguraci najdete v dialogovém okně Azure AD Connect.](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Odhlaste se nebo se přihlaste a v nabídce Start vyberte **synchronizační službu**.

#### <a name="configuration"></a>Konfigurace
Pokud jste provedli vlastní změny primárního serveru a chcete porovnat konfiguraci s pracovním serverem, použijte [Azure AD Connect Configuration Documentation](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Import a synchronizace
1. Vyberte **konektory** a vyberte první konektor s typem **Active Directory Domain Services**. Klikněte na **Spustit**, vyberte **úplný import** a pak **OK**. Proveďte tyto kroky u všech konektorů tohoto typu.
2. Vyberte konektor typu **Azure Active Directory (Microsoft)**. Klikněte na **Spustit**, vyberte **úplný import** a pak **OK**.
3. Ujistěte se, že jsou konektory karet stále vybrané. U každého konektoru s typem **Active Directory Domain Services** klikněte na **Spustit**, vyberte **rozdílovou synchronizaci** a pak na **OK**.
4. Vyberte konektor typu **Azure Active Directory (Microsoft)**. Klikněte na **Spustit**, vyberte **rozdílovou synchronizaci** a pak na **OK**.

Teď máte připravené změny v exportu do Azure AD a místní služby AD (Pokud používáte hybridní nasazení Exchange). Následující kroky vám umožní zkontrolovat, co se chystá změnit, než začnete s exportem do adresářů.

#### <a name="verify"></a>Ověření
1. Spuštění příkazového řádku a přechod na `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Run (spustit): `csexport "Name of Connector" %temp%\export.xml /f:x` název konektoru najdete ve službě synchronizace. Má název podobný řetězci "contoso.com – Azure AD" pro Azure AD.
3. Spustit: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` máte soubor v% Temp% s názvem export.csv, který lze prozkoumat v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
4. Proveďte nezbytné změny dat nebo konfigurace a spusťte tyto kroky znovu (import a synchronizace a ověření), dokud se neočekávají změny, které se chystá exportovat.

**Princip souboru export.csv** Většina souboru je zřejmá. Některé zkratky pro pochopení obsahu:
* OMODT – typ změny objektu. Určuje, zda je operace na úrovni objektu Add, Update nebo DELETE.
* AMODT – typ změny atributu Určuje, zda je operace na úrovni atributu přidat, aktualizovat nebo odstranit.

**Načíst společné identifikátory** Soubor export.csv obsahuje všechny změny, které mají být exportovány. Každý řádek odpovídá změně objektu v prostoru konektoru a objekt je identifikován atributem DN. Atribut DN je jedinečný identifikátor přiřazený objektu v prostoru konektoru. Pokud máte v export.csv k analýze mnoho řádků nebo změn, může být obtížné zjistit, které objekty se tyto změny týkají, na základě samotného atributu DN. K zjednodušení procesu analýzy změn použijte skript prostředí PowerShell csanalyzer.ps1. Skript načte běžné identifikátory (například DisplayName, userPrincipalName) objektů. Postup použití skriptu:
1. Zkopírujte skript PowerShellu z části [CSAnalyzer](#appendix-csanalyzer) do souboru s názvem `csanalyzer.ps1` .
2. Otevřete okno PowerShellu a přejděte do složky, ve které jste vytvořili PowerShellový skript.
3. Spusťte `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Nyní máte soubor s názvem **processedusers1.csv** , který lze prozkoumat v aplikaci Microsoft Excel. Všimněte si, že soubor poskytuje mapování z atributu DN na společné identifikátory (například DisplayName a userPrincipalName). V současné době nezahrnuje změny atributů, které mají být exportovány.

#### <a name="switch-active-server"></a>Přepnout aktivní server
1. Na aktuálně aktivním serveru buď vypněte Server (DirSync/FIM/Azure AD Sync), takže se neexportuje do služby Azure AD ani nenastaví v pracovním režimu (Azure AD Connect).
2. Spusťte Průvodce instalací na serveru v **pracovním režimu** a zakažte **pracovní režim**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Zotavení po havárii
Součástí návrhu implementace je naplánovat, co dělat v případě, že dojde k výpadku, kdy se synchronizační server ztratí. Existují různé modely, které je potřeba použít a které z nich je možné použít na několika faktorech, mezi které patří:

* Jaká je vaše tolerance, aby se během výpadku nemohly měnit objekty v Azure AD?
* Pokud používáte synchronizaci hesel, uživatelé přijmou, aby v případě změny v místním prostředí používali staré heslo ve službě Azure AD.
* Máte závislost na operacích v reálném čase, jako je třeba zpětný zápis hesla?

V závislosti na odpovědích na tyto otázky a zásady vaší organizace je možné implementovat jednu z následujících strategií:

* V případě potřeby znovu sestavte.
* Mít náhradní pohotovostní server, známý jako **pracovní režim**.
* Použijte virtuální počítače.

Pokud nepoužíváte integrovanou databázi SQL Express, měli byste si také projít část s [vysokou dostupností SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Znovu sestavit v případě potřeby
Životaschopná strategie je plánování serveru, který je v případě potřeby znovu sestaven. Obvykle je instalace synchronizačního modulu a provádění počátečního importu a synchronizace dokončena během několika hodin. Pokud není k dispozici náhradní server, je možné dočasně použít řadič domény k hostování synchronizačního modulu.

Server synchronizačního modulu neukládá žádný stav objektů, aby bylo možné databázi znovu vytvořit z dat ve službě Active Directory a v Azure AD. Atribut **sourceAnchor** se používá pro připojení k objektům z místního prostředí a cloudu. Pokud znovu sestavíte Server s existujícími objekty v místním prostředí i v cloudu, pak modul synchronizace tyto objekty porovnává znovu po přeinstalaci. K dokumentování a ukládání jsou potřeba změny v konfiguraci serveru, jako jsou například pravidla filtrování a synchronizace. Tyto vlastní konfigurace je nutné znovu použít před zahájením synchronizace.

### <a name="have-a-spare-standby-server---staging-mode"></a>Mít náhradní pohotovostní server – režim přípravy
Pokud máte složitější prostředí, doporučuje se mít jeden nebo více pohotovostních serverů. Během instalace můžete povolit, aby byl server v **pracovním režimu**.

Další informace najdete v tématu [pracovní režim](#staging-mode).

### <a name="use-virtual-machines"></a>Použití virtuálních počítačů
Běžnou a podporovanou metodou je spuštění synchronizačního modulu na virtuálním počítači. V případě, že hostitel má problém, image se serverem synchronizačního stroje se dá migrovat na jiný server.

### <a name="sql-high-availability"></a>Vysoká dostupnost SQL
Pokud nepoužíváte SQL Server Express, která se dodává s Azure AD Connect, měli byste zvážit také vysokou dostupnost pro SQL Server. Mezi podporovaná řešení s vysokou dostupností patří clustering SQL a AOA (skupiny dostupnosti Always On). Nepodporovaná řešení zahrnují zrcadlení.

Do Azure AD Connect ve verzi 1.1.524.0 se přidala podpora pro SQL AOA. Před instalací Azure AD Connect musíte povolit SQL AOA. Během instalace Azure AD Connect zjistí, jestli je zadaná instance SQL povolená pro SQL AOA nebo ne. Pokud je povolený SQL AOA, Azure AD Connect dál vyhodnotí, pokud je SQL AOA nakonfigurované na používání synchronní replikace nebo asynchronní replikace. Při nastavování naslouchacího procesu skupiny dostupnosti doporučujeme nastavit vlastnost RegisterAllProvidersIP na hodnotu 0. Důvodem je to, že Azure AD Connect aktuálně používá SQL Native Client k připojení k SQL a SQL Native Client nepodporuje použití vlastnosti MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Příloha CSAnalyzer
Informace o tom, jak tento skript použít, najdete [v části.](#verify)

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

## <a name="next-steps"></a>Další kroky
**Témata s přehledem**  

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)  
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)  
