---
title: Aktualizace zásobníku Azure 1712 | Microsoft Docs
description: Další informace o tom, co je v aktualizaci 1712 pro Azure zásobníku integrované systémy, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: brenduns
ms.openlocfilehash: b4c5b53a46792e31316f752f8902d7a05554b57d
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604144"
---
# <a name="azure-stack-1712-update"></a>Azure aktualizace 1712 zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje vylepšení a opravy v tomto balíčku aktualizace – známé problémy pro tuto verzi a toho, kde se stáhnout aktualizaci. Známé problémy jsou rozdělené do známé problémy přímo související s proces aktualizace a známé problémy s sestavení (po instalaci).

> [!IMPORTANT]
> Tento balíček aktualizace platí pouze pro Azure zásobníku integrované systémy. Tento balíček aktualizace nevztahují na Azure zásobníku Development Kit.

## <a name="build-reference"></a>Odkaz na sestavení

Číslo sestavení aktualizace 1712 zásobník Azure je **180106.1**. Pokud zákazník nasadil **180103.2** dříve, není potřeba použít **180106.1**.

## <a name="before-you-begin"></a>Než začnete

> [!IMPORTANT]
> Nepokoušejte se k vytvoření virtuálních počítačů během procesu instalace 1712 aktualizace. V tématu [správě aktualizací v přehledu Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) další podrobnosti.

### <a name="prerequisites"></a>Požadavky

Je nutné nejprve nainstalovat Azure zásobníku [aktualizovat 1711](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) před instalací této aktualizace.

