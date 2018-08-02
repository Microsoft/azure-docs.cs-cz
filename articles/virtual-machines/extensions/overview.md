---
title: Funkce a rozšíření virtuálních počítačů Azure | Dokumentace Microsoftu
description: Naučte se, jaké anre rozšíření virtuálního počítače Azure k použití s virtuálními počítači Azure
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: ec201f7f82aea97b9927b85a6b185fad51f6081d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412596"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Funkce a rozšíření virtuálních počítačů Azure
Rozšíření virtuálních počítačů (VM) Azure jsou malých aplikací, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure, můžete použít existující Image a pak si je přizpůsobit, jako součást nasazení, začít z firmy vlastní vytváření bitové kopie.

Platforma Azure je hostitelem mnoho rozšíření, které v rozsahu od konfigurace virtuálního počítače, monitorování, zabezpečení a nástroje aplikace. Vydavatelé trvat aplikace, potom zabalit do rozšíření a zjednodušit instalaci, takže všechno, co je potřeba je zadat povinné parametry. 

 Existuje velké řadu první a rozšíření jiných výrobců, pokud aplikace v úložišti rozšíření neexistuje, pak můžete použít rozšíření vlastních skriptů a na virtuálním počítači nakonfigurovat vlastní skripty a příkazy.

Příklady klíčových scénářů, které se používají rozšíření:
* Konfigurace virtuálního počítače, můžete použít Powershell DSC (Desired State Configuration), Chef, Puppet nebo rozšíření vlastních skriptů k instalaci konfigurace agentů virtuálních počítačů a konfiguraci virtuálního počítače. 
* Antivirové produkty, jako například Symantec, ESET.
* Nástroj ohrožení zabezpečení virtuálních počítačů, například Qualys, Rapid7, HPE.
* Virtuální počítač a nástroje, jako je například DynaTrace, Azure Network Watcher, Site24x7 a Stackify monitorování aplikací.

Rozšíření je možné seskupit s nasazením nového virtuálního počítače. Například může být součástí větší nasazení, konfigurace aplikací na zřizování virtuálních počítačů, nebo spouštět všechny podporované rozšíření provozuje systémy po nasazení.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak najdu, jaká rozšíření jsou k dispozici?
Můžete dostupná rozšíření zobrazit v okně virtuálního počítače na portálu v části rozšíření, to představuje jen malou, seznam, můžete používat nástroje rozhraní příkazového řádku, naleznete v tématu [zjišťování rozšíření virtuálních počítačů pro Linux](features-linux.md) a [ Zjišťování rozšíření virtuálních počítačů pro Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak můžete nainstalovat rozšíření?
Rozšíření virtuálního počítače Azure je možné spravovat pomocí rozhraní příkazového řádku Azure CLI 2.0, Azure Powershellu, šablon Azure Resource Manageru a webu Azure portal. Pokud chcete vyzkoušet rozšíření, můžete přejít na webu Azure portal vyberte rozšíření vlastních skriptů, potom předejte příkazu / skript a spustíte rozšíření.

Pokud chcete do stejného rozšíření, které jste přidali na portálu pomocí šablony Resource Manageru nebo rozhraní příkazového řádku, najdete dokumentaci k jiné rozšíření, jako například [rozšíření vlastních skriptů Windows](custom-script-windows.md) a [rozšíření vlastních skriptů Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak spravovat životní cyklus aplikace rozšíření?
Nemusíte připojit přímo k instalaci nebo odstranit rozšíření virtuálního počítače. Životní cyklus aplikace rozšíření Azure se spravovanými mimo virtuální počítač a integrované do platformy Azure, budete mít také integrovaného stav rozšíření.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Cokoli, co jsem by měl být uvažujete o pro rozšíření?
Rozšíření instalovat aplikace, stejně jako všechny aplikace jsou některé požadavky, že rozšíření je seznam podporovaných operačních systémů Linux a Windows a budete muset nainstalovaní agenti virtuálního počítače Azure. Některé jednotlivé aplikace rozšíření virtuálního počítače mohou mít své vlastní požadavky prostředí, jako je například přístup do koncového bodu.

## <a name="next-steps"></a>Další postup
* Další informace o fungování agenta pro Linux a rozšíření najdete v tématu [funkcí a rozšíření virtuálních počítačů Azure pro Linux](features-linux.md).
* Další informace o fungování agenta hosta Windows a rozšíření najdete v tématu [funkcí a rozšíření virtuálních počítačů Azure pro Windows](features-windows.md).  
* Chcete-li nainstalovat agenta hosta Windows, naleznete v tématu [přehled agenta virtuálního počítače Windows Azure ](agent-windows.md).  
* Pokud chcete nainstalovat agenta pro Linux, najdete v článku [přehled agenta virtuálního počítače Linux Azure ](agent-linux.md).  

