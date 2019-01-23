---
title: 'Synchronizace Azure AD Connect: Provozní úlohy a důležité informace | Dokumentace Microsoftu'
description: Toto téma popisuje provozní úlohy synchronizace Azure AD Connect a postup přípravy k provozování této součásti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
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
ms.openlocfilehash: c4dc5ae107cc8babbd425edd6c5de428e130fc3a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467532"
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Synchronizace Azure AD Connect: Provozní úlohy a důležité informace
Cílem tohoto tématu je k popisu provozní úlohy pro synchronizaci Azure AD Connect.

## <a name="staging-mode"></a>Pracovní režim
Pracovní režim lze použít pro několik scénářů, včetně:

* Vysoká dostupnost
* Testujte a nasazujte nové změny konfigurace.
* Zavádí nový server a vyřadit z provozu starého.

Se serverem v pracovním režimu můžete provést změny v konfiguraci a zobrazit náhled změn před provedením server aktivní. Také umožňuje spustit úplný import a úplnou synchronizaci za účelem ověření, že všechny změny se očekává, že před provedením těchto změn do vašeho provozního prostředí.

Během instalace, můžete vybrat server v **pracovním režimu**. Tato akce aktivuje na server pro import a synchronizaci, ale nespustí se žádné exporty. Server v pracovní režimu neběží synchronizaci hesel nebo zpětný zápis hesla i v případě, že jste vybrali při instalaci těchto funkcí. Pokud zakážete pracovní režim, server začne export, umožňuje synchronizaci hesel a aktivovat zpětný zápis hesla.

> [!NOTE]
> Předpokládejme, že máte Azure AD Connect s povolenou funkcí synchronizace hodnot Hash hesel. Když povolíte pracovní režim, server zastavuje synchronizace hesel se změní z místní služby AD. Pokud zakážete pracovní režim, server pokračuje v synchronizaci změn hesel z tam, kde poslední skončila. Pokud na serveru je v pracovním režimu delší dobu, může trvat nějakou dobu serveru a synchronizuje všechny změny hesel, které došlo během časového období nastaly.
>
>

Export stále můžete vynutit pomocí synchronization service manager.

Server v pracovní režimu i nadále přijímat změny z Active Directory a Azure AD. Vždy má kopii nejnovější změny a zkuste velmi rychlé zpracování může přes odpovědnosti jiný server. Pokud provedete změny v konfiguraci primárního serveru, je vaší odpovědností, abyste provést stejné změny na server v pracovní režimu.

Pro ty z vás se znalostí starší verze technologií synchronizace se liší pracovní režim, protože má svou vlastní databázi SQL server. Tato architektura umožňuje pracovní režim serveru byly umístěné v jiném datovém centru.

### <a name="verify-the-configuration-of-a-server"></a>Ověření konfigurace serveru
Chcete-li použít tuto metodu, postupujte podle těchto kroků:

