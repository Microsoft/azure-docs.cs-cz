---
title: Nasazení virtuálního počítače se systémem Windows 7 – Azure
description: Jak nakonfigurovat a nasadit virtuální počítač se systémem Windows 7 na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
ms.openlocfilehash: 90b871c2b75f7ed40c290231ef822258c6b4e6d4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606872"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Nasazení virtuálního počítače s Windows 7 do Windows Virtual Desktopu

Proces nasazení virtuálního počítače s Windows 7 na virtuálním počítači s Windows se trochu liší od virtuálních počítačů, na kterých běží novější verze Windows. V této příručce se dozvíte, jak nasadit Windows 7.

## <a name="prerequisites"></a>Požadavky

Než začnete, vytvořte fond hostitelů podle pokynů v tématu [Vytvoření fondu hostitelů s prostředím PowerShell](create-host-pools-powershell.md) . Potom postupujte podle pokynů v tématu [vytvoření fondů hostitelů v nástroji Azure Marketplace](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) k přiřazení jednoho nebo více uživatelů do skupiny desktopových aplikací.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurace virtuálního počítače s Windows 7

Po dokončení požadovaných součástí jste připraveni ke konfiguraci virtuálního počítače se systémem Windows 7 pro nasazení na virtuálním počítači s Windows.

Postup nastavení virtuálního počítače se systémem Windows 7 na virtuálním počítači s Windows:

1. Přihlaste se k Azure Portal a vyhledejte bitovou kopii systému Windows 7 Enterprise nebo nahrajte vlastní přizpůsobenou bitovou kopii systému Windows 7 Enterprise (x64).  
2. Nasaďte jeden nebo několik virtuálních počítačů se systémem Windows 7 Enterprise jako hostitelský operační systém. Zajistěte, aby virtuální počítače povolovaly protokol RDP (Remote Desktop Protocol) (RDP) (port TCP/3389).
3. Připojte se k hostiteli se systémem Windows 7 pomocí protokolu RDP a proveďte ověření pomocí přihlašovacích údajů, které jste definovali při konfiguraci nasazení. 
4. Přidejte účet, který jste použili při připojování k hostiteli pomocí protokolu RDP, do skupiny Remote Desktop User. Pokud to neuděláte, možná se nebudete moct připojit k virtuálnímu počítači po jeho připojení k doméně služby Active Directory.
5. Na svém VIRTUÁLNÍm počítači přejdete na web Windows Update.
6. Nainstalujte všechny aktualizace Windows v důležité kategorii.
7. Nainstalovat všechny aktualizace systému Windows v nepovinné kategorii (kromě jazykových sad). Tím se nainstaluje aktualizace protokol RDP (Remote Desktop Protocol) 8,0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35393)), kterou potřebujete k dokončení těchto pokynů.
8. Otevřete Editor místních zásad skupiny a přejděte na **Konfigurace počítače** > **šablony pro správu** > **součásti systému Windows** > **Vzdálená plocha** > **hostitel relace vzdálené plochy**  > **prostředí vzdálené relace**.
9. Povolte zásadu protokol RDP (Remote Desktop Protocol) 8,0.
10. Restartujte virtuální počítač spuštěním následujícího příkazu:
    
     ```cmd
     shutdown /r /t 0
     ```
    
11. Pokud chcete získat registrační token, postupujte podle pokynů uvedených [tady](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo) .
12. [Stáhněte si agenta virtuální plochy Windows pro Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
13. [Stáhněte správce agenta virtuálních počítačů s Windows pro Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
14. Spusťte instalační program agenta virtuálních počítačů s Windows a postupujte podle pokynů. Po zobrazení výzvy zadejte registrační klíč, který jste vytvořili v kroku 11.
15. Spusťte instalační program virtuálních klientů Windows a postupujte podle pokynů.
16. Volitelně můžete port TCP/3389 zablokovat, aby se odebral přímý protokol RDP (Remote Desktop Protocol) přístup k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

Nasazení virtuálního počítače s Windows je teď připravené k použití. Abyste mohli začít, [Stáhněte si nejnovější verzi klienta virtuální plochy Windows](https://aka.ms/wvd/clients/windows) .

Seznam známých problémů a pokyny k řešení potíží pro Windows 7 na virtuálním počítači s Windows najdete v našem článku věnovaném řešení potíží při [řešení potíží s virtuálními počítači s Windows 7 ve virtuálním počítači](troubleshoot-windows-7-vm.md)s Windows.
