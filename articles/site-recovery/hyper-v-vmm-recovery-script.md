---
title: Přidání skriptu do plánu obnovení v Azure Site Recovery
description: Naučte se, jak přidat skript VMM do plánu obnovení pro zotavení po havárii virtuálních počítačů Hyper-V v cloudech VMM.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: 3217c30737a133c1c1092fc4a8a8caaa0338e980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425870"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Přidání skriptu VMM do plánu obnovení

Tento článek popisuje, jak vytvořit skript System Center Virtual Machine Manager (VMM) a přidat ho do plánu obnovení v [Azure Site Recovery](site-recovery-overview.md).

Všechny komentáře nebo dotazy můžete vystavit na konci tohoto článku nebo na [stránce s dotazem Microsoft Q&pro Azure Recovery Services](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Požadavky

V plánech obnovení můžete použít skripty prostředí PowerShell. Aby bylo možné získat přístup z plánu obnovení, je nutné vytvořit skript a umístit skript do knihovny VMM. Při psaní skriptu mějte na paměti následující skutečnosti:

* Zajistěte, aby skripty používaly bloky try-catch, aby byly výjimky zpracovávány řádným způsobem.
    - Pokud ve skriptu dojde k výjimce, skript se zastaví a úloha se zobrazí jako neúspěšná.
    - Pokud dojde k chybě, zbývající část skriptu se nespustí.
    - Pokud dojde k chybě při spuštění neplánovaného převzetí služeb při selhání, plán obnovení pokračuje.
    - Pokud při spuštění plánovaného převzetí služeb při selhání dojde k chybě, plán obnovení se zastaví. Opravte skript, zkontrolujte, že funguje podle očekávání, a pak znovu spusťte plán obnovení.
        - `Write-Host`Příkaz nefunguje ve skriptu plánu obnovení. Pokud použijete `Write-Host` příkaz ve skriptu, skript se nezdařil. Pokud chcete vytvořit výstup, vytvořte skript proxy, který zase spustí hlavní skript. Chcete-li zajistit, aby byl veškerý výstup z kanálu, použijte **\>\>** příkaz.
        - Vyprší časový limit skriptu, pokud se nevrátí do 600 sekund.
        - Pokud je do STDERR zapisována žádná z nich, je skript klasifikován jako neúspěšný. Tyto informace se zobrazí v podrobnostech spuštění skriptu.

* Skripty v plánu obnovení běží v kontextu účtu služby VMM. Zajistěte, aby měl tento účet oprávnění ke čtení pro vzdálenou sdílenou složku, na které je umístěný skript. Otestujte skript, který se má spustit se stejnou úrovní uživatelských práv jako účet služby VMM.
* Rutiny VMM se dodávají v modulu Windows PowerShellu. Modul se nainstaluje při instalaci konzoly VMM. Pokud chcete modul načíst do skriptu, použijte ve skriptu následující příkaz: 

    `Import-Module -Name virtualmachinemanager`

    Další informace najdete v tématu [Začínáme s prostředím Windows PowerShell a nástrojem VMM](/previous-versions/system-center/system-center-2012-R2/hh875013(v=sc.12)).
* Ujistěte se, že máte ve svém nasazení VMM aspoň jeden server knihovny. Ve výchozím nastavení je cesta ke sdílené složce knihovny pro server VMM místně umístěná na serveru VMM. Název složky je MSCVMMLibrary.

  Pokud je vaše cesta ke sdílené složce knihovny vzdálená (nebo pokud je místní, ale není sdílená s MSCVMMLibrary), nakonfigurujte sdílenou složku následujícím způsobem, a \\ to pomocí libserver2. contoso. com\share\ jako příklad:
  
  1. Otevřete Editor registru a pak pokračujte na **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Změňte hodnotu **ScriptLibraryPath** na **\\ \libserver2.contoso.com\share \\**. Zadejte úplný plně kvalifikovaný název domény. Zadejte oprávnění k umístění sdílené složky. Toto je kořenový uzel sdílené složky. Chcete-li kontrolovat kořenový uzel, v nástroji VMM, přejít do kořenového uzlu v knihovně. Cesta, která se otevře, je kořenem cesty. Toto je cesta, kterou je nutné použít v proměnné.

  1. Otestujte skript pomocí uživatelského účtu, který má stejnou úroveň uživatelských práv jako účet služby VMM. Pomocí těchto uživatelských práv ověříte, že tyto samostatné testované skripty běží stejným způsobem jako v plánech obnovení. Na serveru VMM nastavte zásady spouštění na vynechat následujícím způsobem:

     a. Otevřete konzolu **prostředí Windows PowerShell 64** jako správce.
     
     b. Zadejte **Set-ExecutionPolicy bypass**. Další informace najdete v tématu [použití rutiny Set-ExecutionPolicy](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)).

     > [!IMPORTANT]
     > Nastavte **vynechání Set-ExecutionPolicy** pouze v konzole powershellu 64. Pokud ho nastavíte pro konzoli PowerShellu 32, skripty se nespustí.

## <a name="add-the-script-to-the-vmm-library"></a>Přidání skriptu do knihovny VMM

Pokud máte zdrojovou lokalitu VMM, můžete vytvořit skript na serveru VMM. Pak do svého plánu obnovení zahrňte skript.

1. Ve sdílené složce knihovny vytvořte novou složku. Například \<VMM server name> \MSSCVMMLibrary\RPScripts. Umístěte složku na zdrojový a cílový server VMM.
1. Vytvořte skript. Například název skriptu RPScript. Ověřte, že skript funguje podle očekávání.
1. Umístěte skript do \<VMM server name> složky \MSSCVMMLibrary na zdrojovém a cílovém serveru VMM.

## <a name="add-the-script-to-a-recovery-plan"></a>Přidání skriptu do plánu obnovení

Po přidání virtuálních počítačů nebo skupin replikace do plánu obnovení a vytvoření plánu můžete do skupiny přidat skript.

1. Otevřete plán obnovení.
1. V seznamu **Krok** vyberte položku. Pak vyberte buď **skript** , nebo **Ruční akce**.
1. Určete, zda se má přidat skript nebo akce před nebo za vybranou položkou. Chcete-li přesunout pozici skriptu nahoru nebo **dolů, vyberte tlačítko** nahoru **a dolů** .
1. Pokud přidáte skript VMM, vyberte **převzetí služeb při selhání do skriptu VMM**. Do pole **cesta ke skriptu** zadejte relativní cestu ke sdílené složce. Zadejte například **\RPScripts\RPScript.PS1**.
1. Pokud přidáte Azure Automation Runbook, zadejte účet Automation, ve kterém se sada Runbook nachází. Pak vyberte skript Azure Runbook, který chcete použít.
1. Chcete-li zajistit, že skript funguje podle očekávání, proveďte test převzetí služeb při selhání plánu obnovení.


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [spuštění převzetí služeb při selhání](site-recovery-failover.md).

