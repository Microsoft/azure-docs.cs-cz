---
title: Běžné příčiny recyklování rolí cloudové služby | Dokumentace Microsoftu
description: Role cloudové služby, který náhle recykluje může způsobovat významné výpadky. Tady jsou některé běžné problémy, které můžou způsobit recyklaci, rolí, které může pomoci snížit prostoje.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 20b98fa9656c9c66a81ff98a70fcdbfb29d4dad6
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35899460"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Běžné potíže, které můžou způsobit recyklaci rolí
Tento článek popisuje některé běžné příčiny problémů s nasazením a poskytuje tipy pro řešení potíží, které vám pomohou vyřešit tyto problémy. Jako ukazatel toho, že existuje problém s aplikací je role instance se nepodaří spustit, nebo ho cyklů mezi stavy inicializace, zaneprázdněný a zastavuje.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Chybějící závislosti modulu runtime
Pokud role ve vaší aplikaci závisí na všech sestavení, které není součástí rozhraní .NET Framework nebo Azure – spravovaná knihovna, je nutné explicitně zahrnout sestavení v balíčku aplikace. Uvědomte si, že ostatní platformy Microsoft nejsou k dispozici v Azure ve výchozím nastavení. Pokud vaše role závisí na těchto rozhraní, musíte přidat tato sestavení do balíčku aplikace.

Před sestavit a zabalit aplikaci, ověřte následující:

* Pokud používáte Visual studio, ujistěte se, že **Kopírovat místně** je nastavena na **True** pro každého odkazovaného sestavení v projektu, který není součástí sady Azure SDK nebo rozhraní .NET Framework.
* Ujistěte se, že v souboru web.config neodkazuje na žádné nepoužité sestavení v elementu kompilace.
* **Akce sestavení** každý .cshtml souboru se nastaví na **obsahu**. Tím se zajistí, že soubory se zobrazí správně v balíčku a umožňuje další odkazované soubory zobrazit v balíčku.

## <a name="assembly-targets-wrong-platform"></a>Špatná platforma sestavení cíle
Azure je prostředí s 64bitovým kompilátorem. Sestavení .NET, které jsou kompilovány pro 32bitové cílové proto nebude fungovat v Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Role při inicializaci nebo zastavení vyvolává neošetřené výjimky
Všechny výjimky, které jsou vyvolány pomocí metody [RoleEntryPoint] třída, která obsahuje [Operace OnStart], [OnStop], a [Spuštění] metody, jsou neošetřené výjimky. Pokud dojde k neošetřené výjimce v jednom z těchto metod, budou recyklovat roli. Pokud role se recykluje opakovaně, může došlo k neošetřené výjimce pokaždé, když se pokusí spustit.

## <a name="role-returns-from-run-method"></a>Role vracející se z metody Run
[Spuštění] metoda je určena pro spuštění po neomezenou dobu. Pokud váš kód přepíše [Spuštění] metody ji by měl přejít do režimu spánku po neomezenou dobu. Pokud [Spuštění] metoda vrací výsledek, recyklování role.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nesprávné nastavení DiagnosticsConnectionString
Pokud aplikace používá Azure Diagnostics, musíte zadat konfigurační soubor služby `DiagnosticsConnectionString` nastavení konfigurace. Toto nastavení by měl určit připojení HTTPS k vašemu účtu úložiště v Azure.

Chcete-li zajistit vaše `DiagnosticsConnectionString` nastavení je správný, před nasazením balíčku aplikace do Azure, ověřte následující:  

* `DiagnosticsConnectionString` Nastavení bodů na platný účet úložiště v Azure.  
  Ve výchozím nastavení toto nastavení odkazuje na účet emulované úložiště, musí explicitně toto nastavení změníte před nasazením balíčku aplikace. Pokud není toto nastavení změnit, je vyvolána výjimka při pokusu o spuštění monitorování diagnostiky role instance. To může způsobit instanci role recyklovat po neomezenou dobu.
* Je zadaný připojovací řetězec v následujícím [formátu](../storage/common/storage-configure-connection-string.md). (Protokol musí zadat jako HTTPS). Nahraďte *MyAccountName* s názvem účtu úložiště a *MyAccountKey* přístupovým klíčem:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Pokud vyvíjíte aplikace pomocí nástroje Azure pro sadu Microsoft Visual Studio, můžete tuto hodnotu nastavit na stránkách vlastností.

## <a name="exported-certificate-does-not-include-private-key"></a>Exportovaný certifikát neobsahuje privátní klíč
Ke spuštění webové role v rámci SSL, musíte zajistit, že certifikát pro správu. exportovaný obsahuje privátní klíč. Pokud používáte *správce certifikátů Windows* exportujte certifikát, je potřeba vybrat možnost **Ano** pro **exportovat soukromý klíč** možnost. Certifikát musí být exportován ve formátu PFX, který je jediným formátem v tuto chvíli nepodporuje.

## <a name="next-steps"></a>Další postup
Zobrazit další [článků o řešení potíží](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pro cloudové služby.

Zobrazit další role recykluje scénáře v [Kevin Williamson blogovou sérii](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[Operace OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Spuštění]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
