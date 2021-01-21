---
title: Spouštění operačního systému – počítač se neočekávaně restartoval nebo narazil na neočekávanou chybu.
description: Tento článek popisuje kroky pro řešení problémů, při kterých při instalaci systému Windows dojde k neočekávanému restartování nebo chybě virtuálního počítače.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632686"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Spouštění operačního systému – počítač se neočekávaně restartoval nebo narazil na neočekávanou chybu.

Tento článek popisuje kroky pro řešení problémů, ve kterých se virtuální počítač pokouší o neočekávané restartování nebo chybu při instalaci systému Windows.

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje chybu instalace systému Windows s následující chybou:

**Počítač se neočekávaně restartoval nebo došlo k neočekávané chybě. Instalace systému Windows nemůže pokračovat. Chcete-li nainstalovat systém Windows, klikněte na tlačítko OK a restartujte počítač a poté instalaci spusťte znovu.**

![Došlo k chybě, když probíhá instalace systému Windows: počítač se neočekávaně restartoval nebo došlo k neočekávané chybě. Instalace systému Windows nemůže pokračovat. Chcete-li nainstalovat systém Windows, klikněte na tlačítko OK a restartujte počítač a poté instalaci spusťte znovu.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Chyba při spouštění instalačního programu systému Windows: počítač se neočekávaně restartoval nebo došlo k neočekávané chybě. Instalace systému Windows nemůže pokračovat. Chcete-li nainstalovat systém Windows, klikněte na tlačítko OK a restartujte počítač a poté instalaci spusťte znovu.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Příčina

Počítač se pokouší provést počáteční spuštění [generalizované image](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), ale dojde k problému, protože se zpracovává vlastní soubor odpovědí (Unattend.xml). **Vlastní soubory odpovědí se v Azure nepodporují**. 

Soubor odpovědí je speciální soubor XML, který obsahuje nastavení definic a hodnot pro nastavení konfigurace, která chcete automatizovat během instalace instalace operačního systému Windows Server. Mezi možnosti konfigurace patří pokyny, jak rozdělit disky na oddíly, kde najít bitovou kopii Windows, která se má nainstalovat, použít kódy Product Key a další příkazy, které byste chtěli spustit.

V Azure se znovu nepodporují vlastní soubory odpovědí. Proto k této situaci dochází, když se image připravuje pro použití v Azure, ale zadali jste vlastní soubor Unattend.xml pomocí **nástroje Sysprep** s příznakem podobným následujícímu příkazu:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

V Azure použijte v **grafickém uživatelském rozhraní nástroje pro přípravu systému** možnost spustit systém za běhu **(OOBE)** nebo použijte `sysprep /oobe` místo souboru Unattend.xml.

Tento problém se nejčastěji vytváří při použití nástroje Sysprep s místním virtuálním počítačem k nahrání zobecněného virtuálního počítače do Azure. V této situaci může být také zajímat, jak správně nahrát zobecněný virtuální počítač.

## <a name="solution"></a>Řešení

### <a name="do-not-use-unattendxml"></a>Nepoužívat Unattend.xml

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Pokud chcete tento problém vyřešit, postupujte podle pokynů [pro Azure na stránce Příprava/zachycení image](../windows/upload-generalized-managed.md) a Příprava nové generalizované image. Během programu Sysprep **Nepoužívejte `/unattend:<your file’s name>` příznak**. Místo toho použijte pouze následující příznaky:

`sysprep /oobe /generalize /shutdown`

- Rozhraní OOBE (out-of-box) je podporované nastavení pro virtuální počítače Azure.

Můžete také použít **grafické uživatelské rozhraní nástroje pro přípravu systému** k provedení stejné úlohy jako příkaz výše, a to výběrem níže uvedených možností:

- Přejít na integrované prostředí
- Generalizovat
- Vypnutí
 
![Okno nástroje pro přípravu systému s vybranými možnostmi OOBE, generalizace a vypnutí](./media/unexpected-restart-error-during-vm-boot/3.png)
