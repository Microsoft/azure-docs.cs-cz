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
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87036178"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Spouštění operačního systému – počítač se neočekávaně restartoval nebo narazil na neočekávanou chybu.

Tento článek popisuje kroky pro řešení problémů, ve kterých se virtuální počítač pokouší o neočekávané restartování nebo chybu při instalaci systému Windows.

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](./boot-diagnostics.md) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že snímek obrazovky zobrazuje chybu instalace systému Windows s následující chybou:

**Počítač se neočekávaně restartoval nebo došlo k neočekávané chybě. Instalace systému Windows nemůže pokračovat. Chcete-li nainstalovat systém Windows, klikněte na tlačítko OK a restartujte počítač a poté instalaci spusťte znovu.**

![Došlo k chybě, když probíhá instalace systému Windows: počítač se neočekávaně restartoval nebo došlo k neočekávané chybě. Instalace systému Windows nemůže pokračovat. Chcete-li nainstalovat systém Windows, klikněte na tlačítko OK a restartujte počítač a poté instalaci spusťte znovu.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Chyba při spouštění instalačního programu systému Windows: počítač se neočekávaně restartoval nebo došlo k neočekávané chybě. Instalace systému Windows nemůže pokračovat. Chcete-li nainstalovat systém Windows, klikněte na tlačítko OK a restartujte počítač a poté instalaci spusťte znovu.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Příčina

Počítač se pokouší provést počáteční spuštění [generalizované image](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), ale dojde k problému, protože se zpracovává vlastní soubor odpovědí (unattend.xml). Vlastní soubory odpovědí se v Azure nepodporují. 

Soubor odpovědí je speciální soubor XML, který obsahuje nastavení definic a hodnot pro nastavení konfigurace, která chcete automatizovat během instalace instalace operačního systému Windows Server. Mezi možnosti konfigurace patří pokyny, jak rozdělit disky na oddíly, kde najít bitovou kopii Windows, která se má nainstalovat, použít kódy Product Key a další příkazy, které byste chtěli spustit.

V Azure se vlastní soubory odpovědí nepodporují. Pokud jste zadali vlastní soubor **Unattend.xml** pomocí `sysprep /unattend:<your file’s name>` Možnosti, může k této chybě dojít.

V Azure použijte možnost jako **počáteční prostředí (OOBE) systému** ve **Sysprep.exe**nebo použijte `sysprep /oobe` místo souboru Unattend.xml.

Tento problém se nejčastěji vytváří, když používáte **Sysprep.exe** s místním virtuálním počítačem k nahrání ZOBECNĚNÉHO virtuálního počítače do Azure. V této situaci může být také zajímat, jak správně nahrát zobecněný virtuální počítač.

## <a name="solution"></a>Řešení

### <a name="replace-unattended-answer-file-option"></a>Nahradit možnost souboru bezobslužné odpovědi

K této situaci dochází, když se image připravuje pro použití v Azure, ale použila vlastní soubor odpovědí, který se v Azure nepodporuje. Pokud jste použili **Nástroj Sysprep** s příznakem, který je podobný následujícímu příkazu:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- V předchozím příkazu nahraďte `<NameOfYourAnswerFile.XML>` názvem souboru.

Pokud chcete tento problém vyřešit, postupujte podle pokynů [pro Azure na stránce Příprava/zachycení image](../windows/upload-generalized-managed.md) a Příprava nové generalizované image. Během programu Sysprep Nepoužívejte `/unattend:<answerfile>` příznak. Místo toho použijte pouze následující příznaky:

`sysprep /oobe /generalize /shutdown`

- Rozhraní OOBE ( **out-of-box** ) je podporované nastavení pro virtuální počítače Azure.

Můžete také použít **grafické uživatelské rozhraní nástroje pro přípravu systému** k provedení stejné úlohy jako příkaz výše, a to výběrem níže uvedených možností:

- Přejít na integrované prostředí
- Generalizovat
- Vypnutí
 
![Okno nástroje pro přípravu systému s vybranými možnostmi OOBE, generalizace a vypnutí](./media/unexpected-restart-error-during-vm-boot/3.png)
