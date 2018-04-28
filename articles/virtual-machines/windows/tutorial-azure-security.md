---
title: Virtuální počítače Azure Security Center a systému Windows v Azure | Microsoft Docs
description: Další informace o zabezpečení pro virtuální počítač Azure Windows s Azure Security Center.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/01/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 35b6c8910b0dd5e8d1c388ee83223ce77a08f6d1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Monitorování zabezpečení virtuálních počítačů pomocí Azure Security Center

Azure Security Center vám může pomoct získat přehled o postupech zabezpečení vašich prostředků Azure. Security Center nabízí integrované monitorování zabezpečení. Dokáže detekovat hrozby, kterých byste si jinak možná nevšimli. V tomto kurzu se seznámíte s Azure Security Center a následujícími postupy:
 
> [!div class="checklist"]
> * Nastavení shromažďování dat
> * Nastavení zásad zabezpečení
> * Zobrazení a oprava problémů se stavem konfigurace
> * Kontrola zjištěných hrozeb  

## <a name="security-center-overview"></a>Přehled služby Security Center

Security Center identifikuje potenciální problémy s konfigurací virtuálních počítačů a cílené bezpečnostní hrozby. Ty můžou zahrnovat virtuální počítače bez skupin zabezpečení sítě, nešifrované disky a útoky hrubou silou na protokol RDP (Remote Desktop Protocol). Tyto informace se zobrazí v přehledných grafech na řídicím panelu Security Center.

Přístup k řídicímu panelu Security Center získáte výběrem možnosti **Security Center** v nabídce na webu Azure Portal. Na řídicím panelu můžete zobrazit stav zabezpečení vašeho prostředí Azure, zjistit aktuální počet doporučení a zobrazit aktuální stav upozornění na hrozby. Každý graf vysoké úrovně můžete rozbalit a zobrazit tak další podrobnosti.

