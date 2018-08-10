---
title: Jak řešit běžné problémy při vytváření virtuálního pevného disku | Dokumentace Microsoftu
description: Odpovědi na běžné otázky k řešení problémů a problémů při vytváření virtuálního pevného disku.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c4e88a9fbb15dd90d619b159ae1065dfacc1907f
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713393"
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Jak řešit běžné problémy během vytváření virtuálního pevného disku
Tento článek je určena k vydavatele na webu Azure Marketplace a/nebo spolupracující správce, který může docházet k problému nebo mít běžné otázky při publikování nebo správy řešení jejich virtuálních počítačů.

1. Změna názvu hostitele
   
    Po vytvoření virtuálního počítače, uživatele nelze aktualizovat název hostitele.
2. Jak obnovit službu Vzdálená plocha nebo jeho heslo pro přihlášení?
   
   * [Referenční informace pro Windows virtuální počítač](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Referenční informace pro virtuální počítač s Linuxem](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Jak vygenerovat nový ssh certifikáty?
   
   Podrobnosti najdete na odkazu: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Jak nakonfigurovat certifikát VPN otevřené?
   
   Podrobnosti najdete na odkazu: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Co jsou zásady podpory pro Microsoft spouštěným v prostředí virtuálního počítače Microsoft Azure (infrastructure-as--service)?
   
   Podrobnosti najdete na odkazu: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Mají virtuální počítače libovolné jedinečný identifikátor?
   
   Azure kóduje jedinečné ID virtuálního počítače Azure v každému virtuálnímu počítači. Podrobnosti najdete v tomto blogu a dokumentace.
7. Ve virtuálním počítači Jak můžu spravovat rozšíření vlastních skriptů v úloze po spuštění?
   
   Podrobnosti najdete na odkazu: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Postup vytvoření virtuálního počítače z virtuálního pevného disku, který se nahraje do služby premium storage pomocí webu Azure portal?
   
   Tato funkce zatím nepodporujeme.
9. Je podporováno 32bitovou aplikaci na webu Azure Marketplace?
   
   Najdete odkaz Další informace o zásadách podpory: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Pokaždé, když se pokouším se k vytvoření image z mé virtuální pevné disky, zobrazí chybová zpráva ". Virtuální pevný disk je už úložiště imagí zaregistrované jako prostředek"v prostředí PowerShell. Nebyl vytvořen žádný obrázek před ani nebyla v Azure najít žádné image s tímto názvem. Jak to můžu vyřešit?
    
    To obvykle dojít, pokud uživatel zřízení virtuálního počítače z tohoto virtuálního pevného disku a je zámek na tento virtuální pevný disk. Zkontrolujte prosím, že neexistuje žádný virtuální počítač přiděluje z tohoto virtuálního pevného disku. Pokud chyba přetrvá, pak prosím lístek podpory pomocí tohoto odkazu nebo publikování portál týkající se to (podrobnosti jsou uvedeny v odpovědi na dotaz otázku 11).