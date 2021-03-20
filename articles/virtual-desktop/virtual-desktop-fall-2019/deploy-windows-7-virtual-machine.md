---
title: Nasazení virtuálního počítače se systémem Windows 7 (Classic) – Azure
description: Jak nakonfigurovat a nasadit virtuální počítač se systémem Windows 7 na virtuálním počítači Windows Virtual Desktop na Windows (Classic)
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 259e49fbdd6a0eb392ddf6a3cd3c318798cfabd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005064"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>Nasazení virtuálního počítače s Windows 7 na virtuálním počítači s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se pokoušíte spravovat Azure Resource Manager objekty virtuálních klientů Windows zavedené v aktuální verzi virtuálního klienta Windows, přečtěte si [Tento článek](../deploy-windows-7-virtual-machine.md).

Proces nasazení virtuálního počítače s Windows 7 na virtuálním počítači s Windows se trochu liší od virtuálních počítačů, na kterých běží novější verze Windows. V této příručce se dozvíte, jak nasadit Windows 7.

## <a name="prerequisites"></a>Předpoklady

Než začnete, vytvořte fond hostitelů podle pokynů v tématu [Vytvoření fondu hostitelů s prostředím PowerShell](create-host-pools-powershell-2019.md) . Potom postupujte podle pokynů v tématu [vytvoření fondů hostitelů v nástroji Azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) k přiřazení jednoho nebo více uživatelů do skupiny desktopových aplikací.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurace virtuálního počítače s Windows 7

Po dokončení požadovaných součástí jste připraveni ke konfiguraci virtuálního počítače se systémem Windows 7 pro nasazení na virtuálním počítači s Windows.

Postup nastavení virtuálního počítače se systémem Windows 7 na virtuálním počítači s Windows:

1. Přihlaste se k Azure Portal a vyhledejte bitovou kopii systému Windows 7 Enterprise nebo nahrajte vlastní přizpůsobenou bitovou kopii systému Windows 7 Enterprise (x64).
2. Nasaďte jeden nebo několik virtuálních počítačů se systémem Windows 7 Enterprise jako hostitelský operační systém. Zajistěte, aby virtuální počítače povolovaly protokol RDP (Remote Desktop Protocol) (RDP) (port TCP/3389).
3. Připojte se k hostiteli se systémem Windows 7 pomocí protokolu RDP a proveďte ověření pomocí přihlašovacích údajů, které jste definovali při konfiguraci nasazení.
4. Přidejte účet, který jste použili při připojování k hostiteli pomocí protokolu RDP, do skupiny Remote Desktop User. Pokud to neuděláte, možná se nebudete moct připojit k virtuálnímu počítači po jeho připojení k doméně služby Active Directory.
5. Na svém VIRTUÁLNÍm počítači přejdete na web Windows Update.
6. Nainstalujte všechny aktualizace Windows v důležité kategorii.
7. Nainstalovat všechny aktualizace systému Windows v nepovinné kategorii (kromě jazykových sad). Tím se nainstaluje aktualizace protokol RDP (Remote Desktop Protocol) 8,0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)), kterou potřebujete k dokončení těchto pokynů.
8. Otevřete Editor místních zásad skupiny a přejděte do části **Konfigurace počítače**  >  **šablony pro správu**  >  **součásti systému Windows**  >  **Vzdálená plocha**  >  **hostitel relace vzdálené plochy**  >  **vzdálené prostředí relace**.
9. Povolte zásadu protokol RDP (Remote Desktop Protocol) 8,0.
10. Připojte tento virtuální počítač k doméně služby Active Directory.
11. Restartujte virtuální počítač spuštěním následujícího příkazu:

     ```cmd
     shutdown /r /t 0
     ```

12. Pokud chcete získat registrační token, postupujte podle pokynů uvedených [tady](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) .
13. [Stáhněte si agenta virtuální plochy Windows pro Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Stáhněte správce agenta virtuálních počítačů s Windows pro Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Spusťte instalační program agenta virtuálních počítačů s Windows a postupujte podle pokynů. Po zobrazení výzvy zadejte registrační klíč, který jste vytvořili v kroku 12.
16. Otevřete Správce agenta virtuálních počítačů s Windows a postupujte podle pokynů.
17. Volitelně můžete port TCP/3389 zablokovat, aby se odebral přímý protokol RDP (Remote Desktop Protocol) přístup k virtuálnímu počítači.
18. Volitelně můžete zkontrolovat, že rozhraní .NET Framework má alespoň 4.7.2 verze. To je obzvláště důležité, pokud vytváříte vlastní image.

## <a name="next-steps"></a>Další kroky

Nasazení virtuálního počítače s Windows je teď připravené k použití. Abyste mohli začít, [Stáhněte si nejnovější verzi klienta virtuální plochy Windows](https://aka.ms/wvd/clients/windows) .

Seznam známých problémů a pokyny k řešení potíží pro Windows 7 na virtuálním počítači s Windows najdete v našem článku věnovaném řešení potíží při [řešení potíží s virtuálními počítači s Windows 7 ve virtuálním počítači](troubleshoot-windows-7-vm.md)s Windows.
