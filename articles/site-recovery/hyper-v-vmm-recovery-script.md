---
title: Přidání skriptu do plánu obnovení v Azure Site Recovery
description: Zjistěte, jak přidat skript VMM do plánu obnovení pro zotavení po havárii virtuálních virtuálních měn Hyper-V v cloudech.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084876"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Přidání skriptu VMM do plánu obnovení

Tento článek popisuje, jak vytvořit skript Správce virtuálních strojů system center (VMM) a přidat ho do plánu obnovení v [Azure Site Recovery](site-recovery-overview.md).

Komentáře nebo dotazy zveřejněte v dolní části tohoto článku nebo na [fóru Služby Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Požadavky

Skripty prostředí PowerShell můžete použít ve svých plánech obnovení. Chcete-li být přístupné z plánu obnovení, musíte vytvořit skript a umístit skript do knihovny VMM. Při psaní skriptu mějte na paměti následující skutečnosti:

* Ujistěte se, že skripty používají try-catch bloky, tak, aby výjimky jsou zpracovány řádně.
    - Pokud dojde k výjimce ve skriptu, skript se zastaví a úloha se zobrazí jako neúspěšná.
    - Pokud dojde k chybě, zbytek skriptu nelze spustit.
    - Pokud dojde k chybě při spuštění neplánované převzetí služeb při selhání, plán obnovení pokračuje.
    - Pokud dojde k chybě při spuštění plánované převzetí služeb při selhání, plán obnovení se zastaví. Opravte skript, zkontrolujte, zda je spuštěn podle očekávání, a pak znovu spusťte plán obnovení.
        - Příkaz `Write-Host` nefunguje ve skriptu plánu obnovení. Pokud použijete `Write-Host` příkaz ve skriptu, skript se nezdaří. Chcete-li vytvořit výstup, vytvořte proxy skript, který zase spustí hlavní skript. Chcete-li zajistit, aby byl veškerý ** \> ** výstup odveden, použijte příkaz.
        - Pokud se skript nevrátí do 600 sekund, dojde k jeho vypršení.
        - Pokud je něco zapsáno do STDERR, skript je klasifikován jako neúspěšný. Tyto informace se zobrazí v podrobnostech spuštění skriptu.

* Skripty v plánu obnovení jsou spuštěny v kontextu účtu služby VMM. Ujistěte se, že tento účet má oprávnění ke čtení pro vzdálenou sdílenou složku, na které je skript umístěn. Otestujte spuštění skriptu se stejnou úrovní uživatelských práv jako účet služby VMM.
* Rutiny VMM jsou dodávány v modulu prostředí Windows PowerShell. Modul je nainstalován při instalaci konzoly VMM. Chcete-li modul načíst do skriptu, použijte ve skriptu následující příkaz: 

    `Import-Module -Name virtualmachinemanager`

    Další informace naleznete [v tématu Začínáme s prostředím Windows PowerShell a VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Ujistěte se, že máte alespoň jeden server knihovny v nasazení VMM. Ve výchozím nastavení je cesta ke sdílení knihovny pro server VMM umístěna místně na serveru VMM. Název složky je MSCVMMLibrary.

  Pokud je cesta ke sdílení knihovny vzdálená (nebo je místní, ale nesdílená s knihovnou \\MSCVMMLibrary), nakonfigurujte sdílenou složku následujícím způsobem pomocí libserver2.contoso.com\share\jako příklad:
  
  1. Otevřete Editor registru a přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Změňte hodnotu **aplikace ScriptLibraryPath** na ** \\\libserver2.contoso.com\share\\**. Zadejte úplný plně kvalifikovaný kvit. Poskytněte oprávnění k umístění sdílené složky. Toto je kořenový uzel sdílené složky. Chcete-li zkontrolovat kořenový uzel, přejděte v v knihovně do kořenového uzlu. Cesta, která se otevře, je kořen cesty. Toto je cesta, kterou je nutné použít v proměnné.

  1. Otestujte skript pomocí uživatelského účtu, který má stejnou úroveň uživatelských práv jako účet služby VMM. Pomocí těchto uživatelských práv ověří, že samostatné, testované skripty spustit stejným způsobem, že jsou spuštěny v plánech obnovení. Na serveru VMM nastavte zásady spuštění obejít, a to takto:

     a. Otevřete **64bitovou** konzolu prostředí Windows PowerShell jako správce.
     
     b. Zadejte **obejít zásadu spuštění sady**. Další informace naleznete [v tématu Použití rutiny Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Nastavte **zásady spuštění sady pouze** v 64bitové konzoli PowerShell. Pokud ji nastavíte pro 32bitovou konzolu PowerShell, skripty se nespustí.

## <a name="add-the-script-to-the-vmm-library"></a>Přidání skriptu do knihovny VMM

Pokud máte zdrojový web VMM, můžete vytvořit skript na serveru VMM. Potom zahrňte skript do plánu obnovení.

1. Ve sdílené složce knihovny vytvořte novou složku. Například \<název serveru VMM>\MSSCVMLibrary\RPScripts. Umístěte složku na zdrojové a cílové servery VMM.
1. Vytvořte skript. Pojmenujte například skript RPScript. Ověřte, zda skript funguje podle očekávání.
1. Umístěte skript do \<názvu serveru VMM>\MSSCVMLibrary na zdrojové a cílové servery VMM.

## <a name="add-the-script-to-a-recovery-plan"></a>Přidání skriptu do plánu obnovení

Po přidání virtuálních účtů nebo replikačních skupin do plánu obnovení a vytvoření plánu můžete přidat skript do skupiny.

1. Otevřete plán obnovení.
1. V seznamu **Krok** vyberte položku. Potom vyberte **skript** nebo **ruční akci**.
1. Určete, zda chcete přidat skript nebo akci před nebo za vybranou položku. Chcete-li přesunout pozici skriptu nahoru nebo dolů, vyberte tlačítka **Přesunout nahoru** a **Přesunout dolů.**
1. Pokud přidáte skript VMM, vyberte **možnost Převzetí služeb při selhání skriptu VMM**. V **části Cesta skriptu**zadejte relativní cestu ke sdílené položce. Zadejte například **\RPScripts\RPScript.PS1**.
1. Pokud přidáte runbook Azure Automation, zadejte účet Automation, ve kterém je umístěn. Potom vyberte skript runbooku Azure, který chcete použít.
1. Chcete-li zajistit, aby skript fungoval podle očekávání, proveďte zkušební převzetí služeb při selhání plánu obnovení.


## <a name="next-steps"></a>Další kroky
* Další informace o [spuštění převzetí služeb při selhání](site-recovery-failover.md).

