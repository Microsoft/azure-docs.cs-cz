---
title: Selhání spuštění instance role pro Azure Cloud Services (Rozšířená podpora)
description: Řešení chyb při spuštění instance role pro Azure Cloud Services (Rozšířená podpora)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382351"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Řešení potíží s rolemi služby Azure Cloud Service (Rozšířená podpora), které se nedaří spustit
Tady jsou některé běžné problémy a řešení týkající se rolí Cloud Services (Rozšířená podpora), které se nedaří spustit.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>Operace cloudové služby selhala s RoleInstanceStartupTimeoutError
Jedna nebo víc instancí role vaší služby může (Rozšířená podpora) se ve stanoveném čase nespustí. Je možné, že tyto instance rolí vychází z doby spuštění nebo může být recyklace a instance role může selhat s RoleInstanceStartupTimeoutError se jedná o chybu aplikace role. Aplikace role obsahuje dvě hlavní části: "úlohy po spuštění" a "kód role (implementace RoleEntryPoint)", které by mohly způsobit recyklaci rolí. Pokud dojde k chybě role, agent PaaS ho vždy znovu spustí. 

Pokud chcete získat aktuální stav a podrobnosti instancí rolí v případě chyb, použijte:

* PowerShell: pomocí rutiny [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) můžete načíst informace o stavu běhu instance role v cloudové službě.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure Portal: Přejít na svou cloudovou službu a vybrat kartu role a instance. Kliknutím na instanci role získáte podrobnosti o jeho stavu. 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="Image znázorňuje selhání spuštění role na portálu.":::
   
Tady jsou některé běžné problémy a řešení týkající se rolí Azure Cloud Services (Rozšířená podpora), které se nedaří spustit nebo se zacykluje mezi inicializací, zaneprázdněním a stavem zastavení.

## <a name="missing-dlls-or-dependencies"></a>Chybějící knihovny DLL nebo závislosti
Nereagující role a role, které se cyklují mezi inicializací, zaneprázdnění a stavy zastavení, mohou být způsobeny chybějícími knihovnami DLL nebo sestaveními.
Příznaky chybějících knihoven DLL nebo sestavení mohou být:

* Vaše instance role se cykluje prostřednictvím **inicializace**, **zaneprázdnění** a stavu **zastavení** .
* Vaše instance role byla přesunuta do **připraveného** , ale pokud přejdete na webovou aplikaci, stránka se nezobrazí.

