---
title: Řešení potíží s rolemi, které nejde spustit | Dokumentace Microsoftu
description: Tady je několik běžných příčin, proč nemusí podařit spustit roli cloudové služby. Řešení těchto problémů jsou také k dispozici.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 4b4669ecdae474c8926a346ed02f40913cf67265
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35899402"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Řešení potíží s rolí cloudové služby, které nejde spustit
Tady jsou některé běžné problémy a řešení souvisejících s Azure Cloud Services role, které nejde spustit.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Chybějící knihovny DLL nebo závislosti
Reagovat role a role, které jsou cyklické mezi **inicializace**, **zaneprázdněn**, a **zastavení** stavy může být způsobeno chybějící knihovny DLL nebo sestavení.

Mezi příznaky chybějící knihovny DLL nebo sestavení může být:

* Role instance se cyklí prostřednictvím **inicializace**, **zaneprázdněn**, a **zastavení** stavy.
* Role instance se přesunula do **připravené** , ale při přechodu na webovou aplikaci, na stránce zřejmě není.

Pro zkoumání těchto potíží s několika doporučené způsoby.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostikujte problémy chybějící knihovny DLL ve webové roli
Když přejdete na web, na kterém je nasazená ve webové roli a prohlížeč zobrazí podobný následující chybě serveru, může to znamenat, že chybí knihovna DLL.

![Chyba serveru v aplikaci '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostika problémů s tím, že vypíná vlastních chyb
Podrobnější informace o chybě můžete prohlížet konfigurace souboru web.config pro webovou roli chcete nastavit režim vlastních chyb vypnuto a znovu nasadit službu.

Chcete-li zobrazit podrobnější chyby bez použití vzdálené plochy:

1. Otevřete řešení v sadě Microsoft Visual Studio.
2. V **Průzkumníka řešení**, vyhledejte soubor web.config a otevřete ho.
3. V souboru web.config najděte část system.web a přidejte následující řádek:

    ```xml
    <customErrors mode="Off" />
    ```
4. Uložte soubor.
5. Znovu zabalit a službu znovu nasaďte.

Jakmile nové nasazení služby se zobrazí chybová zpráva s názvem chybějící sestavení nebo knihovny DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostikovat problémy tak, že vzdálené zobrazení chyby
Vzdálená plocha můžete použít pro přístup k roli a vzdáleně zobrazit podrobnější informace o chybě. Chcete-li zobrazit chyby pomocí vzdálené plochy postupujte následovně:

1. Ujistěte se, že je nainstalovaná sada Azure SDK 1.3 nebo novější.
2. Při nasazení řešení pomocí sady Visual Studio povolte vzdálenou plochu. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí sady Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. Na portálu Microsoft Azure, jakmile se zobrazí stav instance **připravené**, vzdáleně se přihlaste k instanci. Další informace o použití vzdálené plochy s cloudovými službami, naleznete v tématu [vzdáleném připojení k instancím rolí](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které se zadaly během konfigurace vzdálené plochy.
6. Otevřete okno příkazového řádku.
7. Zadejte `IPconfig`.
8. Poznamenejte si IPV4 adresu hodnotu.
9. Spusťte aplikaci Internet Explorer.
10. Zadejte adresu a název webové aplikace. Například, `http://<IPV4 Address>/default.aspx`.

Přejděte na web nyní vrátí více explicitní chybové zprávy:

* Chyba serveru v aplikaci '/'.
* Popis: Během provádění aktuálního webového požadavku došlo k neošetřené výjimce. Přečtěte si prosím trasování zásobníku pro další informace o chybě a o jejím původu v kódu.
* Podrobnosti výjimky: System.IO.FIleNotFoundException: Nelze načíst soubor nebo sestavení "Microsoft.WindowsAzure.StorageClient, verze 1.1.0.0, Culture = neutral, PublicKeyToken = = 31bf856ad364e35" nebo některá z jeho závislostí. Systém nemůže najít zadaný soubor.

Příklad:

![Chyba explicitní serveru v aplikaci '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostikujte problémy pomocí emulátoru compute
Emulátor služby výpočty Microsoft Azure můžete použít k diagnostice a řešení potíží s chybějících závislostí a chyby v souboru web.config.

Nejlepších výsledků dosáhnete při použití této metody diagnostiky by měl použít počítač nebo virtuální počítač, který má čistou instalaci systému Windows. Pro nejlepší simulaci prostředí Azure, použijte Windows Server 2008 R2 x64.

1. Nainstalujte samostatnou verzi [sady Azure SDK](https://azure.microsoft.com/downloads/).
2. Na vývojovém počítači sestavení projektu cloudové služby.
3. V Průzkumníku Windows přejděte ke složce bin\debug projekt cloudové služby.
4. Zkopírujte soubor .csx složky a .cscfg na počítači, který používáte k ladění problémů.
5. Vyčištění počítači otevřete okno příkazového řádku Azure SDK a zadejte `csrun.exe /devstore:start`.
6. Na příkazovém řádku zadejte `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Když se role spustila, zobrazí se podrobné informace o chybě v aplikaci Internet Explorer. Můžete také použít standardní řešení potíží s nástroji Windows při další diagnostice problému.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostikujte problémy pomocí IntelliTrace
Pro pracovníka a webovými rolemi, které používají rozhraní .NET Framework 4, můžete použít [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), která je k dispozici v sadě Microsoft Visual Studio Enterprise.

Postupujte podle těchto kroků nasadíte služby pomocí nástroje IntelliTrace povoleno:

1. Potvrďte, že je nainstalovaná sada Azure SDK 1.3 nebo novější.
2. Nasazení řešení pomocí sady Visual Studio. Během nasazení, zkontrolujte, **povolit IntelliTrace pro role rozhraní .NET 4** zaškrtávací políčko.
3. Jakmile se spustí instanci, otevřete **Průzkumníka serveru**.
4. Rozbalte **Azure\\Cloud Services** uzlu a najděte nasazení.
5. Rozbalte nasazení, dokud se nezobrazí instancí rolí. Klikněte pravým tlačítkem na jednu z instancí.
6. Zvolte **protokoly IntelliTrace zobrazení**. **IntelliTrace – Souhrn** se otevře.
7. Vyhledejte část výjimky souhrnu. Pokud existují výjimky, budou označeny v části **Data výjimky**.
8. Rozbalte **Data výjimky** a hledejte **System.IO.FileNotFoundException** chyby podobný následujícímu:

![Data výjimky, chybí soubor nebo sestavení](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Chybějící knihovny DLL a sestavení
K vyřešení chybějící knihovny DLL a chyby sestavení, postupujte takto:

1. Otevřete řešení v sadě Visual Studio.
2. V **Průzkumníka řešení**, otevřete **odkazy** složky.
3. Klikněte na tlačítko sestavení identifikované v chybě.
4. V **vlastnosti** podokně vyhledejte **vlastnost Kopírovat místně** a nastavte hodnotu na **True**.
5. Znovu nasadíte cloudovou službu.

Jakmile si ověříte, že byly odstraněny všechny chyby, můžete nasadit službu bez kontroly, zda **povolit IntelliTrace pro role rozhraní .NET 4** zaškrtávací políčko.

## <a name="next-steps"></a>Další postup
Zobrazit další [článků o řešení potíží](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pro cloudové služby.

Zjistěte, jak řešit problémy s cloudovou službou role pomocí Azure PaaS počítače diagnostická data, najdete v článku [Kevin Williamson blogovou sérii](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
