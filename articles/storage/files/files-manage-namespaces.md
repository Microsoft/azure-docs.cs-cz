---
title: Jak používat DFS-N se soubory Azure
description: Běžné případy použití systému souborů DFS – N se soubory Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741652"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Jak používat obory názvů DFS se soubory Azure
[Obory názvů distribuovaných systémů souborů](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview), které se běžně označují jako obory názvů DFS nebo DFS-N, jsou role serveru Windows Server, která se často používá ke zjednodušení nasazení a údržby sdílených složek SMB v produkčním prostředí. Obory názvů DFS jsou virtualizační technologie oboru názvů úložiště, což znamená, že vám umožní poskytnout vrstvu dereference mezi cestou UNC ke sdíleným složkám a skutečnými sdílenými složkami souborů. Obory názvů DFS spolupracuje se sdílenými složkami SMB, nezávislá těchto sdílených složek je hostovaný: dá se použít se sdílenými složkami SMB hostovanými na místním souborovém serveru Windows s nebo bez Synchronizace souborů Azure, sdílené složky Azure přímo, sdílené složky SMB hostované v Azure NetApp Files nebo jiných nabídkách třetích stran a dokonce i se sdílenými složkami, které jsou hostované v jiných cloudech. 

V základních oborech názvů DFS poskytuje mapování mezi uživatelsky přívětivou cestou UNC, například `\\contoso\shares\ProjectX` a základní cestou UNC sdílené složky SMB, jako je `\\Server01-Prod\ProjectX` nebo `\\storageaccount.file.core.windows.net\projectx` . Když chce koncový uživatel přejít na sdílenou složku, zapíše uživatelsky přívětivou cestu UNC, ale jejich klient SMB přistupuje k základní cestě SMB mapování. Můžete také roztáhnout tento základní koncept, který převezme existující název souborového serveru, jako je například `\\MyServer\ProjectX` . Tuto možnost můžete využít k tomu, abyste dosáhli následujících scénářů:

- Zadejte název kontrolního údaje pro migraci logické sady dat. V tomto příkladu máte mapování, jako by bylo `\\contoso\shares\Engineering` namapováno na `\\OldServer\Engineering` . Po dokončení migrace do služby soubory Azure můžete mapování změnit tak, aby odkazovalo na uživatelsky přívětivou cestu UNC `\\storageaccount.file.core.windows.net\engineering` . Když koncový uživatel přistupuje k uživatelsky přívětivé cestě UNC, bude se bezproblémově přesměrovat na cestu ke sdílené složce Azure.

- Vytvořte běžný název pro logickou sadu dat distribuovaných na více serverech v různých fyzických lokalitách, například prostřednictvím Synchronizace souborů Azure. V tomto příkladu je název, který `\\contoso\shares\FileSyncExample` je namapován na více cest UNC `\\FileSyncServer1\ExampleShare` , například, `\\FileSyncServer2\DifferentShareName` , `\\FileSyncServer3\ExampleShare` . Když uživatel přistupuje k uživatelsky přívětivému názvu UNC, zobrazí seznam možných cest UNC a vybere ten, který je nejblíže založený na definicích lokalit služby Active Directory (AD) Windows serveru.

- Rozšíření logické sady dat napříč velikostí, v/v nebo v jiných prahových hodnotách škálování. To je běžné při práci s uživatelskými adresáři, kde každý uživatel získá vlastní složku ve sdílené složce nebo s nevyřízenými sdílenými složkami, kde uživatelé získají libovolný prostor, který bude zpracovávat dočasné datové potřeby. U oborů názvů DFS spojíte více složek do soudržného oboru názvů. Například `\\contoso\shares\UserShares\user1` mapuje na `\\storageaccount.file.core.windows.net\user1` , `\\contoso\shares\UserShares\user2` mapuje na `\\storageaccount.file.core.windows.net\user2` a tak dále.  

