---
title: Zřízení pro hloubkové učení virtuální počítač pro datové vědy v Azure | Dokumentace Microsoftu
description: Konfigurace a vytvořit hloubkové učení virtuální počítač pro datové vědy v Azure pro účely analýzy a strojového učení.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 9d64ad70ea49f7fbffd8bd6a5a77177fe490b832
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229658"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Zřízení pro hloubkové učení na virtuálním počítači Azure 

Hloubkové učení virtuálního počítače (DLVM) je varianta speciálně nakonfigurovaného Oblíbené [virtuální počítač pro datové vědy](https://aka.ms/dsvm) (DSVM), aby bylo snazší používat založený na grafickém procesoru virtuálního počítače instance pro rychlé školení modely obsáhlého learningu. Jsou podporovány pro Windows 2016 nebo datové VĚDY se systémem Ubuntu jako základ. DLVM sdílí stejnou základní imagí virtuálních počítačů, a proto bohatou sadu nástrojů, který je k dispozici na DSVM. 

DLVM obsahuje několik nástrojů pro AI včetně GPU edice oblíbených architektur obsáhlého learningu jako Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; Nástroje pro získání a předběžného zpracování obrazu, textových dat, nástroje pro datové vědy modelování a vývojové aktivity, například Microsoft R Server Developer Edition, Anaconda Python, poznámkové bloky Jupyter pro Python a jazyka R, Integrovaná vývojová prostředí pro Python nebo R, SQL databáze a mnoha dalším pro datové vědy a ML nástroje. 

## <a name="create-your-deep-learning-virtual-machine"></a>Vytvoření vašeho hloubkového učení virtuálního počítače
Tady jsou kroky pro vytvoření instance aplikace virtuální počítač pro hloubkové učení: 

1. Přejděte k virtuálnímu počítači na [webu Azure portal](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Vyberte **vytvořit** tlačítko v dolní části mají být provedeny do průvodce.![ Vytvoření dlvm](./media/dlvm-provision-wizard.PNG)
3. Průvodce umožňuje vytvořit DLVM vyžaduje **vstupy** pro každou **čtyři kroky** uvedené na pravé straně tohoto obrázku. Tady jsou vstupy potřebné ke konfiguraci každý z těchto kroků:
   
   1. **Základy**
      
      1. **Název**: název serveru datové vědy vytváříte.
      2. **Vyberte typ operačního systému pro počítače pro obsáhlý Learning**: Zvolte Windows nebo Linuxem (pro Windows 2016 a základní DSVM Ubuntu Linux)
      2. **Uživatelské jméno**: id přihlášení účtu správce.
      3. **Heslo**: heslo účtu správce.
      4. **Předplatné**: Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač.
      5. **Skupina prostředků**: vytvořit nové nebo použijte **prázdný** existující skupinu prostředků Azure v rámci vašeho předplatného.
      6. **Umístění**: Vyberte datové centrum, které je nejvhodnější. Obvykle se většina dat nebo je nejblíže vašemu fyzickému umístění pro nejrychlejší přístup k síti datového centra. 
      
> [!NOTE]
> DLVM podporuje všechny síťového adaptéru a ND řady instancí virtuálního počítače s GPU. Při zřizování DLVM, musíte zvolit jedno z umístění v Azure, který má grafické procesory. Zkontrolujte [produkty Azure podle oblasti stránky](https://azure.microsoft.com/regions/services/) stránce dostupných umístění a zkuste najít **NC-Series**, **řada NCv2**, **řady NCv3-Series** , nebo **i řada ND-Series** pod **Compute**. 

   2. **Nastavení**: vyberte jednu z řady síťový adaptér (NC, řada NCv2, NCv3) nebo řady ND GPU velikosti virtuálních počítačů, která splňuje požadavek na funkční a náklady na omezení. Vytvoření účtu úložiště pro virtuální počítač.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **Souhrn**: Ověřte správnost všechny informace, které jste zadali.
   5. **Koupit**: klikněte na tlačítko **koupit** zahájíte zřizování. Zobrazí se odkaz na podmínky transakce. Virtuální počítač nemá žádné další poplatky za výpočetní prostředky pro velikost serveru, kterou jste zvolili v **velikost** kroku. 

> [!NOTE]
> Zřizování by měla trvat asi 10-20 minut. Stav zřizování se zobrazí na portálu Azure portal.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Jak získat přístup k virtuální počítač pro hloubkové učení

### <a name="windows-edition"></a>Edice Windows
Po vytvoření virtuálního počítače můžete do něj pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím vzdálené plochy **Základy** oddílu. 

### <a name="linux-edition"></a>Edice pro Linux

Po vytvoření virtuálního počítače se můžete přihlásit se pomocí protokolu SSH. Pomocí přihlašovacích údajů účtu, které jste vytvořili v **Základy** část krok 3 pro rozhraní text prostředí. Na klientech systému Windows, si můžete stáhnout nástroj klienta SSH jako [Putty](http://www.putty.org). Pokud dáváte přednost grafické desktop (X systému Windows), můžete použít X11 předávání v Putty nebo nainstalovat klienta X2Go.

> [!NOTE]
> Klient X2Go lepších výsledků než X11 předávání v našich testech. Doporučujeme používat X2Go klienta klasické pracovní plochy grafické rozhraní.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Instalace a konfigurace X2Go klienta
Linux DLVM už zřízeny X2Go serveru a připravené tak, aby přijímal připojení klienta. Pro připojení k desktopu grafické virtuálního počítače s Linuxem, proveďte následující postup u svého klienta:

1. Stažení a instalace klienta X2Go pro vaše klientská platforma z [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Spusťte klienta X2Go a vyberte **novou relaci**. Otevře se okno Konfigurace s více karet. Zadejte následující parametry konfigurace:
   * **Karta relace**:
     * **Hostitel**: název hostitele nebo IP adresu virtuálního počítače s Linuxem datové vědy.
     * **Přihlášení**: uživatelské jméno na Linuxovém virtuálním počítači.
     * **SSH Port**: ponechte výchozí hodnota 22.
     * **Typ relace**: Změňte hodnotu na **XFCE**. Datové VĚDY pro Linux v současné době podporuje pouze Desktop xfce.
   * **Karta média**: můžete vypnout podpory zvuku a klient tisku, pokud není nutné k jejich použití.
   * **Sdílené složky**: Pokud chcete adresářů z klientských počítačů připojené na Linuxovém virtuálním počítači, přidejte adresáře klientské počítače, které chcete sdílet s virtuálním Počítačem na této kartě.

Po přihlášení k virtuálnímu počítači pomocí klienta SSH nebo grafické Desktop xfce prostřednictvím klienta X2Go, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Na XFCE uvidíte aplikacích zástupci v nabídce a ikony na ploše pro celou řadu nástrojů.

Jakmile váš virtuální počítač je vytvořen a zřízené, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na něm. Existují dlaždic nabídky start a ikony na ploše pro celou řadu nástrojů. 