1. [Příprava](#prepare)
2. [Konfigurace](#configuration)
3. [Importovat a synchronizovat](#import-and-synchronize)
4. [Ověření](#verify)
5. [Přepnout aktivní server](#switch-active-server)

#### <a name="prepare"></a>Příprava
1. Instalace služby Azure AD Connect, vyberte **pracovním režimu**a zrušte výběr **spuštění synchronizace** na poslední stránce Průvodce instalací. Tento režim umožňuje uživateli spustit synchronizační modul ručně.
   ![ReadyToConfigure](./media/how-to-connect-sync-operations/readytoconfigure.png)
2. Znaménko vypnout/přihlašování do a z nabídky vyberte možnost zahájení **synchronizační služba**.

#### <a name="configuration"></a>Konfigurace
Pokud vlastní změny provedené na primární server a chcete porovnat konfigurace s testovacím serveru, použijte [dokumentace služby Azure AD Connect konfigurace](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importovat a synchronizovat
1. Vyberte **konektory**a vyberte první konektor s typem **Active Directory Domain Services**. Klikněte na tlačítko **spustit**vyberte **úplný import**, a **OK**. Tyto kroky proveďte pro všechny konektory tohoto typu.
2. Vyberte konektor s typem **Azure Active Directory (Microsoft)**. Klikněte na tlačítko **spustit**vyberte **úplný import**, a **OK**.
3. Ujistěte se, že je stále vybraná karta konektory. Pro každý konektor s typem **Active Directory Domain Services**, klikněte na tlačítko **spustit**vyberte **rozdílová synchronizace**, a **OK**.
4. Vyberte konektor s typem **Azure Active Directory (Microsoft)**. Klikněte na tlačítko **spustit**vyberte **rozdílová synchronizace**, a **OK**.

Nyní máte připravené změny do služby Azure AD a místní AD (Pokud používáte hybridní nasazení systému Exchange) export. Následující kroky umožňují zkontrolovat, co se chystá změna před zahájením skutečně export do adresáře.

#### <a name="verify"></a>Ověřit
1. Spusťte příkazový řádek a přejděte na `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Spusťte: `csexport "Name of Connector" %temp%\export.xml /f:x` Název konektoru můžete najít v synchronizační služba. Má název "contoso.com – AAD" podobně jako pro Azure AD.
3. Spusťte: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Když máte soubor ve složce % temp % s názvem export.csv, které se dají prozkoumat v aplikaci Microsoft Excel. Tento soubor obsahuje všechny změny, které mají být exportovány.
4. Proveďte potřebné změny data nebo konfigurace a projít tyto kroky opakujte (Import a synchronizaci a ověřte, zda) až se očekává, že změny, které mají být exportovány.

**Vysvětlení souboru export.csv** většina souboru je zřejmých. Některé zkratky obsahu:
* OMODT – změny typu objektu. Určuje, zda je operace na úrovni objektu přidání, aktualizace nebo odstranění.
* AMODT – typ atributu úpravy. Určuje, zda je operace na úrovni atribut přidání, aktualizace nebo odstranění.

**Načíst běžné identifikátory** export.csv soubor obsahuje všechny změny, které mají být exportovány. Každý řádek odpovídá změnu pro objekt v prostoru konektoru a objekt je identifikovaný atribut rozlišující název. Atribut rozlišující název je jedinečný identifikátor přiřazena objektu v prostoru konektoru. Pokud máte mnoho řádků/změn v export.csv k analýze, může být obtížné si můžete zjistit, které objekty, že změny jsou podle pouze rozlišující název atributu. Pro zjednodušení procesu analýzy změny pomocí csanalyzer.ps1 skript prostředí PowerShell. Tento skript načte běžné identifikátory (například displayName, userPrincipalName) objekty. Použití skriptu:
1. Zkopírujte skript prostředí PowerShell z části [CSAnalyzer](#appendix-csanalyzer) do souboru s názvem `csanalyzer.ps1`.
2. Otevřete okno Powershellu a přejděte do složky, ve které jste vytvořili skript prostředí PowerShell.
3. Spustit: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Teď máte soubor s názvem **processedusers1.csv** , která se dají prozkoumat v aplikaci Microsoft Excel. Všimněte si, že soubor poskytuje mapování z rozlišující název atributu pro běžné identifikátory (například zobrazovaný název a hodnota userPrincipalName). Aktuálně nezahrnuje změny skutečné atributů, které mají být exportovány.

#### <a name="switch-active-server"></a>Přepnout aktivní server
1. Na aktuálně aktivní server vypnout server (DirSync nebo FIM nebo Azure AD Sync), není export do služby Azure AD nebo ho nastavit v pracovním režimu (Azure AD Connect).
2. Spusťte Průvodce instalací serveru v **pracovním režimu** a zakázat **pracovním režimu**.
   ![ReadyToConfigure](./media/how-to-connect-sync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Zotavení po havárii
Součástí návrhu implementace je plánování co dělat v případě, že nedojde k havárii Pokud ztratíte synchronizační server. Existují různé modely mají použít, a který se má použít, závisí na několika faktorech včetně:

* Co je vaší míře tolerance pro nebude možné provádět změny objektů ve službě Azure AD během výpadku?
* Pokud používáte synchronizace hesel, uživatelé přijmou, že se mají použít staré heslo ve službě Azure AD v případě, že se mění v místním?
* Máte závislost na provoz v reálném čase, jako je například zpětný zápis hesla?

V závislosti na odpovědi na tyto otázky a zásad vaší organizace je možné implementovat jednu z následujících strategií:

* Znovu sestavte v případě potřeby.
* Mít náhradní pohotovostní server, označované jako **pracovním režimu**.
* Použijte virtuální počítače.

Pokud použijete integrovanou databází SQL Express, pak byste si rovněž projít [vysoké dostupnosti pro SQL](#sql-high-availability) oddílu.

### <a name="rebuild-when-needed"></a>Znovu sestavit v případě potřeby
Přijatelné strategií je plánování opětovném sestavení serveru v případě potřeby. Obvykle instalace synchronizačního modulu a nechcete, můžete dokončit počáteční import a synchronizace během pár hodin. Pokud není k dispozici náhradní server, je možné k hostování synchronizační modul dočasně použít řadič domény.

Synchronizační modul server neukládá některému ze stavů o objektech, tak databázi můžete znovu sestavit z dat ve službě Active Directory a Azure AD. **SourceAnchor** atribut se používá pro připojení objekty v místním prostředím a cloudem. Pokud je znovu sestavit server pomocí existující objekty v místním prostředím a cloudem, poté synchronizační modul porovnává tyto objekty společně znovu na přeinstalace. Jsou věci, které potřebujete k dokumentu a uložit změny konfigurace serveru, jako jsou pravidla filtrování a synchronizace. Tyto vlastní konfigurace musí být znovu použit před jejím zahájením.

### <a name="have-a-spare-standby-server---staging-mode"></a>Mít náhradní pohotovostní server – pracovní režim
Pokud máte složitější prostředí, pak máte nejméně jeden pohotovostní server doporučuje. Během instalace, můžete povolit serveru tak, aby se v **pracovním režimu**.

Další informace najdete v tématu [pracovním režimu](#staging-mode).

### <a name="use-virtual-machines"></a>Použijte virtuální počítače
Metoda běžné a podporovaných je ke spuštění synchronizační modul v rámci virtuálního počítače. V případě, že hostitel má problém, bitová kopie se synchronizační modul server můžete migrovat na jiný server.

### <a name="sql-high-availability"></a>Vysoké dostupnosti pro SQL
Pokud nechcete použít SQL Server Express, která se dodává s Azure AD Connect, pak vysoké dostupnosti pro SQL Server. měli byste také zvážit. Řešení vysoké dostupnosti, podporované zahrnují clusteringu SQL a AOA (skupiny dostupnosti Always On). Nepodporovaná řešení obsahovat zrcadlení.

Azure AD Connect verze 1.1.524.0 byla přidaná podpora pro SQL AOA. Před instalací Azure AD Connect je nutné povolit SQL AOA. Během instalace Azure AD Connect zjistí, zda zadaná instance SQL je nebo není povolená pro SQL AOA. Pokud je povolené SQL AOA, Azure AD Connect další přijde na to, pokud SQL AOA je nakonfigurovaná pro používání replikace synchronní nebo asynchronní replikace. Při nastavování naslouchacího procesu skupiny dostupnosti, se doporučuje nastavit vlastnost RegisterAllProvidersIP na hodnotu 0. Je to proto, že Azure AD Connect v současnosti využívá nativní klient systému SQL pro připojení k SQL a nativní klient systému SQL nepodporuje používání vlastnosti MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Appendix CSAnalyzer
V části [ověřte](#verify) o tom, jak pomocí tohoto skriptu.

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

## <a name="next-steps"></a>Další postup
**Témata s přehledem**  

* [Synchronizace Azure AD Connect: Pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)  
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)  
