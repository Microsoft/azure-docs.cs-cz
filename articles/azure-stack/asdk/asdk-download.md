---
title: Stažení a extrakci Azure zásobníku Development Kit (ASDK) | Microsoft Docs
description: Popisuje postup stažení a extrakci Azure zásobníku Development Kit (ASDK).
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
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0cf389c9443a9cff477b884c277d72de27769afc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
ms.locfileid: "29975878"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Stažení a extrakci Azure zásobníku Development Kit (ASDK)
Jakmile ověříte, že hostitelského počítače development kit splňuje základní požadavky pro instalaci ASDK, je dalším krokem pro stažení a extrakci ASDK balíček pro nasazení získat Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Stažení ASDK
1. Než začnete stahování, ujistěte se, zda počítač splňuje následující požadavky:

  - Počítač musí mít minimálně 60 GB volné místo na disku k dispozici na čtyři samostatné, stejné logické pevné disky kromě na disk operačního systému.
  - [Rozhraní .NET framework 4.6 (nebo novější verze)](https://aka.ms/r6mkiy) musí být nainstalován.

2. [Přejděte na stránku Začínáme](https://azure.microsoft.com/overview/azure-stack/try/?v=try) kde můžete stáhnout Development Kit zásobník Azure, zadejte svoje údaje a pak klikněte na tlačítko **odeslání**.
3. Stažení a spuštění [kontrolu nasazení pro Azure zásobníku Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) skriptu kontrolu požadovaných součástí. Tento skript samostatné projde požadavcích kontroly provádí instalace pro Azure zásobníku Development Kit. Poskytuje způsob, jak potvrďte, že byly splněny požadavky na hardware a software před stažením větší balíčku pro Azure zásobníku Development Kit.
4. V části **stáhnout software**, klikněte na tlačítko **Azure zásobníku Development Kit**.

  > [!NOTE]
  > Stahování ASDK (AzureStackDevelopmentKit.exe) je přibližně 10GB.

## <a name="extract-the-asdk"></a>Extrahování ASDK
1. Po dokončení stahování, klikněte na tlačítko **spustit** ke spuštění ASDK Self-Extractor (AzureStackDevelopmentKit.exe).
2. Přečtěte si a přijměte zobrazených licenční smlouvy z **licenční smlouvy** Self-Extractor průvodce a potom klikněte na tlačítko **Další**.
3. Přečtěte si informace o prohlášení o ochraně osobních údajů zobrazené na **důležité informace** Self-Extractor průvodce a potom klikněte na tlačítko **Další**.
4. Vyberte umístění pro instalační soubory zásobník Azure extrahovat na **vyberte cílové umístění** Self-Extractor průvodce a potom klikněte na tlačítko **Další**. Výchozí umístění je *aktuální složce*\Azure zásobníku Development Kit. 
5. Zkontrolujte souhrn na cílové umístění **připraven k extrahování** Self-Extractor průvodce, a pak klikněte na tlačítko **extrahovat** k extrakci CloudBuilder.vhdx (přibližně 25 GB) a ThirdPartyLicenses.rtf soubory. Tento proces trvat delší dobu.
6. Zkopírovat nebo přesunout soubor CloudBuilder.vhdx kořenové jednotce C:\ (C:\CloudBuilder.vhdx) na hostitelském počítači ASDK.

> [!NOTE]
> Po extrahování souborů, můžete odstranit. EXE a. KOŠ soubory k obnovení místa na pevném disku. Nebo můžete zálohovat tyto soubory tak, aby nemusíte znovu stáhnout soubory, pokud potřebujete znovu nasadit ASDK.


## <a name="next-steps"></a>Další postup
[Příprava na hostitelském počítači ASDK](asdk-prepare-host.md)