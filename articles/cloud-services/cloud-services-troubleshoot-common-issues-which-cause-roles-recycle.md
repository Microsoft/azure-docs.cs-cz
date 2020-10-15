---
title: Běžné příčiny recyklace rolí cloudové služby | Microsoft Docs
description: Role cloudové služby, která se náhle recykluje, může způsobit výrazné výpadky. Tady jsou některé běžné problémy, které způsobují recyklaci rolí, což vám může přispět k menšímu výpadku.
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
ms.openlocfilehash: 0484eb919a9de11b64dcc3334c5a9a942d875ca6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075123"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Běžné potíže, které můžou způsobit recyklaci rolí
Tento článek popisuje některé z běžných příčin problémů při nasazení a poskytuje tipy k odstraňování potíží, které vám pomůžou tyto problémy vyřešit. Označuje, že problém s aplikací existuje, když se instance role nepovede spustit, nebo se zacykluje mezi inicializací, zaneprázdněním a stavem zastavení.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Chybějící závislosti modulu runtime
Pokud role ve vaší aplikaci spoléhá na jakékoli sestavení, které není součástí .NET Framework nebo spravované knihovny Azure, musíte toto sestavení explicitně zahrnout do balíčku aplikace. Mějte na paměti, že ve výchozím nastavení nejsou v Azure k dispozici další rozhraní Microsoft. Pokud vaše role spoléhá na takové rozhraní, je nutné přidat tato sestavení do balíčku aplikace.

Před sestavením a zabalením aplikace ověřte následující:

* Pokud používáte sadu Visual Studio, ujistěte se, že je vlastnost **Kopírovat místní** nastavena na **hodnotu true** pro každé odkazované sestavení v projektu, které není součástí sady Azure SDK nebo .NET Framework.
* Ujistěte se, že web.config soubor neodkazuje na nepoužívaná sestavení v elementu compilation.
* **Akce sestavení** každého souboru. cshtml je nastavena na **obsah**. Tím se zajistí, že se soubory v balíčku zobrazí správně a povolí se v balíčku zobrazit další odkazované soubory.

## <a name="assembly-targets-wrong-platform"></a>Sestavení cílí na nesprávnou platformu.
Azure je 64 prostředí. Proto sestavení .NET zkompilované pro 32 cíl nebudou v Azure fungovat.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Role vyvolává neošetřené výjimky při inicializaci nebo zastavování.
Všechny výjimky, které jsou vyvolány metodami třídy [RoleEntryPoint] , která obsahuje metody [OnStart, OnStart]a [Run] [, jsou]neošetřené výjimky. Pokud dojde k neošetřené výjimce v jedné z těchto metod, dojde k recyklování této role. Pokud se role opakovaně recykluje, může při každém pokusu o spuštění dojít k vyvolání neošetřené výjimky.

## <a name="role-returns-from-run-method"></a>Role vrátí z metody Run.
Metoda [Run] je určena ke spuštění po neomezenou dobu. Pokud váš kód přepisuje metodu [Run] , mělo by být v režimu spánku po neomezenou dobu. Pokud metoda [Run] vrátí, recykluje roli.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nesprávné nastavení DiagnosticsConnectionString
Pokud aplikace používá Azure Diagnostics, musí konfigurační soubor služby určovat `DiagnosticsConnectionString` nastavení konfigurace. Toto nastavení by mělo určovat připojení HTTPS k vašemu účtu úložiště v Azure.

Abyste měli jistotu, že `DiagnosticsConnectionString` je nastavení správné, než balíček aplikace nasadíte do Azure, ověřte následující:  

* `DiagnosticsConnectionString`Nastavení odkazuje na platný účet úložiště v Azure.  
  Ve výchozím nastavení toto nastavení ukazuje na Emulovaný účet úložiště, takže musíte explicitně změnit toto nastavení před nasazením balíčku aplikace. Pokud toto nastavení nezměníte, vyvolá se výjimka, když se instance role pokusí spustit monitor diagnostiky. To může způsobit, že se instance role recykluje po neomezenou dobu.
* Připojovací řetězec je zadán v následujícím [formátu](../storage/common/storage-configure-connection-string.md). (Protokol musí být zadaný jako HTTPS.) Nahraďte *MyAccountName* názvem vašeho účtu úložiště a *MyAccountKey* pomocí přístupového klíče:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Pokud vyvíjíte aplikaci pomocí nástrojů Azure pro Microsoft Visual Studio, můžete tuto hodnotu nastavit pomocí stránek vlastností.

## <a name="exported-certificate-does-not-include-private-key"></a>Exportovaný certifikát nezahrnuje privátní klíč.
Chcete-li spustit webovou roli v rámci TLS, je nutné zajistit, aby exportovaný certifikát pro správu obsahoval privátní klíč. Pokud k exportu certifikátu používáte *Správce certifikátů Windows* , nezapomeňte u možnosti **exportovat privátní klíč** vybrat **Ano** . Certifikát musí být exportován ve formátu PFX, což je jediný aktuálně podporovaný formát.

## <a name="next-steps"></a>Další kroky
Podívejte se na další články týkající se [řešení potíží](../index.yml?product=cloud-services&tag=top-support-issue) pro Cloud Services.

Podívejte se na další scénáře recyklace rolí na [blogu Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Spustit]: /previous-versions/azure/reference/ee772746(v=azure.100)