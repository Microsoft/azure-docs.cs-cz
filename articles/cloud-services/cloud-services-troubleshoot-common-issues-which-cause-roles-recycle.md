---
title: Běžné příčiny cloudové služby rolí recyklace | Microsoft Docs
description: Cloudové služby role, kterou najednou recykluje může způsobit významné výpadky. Tady jsou některé běžné problémy, které způsobí role recyklace, které mohou pomoci zkrátit dobu prostojů.
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
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 7f513a7d3fe44cbbf06855059c87c10ddceef7c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984282"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Běžné potíže, které můžou způsobit recyklaci rolí
Tento článek popisuje některé běžné příčiny problémů s nasazením a poskytuje tipy k řešení potíží k řešení těchto problémů. Jako ukazatel toho, zda existuje problém s aplikací je, když se nepodaří spustit instanci role, nebo ji cyklů mezi stavy inicializace, zaneprázdněn a ukončení.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Chybějící závislosti modulu runtime
Pokud roli v aplikaci závisí na žádném sestavení, které není součástí rozhraní .NET Framework nebo Azure spravovanou knihovnu, je nutné explicitně zahrnout toto sestavení v balíčku aplikace. Uvědomte si, že nejsou dostupné v Azure ve výchozím nastavení ostatní platformy Microsoft. Pokud vaše role závisí na takových rozhraní, musíte přidat tyto sestavení do balíčku aplikace.

Než sestavení a balíček aplikace, ověřte následující:

* Pokud pomocí sady Visual studio, zkontrolujte **místní kopie** je nastavena na **True** pro každé odkazované sestavení v projektu, který není součástí sady Azure SDK nebo rozhraní .NET Framework.
* Ujistěte se, že v souboru web.config neodkazuje na žádné nepoužité sestavení v elementu kompilace.
* **Akce sestavení** z každé .cshtml soubor pro **obsahu**. To zajistí, že soubory se zobrazí správně v balíčku a umožňuje další odkazované soubory se objeví v balíčku.

## <a name="assembly-targets-wrong-platform"></a>Sestavení cílů chybné platformě.
Azure je prostředí na 64-bit. Rozhraní .NET sestavení zkompilovaného pro 32bitové cílové proto nebude fungovat v Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Role, vyvolá neošetřené výjimky během inicializace nebo ukončení
Jakékoli výjimky, které jsou vyvolány metody [RoleEntryPoint] třída, která obsahuje [OnStart], [OnStop], a [spustit] metody, jsou neošetřené výjimky. Pokud dojde k neošetřené výjimce v jednom z těchto metod, bude funkce role. Pokud role je recyklace opakovaně, může vyvolání neošetřenou výjimku při každém pokusu spustit.

## <a name="role-returns-from-run-method"></a>Vrátí role z metoda Run
[spustit] metoda má běžela bez omezení. Pokud váš kód přepíše [spustit] metody ho měli režimu spánku po neomezenou dobu. Pokud [spustit] metoda vrací výsledek, recykluje roli.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nesprávné nastavení DiagnosticsConnectionString
Pokud aplikace používá Azure Diagnostics, musíte zadat konfiguračním souboru služby `DiagnosticsConnectionString` nastavení konfigurace. Toto nastavení musí určovat připojení HTTPS k vašemu účtu úložiště v Azure.

Abyste ověřili, že vaše `DiagnosticsConnectionString` před nasazením balíčku aplikace do Azure je nastavení správné, ověřte následující:  

* `DiagnosticsConnectionString` Nastavení bodů na platný účet úložiště v Azure.  
  Ve výchozím nastavení toto nastavení odkazuje na účet emulované úložiště, takže musíte explicitně změnit toto nastavení před nasazením balíčku aplikace. Pokud není toto nastavení změnit, je vyvolána výjimka, když se pokusí spustit monitorování diagnostiky role instance. To může způsobit instance role recyklace po neomezenou dobu.
* Připojovací řetězec je zadána v následujícím [formátu](../storage/common/storage-configure-connection-string.md). (Protokol musí být zadán jako protokol HTTPS.) Nahraďte *MyAccountName* s názvem účtu úložiště a *MyAccountKey* vaším přístupovým klíčem:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Pokud vyvíjíte aplikace pomocí nástroje Azure pro sadu Microsoft Visual Studio, můžete tuto hodnotu nastavit na stránkách vlastností.

## <a name="exported-certificate-does-not-include-private-key"></a>Exportovaný certifikát neobsahuje privátní klíč
Pokud chcete spustit webovou roli v rámci zabezpečení SSL, je nutné zajistit, že svůj certifikát pro správu exportovaný obsahuje privátní klíč. Pokud použijete *správce certifikátů systému Windows* a vyexportovat si certifikát, je nutné vybrat **Ano** pro **exportovat soukromý klíč** možnost. Certifikát musí být exportován ve formátu PFX, který je formátem pouze aktuálně podporované.

## <a name="next-steps"></a>Další kroky
Zobrazení [řešení potíží s články](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) pro cloudové služby.

Zobrazit další role recyklace scénáře v [řady blogu kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[spustit]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
