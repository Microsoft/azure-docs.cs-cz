---
title: Azure Site Recovery řešení potíží z VMware do Azure | Dokumentace Microsoftu
description: Řešení chyb při replikaci virtuálních počítačů Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 07/06/2018
ms.openlocfilehash: 8d5db03eeebb659414ea1f554e5b34c938fd2795
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952905"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Řešení potíží s nabízenou instalací služby Mobility

Tento článek popisuje, jak řešit běžné chyby, které může setkat při pokusu o instalaci služby Mobility Azure Site Recovery na zdrojovém serveru pro povolení ochrany.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Chyba 78007 – požadovanou operaci nešlo dokončit.
Tato chyba může být vyvolána službou z několika důvodů. Zvolte řešení potíží s další odpovídající chybě zprostředkovatele.

* [Chyba 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Chyba 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Chyba 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Chyba 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Chyba 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Chyba 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Chyba 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Chyba 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Chyba 95105 - ochranu nebylo možné povolené (EP0856)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95105 </br>**Zpráva:** nabízenou instalaci služby Mobility na zdrojovém počítači se nezdařilo s kódem chyby **EP0856**. <br> Buď **sdílení souborů a tiskáren** není povolena na zdrojovém počítači nebo že jsou síťové potíže s připojením mezi procesovým serverem a zdrojovým počítačem.| **Sdílení souborů a tiskáren** není povolená. | Povolit **sdílení souborů a tiskáren** na zdrojovém počítači v bráně Windows Firewall. Na zdrojovém počítači v části **brány Windows Firewall** > **povolit aplikaci nebo funkci průchod bránou Firewall**vyberte **sdílení souborů a tiskáren pro všechny profily**. </br> Kromě toho zkontrolujte následující požadavky pro úspěšné dokončení nabízené instalace.<br> Další informace o [řešení potíží s WMI vydá](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Chyba 95107 - ochranu nebylo možné povolené (EP0858)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95107 </br>**Zpráva:** nabízenou instalaci služby Mobility na zdrojovém počítači se nezdařilo s kódem chyby **EP0858**. <br> Buď nejsou správné přihlašovací údaje zadané pro instalaci služby Mobility nebo uživatelský účet nemá dostatečná oprávnění. | Pověření uživatele zadané pro instalaci služby Mobility na zdrojovém počítači nejsou správné. | Ujistěte se, že jsou správné přihlašovací údaje uživatele k dispozici u tohoto zdrojového počítače na konfiguračním serveru. <br> Můžete přidat nebo upravit přihlašovací údaje uživatele, přejděte na konfigurační server a vyberte **Cspsconfigtool** > **spravovat účet**. </br> Kromě toho zkontrolujte následující [požadavky](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) úspěšné dokončení nabízené instalace.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Chyba 95117 - ochranu nebylo možné povolené (EP0865)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95117 </br>**Zpráva:** nabízenou instalaci služby Mobility na zdrojovém počítači se nezdařilo s kódem chyby **EP0865**. <br> Buď zdrojový počítač není spuštěný nebo jsou mezi procesovým serverem a zdrojovým počítačem problémy s připojením k síti. | Problémy s připojením k síti mezi procesovým serverem a zdrojovém serveru. | Zkontrolujte připojení mezi procesovým serverem a zdrojovém serveru. </br> Kromě toho zkontrolujte následující [požadavky](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) úspěšné dokončení nabízené instalace.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Chyba 95103 - ochranu nebylo možné povolené (EP0854)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95103 </br>**Zpráva:** nabízenou instalaci služby Mobility na zdrojovém počítači se nezdařilo s kódem chyby **EP0854**. <br> Na zdrojovém počítači není povolená Windows Management Instrumentation (WMI) nebo existují problémy s připojením k síti mezi procesovým serverem a zdrojovým počítačem.| Rozhraní WMI je blokována v bráně Windows Firewall. | Povolit rozhraní WMI v bráně Windows Firewall. V části **brány Windows Firewall** > **povolit aplikaci nebo funkci průchod bránou Firewall**vyberte **rozhraní WMI pro všechny profily**. </br> Kromě toho zkontrolujte následující [požadavky](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) úspěšné dokončení nabízené instalace.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Chyba 95213 - ochranu nebylo možné povolené (EP0874)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95213 </br>**Zpráva:** instalace služby Mobility na zdrojový počítač % SourceIP; selhalo s kódem chyby **EP0874**. <br> | Verze operačního systému na zdrojovém počítači není podporovaná. <br>| Ujistěte se, že zdrojový počítač je podporována verze operačního systému. Přečtěte si [systém podpory replikace z](https://aka.ms/asr-os-support). </br> Kromě toho zkontrolujte následující [požadavky](https://aka.ms/pushinstallerror) úspěšné dokončení nabízené instalace.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Chyba 95108 - ochranu nebylo možné povolené (EP0859)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95108 </br>**Zpráva:** nabízenou instalaci služby Mobility na zdrojovém počítači se nezdařilo s kódem chyby **EP0859**. <br>| Buď nejsou správné přihlašovací údaje zadané pro instalaci služby Mobility nebo uživatelský účet nemá dostatečná oprávnění. <br>| Ujistěte se, že jsou zadané přihlašovací údaje **kořenové** přihlašovací údaje účtu. Můžete přidat nebo upravit přihlašovací údaje uživatele, přejděte na konfigurační server a vyberte **Cspsconfigtool** ikonu zástupce na ploše. Vyberte **spravovat účet** můžete přidat nebo upravit přihlašovací údaje.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Chyba 95265 - ochranu nebylo možné povolené (EP0902)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95265 </br>**Zpráva:** nabízenou instalaci služby Mobility na zdrojovém počítači byla úspěšná, ale na zdrojovém počítači vyžaduje restartování pro některé změny systému, než se projeví. <br>| Starší verze služby Mobility byl již nainstalován na serveru.| Replikace virtuálního počítače bude bez problémů pokračovat.<br> Restartujte server během dalšího období údržby získáte výhody nových vylepšení služby Mobility.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Chyba 95224 - ochranu nebylo možné povolené (EP0883)

**Kód chyby:** | **Možné příčiny** | **Chyba konkrétní doporučení**
--- | --- | ---
95224 </br>**Zpráva:** nabízená instalace služby Mobility na zdrojovém počítači % SourceIP; nebyla úspěšná, kód chyby **EP0883**. Restart systému z předchozí instalace nebo aktualizace čeká na vyřízení.| Při odinstalaci starší nebo nekompatibilní verze služby Mobility nebyla restartování systému.| Ujistěte se, že neexistuje žádná verze služby Mobility na serveru. <br> Restartujte server a spusťte znovu úlohu zapnutí ochrany.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Prostředek k odstraňování problémů instalace push

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Řešení potíží s souboru a tisku sdílení problémy
* [Povolení nebo zakázání sdílení se zásadami skupiny souborů](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Povolit sdílení souborů a tiskáren přes bránu Windows Firewall](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Řešení potíží s WMI
* [Základní testování služby WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Řešení problémů WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Poradce při potížích se skripty WMI a služby rozhraní WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](vmware-azure-tutorial.md) nastavit zotavení po havárii pro virtuální počítače VMware.