### <a name="post-update-steps"></a>Postup po aktualizaci
- Po instalaci 1712 nainstalujte všechny použitelné opravy hotfix. Další informace naleznete v následujících článcích znalostní báze knowledge base, a také naše [obsluhy zásad](azure-stack-servicing-policy.md).  
  - [KB 4103348 – rozhraní API funkce síťový adaptér služby dojde k chybě při pokusu o instalaci aktualizace Azure zásobníku](https://support.microsoft.com/help/4103348)


- Aktualizace 1712 také vyžaduje instalaci aktualizace firmwaru z partnerského OEM po dokončení instalace aktualizace 1712 zásobník Azure.

  > [!NOTE]
  > Naleznete na webu výrobce OEM partnera můžete stáhnout aktualizace.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Tato aktualizace zahrnuje následující vylepšení a opravy pro Azure zásobníku.

#### <a name="new-features"></a>Nové funkce

- Rutina test-AzureStack ověření cloudu zásobník Azure k dispozici prostřednictvím privilegované koncový bod
- Možnost registrovat odpojené nasazení Azure zásobníku
- Monitorování upozornění na vypršení platnosti certifikátu a uživatele účtu
- Přidané rutiny Set-BmcPassword v období pro otočení heslo BMC
- Aktualizace protokolování sítě pro podporu protokolování na vyžádání
- Operace obnovení z Image podporu pro virtuální počítač měřítka sady (VMSS)
- Povolit celoobrazovkového režimu na virtuálním počítači ERCS pro CloudAdmin přihlášení
- Klienty můžete aktivovat automaticky virtuálních počítačů Windows

#### <a name="fixes"></a>Opravy

- Oprava při spuštění opravy zobrazit provozní stav uzlu ve stavu údržby
- Opravte do správné veřejnou IP adresu využití záznamy časového razítka
- Různé další výkonu, stability a opravy zabezpečení
- TimeSource a Defender privilegovaný koncový bod modulu opravy chyb

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 nové funkce a opravy

- [Leden, 3.-2018 – KB4056890 (sestavení operačního systému 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - Tato aktualizace obsahuje opravy softwaru pro zabezpečení napříč celým průmyslem problém popsaného [střediska MSRC Security Advisory ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

Tato část obsahuje známé problémy, které mohou nastat při instalaci aktualizace 1712.

1. **Příznak:** operátory Azure zásobníku dojít k následující chybě během procesu aktualizace: *"typ"CheckHealth"Role 'Virtuálních počítačů' vyvolána k výjimce: \n\nVirtual kontrolu stavu počítače pro - ACS01 vytváří následující chyby. \nThere došlo k chybě při načítání informace o virtuálních počítačů z hostitelů. Výjimka. Podrobnosti: \nGet-virtuálních počítačů: operace v počítači se nezdařilo Node03: Služba WS-Management nemůže zpracovat žádost. \Nservice rozhraní WMI nebo zprostředkovatele rozhraní WMI vrátila neznámou chybu: HRESULT 0x8004106c. "*
    1. **Příčina:** tento problém je způsoben problém systému Windows Server, který je určený vzít v úvahu v následných aktualizací serveru okno.
    2. **Řešení:** obraťte se na oddělení zákaznických služeb a podpory společnosti Microsoft pro pomoc.
<br><br>
2. **Příznak:** operátory Azure zásobníku dojít k následující chybě během procesu aktualizace:*"povolení prstenec počáteční hodnoty neúspěšné virtuálního počítače na uzlu Node03 hostitele k chybě: [hostitel-Node03] připojení ke vzdálenému serveru hostitele Node03 se nezdařilo s následující chybová zpráva: Klient WinRM přijal stav chyby serveru HTTP (500), ale vzdálená služba neobsahuje žádné další informace o příčinu selhání. "*
    1. **Příčina:** tento problém je způsoben problém systému Windows Server, který je určený vzít v úvahu v následných aktualizací serveru okno. 
    2. **Řešení:** obraťte se na oddělení zákaznických služeb a podpory společnosti Microsoft pro pomoc.
<br><br>

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Tato část obsahuje po instalaci Známé problémy s sestavení **180106.1**.

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
- **Stav služby** okno nepodaří načíst. Když otevřete okno Stav služby je správce nebo uživatel portálu Azure zásobníku zobrazí chybu a nenačte informace. Toto je očekávané chování. I když můžete vybrat a otevřete stav služby, tato funkce ještě není k dispozici, ale bude implementované v budoucí verzi systému Azure zásobníku.

#### <a name="health-and-monitoring"></a>Sledování stavu a

- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Výstrahy #1:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu prezenčního signálu skener není k dispozici. To může ovlivnit sestavy o stavu a metrik.  

  Výstrahy #2:
   - Název: Infrastruktury role není v pořádku
   - ZÁVAŽNOST: upozornění
   - SOUČÁSTÍ: Řízení stavu
   - Popis: Řadičem stavu skener chyby není k dispozici. To může ovlivnit sestavy o stavu a metrik.

  Obě výstrahy můžete bezpečně ignorovat. Se automaticky zavře v čase.  

- Pokud restartujete instance role infrastruktury, zobrazí se zpráva s oznámením, že restartování se nezdařilo. Ale restartování ve skutečnosti bylo úspěšné.



#### <a name="marketplace"></a>Marketplace
- Některé položky marketplace jsou odebírána v této verzi z důvodu kompatibility. Toto bude znovu povolit po další ověření.
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

    > [!NOTE]
    > Při aktualizaci na verzi 1712 vaše systémy integrované zásobník Azure, by neměla mít dopad uživatelům existující SQL nebo MySQL prostředků zprostředkovatele. Můžete nadále používat aktuální SQL nebo MySQL prostředků zprostředkovatele buildy, dokud je dostupná nová aktualizace zásobníku Azure.

#### <a name="app-service"></a>App Service
- Uživatel musí zaregistrovat zprostředkovatele prostředku úložiště dřív, než vytvoří jejich první funkce Azure v rámci předplatného.

#### <a name="identity"></a>Identita

V Azure Active Directory Federation Services (ADFS) nasazené prostředí, **azurestack\azurestackadmin** účet už není vlastníkem předplatného výchozí zprostředkovatel. Místo protokolování do **portál pro správu nebo koncový bod adminmanagement** s **azurestack\azurestackadmin**, můžete použít **azurestack\cloudadmin** účet, takže která spravujete a používáte výchozí zprostředkovatel odběru.

> [!IMPORTANT]
> I když **azurestack\cloudadmin** účet je vlastníkem předplatného poskytovatele výchozí v prostředí služby AD FS nasadit, nemá oprávnění pro připojení RDP na hostiteli. Nadále používat **azurestack\azurestackadmin** účet nebo účet místního správce pro přihlášení, přístup a spravovat hostitele, podle potřeby.

## <a name="download-the-update"></a>Stažení aktualizace

Můžete si stáhnout balíček aktualizace 1712 zásobník Azure z [zde](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Další informace

Společnost Microsoft poskytuje způsob, jak sledovat a pokračovat v použití privilegovaných koncový bod období s 1712 aktualizace nainstalovat aktualizace.

- Najdete v článku [monitorování aktualizací v zásobníku Azure pomocí dokumentace privilegované koncový bod](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Další informace najdete v tématech

- V tématu [správě aktualizací v přehledu Azure zásobníku](azure-stack-updates.md) přehled správy aktualizací v zásobníku Azure.
- V tématu [aktualizace v zásobníku Azure](azure-stack-apply-updates.md) Další informace o tom, jak používat aktualizace s zásobník Azure.