Existuje několik doporučených metod pro zkoumání těchto problémů.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostika chybějících problémů knihoven DLL ve webové roli
Když přejdete na web, který je nasazený ve webové roli, a v prohlížeči se zobrazí chyba serveru, která je podobná následujícímu, může to znamenat, že chybí knihovna DLL.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="Obrázek ukazuje chybu za běhu při spuštění role.":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostikujte problémy vypnutím vlastních chyb.
Další informace o kompletní chybě najdete v části Konfigurace web.config pro webovou roli pro nastavení vlastního režimu chyb na vypnuto a opětovné nasazení služby.
Zobrazení dalších úplných chyb bez použití funkce Vzdálená plocha:
1.  Otevřete řešení v Microsoft Visual Studio.
2.  V Průzkumník řešení vyhledejte soubor web.config a otevřete ho.
3.  V souboru web.config vyhledejte část System. Web a přidejte následující řádek:
 ```xml
<customErrors mode="Off" />
```
4.  Soubor uložte.
5.  Znovu zabalit a znovu nasaďte službu.
Po opětovném nasazení služby se zobrazí chybová zpráva s názvem chybějícího sestavení nebo knihovny DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostika potíží zobrazením chyby vzdáleně
Pomocí vzdálené plochy můžete získat přístup k roli a vzdáleně zobrazit informace o chybě. K zobrazení chyb pomocí vzdálené plochy použijte následující postup:
1.  Povolit rozšíření vzdálené plochy pro cloudovou službu (Rozšířená podpora). Další informace najdete v tématu [použití rozšíření vzdálené plochy pro Cloud Services (Rozšířená podpora) pomocí Azure Portal](enable-rdp.md)
2.  Na Azure Portal, jakmile instance zobrazí stav připraveno, vzdáleně do instance. Další informace o používání vzdálené plochy s Cloud Services (Rozšířená podpora) najdete v tématu [připojení k instancím rolí pomocí vzdálené plochy](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled) .
3.  Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při konfiguraci vzdálené plochy.
4.  Otevřete příkazové okno.
5.  Zadejte příkaz IPconfig.
6.  Poznamenejte si hodnotu adresy IPv4.
7.  Otevřete Internet Explorer.
8.  Zadejte adresu a název webové aplikace. Například http:// <IPV4 Address> /Default.aspx.
Když přejdete na web, budou se teď vracet explicitní chybové zprávy:
* Chyba serveru v aplikaci/
* Popis: při provádění aktuálního webového požadavku došlo k neošetřené výjimce. Přečtěte si prosím trasování zásobníku, kde najdete další informace o chybě a kde vznikly v kódu.
* Podrobnosti výjimky: System. IO. FIleNotFoundException: nelze načíst soubor nebo sestavení Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 nebo jedna z jeho závislostí. Systém nemůže najít zadaný soubor.
Například:

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="Image zobrazuje výjimku při spuštění role.":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostika problémů pomocí emulátoru COMPUTE
Emulátor služby COMPUTE Azure můžete použít k diagnostice a řešení potíží s chybějícími závislostmi a web.configmi chybami.
Pro dosažení nejlepších výsledků použití této metody diagnostiky byste měli použít počítač nebo virtuální počítač, který má čistou instalaci systému Windows. 
1.  Instalace [sady Azure SDK](https://azure.microsoft.com/downloads/) 
2.  Na vývojovém počítači Sestavte projekt cloudové služby.
3.  V Průzkumníku Windows přejděte do složky bin\Debug projektu cloudové služby.
4.  Zkopírujte složku. csx a soubor. cscfg do počítače, který používáte k ladění problémů.
5.  Na čistém počítači otevřete okno příkazového řádku sady Azure SDK a zadejte csrun.exe/devstore: Start.
6.  Na příkazovém řádku zadejte příkaz run csrun <cesta do složky. csx> <cesta k souboru. cscfg>/launchBrowser.
7.  Po spuštění role se v Internet Exploreru zobrazí podrobné informace o chybě. K dalšímu diagnostikování problému můžete také použít standardní nástroje pro řešení potíží se systémem Windows.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostika problémů pomocí IntelliTrace
U pracovních procesů a webových rolí, které používají .NET Framework 4, můžete použít [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace), který je k dispozici v Microsoft Visual Studio Enterprise.
Pomocí těchto kroků nasaďte službu s povoleným IntelliTrace:
1.  Potvrďte, že je nainstalovaná sada Azure SDK 1,3 nebo novější.
2.  Nasaďte řešení pomocí sady Visual Studio. Během nasazování zaškrtněte políčko Povolit IntelliTrace pro role .NET 4.
3.  Po spuštění instance otevřete Průzkumník serveru.
4.  Rozbalte uzel služby Azure\Cloud Services a vyhledejte nasazení.
5.  Rozbalte nasazení, dokud neuvidíte instance role. Klikněte pravým tlačítkem na jednu z těchto instancí.
6.  Vyberte možnost Zobrazit protokoly IntelliTrace. Otevře se souhrn IntelliTrace.
7.  Vyhledejte část s výjimkami v části Souhrn. Pokud existují výjimky, oddíl bude označovat data výjimky.
8.  Rozbalte data výjimky a vyhledejte chyby System. IO. FileNotFoundException podobné následujícímu:

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="Při spuštění role se v imagi zobrazuje data výjimky." lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Adresa chybějících knihoven DLL a sestavení
Chcete-li vyřešit chybějící DLL a chyby sestavení, postupujte podle následujících kroků:
1.  Otevřete řešení v sadě Visual Studio.
2.  V Průzkumník řešení otevřete složku odkazy.
3.  Klikněte na sestavení identifikované v chybě.
4.  V podokně vlastnosti Najděte položku Kopírovat místní vlastnost a nastavte hodnotu na true.
5.  Znovu nasaďte cloudovou službu.
Jakmile ověříte, že byly všechny chyby opraveny, můžete službu nasadit bez zaškrtnutí políčka Povolit IntelliTrace pro role .NET 4.

## <a name="next-steps"></a>Další kroky 
- Informace o řešení potíží s rolemi cloudové služby pomocí dat diagnostiky počítačů Azure PaaS najdete v tématu [série blogů v Kevin Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).
