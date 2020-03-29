---
title: Nasazení virtuálního počítače windows 7 Windows Virtual Desktop – Azure
description: Jak nakonfigurovat a nasadit virtuální počítač se systémem Windows 7 na virtuální ploše Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366679"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Nasazení virtuálního počítače s Windows 7 do Windows Virtual Desktopu

Proces nasazení virtuálního počítače (VM) systému Windows 7 na virtuální ploše windows je mírně odlišný než u virtuálních počítačů s novějšími verzemi systému Windows. Tato příručka vám řekne, jak nasadit Systém Windows 7.

## <a name="prerequisites"></a>Požadavky

Než začnete, postupujte podle pokynů v [části Vytvoření fondu hostitelů pomocí Prostředí PowerShell](create-host-pools-powershell.md) vytvořte fond hostitelů. Poté postupujte podle pokynů v části [Vytvoření fondů hostitelů na Azure Marketplace](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) a přiřaďte jednoho nebo více uživatelů ke skupině aplikací pro stolní počítače.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurace virtuálního počítače se systémem Windows 7

Jakmile skonte s požadavky, jste připraveni nakonfigurovat virtuální počítač se systémem Windows 7 pro nasazení ve Virtuální ploše Windows.

Nastavení virtuálního počítače se systémem Windows 7 na virtuální ploše Windows:

1. Přihlaste se k portálu Azure portal a vyhledejte bitovou kopii Windows 7 Enterprise nebo nahrajte vlastní přizpůsobenou bitovou kopii Windows 7 Enterprise (x64).  
2. Nasaďte jeden nebo více virtuálních počítačů s Windows 7 Enterprise jako hostitelským operačním systémem. Ujistěte se, že virtuální počítače umožňují protokol RDP (RDP) (port TCP/3389).
3. Připojte se k hostiteli systému Windows 7 Enterprise pomocí programu RDP a ověřte se pomocí pověření, která jste definovali při konfiguraci nasazení. 
4. Přidejte účet, který jste použili při připojování k hostiteli pomocí programu RDP, do skupiny Uživatel vzdálené plochy. Pokud tak neučiníte, možná se po připojení k doméně služby Active Directory nebudete moct připojit k virtuálnímu počítači.
5. Přejděte na webu Windows Update.
6. Nainstalujte všechny aktualizace systému Windows v kategorii Důležité.
7. Nainstalujte všechny aktualizace systému Windows do volitelné kategorie (s výjimkou jazykových sad). Tím nainstalujete aktualizaci protokolu Rd Desktop Protocol 8.0[(KB2592687),](https://www.microsoft.com/download/details.aspx?id=35387)kterou potřebujete k provedení těchto pokynů.
8. Otevřete Editor místních zásad skupiny a přejděte do části Šablony **pro správu konfigurace** > počítače**Součásti služby** > **Administrative Templates** > **Vzdálená plocha služby** > **Vzdálená plocha Hostitel** > **vzdálené relace Vzdálené relací Environment**.
9. Povolte zásady protokolu RDP 8.0.
10. Připojte tento virtuální počítač k doméně služby Active Directory.
11. Restartujte virtuální počítač spuštěním následujícího příkazu:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Postupujte podle pokynů [zde](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) získat registrační token.
13. [Stáhněte si windows virtuální desktop agent pro Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Stáhněte si Správce agenta virtuální plochy systému Windows pro systém Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Otevřete instalační program Windows Virtual Desktop Agent a postupujte podle pokynů. Po zobrazení výzvy přidejte registrační klíč, který jste vytvořili v kroku 12.
16. Otevřete instalační program virtuální plochy systému Windows a postupujte podle pokynů.
17. Volitelně můžete zablokovat port TCP/3389 a odebrat přímý přístup k virtuálnímu počítači protokolu RDP.

## <a name="next-steps"></a>Další kroky

Nasazení virtuální plochy systému Windows je nyní připraveno k použití. [Chcete-li začít, stáhněte si nejnovější verzi klienta Virtuální plochy systému Windows.](https://aka.ms/wvd/clients/windows)

Seznam známých problémů a pokynů pro řešení potíží pro Windows 7 ve službě Windows Virtual Desktop naleznete v našem článku pro řešení potíží [s virtuálními počítači se systémem Windows 7 v aplikaci Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
