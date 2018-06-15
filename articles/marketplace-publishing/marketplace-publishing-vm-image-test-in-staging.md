---
title: Testovací virtuální počítač nabídku pro Marketplace | Microsoft Docs
description: Pochopit, jak chcete otestovat bitové kopie virtuálních počítačů v Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mbaldwin
ms.openlocfilehash: 7f225f113dd465f79d5d195b59a809fe3be6a1f8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936935"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Při přípravě otestovat vaši nabídku virtuálních počítačů pro Azure Marketplace
Pracovní znamená nasazení vaší SKU privátního "izolovaného", kde můžete testovat a před nasazením na Marketplace s cílem ověřit její funkce. Verze SKU se zobrazí v pracovním stejně, jako by zákazníkovi, který je nasazena. Chcete-li být vložena do přípravy musí mít certifikovanou bitové kopie virtuálního počítače.

## <a name="step-1-push-your-offer-to-staging"></a>Krok 1: Push vaši nabídku do přípravy
1. Na **publikovat** , klikněte na **nabízená pracovní**.
   
    ![Kreslení](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Pokud portálu publikování upozorňuje na všechny chyby, opravte je.
3. V **přístup dvoufázové instalace nabídku?** dialogovém okně zadejte seznam předplatných Azure, které budete používat k zobrazení náhledu vaši nabídku v [portál Azure preview](https://portal.azure.com).
   
   > [!NOTE]
   > V případě virtuálních počítačů a šablony řešení prosím **nepodporují** povolených odběrů typu CSP, DreamSpark nebo Azure v otevřené.
   > 
   > 

    > V případě virtuálních počítačů, když kliknete na tlačítko **PUSH pracovní**, následující kroky se provádějí za scény. Bude moct zobrazovat průběh každého kroku na kartě Publikovat v publikační portálu. Je nutné zkontrolovat tuto stránku v pravidelných intervalech (než je ve stavu PŘIPRAVENÝ) pro všechny informace o selhání, které musí oprava z vaší elementu end.

    > - Na první pracovní žádost přejde na certifikační týmu, který ověření virtuální pevný disk. Ale pokud vaši žádost má potom pouze marketingové změnit, pak certifikační krok se přeskočí.
    > - Po dokončení certifikační replikace nabídku start přes Azure datových center. Obvykle trvá 24 48hours na dokončení replikace, ale může trvat až do týdne v závislosti na velikosti virtuálního pevného disku. Pokud vaše žádost má potom pouze marketingové změnit, pak replikace je však rychlejší.
    > - Po dokončení replikace poté nabídku bude k dispozici v [portál Azure](http:/portal.azure.com). V této době stav stát dvoufázové instalace v publikační portálu. Dvoufázové instalace nabídky se zobrazí na [portál Azure](http:/portal.azure.com) jenom pomocí ID e-mailu přidruženou k odběru, ke které je vynášené nabídku.

1. Přihlaste se k [portál Azure preview](https://portal.azure.com) pomocí jednoho z předplatných Azure uvedené v předchozím kroku.
2. Najít vaši nabídku a ověřit bodům bitové kopie virtuálního počítače:
   
   * Ujistěte se, že marketingové obsah se zobrazí správně v Marketplace.
   * Koncové nasazení bitové kopie virtuálního počítače.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Vaši nabídku zůstane v pracovním dokud oznámit Microsoftu prostřednictvím portálu publikování [**publikovat** kartě > klikněte na tlačítko **"Žádosti o schválení pro nabízené do výroby"**] je vše připraveno k produkční. To je ideální třeba mít všechny členy vašeho týmu kontroly nad vše v rámci přípravy vaši nabídku budete uvedené.
> 
> Pracovní platformy je určená pro testování nabídku v režimu náhledu vydavatelem. Důrazně bránit pomocí této platofrm pro obchodní účely.
> 
> 

## <a name="next-steps"></a>Další postup
Teď, když vaši nabídku je "připravený" a testování její funkce a uvádění na trh obsah, můžete přejít k publikování závěrečné **krok 4**: [nasazení vaši nabídku Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md)

