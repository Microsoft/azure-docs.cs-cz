---
title: "Zřídit hluboká učení datové vědy virtuálního počítače na Azure | Microsoft Docs"
description: "Konfigurace a vytvoření hloubkové učení Data vědecké účely virtuálního počítače na platformě Azure pro analýzy a strojového učení."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 75baef65b8a826009cdbaac3a260d021559504da
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Zřídit hluboká učení virtuálního počítače na Azure 

Hloubkové učení virtuálního počítače (DLVM) je speciálně konfigurované varianta oblíbených [datové vědy virtuální počítač](http://aka.ms/dsvm) (DSVM), aby bylo snazší používat virtuálních počítačů na bázi GPU instance pro rychle cvičení hloubkové learning modelů. Podporuje se Windows 2016 nebo Ubuntu DSVM jako základní. DLVM sdílí stejné Image virtuálních počítačů jádra a proto bohatá sada nástrojů dostupná na DSVM. 

DLVM obsahuje několik nástrojů pro AI včetně GPU edicích oblíbených hloubkové learning architektury, jako je Microsoft kognitivní Toolkit, TensorFlow, Keras, Caffe2, zřetězeného souboru; Nástroje pro získání a předem zpracování image, textových dat, nástroje pro datové vědy modelování a vývoj aktivity, například Microsoft R Server Developer Edition, Anaconda Python, poznámkové bloky Jupyter pro jazyk Python a R a integrovaného vývojového prostředí pro Python a R, SQL databáze a mnoho dalších vědecké zpracování dat a nástroje ML. 

## <a name="create-your-deep-learning-virtual-machine"></a>Vytvoření vaší přímým učení virtuálního počítače
Tady jsou kroky k vytvoření instance služby hloubkové Virtual Machine Learning: 

1. Přejděte k virtuálnímu počítači výpis na [portál Azure](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Vyberte **vytvořit** tlačítko dole mají být provedeny do průvodce.![ Vytvoření dlvm](./media/dlvm-provision-wizard.PNG)
3. Průvodce slouží k vytvoření DLVM vyžaduje **vstupy** pro každou z **čtyři kroky** uvedené na pravé straně tohoto obrázku. Zde jsou vstupy potřebná ke konfiguraci jednotlivých kroků:
   
   1. **Základy**
      
      1. **Název**: název vašeho serveru vědecké účely data vytváříte.
      2. **Vyberte typ operačního systému pro virtuální počítač hloubkového Learning**: Zvolte Windows nebo Linux (pro Windows 2016 a základní DSVM Ubuntu Linux)
      2. **Uživatelské jméno**: id přihlášení účtu správce.
      3. **Heslo**: heslo účtu správce.
      4. **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se tento počítač je vytvořen a účtují.
      5. **Skupina prostředků**: můžete vytvořit novou nebo použijte **prázdný** existující skupina prostředků Azure v rámci vašeho předplatného.
      6. **Umístění**: Vyberte datové centrum, která je nejvhodnější. Obvykle je datové centrum, které má většina vašich dat, nebo je nejblíže vašemu fyzické umístění pro nejrychlejší přístup k síti. 
      
> [!NOTE]
> Vzhledem k tomu, že DLVM je zřízený v instancích virtuálního počítače Azure NC-Series GPU, musíte zvolit jedno z umístění v Azure, který má grafickými procesory. Aktuálně jsou umístění, které mají virtuální počítače GPU: **Východ USA, severní jihu USA, Jižní střední USA, západní USA 2, Severní Evropa, západní Evropa**. Nejnovější seznam, zkontrolujte [produkty Azure podle oblasti stránky](https://azure.microsoft.com/en-us/regions/services/) a vyhledejte **NC-Series** pod **výpočetní**. 

   2. **Nastavení**: vyberte jednu z NC-Series GPU velikost virtuálního počítače, který splňuje požadavek na funkční a náklady na omezení. Vytvořte účet úložiště pro virtuální počítač.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **Souhrn**: Zkontrolujte, zda jsou všechny informace, které jste zadali správné.
   5. **Kupte si**: klikněte na tlačítko **koupit** zahájíte přidělení přístupových práv. Je k dispozici odkaz na podmínky transakce. Virtuální počítač nemá žádné další poplatky za výpočetní pro velikost serveru, který jste zvolili v **velikost** krok. 

> [!NOTE]
> Zajišťování zabere asi 10-20 minut. Stav zřizování se zobrazí na portálu Azure.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Jak získat přístup k hloubkové Virtual Machine Learning

### <a name="windows-edition"></a>Edice Windows
Po vytvoření virtuálního počítače můžete do ní pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím vzdálené plochy **Základy** části. 

### <a name="linux-edition"></a>Edice systému Linux

Po vytvoření virtuálního počítače se můžete přihlásit se pomocí protokolu SSH. Použít pověření účtu, které jste vytvořili v **Základy** části kroku 3 pro rozhraní prostředí text. U klienta systému Windows, si můžete stáhnout nástroj pro klienta na SSH jako [Putty](http://www.putty.org). Pokud dáváte přednost grafické desktop (systém Windows X), můžete použít X11 předávání na Putty nebo instalace klienta na X2Go.

> [!NOTE]
> Klient X2Go lepší výsledky než X11 předávání v našich testech. Doporučujeme používat klienta X2Go pro grafické rozhraní plochy.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Instalace a konfigurace klienta X2Go
Linux DLVM již zřízená X2Go server a přijímá připojení klienta. Pro připojení k ploše virtuálního počítače s Linuxem grafického rozhraní, proveďte následující postup na vašeho klienta:

1. Stáhněte a nainstalujte X2Go klienta pro vaši platformu klienta z [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Spuštění klienta X2Go a vyberte **novou relaci**. Otevře okno Konfigurace s několik karet. Zadejte následující konfigurační parametry:
   * **Karta relace**:
     * **Hostitele**: název hostitele nebo IP adresy virtuálním počítačům s Linuxem dat vědecké účely.
     * **Přihlášení**: uživatelské jméno v virtuálního počítače s Linuxem.
     * **SSH Port**: necháte ve 22, výchozí hodnota.
     * **Typ relace**: Změňte hodnotu na **XFCE**. Linux DSVM aktuálně podporuje jenom XFCE plochy.
   * **Karta média**: můžete vypnout zvukové podporu a klienta, tisk, pokud nepotřebujete používat.
   * **Sdílené složky**: Pokud chcete adresáře z vaší klientské počítače na virtuální počítač s Linuxem připojeny, přidejte adresáře klientské počítače, které chcete sdílet s virtuálním Počítačem na této kartě.

Po přihlášení k virtuálnímu počítači pomocí SSH klienta nebo XFCE grafické plochy prostřednictvím klienta X2Go, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované ve virtuálním počítači. Na XFCE můžete zobrazit zástupce aplikace nabídky a ikony na ploše pro řadu nástrojů.

Jakmile virtuálního počítače se vytvoří a zřizovat, jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na něm. Existují dlaždice úvodní nabídky a ikony na ploše pro řadu nástrojů. 
