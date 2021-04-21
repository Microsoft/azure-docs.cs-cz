---
title: Chyba při spuštění instance role v Azure Cloud Services (Rozšířená podpora)
description: Řešení potíží se spuštěním instance role v Azure Cloud Services (Rozšířená podpora).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748924"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Řešení potíží s rolemi služby Azure Cloud Services (Rozšířená podpora), které se nedaří spustit

Tady jsou některé běžné problémy a řešení týkající se rolí Azure Cloud Services (Rozšířená podpora), které se nedaří spustit.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>Operace cloudové služby se nezdařila s RoleInstanceStartupTimeoutError

Jedna nebo více instancí rolí v Azure Cloud Services (Rozšířená podpora) může být pomalé, nebo se můžou recyklovat a instance role se nezdařila. Zobrazí se chyba aplikace role `RoleInstanceStartupTimeoutError` .

Aplikace role obsahuje dvě části, které by mohly způsobit recyklaci rolí: *úlohy po spuštění* a *kód role (implementace RoleEntryPoint)*. 

Pokud se role zastaví, agent platformy jako služby (PaaS) restartuje roli.

Aktuální stav a podrobnosti o instanci role můžete získat pro diagnostiku chyb pomocí prostředí PowerShell nebo Azure Portal:

* **PowerShell**: pomocí rutiny [Get-AzCloudServiceRoleInstanceView](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) získáte informace o stavu modulu runtime instance role:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure Portal**: na portálu přejdete do instance cloudové služby. Chcete-li zobrazit podrobnosti o stavu, vyberte **role a instance** a pak vyberte instanci role.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Snímek obrazovky zobrazující selhání spuštění role ve Azure Portal.":::

## <a name="missing-dlls-or-dependencies"></a>Chybějící knihovny DLL nebo závislosti

Nereagující role a role, které se cyklují mezi stavy, mohou být způsobeny chybějícími knihovnami DLL nebo sestaveními.

Zde jsou některé příznaky příznaků chybějících knihoven DLL nebo sestavení:

* Vaše instance role cykluje v průběhu **inicializace**, **zaneprázdnění** a **zastavování**.
* Vaše instance role se přesunula do stavu **připraveno** , ale pokud přejdete do webové aplikace, stránka není viditelná.


Web, který je nasazený ve webové roli a chybějící knihovna DLL, může zobrazit tuto chybu při běhu serveru:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Snímek obrazovky, který zobrazuje chybu za běhu po selhání spuštění role.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Řešení chybějících knihoven DLL a sestavení

Řešení chyb chybějících knihoven DLL a sestavení:

1. V aplikaci Visual Studio otevřete řešení.
2. V Průzkumník řešení otevřete složku *odkazy* .
3. Vyberte sestavení identifikované v chybě.
4. V části **vlastnosti** nastavte vlastnost **Kopírovat místní** na **hodnotu true**.
5. Znovu nasaďte cloudovou službu.

Po ověření, že se již chyby nezobrazí, službu znovu nasaďte. Při nastavování nasazení nezaškrtnete políčko **Povolit IntelliTrace pro role rozhraní .NET 4** .

## <a name="diagnose-role-instance-errors"></a>Diagnostika chyb instance role

Pokud chcete diagnostikovat problémy s instancemi rolí, vyberte jednu z následujících možností.

### <a name="turn-off-custom-errors"></a>Vypnout vlastní chyby

Chcete-li zobrazit úplné informace o chybách, nastavte v souboru *web.config* pro webovou roli vlastní režim chyb na a `off` poté znovu nasaďte službu:

1. V aplikaci Visual Studio otevřete řešení.
2. V Průzkumník řešení otevřete *web.config* soubor.
3. V `system.web` části přidejte následující kód:

   ```xml
   <customErrors mode="Off" />
   ```

4. Soubor uložte.
5. Znovu zabalit a znovu nasaďte službu.

