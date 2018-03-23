---
title: Aktualizace zásobníku Azure 1711 | Microsoft Docs
description: Další informace o tom, co je v aktualizaci 1711 pro Azure zásobníku integrované systémy, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fd57699a329fbccdbefc73dae7d473070cd831ea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-1711-update"></a>Azure aktualizace 1711 zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje vylepšení a opravy v tomto balíčku aktualizace – známé problémy pro tuto verzi a toho, kde se stáhnout aktualizaci. Známé problémy jsou rozdělené do známé problémy přímo související s proces aktualizace a známé problémy s sestavení (po instalaci).

> [!IMPORTANT]
> Tento balíček aktualizace platí pouze pro Azure zásobníku integrované systémy. Tento balíček aktualizace nevztahují na Azure zásobníku Development Kit.

## <a name="build-reference"></a>Odkaz na sestavení

Číslo sestavení aktualizace 1711 zásobník Azure je **171201.3**.

## <a name="before-you-begin"></a>Než začnete

### <a name="prerequisites"></a>Požadavky

- Je nutné nejprve nainstalovat Azure zásobníku [aktualizovat 1710](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) před instalací této aktualizace.

- Zkontrolujte použití **CloudAdmin** jako název účtu před instalací aktualizace 1711. Počínaje verzí 1711, *CloudAdmin* se o název vyhrazený účet a nesmí být zadaný ručně. Při aktualizaci na verzi 1711 aktualizace odebere existující instance nasazení účtu (obvykle nazývá AzureStackAdmin). Pokud jste s názvem účet nasazení *CloudAdmin*, aktualizace až po 1711 odstraní. 

  *CloudAdmin* je předdefinovaný účet pro připojení k [ *privilegované koncový bod* ](azure-stack-privileged-endpoint.md) (období). Odstranění tohoto účtu může způsobit uzamčení období, pokud již existuje jiný uživatelský účet, který je členem skupiny CloudAdmin. 

  Pokud jste použili CloudAdmin jako název účtu, nasazení, přidáte nového uživatele CloudAdmin k vaší období před zahájením aktualizace 1711, aby nedošlo k uzamknutí mimo Azure zásobníku. Chcete-li přidat nového uživatele CloudAdmin, spusťte rutinu **New-CloudAdminUser** na období.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato aktualizace zahrnuje následující vylepšení a opravy pro Azure zásobníku.

#### <a name="new-features"></a>Nové funkce

- Podpora pro Syndicating šablony řešení
- Aktualizace v Azure Graph zásobníku protokolování a zpracování chyb
- Možnost zapnout hostitelů zapnout a vypnout
- Uživatelé můžou teď aktivovat virtuálních počítačů Windows automaticky
- Přidání privilegované koncový bod rutiny prostředí PowerShell k načtení klíčů pro obnovení nástroje BitLocker pro účely uchování
- Podpora aktualizace offline image při aktualizaci infrastruktury
- Povolit zálohování infrastruktury pomocí služby zálohování povolit

#### <a name="fixes"></a>Opravy

- Opravené časování ve službě DNS během pole replaceable jednotka (FRU) a také aktualizované clusteru protokolování
- Oprava restartování schopnost zakázat hostitele v poli replaceable jednotka (FRU)
- Různé další výkon, zabezpečení a opravy stability

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 nové funkce a opravy

