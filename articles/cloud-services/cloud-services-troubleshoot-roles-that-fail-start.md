---
title: Poradce při potížích s rolemi, které se nepodařilo spustit | Dokumenty společnosti Microsoft
description: Zde jsou některé běžné důvody, proč role cloudové služby může selhat ke spuštění. Řešení těchto problémů jsou také k dispozici.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 869453d92f536a62aacc2be52598223158566ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122733"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Poradce při potížích s rolemi cloudové služby, které se nepodařilo spustit
Tady jsou některé běžné problémy a řešení související s rolemi Cloudových služeb Azure, které se nedaří spustit.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Chybějící knihovny DLL nebo závislosti
Nereagující role a role, které jsou cyklické mezi **inicializace**, **Zaneprázdněn**a **Zastavení** stavy mohou být způsobeny chybějící knihovny DLL nebo sestavení.

Příznaky chybějících knihoven DLL nebo sestavení mohou být:

* Instance role je cyklické prostřednictvím **inicializace**, **Zaneprázdněn**a **Zastavení** stavy.
* Instance role byla přesunuta na **Připraveno,** ale pokud přejdete do webové aplikace, stránka se nezobrazí.

Existuje několik doporučených metod pro zkoumání těchto problémů.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnostika chybějících problémů s dll ve webové roli
Když přejdete na web, který je nasazen ve webové roli, a prohlížeč zobrazí chybu serveru podobnou následující, může to znamenat, že chybí dll.

![Chyba serveru v aplikaci/.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnostika problémů vypnutím vlastních chyb
Úplnější informace o chybách lze zobrazit konfigurací souboru web.config pro webovou roli tak, aby nastavila vlastní chybový režim na Vypnuto, a opětovným nasazením služby.

Zobrazení úplnějších chyb bez použití vzdálené plochy:

1. Otevřete řešení v aplikaci Microsoft Visual Studio.
2. V **Průzkumníku řešení**vyhledejte soubor web.config a otevřete jej.
3. V souboru web.config vyhledejte oddíl system.web a přidejte následující řádek:

    ```xml
    <customErrors mode="Off" />
    ```
4. Uložte soubor.
5. Znovu zabalte a znovu nasaďte službu.

Po opětovném nasazení služby se zobrazí chybová zpráva s názvem chybějícího sestavení nebo dll.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnostika problémů zobrazením chyby na dálku
Vzdálená plocha můžete použít pro vzdálený přístup k roli a vzdálené zobrazení úplnějších informací o chybách. Pomocí následujících kroků můžete zobrazit chyby pomocí vzdálené plochy:

1. Ujistěte se, že je nainstalovaná sada Azure SDK 1.3 nebo novější.
2. Během nasazení řešení pomocí sady Visual Studio povolte vzdálenou plochu. Další informace najdete [v tématu Povolení připojení ke vzdálené ploše pro roli v Cloudových službách Azure pomocí sady Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. Na portálu Microsoft Azure, jakmile instance zobrazí stav **Připraveno**, vzdálené do instance. Další informace o používání vzdálené plochy pomocí cloudových služeb naleznete v [tématu Remote into role instance](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které byly zadány během konfigurace vzdálené plochy.
6. Otevřete příkazové okno.
7. Zadejte `IPconfig`.
8. Poznamenejte si hodnotu adresy IPV4.
9. Otevřete Internet Explorer.
10. Zadejte adresu a název webové aplikace. Například, `http://<IPV4 Address>/default.aspx`.

Přechod na web nyní vrátí více explicitní chybové zprávy:

* Chyba serveru v aplikaci/.
* Popis: Během provádění aktuálního webového požadavku došlo k neošetřené výjimce. Zkontrolujte trasování zásobníku další informace o chybě a kde pochází v kódu.
* Podrobnosti o výjimce: System.IO.FIleNotFoundException: Nelze načíst soubor nebo sestavení 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' nebo jednu z jeho závislostí. Systém nemůže najít zadaný soubor.

Například:

![Explicitní chyba serveru v aplikaci '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnostika problémů pomocí emulátoru výpočetních prostředků
Výpočetní emulátor Microsoft Azure můžete použít k diagnostice a řešení problémů s chybějícími závislostmi a chybami web.config.

Chcete-li dosáhnout nejlepších výsledků při používání této metody diagnostiky, měli byste použít počítač nebo virtuální počítač s čistou instalací systému Windows. Chcete-li co nejlépe simulovat prostředí Azure, použijte Windows Server 2008 R2 x64.

1. Nainstalujte samostatnou verzi [sady Azure SDK](https://azure.microsoft.com/downloads/).
2. Na vývojovém počítači sestavte projekt cloudové služby.
3. V Průzkumníkovi Windows přejděte do složky bin\debug projektu cloudové služby.
4. Zkopírujte složku CSX a soubor CSCFG do počítače, který používáte k ladění problémů.
5. Na čistém počítači otevřete okno příkazového `csrun.exe /devstore:start`řádku sady Azure SDK a zadejte .
6. Na příkazovém `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`řádku zadejte příkaz .
7. Při spuštění role se v aplikaci Internet Explorer zobrazí podrobné informace o chybě. K další diagnostice problému můžete problém dále diagnostikovat pomocí standardních nástrojů pro řešení potíží systému Windows.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnostika problémů pomocí intelliTrace
Pro pracovní a webové role, které používají rozhraní .NET Framework 4, můžete použít [intelliTrace](/visualstudio/debugger/intellitrace), který je k dispozici v aplikaci Microsoft Visual Studio Enterprise.

Chcete-li nasadit službu s povoleným technologiem IntelliTrace, postupujte takto:

1. Zkontrolujte, zda je nainstalovaná sada Azure SDK 1.3 nebo novější.
2. Nasaďte řešení pomocí sady Visual Studio. Během nasazení zaškrtněte políčko **Povolit intelliTrace pro role .NET 4.**
3. Po spuštění instance otevřete **Průzkumníka serveru**.
4. Rozbalte uzel **Cloudových služeb Azure\\** a vyhledejte nasazení.
5. Rozbalte nasazení, dokud se nezobrazí instance role. Klikněte pravým tlačítkem myši na jednu z instancí.
6. Zvolte **Zobrazit protokoly IntelliTrace**. Otevře se **souhrn IntelliTrace.**
7. Vyhledejte část výjimek v souhrnu. Pokud existují výjimky, bude oddíl označen jako **Data výjimek**.
8. Rozbalte **data výjimky** a vyhledejte chyby **System.IO.FileNotFoundException** podobné následujícím u následujících:

![Data výjimky, chybějící soubor nebo sestavení](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adresa chybějících knihoven DLL a sestavení
Chcete-li vyřešit chybějící chyby dll a sestavení, postupujte takto:

1. Otevřete řešení v sadě Visual Studio.
2. V **Průzkumníku řešení**otevřete složku **References.**
3. Klepněte na sestavení identifikované v chybě.
4. V podokně **Vlastnosti** **vyhledejte vlastnost Kopírovat místní** a nastavte hodnotu na **hodnotu True**.
5. Znovu nasaďte cloudovou službu.

Po ověření, že byly opraveny všechny chyby, můžete nasadit službu bez zaškrtnutí políčka **Povolit intelliTrace pro role .NET 4.**

## <a name="next-steps"></a>Další kroky
Zobrazit další [články o řešení potíží](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pro cloudové služby.

Informace o řešení problémů s rolí cloudové služby pomocí dat diagnostiky počítače Azure PaaS najdete v [tématu Kevin Williamson blog series](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