Při opětovném nasazení služby obsahuje chybová zpráva název chybějících sestavení nebo knihoven DLL.

### <a name="use-remote-desktop"></a>Použití Vzdálené plochy

Pro přístup k roli použijte vzdálenou plochu a zobrazte informace o kompletní chybě:

1. [Přidejte rozšíření vzdálené plochy pro Azure Cloud Services (Rozšířená podpora)](enable-rdp.md).
2. Pokud v Azure Portal instance cloudové služby zobrazuje stav **připraveno** , přihlaste se ke cloudové službě pomocí vzdálené plochy. Další informace najdete v tématu [připojení k instancím rolí pomocí vzdálené plochy](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled).
3. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste použili k nastavení vzdálené plochy.
4. Otevřete okno příkazového řádku.
5. Na příkazovém řádku zadejte příkaz **ipconfig**. Poznamenejte si vrácenou hodnotu pro adresu IPv4.
6. Otevřete aplikaci Microsoft Internet Explorer.
7. Do adresního řádku zadejte IPv4 adresu následovaný lomítkem a názvem výchozího souboru webové aplikace. Například, `http://<IPv4 address>/default.aspx`.

Pokud teď na webu přejdete, zobrazí se chybové zprávy, které obsahují další informace. Tady je příklad:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Snímek obrazovky, který ukazuje příklad chybové zprávy.":::
  
### <a name="use-the-compute-emulator"></a>Použití emulátoru COMPUTE

Emulátor služby COMPUTE Azure můžete použít k diagnostice a řešení potíží s chybějícími závislostmi a *web.configmi* chybami. Když použijete tuto metodu k diagnostice problémů, pro dosažení nejlepších výsledků použijte počítač nebo virtuální počítač, který má čistou instalaci systému Windows.

Diagnostikujte problémy pomocí emulátoru služby COMPUTE Azure:

1. Nainstalujte [sadu Azure SDK](https://azure.microsoft.com/downloads/).
2. Na vývojovém počítači Sestavte projekt cloudové služby.
3. V Průzkumníkovi souborů v projektu cloudové služby otevřete složku *bin\Debug* .
4. Zkopírujte složku *. csx* a soubor *. cscfg* do počítače, který používáte k ladění problémů.
5. Na čistém počítači otevřete okno příkazového řádku sady Azure SDK.
6. Do příkazového řádku zadejte **csrun.exe/devstore: Start**.
7. Pak zadejte **Run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser**.
8. Po spuštění role zobrazí Internet Explorer podrobné informace o chybě.

Pokud potřebujete další diagnostiku, můžete použít standardní nástroje pro odstraňování potíží se systémem Windows.

### <a name="use-intellitrace"></a>Použití IntelliTrace

U pracovních procesů a webových rolí, které používají .NET Framework 4, můžete použít [IntelliTrace](/visualstudio/debugger/intellitrace). IntelliTrace je k dispozici v Visual Studio Enterprise.

Nasazení cloudové služby pomocí IntelliTrace zapnutého:

1. Potvrďte, že je nainstalovaná sada Azure SDK 1,3 nebo novější.
2. V aplikaci Visual Studio nasaďte řešení. Při nastavování nasazení zaškrtněte políčko **Povolit IntelliTrace pro role rozhraní .NET 4** .
3. Po spuštění instance role otevřete Průzkumník serveru.
4. Rozbalte uzel **služby Azure\Cloud Services** .
5. Rozšířením nasazení rozbalte seznam instancí rolí. Klikněte pravým tlačítkem na instanci role.
6. Vyberte **Zobrazit protokoly IntelliTrace**.
7. V **souhrnu IntelliTrace** otevřete  **data výjimky**.
8. Rozbalte **data výjimky** a vyhledejte `System.IO.FileNotFoundException` chybu:

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Snímek dat výjimky pro chybu spuštění role" lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Další kroky

- Naučte se [řešit potíže s rolemi cloudové služby pomocí dat diagnostiky počítačů Azure PaaS](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
