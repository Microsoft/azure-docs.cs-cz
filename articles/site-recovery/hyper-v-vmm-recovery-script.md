---
title: Přidat skript do plánu obnovení ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Další informace o požadavky pro přidání nový skript System Center Virtual Machine Manager (VMM) do plánu obnovení v Azure.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: rochakm
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 71991347ffaf036065aae9e1a93b7eb83a14b15c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917321"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Skript přidáte tak nástroj VMM do plánu obnovení

Tento článek popisuje, jak vytvořit skript, System Center Virtual Machine Manager (VMM) a přidat do plánu obnovení v [Azure Site Recovery](site-recovery-overview.md).

Jakékoli dotazy nebo připomínky můžete publikovat na konci tohoto článku nebo na [fórum služby Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Požadavky

Pomocí skriptů Powershellu v plánech obnovení. Byla přístupná z plánu obnovení, musíte vytvořit skript a umístěte skript do knihovny VMM. Při psaní skriptu, mít na paměti následující aspekty:

* Ujistěte se, že skripty používají try-catch – bloky tak, aby řádně zpracování výjimek.
    - Pokud dojde k výjimce ve skriptu, skript se zastaví a úloha se zobrazí jako neúspěšný.
    - Pokud dojde k chybě, zbývající část skriptu se nespustí.
    - Pokud dojde k chybě, když spustíte neplánované převzetí služeb při selhání, bude pokračovat plánu obnovení.
    - Pokud dojde k chybě při spuštění plánovaného převzetí služeb při selhání, zastaví se plán obnovení. Opravte skript, zkontrolujte, že pracuje podle očekávání a potom spusťte znovu plánu obnovení.
        - `Write-Host` Příkaz nefunguje v skript plánu obnovení. Pokud používáte `Write-Host` příkaz ve skriptu, skript selže. Pokud chcete vytvořit výstup, vytvořte skript proxy serveru, na kterém běží zase hlavního skriptu. K zajištění, že veškerý výstup je přesměrovaná použijte **\> \>** příkazu.
        - Skript vyprší časový limit, pokud nevrací 600 sekund.
        - Pokud se nic se zapisují do výstupu STDERR, skript je klasifikován jako neúspěšný. Tyto informace se zobrazují v podrobnosti spuštění skriptu.

* V kontextu účtu služby VMM spustit skripty v plánu obnovení. Ujistěte se, že tento účet má oprávnění ke čtení pro vzdálené sdílené složce, ve kterém se skript nachází. Testovací skript ke spuštění se stejnou úrovní uživatelských práv jako účet služby VMM.
* V modulu Windows PowerShell se doručují rutin služby VMM. Modul je nainstalován při instalaci konzole VMM. Načtení modulu do vašeho skriptu, použijte následující příkaz ve skriptu: 

    `Import-Module -Name virtualmachinemanager`

    Další informace najdete v tématu [prostředí Windows PowerShell a nástroj VMM vám začít](https://technet.microsoft.com/library/hh875013.aspx).
* Ujistěte se, že máte alespoň jeden server knihovny ve vašem nasazení nástroje VMM. Ve výchozím nastavení cesta ke sdílené složce knihovny pro VMM server nachází místně na serveru VMM. Název složky je MSCVMMLibrary.

  Pokud vaše cesta ke sdílené složce knihovny je vzdálené (nebo zda je místní, ale nikoli sdíleným s MSCVMMLibrary), nakonfigurujte sdílenou složku následujícím způsobem pomocí \\libserver2.contoso.com\share\ jako příklad:
  
  1. Otevřete Editor registru a přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure lokality Recovery\Registration**.

  2. Změňte hodnotu **ScriptLibraryPath** k  **\\\libserver2.contoso.com\share\\**. Zadejte úplný plně kvalifikovaný název domény. Zadejte oprávnění k umístění sdílené složky. Toto je kořenový uzel sdílené složky. Ke kontrole kořenový uzel v nástroji VMM, přejděte do kořenového uzlu v knihovně. Cesta, která se otevře je kořenovou cestu. Toto je cesta, která je nutné použít v proměnné.

  3. Testování skriptu pomocí uživatelského účtu, který má stejnou úroveň uživatelská práva jako účet služby VMM. Pomocí těchto uživatelských práv ověří poskytovaných samostatnou otestované skripty spustit stejným způsobem, který se spustí v plánech obnovení. Na serveru VMM nastavte zásady spouštění obejít, následujícím způsobem:

     a. Otevřít **64bitová verze Windows Powershellu** konzoly jako správce.
     
     b. Zadejte **Set-executionpolicy obejít**. Další informace najdete v tématu [pomocí rutiny Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Nastavte **Set-executionpolicy obejít** pouze v konzole Powershellu 64-bit. Pokud ji nastavíte pro konzolu Powershellu 32-bit, nejsou spuštěny skripty.

## <a name="add-the-script-to-the-vmm-library"></a>Přidat skript do knihovny VMM

Pokud máte ve zdrojové lokalitě nástroje VMM, můžete vytvořit skript na serveru VMM. Pak musíte uvést skript v plánu obnovení.

1. Ve sdílené složce knihovny vytvořte novou složku. Například \<název serveru VMM > \MSSCVMMLibrary\RPScripts. Umístění složky na zdrojové a cílové servery VMM.
2. Vytvořte skript. Například zadejte název skriptu RPScript. Ověřte, že skript funguje podle očekávání.
3. Skript v umístit \<název serveru VMM > \MSSCVMMLibrary složky na zdrojové i cílové servery VMM.

## <a name="add-the-script-to-a-recovery-plan"></a>Přidat skript do plánu obnovení

Po přidání virtuálních počítačů nebo skupiny replikace do plánu obnovení a vytvořili plán, můžete přidat skript do skupiny.

1. Otevřete plán obnovení.
2. V **krok** seznamu, vyberte nějakou položku. Vyberte buď **skript** nebo **ruční akce**.
3. Zadejte, jestli se má přidat skript nebo akce před nebo po ní vybranou položku. Umístění skriptu přesunout nahoru nebo dolů, vyberte **nahoru** a **přesunout dolů** tlačítka.
4. Pokud chcete přidat skript VMM, vyberte **převzetí služeb při selhání skriptu VMM**. V **cesta ke skriptu**, zadejte relativní cestu ke sdílené složce. Zadejte například **\RPScripts\RPScript.PS1**.
5. Pokud chcete přidat runbook Azure Automation, zadejte účet služby Automation, ve kterém se nachází sadě runbook. Potom vyberte skript runbooku Azure, který chcete použít.
6. Aby bylo zajištěno, že skript funguje podle očekávání, proveďte testovací převzetí služeb při selhání plánu obnovení.


## <a name="next-steps"></a>Další postup
* Další informace o [spuštění převzetí služeb při selhání](site-recovery-failover.md).