![Řídicí panel Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center jde nad rámec zjišťování dat a poskytuje doporučení k problémům, které detekuje. Pokud se například nasadil virtuální počítač bez připojené skupiny zabezpečení sítě, Security Center zobrazí doporučení s nápravnými kroky, které můžete použít. Získáte automatizovanou nápravu, aniž byste opustili kontext Security Center.  

![Doporučení](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Nastavení shromažďování dat

Abyste mohli získat přehled o konfiguracích zabezpečení virtuálních počítačů, je potřeba v Security Center nastavit shromažďování dat. To zahrnuje zapnutí shromažďování dat a vytvoření účtu úložiště Azure, do kterého se shromážděná data budou ukládat. 

1. Na řídicím panelu Security Center klikněte na **Zásady zabezpečení** a pak vyberte své předplatné. 
2. V části **Shromažďování dat** vyberte **Zapnuto**.
3. Účet úložiště vytvoříte výběrem možnosti **Zvolit účet úložiště**. Pak vyberte **OK**.
4. V okně **Zásady zabezpečení** vyberte **Uložit**. 

Na všechny virtuální počítače se pak nainstaluje agent Security Center pro shromažďování dat a zahájí se shromažďování dat. 

## <a name="set-up-a-security-policy"></a>Nastavení zásady zabezpečení

Zásady zabezpečení slouží k definici položek, pro které Security Center shromažďuje data a poskytuje doporučení. Na různé sady prostředků Azure můžete použít různé zásady zabezpečení. Přestože se ve výchozím nastavení u prostředků Azure vyhodnocují všechny položky zásad, jednotlivé položky zásad můžete pro všechny prostředky Azure nebo skupinu prostředků vypnout. Podrobné informace o zásadách zabezpečení Security Center najdete v tématu [Nastavení zásad zabezpečení v Azure Security Center](../../security-center/security-center-policies.md). 

Nastavení zásady zabezpečení pro všechny prostředky Azure:

1. Na řídicím panelu Security Center vyberte **Zásady zabezpečení** a pak vyberte své předplatné.
2. Vyberte **Zásady prevence**.
3. Zapněte nebo vypněte položky zásad, které chcete použít pro všechny prostředky Azure.
4. Jakmile budete hotovi s výběrem nastavení, vyberte **OK**.
5. V okně **Zásady zabezpečení** vyberte **Uložit**. 

Nastavení zásady pro konkrétní skupinu prostředků:

1. Na řídicím panelu Security Center vyberte **Zásady zabezpečení** a pak vyberte skupinu prostředků.
2. Vyberte **Zásady prevence**.
3. Zapněte nebo vypněte položky zásad, které chcete pro tuto skupinu prostředků použít.
4. V části **DĚDIČNOST** vyberte **Jedinečná**.
5. Jakmile budete hotovi s výběrem nastavení, vyberte **OK**.
6. V okně **Zásady zabezpečení** vyberte **Uložit**.  

Na této stránce můžete také vypnout shromažďování dat pro konkrétní skupinu zabezpečení.

V následujícím příkladu se vytvořila jedinečná zásada pro skupinu prostředků *myResoureGroup*. V této zásadě jsou vypnutá doporučení k šifrování disku a Firewallu webových aplikací.

![Jedinečná zásada](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Zobrazení stavu konfigurace virtuálních počítačů

Po zapnutí shromažďování dat a nastavení zásady zabezpečení začne Security Center poskytovat upozornění a doporučení. Při nasazování virtuálních počítačů se nainstaluje agent pro shromažďování dat. Security Center se pak naplní daty o těchto nových virtuálních počítačích. Podrobné informace o stavu konfigurace virtuálních počítačů najdete v tématu [Ochrana virtuálních počítačů v Security Center](../../security-center/security-center-virtual-machine-recommendations.md). 

S tím, jak se shromažďují data, se agreguje stav prostředků jednotlivých virtuálních počítačů a souvisejících prostředků Azure. Tyto informace se zobrazí v přehledném grafu. 

Zobrazení stavu prostředků:

1.  Na řídicím panelu Security Center v části **Stav zabezpečení prostředků** vyberte **Compute**. 
2.  V okně **Compute** vyberte **Virtuální počítače**. Toto zobrazení obsahuje souhrn stavu konfigurace všech vašich virtuálních počítačů.

![Stav výpočetních prostředků](./media/tutorial-azure-security/compute-health.png)

Pokud chcete zobrazit všechna doporučení pro virtuální počítač, vyberte příslušný virtuální počítač. Doporučením a nápravám se podrobněji věnuje další část tohoto kurzu.

## <a name="remediate-configuration-issues"></a>Náprava problémů s konfigurací

Jakmile se Security Center začne naplňovat daty o konfiguraci, začnou se poskytovat doporučení k zásadám zabezpečení, které jste nastavili. Pokud se například nastavil virtuální počítač bez přidružené skupiny zabezpečení sítě, poskytne se doporučení k jejímu vytvoření. 

Zobrazení seznamu všech doporučení: 

1. Na řídicím panelu Security Center vyberte **Doporučení**.
2. Vyberte konkrétní doporučení. Zobrazí se seznam všech prostředků, na které se toto doporučení vztahuje.
3. Pokud chcete doporučení použít, vyberte konkrétní prostředek. 
4. Postupujte podle pokynů k nápravným krokům. 

V řadě případů poskytuje Security Center praktické kroky, pomocí kterých můžete doporučení vyřešit, aniž byste opustili Security Center. V následujícím příkladu Security Center detekuje skupinu zabezpečení sítě, která obsahuje neomezené příchozí pravidlo. Na stránce doporučení můžete vybrat tlačítko **Upravit příchozí pravidla**. Zobrazí se uživatelské rozhraní potřebné k úpravě pravidla. 

![Doporučení](./media/tutorial-azure-security/remediation.png)

Když se doporučení napraví, označí se jako vyřešená. 

## <a name="view-detected-threats"></a>Zobrazení zjištěných hrozeb

Kromě doporučení ke konfiguraci prostředků zobrazuje Security Center upozornění na zjištěné hrozby. Funkce výstrah zabezpečení agreguje data shromážděná z jednotlivých virtuálních počítačů, síťových protokolů Azure a připojených partnerských řešení a detekuje bezpečnostní hrozby u prostředků Azure. Podrobné informace o možnostech detekce hrozeb v Security Center najdete v tématu [Možnosti detekce v Azure Security Center](../../security-center/security-center-detection-capabilities.md).

Funkce výstrah zabezpečení vyžaduje zvýšení cenové úrovně Security Center z úrovně *Free* na úroveň *Standard*. Při přesunu na tuto vyšší cenovou úroveň je k dispozici 30denní **bezplatná zkušební verze**. 

Změna cenové úrovně:  

1. Na řídicím panelu Security Center klikněte na **Zásady zabezpečení** a pak vyberte své předplatné.
2. Vyberte **Cenová úroveň**.
3. Vyberte novou úroveň a pak **Vybrat**.
4. V okně **Zásady zabezpečení** vyberte **Uložit**. 

Po změně cenové úrovně se s detekovanými výstrahami zabezpečení začne naplňovat graf výstrah zabezpečení.

![Výstrahy zabezpečení](./media/tutorial-azure-security/security-alerts.png)

Výběrem výstrahy zobrazíte informace. Můžete například zobrazit popis hrozby, čas detekce, všechny pokusy o zneužití hrozby a doporučenou nápravu. V následujícím příkladu se detekoval útok hrubou silou na protokol RDP s 294 pokusy o připojení RDP, které selhaly. Zobrazí se doporučené řešení.

![Útok na protokol RDP](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste nastavili Azure Security Center a pak jste v Security Center zkontrolovali virtuální počítače. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavení shromažďování dat
> * Nastavení zásad zabezpečení
> * Zobrazení a oprava problémů se stavem konfigurace
> * Kontrola zjištěných hrozeb

Přechodu na v dalším kurzu se dozvíte, jak vytvořit kanál CI/CD s Visual Studio Team Services a virtuální počítač s Windows služby IIS.

> [!div class="nextstepaction"]
> [Visual Studio Team Services CI/CD kanálu](./tutorial-vsts-iis-cicd.md)