Příklad použití oborů názvů DFS s nasazením souborů Azure najdete v následujícím přehledu videa.

[![Ukázka, jak nastavit DFS-N se soubory Azure – kliknutím zapnete přehrávání.](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Ve videu přejděte na 10:10 a zjistěte, jak nastavit obory názvů DFS.

Pokud už máte obor názvů DFS, nejsou potřeba žádné zvláštní kroky, abyste ho mohli používat se soubory Azure a Synchronizace souborů. Pokud ke sdílené složce Azure přistupujete z místního prostředí, platí normální požadavky na síť. Další informace najdete v tématu věnovaném [důležitým informacím o sítích Azure Files](./storage-files-networking-overview.md) .

## <a name="namespace-types"></a>Typy oboru názvů
Obory názvů DFS poskytují dva hlavní typy oborů názvů:

- **Obor názvů založený na doméně**: obor názvů hostovaný jako součást domény Windows Server AD. Obory názvů hostované jako součást služby AD budou mít cestu UNC, která obsahuje název vaší domény, například `\\contoso.com\shares\myshare` , pokud je vaše doména `contoso.com` . Obory názvů založené na doméně podporují větší limity škálování a integrovanou redundanci prostřednictvím služby AD. Obory názvů založené na doméně nemůžou být clusterovaný prostředek v clusteru s podporou převzetí služeb při selhání. 
- **Samostatný obor názvů**: obor názvů hostovaný na samostatném serveru, který není hostovaný jako součást služby Windows Server AD. Samostatné obory názvů budou mít název na základě názvu samostatného serveru, například `\\MyStandaloneServer\shares\myshare` , kde je název samostatného serveru `MyStandaloneServer` . Samostatné obory názvů podporují nižší cíle škálování než obory názvů založené na doméně, ale můžou být hostované jako clusterový prostředek v clusteru s podporou převzetí služeb při selhání

## <a name="requirements"></a>Požadavky
Pokud chcete používat obory názvů DFS se soubory a Synchronizace souborů Azure, musíte mít následující prostředky:

- Doména služby Active Directory. To je možné hostovat kdekoli, jako je například místní, na virtuálním počítači Azure nebo dokonce v jiném cloudu.
- Systém Windows Server, který může hostovat obor názvů. Vzor nasazení běžných vzorů pro obory názvů DFS je použití řadiče domény služby Active Directory k hostování oborů názvů, ale obory názvů je možné nastavit z libovolného serveru s nainstalovanou rolí server oborů názvů DFS. Obory názvů DFS jsou k dispozici ve všech podporovaných verzích Windows serveru.
- Sdílená složka SMB hostovaná v prostředí připojeném k doméně, jako je třeba sdílená složka Azure hostovaná v rámci účtu úložiště připojeného k doméně, nebo sdílená složka hostovaná na souborovém serveru s Windows připojeným k doméně pomocí Synchronizace souborů Azure. Další informace o připojení účtu úložiště k doméně najdete v tématu [ověřování na základě identity](storage-files-active-directory-overview.md). Souborové servery Windows jsou připojené k doméně stejným způsobem bez ohledu na to, jestli používáte Synchronizace souborů Azure.
- Sdílené složky SMB, které chcete použít s obory názvů DFS, musí být dosažitelné z místních sítí. To je primárně důležité pro sdílené složky Azure, které se ale vztahují na všechny sdílené složky hostované v Azure nebo v jakémkoli jiném cloudu. Další informace o sítích najdete v tématu [požadavky na síť pro přímý přístup](storage-files-networking-overview.md).

## <a name="install-the-dfs-namespaces-server-role"></a>Instalace role serveru obory názvů DFS
Pokud už používáte obory názvů DFS nebo chcete nastavit obory názvů DFS v řadiči domény, můžete tyto kroky bezpečně přeskočit.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete nainstalovat roli serveru obory názvů DFS, otevřete Správce serveru na serveru. Vyberte **Spravovat** a pak vyberte **Přidat role a funkce**. Výsledný Průvodce vás provede instalací nezbytných součástí systému Windows ke spuštění a správě oborů názvů DFS. 

V části **typ instalace** v Průvodci instalací vyberte přepínač instalace na základě **rolí nebo na základě funkcí** a vyberte možnost **Další**. V části **Výběr serveru** vyberte požadované servery, na které chcete nainstalovat roli serveru obory názvů DFS, a pak vyberte **Další**. 

V části **role serveru** vyberte a ověřte roli **obory názvů DFS** ze seznamu rolí. Najdete ho v části souborové **služby a**  >  **služby úložiště a služby iSCSI**. Když vyberete roli serveru obory názvů DFS, může taky přidat všechny požadované podpůrné role serveru nebo funkce, které ještě nejsou nainstalované.

![Snímek obrazovky * * Průvodce přidáním rolí a funkcí * * s vybranou rolí * * obory názvů DFS * *](./media/files-manage-namespaces/dfs-namespaces-install.png)

Po zaškrtnutí políčka role **obory názvů DFS** můžete vybrat **Další** na všech dalších obrazovkách a vybrat **nainstalovat** hned, Jakmile Průvodce povolí tlačítko. Až se instalace dokončí, můžete nakonfigurovat obor názvů.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Z relace PowerShellu se zvýšenými oprávněními (nebo pomocí vzdálené komunikace PowerShellu) spusťte následující příkazy.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Přebírat existující názvy serverů s kořenovou konsolidací
Důležité použití pro obory názvů DFS je přebírat existující název serveru pro účely refaktoringu fyzického rozložení sdílených složek souborů. Můžete například chtít konsolidovat sdílené složky z několika starých souborových serverů společně na jednom souborovém serveru během modernizace modernizace. V obvyklých případech a v případě, že je v rámci jednoho hostitele možné sloučit sdílené složky mezi různorodými souborovými servery, ale funkce pro kořenové obory názvů DFS vám umožní vytvořit jeden server na více názvů serverů a směrovat ho do příslušného názvu sdílené složky.

I když je to užitečné pro různé scénáře migrace datacentra, je kořenová konsolidace zvláště užitečná pro přijetí cloudových sdílených složek Azure v cloudu, protože:

- Sdílené složky Azure neumožňují zachovat existující názvy místních serverů.
- Ke sdíleným složkám Azure se musí přihlédnout prostřednictvím plně kvalifikovaného názvu domény (FQDN) účtu úložiště. Například sdílená složka Azure `share` , která je volána v účtu úložiště, `storageaccount` je vždy k dispozici prostřednictvím `\\storageaccount.file.core.windows.net\share` cesty UNC. To může být matoucí pro koncové uživatele, kteří očekávají krátký název (např. `\\MyServer\share`) nebo název, který je subdoménou názvu domény organizace (např. `\\MyServer.contoso.com\share`).

Kořenová konsolidace se dá použít jenom se samostatnými obory názvů. Pokud již pro sdílené složky máte existující obor názvů založený na doméně, není nutné vytvořit kořenový konsolidovaný obor názvů.

### <a name="enabling-root-consolidation"></a>Povolování kořenové konsolidace
Kořenovou konsolidaci můžete povolit nastavením následujících klíčů registru z relace PowerShellu se zvýšenými oprávněními (nebo pomocí vzdálené komunikace PowerShellu).

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>Vytváření záznamů DNS pro existující názvy souborového serveru
Aby obory názvů DFS reagovaly na existující názvy souborového serveru, vytvořte záznamy aliasu (CNAME) pro existující souborové servery, které ukazují na název serveru obory názvů DFS. Přesný postup aktualizace záznamů DNS může záviset na serverech, které vaše organizace používá, a na tom, jestli vaše organizace používá vlastní nástroje pro automatizaci správy DNS. Pro server DNS, který je součástí Windows serveru a automaticky používá Windows AD, se zobrazí následující kroky.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Na serveru DNS Windows otevřete konzolu pro správu DNS. Najdete ho tak, že vyberete tlačítko **Start** a zadáte **DNS**. Přejděte do zóny dopředného vyhledávání pro vaši doménu. Pokud je vaše doména například `contoso.com` , zóna dopředného vyhledávání se dá najít v části **zóny dopředného vyhledávání**  >  **`contoso.com`** v konzole pro správu. Přesná hierarchie zobrazená v tomto dialogovém okně bude záviset na konfiguraci DNS pro vaši síť.

Klikněte pravým tlačítkem na zónu dopředného vyhledávání a vyberte **nový alias (CNAME)**. V výsledném dialogu zadejte krátký název souborového serveru, který nahrazujete (plně kvalifikovaný název domény se automaticky vyplní v textovém poli s názvem **plně kvalifikovaný název domény**). Do textového pole s názvem **plně kvalifikovaný název domény (FQDN) pro cílového hostitele** zadejte název serveru DFS-N, který jste nastavili. V případě potřeby můžete použít tlačítko **Procházet** , které vám umožní vybrat server. Vyberte **OK** a vytvořte záznam CNAME pro váš server.

![Snímek obrazovky znázorňující záznam * * nového prostředku * * pro záznam DNS CNAME.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Na serveru DNS Windows otevřete relaci PowerShellu (nebo pomocí vzdálené komunikace PowerShellu) spusťte následující příkazy, naplnění `$oldServer` a `$dfsnServer` s odpovídajícími hodnotami pro vaše prostředí ( `$domain` automaticky se naplní názvem domény, ale můžete taky také zadat i tento příkaz).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Vytvoření oboru názvů
Základní jednotkou správy pro obory názvů DFS je obor názvů. Kořen oboru názvů nebo název je počátečním bodem oboru názvů, jako je například v cestě UNC, `\\contoso.com\Public\` kořen oboru názvů je `Public` . 

Pokud používáte obory názvů DFS k převzetí existujícího názvu serveru pomocí kořenové konsolidace, název oboru názvů by měl být název serveru, který chcete převzít, spolu s `#` znakem. Pokud byste například chtěli převzít existující server s názvem `MyServer` , vytvořili byste obor názvů DFS-N `#MyServer` . Níže uvedený oddíl PowerShellu se postará o předčekání `#` , ale pokud vytvoříte prostřednictvím konzoly pro správu systému souborů DFS, budete muset podle potřeby předplatit. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
Chcete-li vytvořit nový obor názvů, otevřete konzolu **pro správu systému souborů DFS** . Najdete ho tak, že vyberete tlačítko **Start** a napíšete **Správa systému souborů DFS**. Výsledná Konzola pro správu má dva oddíly **obory názvů** a **replikaci**, které odkazují na obory názvů DFS a replikace DFS (DFS-R) v uvedeném pořadí. Synchronizace souborů Azure poskytuje moderní mechanismus pro replikaci a synchronizaci, který se dá použít místo DFS-R, pokud se taky požaduje replikace.

Vyberte oddíl **obory názvů** a klikněte na tlačítko **Nový obor názvů** (můžete také kliknout pravým tlačítkem na oddíl **namespaces** ). Výsledný **Nový Průvodce oborem názvů** vás provede vytvořením oboru názvů. 

První část průvodce vyžaduje, abyste si vybrali server oboru názvů DFS pro hostování oboru názvů. Obor názvů může hostovat víc serverů, ale budete muset nastavit obory názvů DFS vždy na jednom serveru. Zadejte název požadovaného serveru oboru názvů DFS a vyberte **Další**. V části **název a nastavení oboru názvů** můžete zadat požadovaný název oboru názvů a vybrat **Další**. 

Oddíl **typ oboru názvů** umožňuje zvolit mezi **oborem názvů založeným na doméně** a **samostatným oborem názvů**. Pokud máte v úmyslu použít obory názvů DFS k zachování existujícího názvu souborového serveru nebo zařízení NAS, měli byste vybrat možnost samostatný obor názvů. Pro všechny ostatní scénáře byste měli vybrat obor názvů založený na doméně. Další informace o výběru typů oboru názvů najdete v části [typy oborů názvů](#namespace-types) výše.

![Snímek obrazovky s výběrem oboru názvů založeného na doméně a samostatného oboru názvů v průvodci * * nový obor názvů * *.](./media/files-manage-namespaces/dfs-namespace-type.png)

Vyberte požadovaný typ oboru názvů pro vaše prostředí a vyberte **Další**. Průvodce pak provede Shrnutí oboru názvů, který se má vytvořit. Vyberte **vytvořit** , pokud chcete vytvořit obor názvů a **Zavřít** po dokončení dialogu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Z relace PowerShellu na serveru oboru názvů DFS spusťte následující příkazy PowerShellu, naplnění `$namespace` , `$type` a `$takeOverName` s příslušnými hodnotami pro vaše prostředí.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Konfigurace cílů složek a složek
Aby byl obor názvů užitečný, musí mít cíle složky a složky. Každá složka může mít jeden nebo více cílů složky, které jsou ukazateli na sdílené složky SMB, které tento obsah hostují. Když uživatelé procházejí složku s cíli složky, klientský počítač obdrží odkaz, který transparentně přesměruje klientský počítač na jeden z cílů složky. Můžete také mít složky bez cílů složky pro přidání struktury a hierarchie do oboru názvů.

Složky oborů názvů DFS si můžete představit jako analogické ke sdíleným složkám souborů. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
V konzole pro správu systému souborů DFS vyberte obor názvů, který jste právě vytvořili, a vyberte **Nová složka**. Dialogové okno výsledné **nové složky** vám umožní vytvořit složku i její cíle.

![Snímek obrazovky dialogového okna * * Nová složka * *.](./media/files-manage-namespaces/dfs-folder-targets.png)

Do textového pole s popiskem **název** zadejte název složky. Vyberte **Přidat...** a přidejte cíle složky pro tuto složku. Výsledný dialog pro **Přidání cílové složky** obsahuje textové pole s popiskem **cesta k cíli složky** , kde můžete zadat cestu UNC k požadované složce. V dialogovém okně **Přidat cíl složky** vyberte **OK** . Pokud přidáváte cestu UNC ke sdílené složce Azure, může se zobrazit zpráva, že server `storageaccount.file.core.windows.net` nemůže být kontaktem. Očekává se to; Pokračujte výběrem **Ano** . Nakonec v dialogovém okně **Nová složka** vyberte **OK** , aby se vytvořily cíle složky a složky.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Teď, když jste vytvořili obor názvů, složku a cíl složky, byste měli být schopni připojit sdílenou složku přes obory názvů DFS. Pokud používáte obor názvů založený na doméně, měla by být úplná cesta ke sdílené složce `\\<domain-name>\<namespace>\<share>` . Pokud používáte samostatný obor názvů, úplná cesta ke sdílené složce by měla být `\\<DFS-server>\<namespace>\<share>` . Pokud používáte samostatný obor názvů s kořenovou konsolidací, můžete přímo přistupovat přes starý název serveru, například `\\<old-server>\<share>` .

## <a name="see-also"></a>Viz také
- Nasazení sdílené složky Azure: [plánování pro nasazení souborů Azure](storage-files-planning.md) a [Postup vytvoření sdílené složky](storage-how-to-create-file-share.md).
- Konfigurace přístupu ke sdílení souborů: [ověřování na základě identity](storage-files-active-directory-overview.md) a [síťové požadavky pro přímý přístup](storage-files-networking-overview.md).
- [systém souborů DFS (Distributed File System) obory názvů Windows serveru](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)