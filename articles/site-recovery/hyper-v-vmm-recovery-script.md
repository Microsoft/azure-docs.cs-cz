---
title: Skript přidáte do plánu obnovení v Azure Site Recovery | Microsoft Docs
description: Další informace o požadavky pro přidání nový skript System Center Virtual Machine Manager (VMM) do plánu obnovení v Azure.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: shons
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 2e00f812fb35ac9a0cb390fc6a3ba40a8678f8dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402430"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Skript VMM přidat do plánu obnovení

Tento článek popisuje, jak vytvořit skript System Center Virtual Machine Manager (VMM) a přidejte ji do plánu obnovení v [Azure Site Recovery](site-recovery-overview.md).

POST jakékoli dotazy nebo připomínky na konci tohoto článku nebo na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Požadavky

Můžete použít skripty prostředí PowerShell v plánu obnovení. Byla přístupná z plánu obnovení, musíte vytvořit skript a umístěte skript do knihovny VMM. Při psaní skriptu, mít na paměti následující aspekty:

* Zkontrolujte, jestli skripty používají bloků try-catch tak, aby se elegantně ke zpracování výjimek.
    - Pokud dojde k výjimce ve skriptu, skript se zastaví a úloha ukazuje, jak se nezdařilo.
    - Pokud dojde k chybě, zbývající část skript nefunguje.
    - Pokud dojde k chybě, když spustíte neplánované převzetí služeb při selhání, pokračuje plánu obnovení.
    - Pokud dojde k chybě při spuštění plánovaného převzetí služeb při selhání, zastaví se plán obnovení. Opravte skript, zkontrolujte, zda pracuje podle očekávání a znovu spusťte obnovení znovu naplánujte.
        - `Write-Host` Příkaz nefunguje v skript plánu obnovení. Pokud použijete `Write-Host` příkaz ve skriptu, skript selže. Pokud chcete vytvořit výstup, vytvořte proxy skript, který zase spustí hlavního skriptu. K zajištění, že všechny je výstup, použijte  **\> \>**  příkaz.
        - Skript časového limitu, pokud není vrátit do 600 sekund.
        - Pokud se nic je zapsán do STDERR, skript je klasifikován jako neúspěšná. Tyto informace se zobrazí v podrobnostech provádění skriptu.

* V kontextu účtu služby VMM spustit skripty v plánu obnovení. Ujistěte se, že tento účet má oprávnění ke čtení pro vzdálené sdílené složce, ve kterém je uložený skript. Otestujte skript, který chcete spustit se stejnou úrovní uživatelských práv jako účet služby VMM.
* Rutin služby VMM se dodávají v modulu Windows PowerShell. Modul je nainstalován při instalaci konzole VMM. Pokud chcete načíst modul do vašeho skriptu, použijte následující příkaz ve skriptu: 

    `Import-Module -Name virtualmachinemanager`

    Další informace najdete v tématu [začít pracovat s prostředí Windows PowerShell a VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Ujistěte se, že máte alespoň jeden server knihovny ve vašem nasazení VMM. Ve výchozím nastavení cesta ke sdílené složce knihovny pro VMM server nachází místně na serveru VMM. MSCVMMLibrary je název složky.

  Pokud vaše cesta ke sdílené složce knihovny vzdálené (nebo pokud je místní, ale není sdílený s MSCVMMLibrary), sdílenou složku nakonfigurovat následujícím způsobem pomocí \\libserver2.contoso.com\share\ jako příklad:
  
  1. Otevřete Editor registru a potom přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure lokality Recovery\Registration**.

  2. Změňte hodnotu **ScriptLibraryPath** k  **\\\libserver2.contoso.com\share\\**. Zadejte úplný plně kvalifikovaný název domény. Zadejte oprávnění k umístění sdílené složky. Toto je kořenový uzel sdílené složky. Zkontrolujte kořenového uzlu, v nástroji VMM, přejděte do kořenového uzlu v knihovně. Cesta, která otevře je kořen cesty. To je cesta, kterou musíte použít v proměnné.

  3. Skript otestujte pomocí uživatelského účtu, který má stejnou úroveň uživatelská práva jako účet služby VMM. Pomocí těchto uživatelských práv ověřuje, že samostatné otestované skripty spusťte stejným způsobem, které poběží v plánech obnovení. Na serveru VMM nastavte zásady spouštění obejít následujícím způsobem:

     a. Otevřete **64bitová verze Windows PowerShell** konzoly jako správce.
     
     b. Zadejte **nepoužívat Set-executionpolicy**. Další informace najdete v tématu [pomocí rutiny Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Nastavit **Set-executionpolicy nepoužívat** pouze v konzole PowerShell 64-bit. Pokud jste nastavili pro konzolu prostředí PowerShell 32-bit, nejsou spuštěny skripty.

## <a name="add-the-script-to-the-vmm-library"></a>Přidejte skript do knihovny VMM

Pokud máte VMM zdrojovou lokalitu, můžete vytvořit skript na serveru VMM. Pak musíte uvést skript v plánu obnovení.

1. Ve sdílené složce knihovny vytvořte novou složku. Například \<název serveru VMM > \MSSCVMMLibrary\RPScripts. Umístit složku na zdrojové a cílové servery VMM.
2. Vytvořte skript. Například název skriptu RPScript. Ověřte, že skript funguje podle očekávání.
3. Umístěte skript v \<název serveru VMM > \MSSCVMMLibrary složky na zdrojové a cílové servery VMM.

## <a name="add-the-script-to-a-recovery-plan"></a>Přidejte skript do plánu obnovení

Po přidání virtuálních počítačů nebo skupiny replikace do plánu obnovení a vytvoření plánu, můžete přidat skript do skupiny.

1. Otevřete plánu obnovení.
2. V **krok** seznamu, vyberte položku. Pak vyberte buď **skriptu** nebo **ruční akce**.
3. Určete, zda chcete přidat skript nebo akce před nebo po vybranou položku. Pozice skript přesunout nahoru nebo dolů, vyberte **nahoru** a **přesunout dolů** tlačítka.
4. Pokud chcete přidat skript VMM, vyberte **převzetí služeb při selhání do skriptu VMM**. V **cestu ke skriptu**, zadejte relativní cestu ke sdílené složce. Zadejte například **\RPScripts\RPScript.PS1**.
5. Pokud přidáte runbook služby automatizace Azure, zadejte účet Automation, ve kterém je sada runbook. Potom vyberte skript runbooku Azure, který chcete použít.
6. Aby se zajistilo, že skript funguje podle očekávání, proveďte testovací převzetí služeb při selhání plánu obnovení.


## <a name="next-steps"></a>Další postup
* Další informace o [systémem převzetí služeb při selhání](site-recovery-failover.md).

