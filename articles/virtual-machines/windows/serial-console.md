---
title: Virtuální počítač Azure konzoly sériového portu | Microsoft Docs
description: Obousměrná sériové konzoly pro virtuální počítače Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuální počítač konzoly sériového portu (preview) 


Konzole sériového portu virtuálního počítače na platformě Azure poskytuje přístup k založený na textu konzoly pro virtuální počítače Linux a Windows. Toto sériové připojení je COM1 sériového portu virtuálního počítače a poskytuje přístup k virtuálnímu počítači a nesouvisí se síť virtuálních počítačů / operačního stavu systému. Přístup ke konzole sériového portu pro virtuální počítač, můžete provést pouze prostřednictvím portálu Azure aktuálně a povoleny pouze pro uživatele, kteří mají Přispěvatel virtuálních počítačů nebo vyšší přístup k virtuálnímu počítači. 

> [!Note] 
> Verze Preview jsou k dispozici pro vás, za předpokladu, že souhlasíte s podmínkami použití. Další informace najdete v tématu [Microsoft Azure doplňkové podmínky použití pro Microsoft Azure Preview.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aktuálně tato služba je v **verzi public preview** a přístup ke konzole sériového portu pro virtuální počítače je k dispozici pro globální oblastech Azure. V tomto okamžiku konzoly sériového portu, není k dispozici cloudu Azure Government, Azure v Německu a Azure China.

 

## <a name="prerequisites"></a>Požadavky 

* Virtuální počítač musí mít [spouštění diagnostiky](boot-diagnostics.md) povoleno 
* Účet, pomocí konzoly sériového portu, musí mít [role Přispěvatel](../../active-directory/role-based-access-built-in-roles.md) pro virtuální počítač a [spouštění diagnostiky](boot-diagnostics.md) účet úložiště. 

## <a name="open-the-serial-console"></a>Otevření konzole sériového portu
konzoly sériového portu pro virtuální počítače je k dispozici pouze prostřednictvím [portál Azure](https://portal.azure.com). Níže jsou uvedené kroky pro přístup k konzole sériového portu pro virtuální počítače prostřednictvím portálu 

  1. Otevřete portál Azure
  2. V nabídce vlevo vyberte virtuální počítače.
  3. Klikněte na virtuální počítač v seznamu. Otevře se stránka Přehled pro virtuální počítač.
  4. Posuňte se na podporu + řešení potíží s části a klikněte na možnost konzoly sériového portu (Preview). Nové podokno s konzole sériového portu se jim otevře a spuštění připojení.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Zakázat funkci
Funkce konzoly sériového portu můžete deaktivovat pro konkrétní virtuální počítače zakázáním nastavení diagnostiky spouštění tohoto virtuálního počítače.

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu 

### <a name="access-security"></a>Zabezpečení přístupu 
Přístup k konzoly sériového portu je omezená na uživatele, kteří mají [virtuálních počítačů přispěvatelé](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) nebo vyšší přístup k virtuálnímu počítači. Pokud klienta služby AAD vyžaduje Vícefaktorové ověřování, tak přístup ke konzole sériového portu bude také nutné vícefaktorového ověřování, jako je přístup přes [portál Azure](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data odeslána zpět a stanovilo se šifrují v drátové síti.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je právě přihlášen [spouštění diagnostiky](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) protokoly virtuálního počítače. Přístup k tyto protokoly jsou ve vlastnictví a řídí správce virtuálního počítače Azure.  

>[!CAUTION] 
Při přihlášení žádná hesla přístup pro konzolu, pokud příkazy spuštění konzoly obsahovat nebo uložení hesla, tajné klíče, uživatelská jména nebo jakoukoli jinou formou z identifikovatelné osobní informace (PII), ty se zapíše do Diagnostika spouštění virtuálního počítače zaznamená, společně s všechny ostatní viditelné text, v rámci implementace funkce scrollback konzole sériového portu. Tyto protokoly jsou cyklické a pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky mít přístup k nim, ale doporučujeme následující osvědčený postup pomocí vzdálené plochy pro všechno, co, která může zahrnovat tajných klíčů nebo PII. 

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojený k konzoly sériového portu a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel připojen způsobem podobají první uživatel stálého a nechá fyzické konzoly a nový uživatel uložený.

>[!CAUTION] 
To znamená, že uživatel, který odpojí nebude odhlásit! Schopnost Vynutit odhlášení při odpojení (prostřednictvím SIGHUP nebo podobné mechanismus) je stále v plánovaná. Pro Windows není automatické vypršení časového limitu povolené ve Speciální konzoly pro správu ale pro Linux můžete nakonfigurovat nastavení limitu terminálu. 


## <a name="accessing-serial-console-for-windows"></a>Přístup k konzoly sériového portu pro Windows 
Bude mít novější bitové kopie systému Windows Server na platformě Azure [Speciální konzoly pro správu](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) ve výchozím nastavení povolené (Speciální konzoly pro správu). Speciální konzoly pro správu je podporována na serveru verze systému Windows, ale není k dispozici v klientských verzích (např. Windows 10, Windows 8 nebo Windows 7). Chcete-li povolit prosím konzoly sériového portu pro virtuální počítače s Windows, které jsou vytvořené s použitím Feb2018 nebo nižší obrázků následující kroky: 

1. Připojení k virtuálnímu počítači přes vzdálenou plochu Windows
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Restartujte systém pro konzolu Speciální konzoly pro správu, aby byl povolen

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Pokud potřebné Speciální konzoly pro správu se dá nastavit do režimu offline, i 

1. Připojte disk systému windows, který chcete speciální konzoly pro správu, které jsou nakonfigurované pro jako datový disk do stávajícího virtuálního počítače. 
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Jak zjistím, zda je povoleno speciální konzoly pro správu, nebo ne 

Pokud [Speciální konzoly pro správu] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) není povoleno konzole sériového portu nezobrazí řádku Speciální konzoly pro správu. V některých případech může zobrazit informace o stavu virtuálního počítače nebo je prázdný.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Povolení spouštěcí nabídky, které se zobrazí v konzole sériového portu 

Pokud potřebujete aktivovat Windows spouštěcí zavaděč zobrazí výzvu pro zobrazení v konzole sériového portu, že následující další možnosti můžete přidat do spouštěcí zavaděč systému Windows.

1. Připojení k virtuálnímu počítači přes vzdálenou plochu Windows
2. Z příkazového řádku pro správu spusťte následující příkazy 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Restartování systému pro spouštěcí nabídky povolení

> [!NOTE] 
> Na tento bod podpora pro funkce klíče není povolená, pokud požadujete rozšířené možnosti spuštění pomocí nástroje bcdedit/set {aktuální} onetimeadvancedoptions na, najdete v části [nástroje bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) další podrobnosti

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Běžné scénáře pro přístup k systému Windows konzoly sériového portu 
Scénář          | Akce v konzole sériového portu                
:------------------|:----------------------------------------
Pravidla brány firewall nesprávný | Přístup ke konzole sériového portu a opravte iptables nebo pravidla brány firewall systému Windows 
Poškození systému souborů nebo kontrola | Přístup ke konzole sériového portu a obnovení systému souborů po přihlášení k CMD Speciální konzoly pro správu
Problémy s konfigurací protokolu RDP | Přístup ke konzole sériového portu a přihlaste se k cmd kanálu. Zkontrolujte stav Terminálové služby a v případě potřeby restartovat.
Sítě uzamčení systému| Konzoly sériového portu přístup a přihlaste se ke cmd kanálu. Zkontrolujte stav brány firewall pomocí [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) příkazového řádku. 

## <a name="errors"></a>Chyby
Nejčastější chyby jsou přechodné ve své podstatě a opakování pokusu o připojení k adrese tyto. Níže uvedená tabulka obsahuje seznam chyb a zmírnění 

Chyba                            |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Nelze načíst nastavení diagnostiky spouštění pro '<VMNAME>'. Pokud chcete používat konzole sériového portu, zkontrolujte, zda že tento Diagnostika spouštění je povolený pro tento virtuální počítač. | Zkontrolujte, zda má virtuální počítač [spouštění diagnostiky](boot-diagnostics.md) povolena. 
Virtuální počítač je v zastaveném stavu deallocated. Spusťte virtuální počítač a opakujte pokus o připojení konzoly sériového portu. | Virtuální počítač musí být ve stavu spuštěna přístup ke konzole sériového portu
Nemáte potřebná oprávnění k použití této konzoly sériového portu virtuálního počítače. Zajistěte, abyste měli aspoň oprávnění role Přispěvatel virtuálních počítačů.| Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. V tématu [přístup k požadavkům](#prerequisites) podrobnosti
Nelze určit skupinu prostředků pro účet úložiště diagnostiky spouštění se<STORAGEACCOUNTNAME>'. Ověřte, zda je povoleno spuštění diagnostiky pro tento virtuální počítač a máte přístup k tomuto účtu úložiště. | Přístup ke konzole sériového portu vyžaduje určitá oprávnění pro přístup. V tématu [přístup k požadavkům](#prerequisites) podrobnosti

## <a name="known-issues"></a>Známé problémy 
Jak jsme jsou stále ve fázích preview pro přístup ke konzole sériového portu, jsme pracují prostřednictvím některé známé problémy, níže je seznam těchto s možných řešení 

Problém                           |   Omezení rizik 
:---------------------------------|:--------------------------------------------|
Neexistuje žádná možnost pomocí konzoly sériového portu sady instance škálování pro virtuální počítač | V době preview přístup ke konzole sériového portu pro instancí sady škálování virtuálního počítače není podporován.
Stiskne zadejte po Banner informující o připojení nezobrazuje protokolu v řádku | [Stiskne zadejte se nic nestane.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Jenom informace o stavu se zobrazí při připojení k virtuální počítač s Windows| [Signály stavu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy 
**Q. Jak lze odeslat zpětnou vazbu?**

A. Poskytnutí zpětné vazby jako problém přechodem na https://aka.ms/serialconsolefeedback. Případně méně (upřednostňované) odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače http://feedback.azure.com 

**Q. Zobrazí chybová zpráva "existující konzoly má konfliktní typ operačního systému"Systém Windows"pomocí požadovaný typ operačního systému Linux?**

A. Jedná se o známý problém tento problém lze vyřešit jednoduše otevřít [prostředí cloudu Azure](https://docs.microsoft.com/azure/cloud-shell/overview) v režimu bash a zkuste to znovu.

**Q. Nejste schopni přistupovat konzole sériového portu, kde můžete soubor případu podpory?**

A. Tato funkce preview se zabývá prostřednictvím Azure Preview podmínky. Podpora pro tento nejlépe vyřeší prostřednictvím kanálů uvedených výše. 

## <a name="next-steps"></a>Další postup
* Je taky dostupná ke konzole sériového portu [Linux](../linux/serial-console.md) virtuální počítače
* Další informace o [bootdiagnostics](boot-diagnostics.md)
