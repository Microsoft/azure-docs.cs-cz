---
title: "Nainstalovat Azure zásobníku Development Kit (ASDK) | Microsoft Docs"
description: "Popisuje postup instalace Azure zásobníku Development Kit (ASDK)."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e1f4cfb74d83cb23631e5a16a6e6f2dba98027ef
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Nainstalovat Azure zásobníku Development Kit (ASDK)
Po [Příprava hostitelský počítač ASDK](asdk-prepare-host.md), ASDK lze nasadit do bitové kopie CloudBuilder.vhdx pomocí následujících kroků v tomto článku.

## <a name="install-the-asdk"></a>Nainstalujte ASDK
Kroky v tomto článku ukazují, jak nasadit ASDK pomocí grafického uživatelského rozhraní (GUI) poskytované stažení a spuštění **asdk installer.ps1** skript prostředí PowerShell.

> [!NOTE]
> Instalační program uživatelské rozhraní pro Azure zásobníku Development Kit je skriptu open source na základě WCF a prostředí PowerShell.


1. Po hostitelský počítač úspěšně spustí do bitové kopie CloudBuilder.vhdx, přihlásit se pomocí pověření správce zadána při jste [připravený development kit hostitelský počítač](asdk-prepare-host.md) ASDK instalace. To by měl být stejný jako přihlašovací údaje místního správce na vývoj kit hostitele.
2. Otevřete konzolu prostředí PowerShell se zvýšenými oprávněními a spusťte  **&lt;písmeno jednotky > \AzureStack_Installer\asdk-installer.ps1** skript (který může být nyní na jiné jednotce než C:\ CloudBuilder.vhdx obrázek). Klikněte na **Nainstalovat**.

    ![](media/asdk-install/1.PNG) 

3. V rámci zprostředkovatele Identity **typ** rozevíracího seznamu vyberte **cloudu Azure** nebo **služby AD FS**. V části **heslo místního správce** zadejte heslo místního správce (který musí odpovídat aktuální heslo místního správce nakonfigurované) **heslo** pole a pak klikněte na tlačítko  **Další**.
    - **Azure Cloud**: nakonfiguruje Azure Active Directory (Azure AD) jako zprostředkovatele identity. Chcete-li použít tuto možnost, musíte připojení k Internetu, celý název Azure AD directory klienta ve formě *domainname*. onmicrosoft.com nebo Azure AD ověřit vlastní název domény a přihlašovací údaje globálního správce pro zadaný adresář. 
    - **Služba AD FS**: výchozí razítko adresářová služba se používá jako zprostředkovatele identity. Je výchozí účet pro přihlášení s azurestackadmin@azurestack.local, a k použití hesla je zadaný jako součást instalace.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > Nejlepších výsledků dosáhnete i v případě, že chcete použít odpojené prostředí zásobníku Azure pomocí služby AD FS jako zprostředkovatele identity je nejvhodnější pro instalaci ASDK při připojení k Internetu. Tímto způsobem, zkušební verze systému Windows Server 2016 zahrnuté do development kit instalace může být aktivovaný v době nasazení.
4. Vyberte síťový adaptér používat pro development kit a potom klikněte na **Další**.

    ![](media/asdk-install/3.PNG)

5. Vyberte DHCP nebo konfigurace statických sítě pro virtuální počítač BGPNAT01.
    > [!TIP]
    > Virtuální počítač BGPNAT01 je hraniční směrovač, který poskytuje možnosti NAT a VPN Azure zásobníku.

    - **DHCP** (výchozí): virtuální počítač získá konfigurace sítě IP ze serveru DHCP.
    - **Statické**: tuto možnost použijte pouze v případě DHCP nelze přiřadit platnou IP adresu pro zásobník Azure pro přístup k Internetu. **Statická IP adresa musí být zadán s délka maska podsítě ve formátu CIDR (například 10.0.0.5/24)**.
    - Zadejte platnou **čas IP adresa serveru** adresu. To vyžaduje, že pole nastaví čas serveru, který má být používána development kit. Tento parametr je zadat jako IP adresa serveru doby platnosti. Názvy serverů nejsou podporovány.
      > [!TIP]
      > Čas serveru najít IP adresu, navštivte [pool.ntp.org](http:\\pool.ntp.org) nebo příkaz ping time.windows.com. 
    - **Volitelně**, nastavte následující hodnoty:
        - **ID sítě VLAN**: Nastaví ID sítě VLAN. Tuto možnost použijte pouze v případě hostitele a AzS BGPNAT01 musíte nakonfigurovat ID sítě VLAN pro přístup k fyzické síti (a Internetu). 
        - **Server DNS pro předávání**: server A DNS je vytvořen jako součást nasazení Azure zásobníku. Pokud chcete umožnit uvnitř řešení překládat názvy mimo razítka, zadejte existující infrastrukturu DNS server. Server DNS v razítko předává Neznámý název k tomuto serveru.

    ![](media/asdk-install/4.PNG)

6. Na **ověření vlastnostech karty síťového rozhraní** stránky, se zobrazí indikátor průběhu. Po dokončení ověření klikněte na tlačítko **Další**.

    ![](media/asdk-install/5.PNG)

9. Na **Souhrn** klikněte na tlačítko **nasadit** ke spuštění instalace ASDK na hostitelském počítači development kit.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Zde můžete také zkopírovat příkazy instalace prostředí PowerShell, které se použijí k instalaci sady development kit. To je užitečné, pokud byste někdy potřebovali [znovu nasaďte ASDK na hostitelském počítači pomocí prostředí PowerShell](asdk-deploy-powershell.md).

10. Pokud provádíte nasazení služby Azure AD, budete vyzváni k zadání přihlašovacích údajů účtu globálního správce Azure AD pár minut po spuštění instalace.

    ![](media/asdk-install/7.PNG)

11. Proces nasazení bude trvat několik hodin, během které doby hostitelský počítač se automaticky restartuje jednou. Pokud chcete sledovat průběh nasazení, přihlaste se jako azurestack\AzureStackAdmin po restartování hostitele development kit. Pokud je nasazení úspěšná, zobrazí konzole PowerShell: **COMPLETE: akce, nasazení,**. 
    > [!IMPORTANT]
    > Pokud se přihlásíte jako místní správce poté, co je počítač připojený k doméně, zobrazí se průběh nasazení. Není znovu spustit nasazení, místo toho se přihlaste jako azurestack\AzureStackAdmin k ověření, zda je spuštěna.

    ![](media/asdk-install/8.PNG)

Blahopřejeme, úspěšně jste nainstalovali ASDK.

Pokud z nějakého důvodu selže nasazení, můžete [znovu nasaďte](asdk-redeploy.md) z nuly nebo použijte PowerShell následující příkazy, z téhož okna zvýšenými prostředí PowerShell, restartujte nasazení z poslední úspěšné kroku:

    ```powershell
    cd C:\CloudDeployment\Setup
    .\InstallAzureStackPOC.ps1 -Rerun
    ```

## <a name="next-steps"></a>Další postup
[Konfigurace nasazení POST](asdk-post-deploy.md)