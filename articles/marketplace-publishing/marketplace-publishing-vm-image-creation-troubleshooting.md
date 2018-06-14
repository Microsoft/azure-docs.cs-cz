---
title: Postup řešení běžných problémů při vytváření virtuálního pevného disku | Microsoft Docs
description: Odpovědi na časté otázky řešení potíží a problémů při vytváření virtuálního pevného disku.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: mbaldwin
ms.openlocfilehash: 65361ad5bd7c3311c428b64b8476ec8f2ea2d17b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29941597"
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Postup řešení běžných problémů s došlo při vytváření virtuálního pevného disku
Tento článek je určena k Azure Marketplace vydavatele nebo spolusprávcem, který může zaznamenat problém nebo mít běžné otázky při publikování nebo správu jejich solution(s) virtuálního počítače.

1. Změna názvu hostitele
   
    Po vytvoření virtuálního počítače uživatele nelze aktualizovat název hostitele.
2. Jak obnovit služby Vzdálená plocha nebo jeho heslo pro přihlášení?
   
   * [Referenční informace pro virtuální počítač s Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Referenční informace pro virtuální počítač s Linuxem](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Jak vygenerovat nový ssh certifikáty?
   
   Podrobnosti najdete odkaz na: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Jak nakonfigurovat certifikát sítě VPN otevřené?
   
   Podrobnosti najdete odkaz na: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Co je zásady podpory pro spuštění serverového softwaru společnosti Microsoft v prostředí virtuálního počítače Microsoft Azure (infrastruktura jako služba)?
   
   Podrobnosti najdete odkaz na: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Mají virtuální počítače žádné jedinečný identifikátor?
   
   Azure kóduje jedinečné ID virtuálního počítače Azure v každé virtuální počítač. Najdete v podrobnostech v tomto blogu a dokumentaci.
7. Do virtuálního počítače, jak lze spravovat rozšíření vlastních skriptů v úloze pro spuštění?
   
   Podrobnosti najdete odkaz na: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Postup vytvoření virtuálního počítače z portálu Azure pomocí virtuálního pevného disku, který je nahrán do úložiště úrovně premium?
   
   Tato funkce ještě nepodporujeme.
9. Je podporováno 32bitovou aplikaci v Azure Marketplace?
   
   Naleznete odkaz podrobnosti o zásadách podpory: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Pokaždé, když chcete vytvořit bitovou kopii z mé virtuálních pevných disků, zobrazí chyba ". Virtuální pevný disk je už registrovaný s úložištěm image jako prostředek "v prostředí PowerShell. Nebyl vytvořen žádný obrázek před ani nebyl nalezen žádný obrázek s tímto názvem v Azure. Jak to lze vyřešit?
    
    K tomu obvykle dojít, pokud uživatel zřízení virtuálního počítače z tento virtuální pevný disk a na tento virtuální pevný disk je uzamčen. Zkontrolujte, že neexistuje žádný virtuální počítač přidělené z tento virtuální pevný disk. Pokud chyba stále přetrvává, pak vyvolejte lístek podpory pomocí tohoto odkazu nebo z publikování portál ohledně to (podrobnosti jsou uvedeny v otázce 11 odpověď).