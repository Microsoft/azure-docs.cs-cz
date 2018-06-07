---
title: Rozšíření virtuálního počítače Azure a funkce | Microsoft Docs
description: Zjistěte, jaké anre rozšíření virtuálního počítače Azure jak používat s virtuálními počítači Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 04f6d68feccf4a9b2bf2fa0f03ad8bd978cf5f17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653259"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Rozšíření virtuálního počítače Azure a funkce
Rozšíření virtuálních počítačů (VM) Azure jsou malých aplikacích, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure, můžete použít existující bitové kopie a potom si je přizpůsobit, v rámci vašeho nasazení, můžete získávání mimo firmy vlastní vytváření bitové kopie.

Platformy Azure hostitelem mnoho rozšíření, která v rozsahu od konfigurace virtuálního počítače, monitorování, zabezpečení a nástroj aplikace. Vydavatelé trvat aplikace, potom zabalit do rozšíření a zjednodušení instalace, tak, aby všechny, které je potřeba zadat povinné parametry. 

 Při volbě velké první a rozšíření třetích stran, pokud aplikace v úložišti rozšíření neexistuje, pak můžete použít rozšíření vlastních skriptů a konfigurace virtuálního počítače pomocí vlastní skripty a příkazy.

Příklady klíčových scénářů, které se používají rozšíření pro:
* Konfigurace virtuálního počítače, můžete Powershell DSC (Konfigurace požadovaného stavu), Chef, Puppet a rozšíření vlastní skript k instalaci agentů konfigurace virtuálních počítačů a konfiguraci virtuálního počítače. 
* AV produkty, jako je například Symantec, obnovit.
* Virtuální počítač nástroj ohrožení zabezpečení, například Qualys, Rapid7, HPE.
* Virtuální počítač a nástroje, jako je například DynaTrace, sledovací proces sítě Azure, Site24x7 a Stackify monitorování aplikací.

Rozšíření můžete dodávat s nové nasazení virtuálního počítače. Například může být součástí větší nasazení, konfiguraci aplikací na zřizování virtuálních počítačů, nebo spouštění na všech podporovaných rozšíření provozovat systémy post nasazení.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak najdete jaké rozšíření jsou k dispozici?
Můžete dostupná rozšíření zobrazit v okně virtuálního počítače na portálu, v seznamu rozšíření, reprezentuje jenom malé množství pro úplný seznam, můžete pomocí nástrojů příkazového řádku najdete v tématu [zjišťování rozšíření virtuálního počítače pro Linux](features-linux.md) a [ Zjišťování rozšíření virtuálního počítače pro systém Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak můžete nainstalovat rozšíření?
Rozšíření virtuálního počítače Azure můžete spravovat pomocí Azure CLI 2.0, prostředí Azure PowerShell, šablon Azure Resource Manageru a webu Azure portal. Pokud chcete vyzkoušet rozšíření, můžete přejít na portálu Azure, vyberte možnost rozšíření vlastních skriptů, pak předat v příkazu / skriptu a spuštění rozšíření.

Pokud chcete stejné rozšiřující jste přidali v portálu pomocí rozhraní příkazového řádku nebo správce prostředků šablony, naleznete v dokumentaci k jiné rozšíření, jako například [rozšíření vlastních skriptů Windows](custom-script-windows.md) a [rozšíření vlastních skriptů Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak lze spravovat životní cyklus aplikace rozšíření?
Není nutné se připojit k virtuálnímu počítači přímo na instalaci nebo odstranit rozšíření. Životní cyklus aplikace rozšíření Azure je spravuje mimo virtuální počítač a integrovat do platformy Azure, získáte také integrované stav rozšíření.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Cokoliv jiného I by měl přemýšlíte o rozšíření?
Rozšíření instalovat aplikace, stejně jako všechny aplikace, které jsou některé požadavky pro rozšíření, že je seznam podporované operační systémy Linux a Windows a je potřeba mít virtuální počítač Azure agenty nainstalovat. Některé aplikace pro jednotlivé rozšíření virtuálního počítače může mít vlastní požadavky prostředí, jako je například přístup ke koncovému bodu.

## <a name="next-steps"></a>Další postup
* Další informace o fungování agenta pro Linux a rozšíření najdete v tématu [rozšíření virtuálního počítače Azure a funkce pro Linux](features-linux.md).
* Další informace o fungování agenta hosta Windows a rozšíření najdete v tématu [rozšíření virtuálního počítače Azure a funkce ve Windows](features-windows.md).  
* Nainstalujte agenta hosta Windows, najdete v tématu [přehled agenta virtuálního počítače Windows Azure ](agent-windows.md).  
* Chcete-li instalovat agenta systému Linux, přečtěte si téma [přehled agenta virtuálního počítače Azure Linux ](agent-linux.md).  

