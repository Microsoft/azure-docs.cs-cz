---
title: "Řešení potíží s rolí, které se nepodařilo spustit | Microsoft Docs"
description: "Tady jsou některé běžné příčiny, proč nemusí podařit spustit roli cloudové služby. K dispozici jsou taky řešení těchto problémů."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 36966151b616a14e429807feb63df93503e7b249
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Řešení potíží s cloudové služby role, které se nepodařilo spustit
Tady jsou některé běžné problémy a řešení souvisejících s Azure Cloud Services rolí, které se nepodařilo spustit.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Chybějící knihovny DLL nebo závislosti
Reagovat role a role, které jsou prosté mezi **Probíhá inicializace**, **zaneprázdněn**, a **zastavení** stavy může být způsobeno chybějícím knihovny DLL nebo sestavení.

Příznaky chybějící knihovny DLL nebo sestavení může být:

* Role instance je prosté prostřednictvím **Probíhá inicializace**, **zaneprázdněn**, a **zastavení** stavy.
* Role instance se přesunula do **připraven** , ale pokud přejdete k vaší webové aplikaci, stránky se nezobrazí.

Existuje několik doporučených metod pro příčin těchto problémů.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Chybí knihovna DLL problémů ve webové roli diagnostiky
Když přejdete na web, který je nasazen ve webové role a prohlížeč zobrazí chybu serveru, který je podobný následujícímu, může to znamenat, že chybí knihovna DLL.

![Chyba serveru v aplikaci '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostikovat problémy vypnutím vlastní chyby
Konfigurace souboru web.config pro webovou roli na nastavení režimu vlastní chyby na vypnutém a opětovného nasazení služby lze zobrazit podrobnější informace o chybě.

Chcete-li zobrazit podrobnější chyby bez pomocí vzdálené plochy:

1. Otevřete řešení v sadě Microsoft Visual Studio.
2. V **Průzkumníku**, vyhledejte soubor web.config a otevřete ji.
3. V souboru web.config najděte část system.web a přidejte následující řádek:

    ```xml
    <customErrors mode="Off" />
    ```
4. Uložte soubor.
5. Znovu zabalte a znovu nasaďte službu.

Jakmile služba je znovu nasazena, zobrazí se chybová zpráva s názvem chybí sestavení nebo DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Vyřešení problémů se zobrazením chyba vzdáleně
Vzdálená plocha můžete použít pro přístup k roli a vzdáleně zobrazit podrobnější informace o chybě. Chcete-li zobrazit chyby pomocí vzdálené plochy pomocí následujících kroků:

1. Zkontrolujte, zda je nainstalovaný Azure SDK 1.3 nebo novější.
2. Při nasazení řešení pomocí sady Visual Studio povolení vzdálené plochy. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí sady Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. Na portálu Microsoft Azure, jakmile se zobrazí stav instance **připraven**vzdálený do instance. Další informace o používání vzdálené plochy s cloudovými službami najdete v tématu [vzdáleného do instance rolí](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které se zadaly během konfigurace vzdálené plochy.
6. Otevřete okno příkazového řádku.
7. Zadejte `IPconfig`.
8. Poznamenejte si hodnotu adresu IPV4.
9. Open Internet Explorer.
10. Zadejte adresu a název webové aplikace. Například, `http://<IPV4 Address>/default.aspx`.

Přejdete na web se teď vrátit podrobnější chybové zprávy:

* Chyba serveru v aplikaci '/'.
* Popis: Při provádění aktuální webové žádosti došlo k neošetřené výjimce. Přečtěte si další informace o této chybě, a místo původu v kódu trasování zásobníku.
* Podrobnosti o výjimce: System.IO.FIleNotFoundException: Nelze načíst soubor nebo sestavení ' Microsoft.WindowsAzure.StorageClient, verze = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35, nebo jeden z jeho závislých. Systém nemůže najít zadaný soubor.

Příklad:

![Chyba explicitní serveru v aplikaci '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Vyřešení problémů pomocí emulátoru služby výpočty v
Emulátoru služby výpočty v Microsoft Azure můžete použít při diagnostice a řešení potíží se chybějící závislosti a chyby v souboru web.config.

Nejlepších výsledků dosáhnete v pomocí této metody diagnostiky měli byste použít počítač nebo virtuální počítač, který má čistou instalaci systému Windows. Chcete-li nejlépe simulaci prostředí Azure, použijte Windows Server 2008 R2 x64.

1. Nainstalujte samostatnou verzi [Azure SDK](https://azure.microsoft.com/downloads/).
2. Na vývojovém počítači sestavení projektu cloudové služby.
3. V Průzkumníku Windows přejděte do složky bin\debug projektu cloudové služby.
4. Zkopírujte soubor složky a .cscfg .csx k počítači, který používáte k ladění problémů.
5. Na vyčištění počítači otevřete okno příkazového řádku Azure SDK a zadejte `csrun.exe /devstore:start`.
6. Na příkazovém řádku zadejte `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Když se spustí roli, zobrazí se podrobné informace o chybě v aplikaci Internet Explorer. Můžete také standardní Windows nástroje pro řešení potíží při další diagnostice problému.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostikovat problémy s použitím technologie IntelliTrace
Pro pracovní a webové role, které používají rozhraní .NET Framework 4, můžete použít [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), která je dostupná v aplikaci Microsoft Visual Studio Enterprise.

Postupujte podle těchto kroků nasadíte službu s použitím technologie IntelliTrace povoleno:

1. Zkontrolujte, že je nainstalovaná sada Azure SDK 1.3 nebo novější.
2. Nasazení řešení pomocí sady Visual Studio. Během nasazení, zkontrolujte **povolit IntelliTrace pro role rozhraní .NET 4** zaškrtávací políčko.
3. Jakmile se spustí instanci, otevřete **Průzkumníka serveru**.
4. Rozbalte **Azure\\cloudové služby** uzlu a najděte nasazení.
5. Rozbalte možnost nasazení, dokud neuvidíte instancí rolí. Klikněte pravým tlačítkem myši na jednu z instancí.
6. Zvolte **protokoly IntelliTrace zobrazení**. **IntelliTrace Souhrn** se otevře.
7. Vyhledejte část výjimky souhrn. Pokud jsou výjimky, bude v části s názvem bez přípony **Data výjimky**.
8. Rozbalte **Data výjimky** a vyhledejte **System.IO.FileNotFoundException** chyby podobný následujícímu:

![Data výjimky, chybí soubor nebo sestavení](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adresa chybějící knihovny DLL a sestavení
Chcete-li vyřešit chybějící DLL a chyby sestavení, postupujte takto:

1. Otevřete řešení v sadě Visual Studio.
2. V **Průzkumníku řešení**, otevřete **odkazy** složky.
3. Klikněte na tlačítko sestavení identifikované v chybě.
4. V **vlastnosti** podokně vyhledejte **místní kopie vlastnosti** a nastavte hodnotu na **True**.
5. Znovu nasaďte cloudové služby.

Jakmile si ověříte, že byly odstraněny všechny chyby, můžete nasadit službu bez kontroly, zda **povolit IntelliTrace pro role rozhraní .NET 4** zaškrtávací políčko.

## <a name="next-steps"></a>Další postup
Zobrazení [řešení potíží s články](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pro cloudové služby.

Informace o řešení problémů role služby cloudu pomocí Azure PaaS dat diagnostiky počítače, v tématu [řady blogu kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