- [14 listopadu 2017 – KB4048953 (sestavení operačního systému 14393.1884) ](https://support.microsoft.com/help/4048953)

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

Tato část obsahuje známé problémy, které mohou nastat při instalaci aktualizace 1711.


1. **Příznak:** operátory Azure zásobníku dojít k následující chybě během procesu aktualizace: *"název: instalace aktualizace.", "Popis": "Instalace aktualizací na hostitelích a Infra virtuálních počítačů.", "errorMessage": "zadejte 'LiveUpdate' role. Virtuálních počítačů vyvolaná výjimka: \n\nThere není dostatek místa na disk.\n\nat <ScriptBlock>, <No file>: line22 ","status":"Chyba","startTimeUtc":" 2017-11-10T16:46:59.123Z ","endTimeUtc":" 2017-11-10T19:20:29.669Z ","kroky": []"*
    2. **Příčina:** tento problém je způsoben nedostatkem volného místa na disku na jeden nebo více virtuálních počítačů, které jsou součástí infrastruktury Azure zásobníku
    3. **Řešení:** obraťte se na oddělení zákaznických služeb a podpory společnosti Microsoft pro pomoc.
<br><br>
2. **Příznak:** operátory Azure zásobníku dojít k následující chybě během procesu aktualizace:*výjimka volání "ExtractToFile" s "3" argumenty: "proces nemá přístup k souboru ' <\\<machineName>-ERCS01\C$ \ Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll >.*
    1. **Příčina:** tento problém je způsoben při pokračování v aktualizaci z portálu, která byla dříve obnovení pomocí privilegované koncového bodu (období).
    2. **Řešení:** obraťte se na oddělení zákaznických služeb a podpory společnosti Microsoft pro pomoc.
<br><br>
3. **Příznak:** operátory Azure zásobníku dojít k následující chybě během procesu aktualizace:*"typ"CheckHealth"Role"virtuálních počítačů, vyvolá k výjimce: \n\nVirtual počítač kontrolu pro <machineName>-ACS01 vytvořeného následující chyby. \nThere došlo k chybě při načítání informace o virtuálních počítačů z hostitelů. Výjimka. Podrobnosti: \nGet-virtuálních počítačů: operace v počítači se nezdařilo Node03: Služba WS-Management nemůže zpracovat žádost. \Nservice rozhraní WMI nebo zprostředkovatele rozhraní WMI vrátila neznámou chybu: HRESULT 0x8004106c ".*
    1. **Příčina:** tento problém je způsoben problém systému Windows Server, který je určený vzít v úvahu v následných aktualizací serveru okno.
    2. **Řešení:** obraťte se na oddělení zákaznických služeb a podpory společnosti Microsoft pro pomoc.
<br><br>
4. **Příznak:** operátory Azure zásobníku dojít k následující chybě během procesu aktualizace:*"typ"DefenderUpdate"Role 'URP' způsobila výjimku: selhání získání verze z \\SU1FileServer\SU1_Public\ DefenderUpdates\x64\{název souboru} .exe po 60 pokusů na kopírování-AzSDefenderFiles, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: řádek 262"*
    1. **Příčina:** tento problém je způsoben pozadí se nezdařilo nebo jsou neúplné stahování aktualizací definic program Windows Defender.
    2. **Řešení:** od první aktualizace zkuste uplynulo prosím pokus o pokračování aktualizace po až 8 hodin.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Tato část obsahuje po instalaci Známé problémy s sestavení **20171201.3**.

#### <a name="portal"></a>Portál

- Nemusí být možné zobrazit úložiště nebo výpočetní prostředky na portálu správce. To znamená, že došlo k chybě při instalaci aktualizace a že aktualizace byla nahlášena nesprávně jako úspěšné. Pokud k tomuto problému dochází, požádejte o pomoc Microsoft CSS.
- Může se zobrazit prázdný řídicí panel portálu. Chcete-li obnovit řídicí panel, vyberte ikonu ozubené kolečko v pravém horním rohu portálu a pak vyberte **obnovit výchozí nastavení**.
- Když máte zobrazeny vlastnosti skupiny prostředků, **přesunout** tlačítko k dispozici. Toto chování je očekávané. Přesunutí skupin prostředků mezi předplatnými není aktuálně podporován.
- Pro jakýkoli pracovní postup, kde v rozevíracím seznamu vyberte předplatné, skupinu prostředků nebo umístění může zaznamenat jeden nebo více z následujících důvodů:

   - Může se zobrazit prázdný řádek v horní části seznamu. By měl mít pořád povolený výběr položky podle očekávání.
   - Pokud je seznam položek v rozevíracím seznamu krátký, asi nebudete moci zobrazit některé názvy položek.
   - Pokud máte více předplatných uživatele, rozevíracího seznamu skupiny prostředků může být prázdná.

        > [!NOTE]
        > Obejít poslední dva problémy, můžete zadat název předplatné nebo skupinu prostředků (pokud ho znáte), nebo můžete místo toho použít PowerShell.

- Odstranění odběrů uživatele za následek osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatele nebo skupinu celý prostředků a potom odstraňte odběry uživatele.
- Nemůžete se moci zobrazit oprávnění k vašemu předplatnému používání portálů zásobník Azure. Jako alternativní řešení můžete ověřit oprávnění pomocí prostředí PowerShell.

#### <a name="health-and-monitoring"></a>Sledování stavu a

- Pokud restartujete instance role infrastruktury, zobrazí se zpráva s oznámením, že restartování se nezdařilo. Ale restartování ve skutečnosti bylo úspěšné.

#### <a name="marketplace"></a>Marketplace
- Při pokusu o přidání položky do zásobníku Azure marketplace s použitím **přidat z Azure** možnost, všechny položky můžou být vidět ke stažení.
- Uživatelé mohou procházet na kompletní nabídku marketplace bez předplatného a uvidí položky pro správu jako plány a nabízí. Tyto položky jsou pro uživatele funkční.

#### <a name="compute"></a>Compute
- Uživatelé mají možnost vytvoření virtuálního počítače pomocí geograficky redundantní úložiště. Tato konfigurace způsobuje, že vytvoření virtuálního počítače k selhání.
- Můžete nakonfigurovat dostupnost virtuálního počítače nastavit pouze u domény selhání jednoho a doméně aktualizace jednoho.
- Neexistuje žádné zkušenosti marketplace pro vytvoření sady škálování virtuálního počítače. Můžete vytvořit škálování nastavit pomocí šablony.
- Nastavení škálování pro sady škálování virtuálního počítače nejsou k dispozici na portálu. Jako alternativní řešení, můžete použít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z důvodu rozdílů verze prostředí PowerShell, je nutné použít `-Name` parametr místo `-VMScaleSetName`.

#### <a name="networking"></a>Sítě
- Nástroj pro vyrovnávání zatížení s veřejnou IP adresu nelze vytvořit pomocí portálu. Jako alternativní řešení můžete použít PowerShell k vytvoření nástroje pro vyrovnávání zatížení.
- Když vytvoříte Vyrovnávání zatížení sítě, musíte vytvořit pravidlo překladu adres sítě. Pokud to neuděláte, obdržíte chybu při pokusu přidat pravidlo NAT po vytvoření nástroje pro vyrovnávání zatížení.
- Veřejnou IP adresu z virtuálního počítače (VM) nelze zrušit přidružení, po virtuálního počítače byla vytvořena a související s touto adresou IP. Zrušení přidružení se zobrazí postup, ale pořád přidruženy původní virtuální počítač dříve přiřazenou veřejnou IP adresu. K tomuto chování dochází i v případě, že je přiřadit IP adresu na nový virtuální počítač (obvykle označuje jako *prohození*). Všechny budoucí pokusy o připojení přes tuto IP adresu povede připojení do původně přidružený virtuální počítač a ne do nového. V současné době je nutné pouze použít nové veřejné IP adresy pro vytvoření nového virtuálního počítače.
- Operátory Azure zásobníku může nelze nasadit, odstraňovat, upravovat virtuální sítě nebo skupiny zabezpečení sítě. Tento problém je primárně zobrazit na následné aktualizace pokusy stejného balíčku. To je způsobeno problémem balení s aktualizaci, která je aktuálně šetření.
- Interní Vyrovnávání zatížení (ILB) nesprávně zpracovává adresy MAC pro virtuální počítače back-end, které dělí instance systému Linux.

#### <a name="sqlmysql"></a>SQL/MySQL
- To může trvat až jednu hodinu, než klienti databáze můžete vytvářet v nové SQL nebo MySQL SKU.
- Vytvoření položky přímo na SQL a MySQL hostitelské servery, které nejsou prováděné poskytovatelem prostředků není podporována a může mít za následek neodpovídající stavu.

#### <a name="app-service"></a>App Service
- Uživatel musí zaregistrovat zprostředkovatele prostředku úložiště dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

#### <a name="identity"></a>Identita

V Azure Active Directory Federation Services (ADFS) nasazené prostředí, **azurestack\azurestackadmin** účet už není vlastníkem předplatného výchozí zprostředkovatel. Místo protokolování do **portál pro správu nebo koncový bod adminmanagement** s **azurestack\azurestackadmin**, můžete použít **azurestack\cloudadmin** účet, takže která spravujete a používáte výchozí zprostředkovatel odběru.

> [!IMPORTANT]
> I když **azurestack\cloudadmin** účet je vlastníkem předplatného poskytovatele výchozí v prostředí služby AD FS nasadit, nemá oprávnění pro připojení RDP na hostiteli. Nadále používat **azurestack\azurestackadmin** účet nebo účet místního správce pro přihlášení, přístup a spravovat hostitele, podle potřeby.

#### <a name="infrastructure-backup-sevice"></a>Zálohování adresářové infrastruktury
<!-- 1974890-->

- **Před 1711 zálohy nejsou podporovány pro obnovení cloudu.**  
  Před 1711 záloh nejsou kompatibilní s obnovení cloudu. Musíte nejdřív aktualizovat 1711 a povolení zálohování. Pokud jste nepovolili zálohy, ujistěte se, že jste po aktualizaci 1711 provedení zálohy. Před 1711 zálohy, měla by být odstraněna.

- **Povolení zálohování infrastruktury na ASDK je jenom pro účely testování.**  
  Infrastruktura zálohování můžete použít k obnovení několika uzly řešení. Můžete povolit zálohování infrastruktury na ASDK, ale neexistuje žádný způsob, jak otestovat obnovení.

Další informace najdete v tématu [zálohování a data obnovení pro zásobník Azure pomocí služby Backup infrastruktury](azure-stack-backup-infrastructure-backup.md).

## <a name="download-the-update"></a>Stažení aktualizace

Můžete si stáhnout balíček aktualizace 1711 zásobník Azure z [zde](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Další informace

Společnost Microsoft poskytuje způsob, jak sledovat a pokračovat v použití privilegovaných koncový bod období s 1711 aktualizace nainstalovat aktualizace.

- Najdete v článku [monitorování aktualizací v zásobníku Azure pomocí dokumentace privilegované koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Další informace najdete v tématech

- V tématu [správě aktualizací v přehledu Azure zásobníku](azure-stack-updates.md) přehled správy aktualizací v zásobníku Azure.
- V tématu [aktualizace v zásobníku Azure](azure-stack-apply-updates.md) Další informace o tom, jak používat aktualizace s zásobník Azure.
