---
title: Běžné příčiny recyklace rolí cloudových služeb | Dokumenty společnosti Microsoft
description: Úloha cloudové služby, která se náhle recykluje, může způsobit významné prostoje. Zde jsou některé běžné problémy, které způsobují recyklování rolí, což vám může pomoci snížit prostoje.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 554508b1bf784e306cd12a4a601f908e06320933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154981"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Běžné potíže, které můžou způsobit recyklaci rolí
Tento článek popisuje některé běžné příčiny problémů s nasazením a poskytuje tipy pro řešení potíží, které vám pomohou tyto problémy vyřešit. Označení, že existuje problém s aplikací je v případě, že instance role se nezdaří spustit, nebo cykly mezi inicializace, zaneprázdněn a zastavení stavy.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Chybějící závislosti modulu runtime
Pokud role ve vaší aplikaci závisí na libovolném sestavení, které není součástí rozhraní .NET Framework nebo spravované knihovny Azure, musíte explicitně zahrnout toto sestavení v balíčku aplikace. Mějte na paměti, že ostatní architektury Microsoftu nejsou ve výchozím nastavení v Azure dostupné. Pokud vaše role závisí na takové rozhraní, je nutné přidat tato sestavení do balíčku aplikace.

Před sestavením a balíčkem aplikace ověřte následující:

* Pokud používáte Visual Studio, ujistěte se, **že copy local** vlastnost je nastavena na **True** pro každé odkazované sestavení v projektu, který není součástí sady Azure SDK nebo .NET Framework.
* Ujistěte se, že soubor web.config neodkazuje na žádná nepoužívaná sestavení v elementu kompilace.
* **Akce sestavení** každého souboru .cshtml je nastavena na **obsah**. Tím zajistíte, že soubory se zobrazí správně v balíčku a umožňuje další odkazované soubory se zobrazí v balíčku.

## <a name="assembly-targets-wrong-platform"></a>Sestavení cílí na nesprávnou platformu
Azure je 64bitové prostředí. Proto sestavení .NET zkompilovaná pro 32bitový cíl nebude fungovat v Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Role vyvolá neošetřené výjimky při inicializaci nebo zastavení.
Všechny výjimky, které jsou vyvolány metodami [Třídy RoleEntryPoint,] která zahrnuje metody [OnStart], [OnStop]a [Run,] jsou neošetřené výjimky. Pokud dojde k neošetřené výjimce v jedné z těchto metod, role bude recyklovat. Pokud role je recyklace opakovaně, může být vyvolání neošetřené výjimky pokaždé, když se pokusí spustit.

## <a name="role-returns-from-run-method"></a>Vrácení role z metody Run
Run [Run] Metoda je určena ke spuštění po neomezenou dobu. Pokud váš kód přepíše [Run] metoda, by měl spát neomezeně dlouho. Pokud [run] metoda vrátí, role recykluje.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nesprávné nastavení programu DiagnosticsConnectionString
Pokud aplikace používá Azure Diagnostics, musí `DiagnosticsConnectionString` konfigurační soubor služby zadat nastavení konfigurace. Toto nastavení by mělo určit připojení HTTPS k vašemu účtu úložiště v Azure.

Chcete-li `DiagnosticsConnectionString` se ujistit, že vaše nastavení je správné před nasazením balíčku aplikace do Azure, ověřte následující:  

* Nastavení `DiagnosticsConnectionString` odkazuje na platný účet úložiště v Azure.  
  Ve výchozím nastavení toto nastavení odkazuje na účet emulovaného úložiště, takže je nutné explicitně změnit toto nastavení před nasazením balíčku aplikace. Pokud toto nastavení nezměníte, je vyvolána výjimka, když se instance role pokusí spustit diagnostický monitor. To může způsobit, že instance role recyklovat neomezeně dlouho.
* Připojovací řetězec je určen v následujícím [formátu](../storage/common/storage-configure-connection-string.md). (Protokol musí být zadán jako HTTPS.) Nahraďte *název MyAccountName* názvem svého účtu úložiště a *klíč MyAccountKey* pomocí přístupového klíče:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Pokud vyvíjíte aplikaci pomocí Nástroje Azure pro Microsoft Visual Studio, můžete tuto hodnotu nastavit pomocí stránek vlastností.

## <a name="exported-certificate-does-not-include-private-key"></a>Exportovaný certifikát neobsahuje soukromý klíč.
Chcete-li spustit webovou roli v části SSL, musíte zajistit, aby exportovaný certifikát správy zahrnoval soukromý klíč. Pokud k exportu certifikátu používáte *Správce certifikátů systému Windows,* nezapomeňte vybrat **možnost Ano** pro možnost **Exportovat soukromý klíč.** Certifikát musí být exportován ve formátu PFX, což je jediný aktuálně podporovaný formát.

## <a name="next-steps"></a>Další kroky
Zobrazit další [články o řešení potíží](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pro cloudové služby.

Zobrazit další scénáře recyklace rolí na [blogu série Kevin Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)je .

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[Při spuštění]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Spustit]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
