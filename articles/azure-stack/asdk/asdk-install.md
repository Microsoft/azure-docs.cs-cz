---
title: Instalace sady Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Popisuje postup instalace Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: a58f5a3794d352fa8671321f5a30d74d2598df75
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977724"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Instalace sady Azure Stack Development Kit (ASDK)
Po [Příprava hostitelském počítači ASDK](asdk-prepare-host.md), je možné nasadit ASDK CloudBuilder.vhdx Image, pomocí následujících kroků v tomto článku.

## <a name="install-the-asdk"></a>Nainstalujte ASDK
Kroky v tomto článku ukazují, jak nasadit ASDK pomocí grafického uživatelského rozhraní (GUI) poskytované stáhnete a nainstalujete **asdk installer.ps1** skript prostředí PowerShell.

> [!NOTE]
> Uživatelské rozhraní instalačního programu pro Azure Stack Development Kit je skriptu open source na základě WCF a prostředí PowerShell.


1. Po hostitelský počítač úspěšně spustí do bitové kopie CloudBuilder.vhdx, přihlásit se pomocí přihlašovacích údajů správce zadán při vám [připravili development kit hostitelský počítač](asdk-prepare-host.md) ASDK instalace. To by měla být stejná jako přihlašovací údaje development kit hostitele místního správce.
2. Otevřete konzolu Powershellu se zvýšenými oprávněními a spusťte  **&lt;písmeno jednotky > \AzureStack_Installer\asdk-installer.ps1** skript prostředí PowerShell. Všimněte si, že skript může být nyní na jinou jednotku než C:\ CloudBuilder.vhdx obrázku. Klikněte na **Nainstalovat**.

    ![](media/asdk-install/1.PNG) 

3. V poskytovateli Identity **typ** rozevíracího seznamu vyberte **Azure China Cloud**, **nám cloudu Azure Government**, **služby AD FS**, nebo **Cloudu azure**. V části **heslo místního správce** zadejte heslo místního správce (který musí odpovídat aktuální heslo místního správce nakonfigurovanou) **heslo** pole a potom klikněte na tlačítko  **Další**.

    ![](media/asdk-install/2.PNG) 
  
   Pokud se rozhodnete poskytovatele identity k předplatnému Azure, budete potřebovat připojení k Internetu, úplný název služby Azure AD tenanta adresáře ve formě *domainname*. onmicrosoft.com, nebo Azure AD ověřit vlastní doménu, název a globální přihlašovací údaje správce pro zadaný adresář.<br><br>Po nasazení není potřeba oprávnění globálního správce Azure Active Directory. Některé operace však může vyžadovat přihlašovací údaje globálního správce. Například skript instalační program zprostředkovatele prostředků nebo nová funkce vyžaduje oprávnění bylo uděleno. Můžete buď dočasně znovu vytvořit oprávnění účtu globálního správce nebo použijte samostatné globální správce účtu, který je vlastníkem *výchozí předplatné poskytovatele*.<br><br>Při použití služby AD FS jako zprostředkovatele identity, se používá výchozí razítko adresářové služby. Je výchozí účet pro přihlášení pomocí azurestackadmin@azurestack.local, a heslo pro použití se zadal jako součást instalace.

  > [!NOTE]
  > Nejlepších výsledků dosáhnete i v případě, že chcete použít odpojené prostředí Azure Stack pomocí služby AD FS jako zprostředkovatele identity, je nejvhodnější pro instalaci ASDK připojené k Internetu. Tímto způsobem, v době nasazení lze aktivovat zkušební verzi Windows serveru 2016 je součástí instalace development kit.

4. Vyberte síťový adaptér používat pro development kit a potom klikněte na **Další**.

    ![](media/asdk-install/3.PNG)

5. Na **konfigurace sítě** zadejte platný **času IP adresa serveru** adresu. To vyžaduje, že pole nastaví čas serveru použije development kit. Tento parametr musí být ve formě IP adresy serveru platný čas. Názvy serverů nejsou podporovány.

      > [!TIP]
      > Čas serveru IP adresu najdete v tématu [ntppool.org](https://www.ntppool.org/) nebo odešlete zprávu ping time.windows.com. 

    **Volitelně**, můžete zadat **server DNS pro předávání** IP adresu. DNS server se vytvoří jako součást nasazení Azure Stack. Povolit počítačům uvnitř řešení k překladu názvů mimo razítka, zadejte existující infrastrukturu DNS server. Server DNS v razítku předá požadavky na řešení Neznámý název k tomuto serveru.

    ![](media/asdk-install/4.PNG)

6. Na **ověřování vlastnosti karty síťového rozhraní** stránce se zobrazí indikátor průběhu. Po ověření se dokončí, klikněte na tlačítko **Další**.

    ![](media/asdk-install/5.PNG)

7. Na **Souhrn** klikněte na **nasadit** postup spuštění instalace ASDK na hostitelském počítači development kit.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Tady můžete také kopírovat instalační příkazy Powershellu, které se použijí k instalaci sady development kit. To je užitečné, pokud byste zas někdy potřebovali [znovu nasadit ASDK v hostitelském počítači pomocí Powershellu](asdk-deploy-powershell.md).

8. Pokud provádíte nasazení služby Azure AD, budete vyzváni k zadání přihlašovacích údajů Azure AD globálního správce účtu pár minut po spuštění instalace.

9. Proces nasazení potrvá několik hodin, během kterých hostitelský počítač automaticky restartuje jednou. Pokud chcete monitorovat průběh nasazení, přihlaste se jako azurestack\AzureStackAdmin po restartování hostitele development kit. Po úspěšném nasazení se zobrazí konzola Powershellu: **DOKONČENÍ: Akce "Nasazení"**. 
    > [!IMPORTANT]
    > Pokud se přihlásíte jako místní správce poté, co je počítač připojen k doméně azurestack, zobrazí se průběh nasazení. Nelze znovu spustit nasazení, místo toho se přihlaste jako azurestack\AzureStackAdmin chcete ověřit, jestli je spuštěná.

    ![](media/asdk-install/7.PNG)

Blahopřejeme, úspěšně jste nainstalovali ASDK.

Pokud z nějakého důvodu nepovede nasazení, můžete si [znovu nasadit](asdk-redeploy.md) od začátku nebo použijte následující příkazy Powershellu, v okně stejné PowerShell se zvýšenými oprávněními k restartování z posledního kroku úspěšné nasazení:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Další postup
[Konfigurace po nasazení](asdk-post-deploy.md)
