---
title: 'Rychlý Start: vytvoření geografické AI Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Nakonfigurujte a vytvořte geografickou Data Science Virtual Machine AI v Azure pro geoprostorové analýzy a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 61d401a543032b1a206a4477f04d7e8e209c0f2a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646608"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Rychlý Start: nastavení geograficky umělého virtuálního počítače s logikou v Azure 

Geografické AI Data Science Virtual Machine (GEO-DSVM) je rozšíření oblíbeného [Azure Data Science Virtual Machine](https://aka.ms/dsvm) , které je speciálně nakonfigurované pro kombinování AI a geoprostorové analýzy. Geoprostorové analýzy ve virtuálním počítači jsou napájené pomocí [ArcGIS pro](https://www.arcgis.com/features/index.html). Data Science Virtual Machine (DSVM) umožňuje rychlé školení modelů strojového učení a dokonce i obsáhlého učení. Pro vývoj těchto modelů používá data, která jsou obohacena o geografické informace. Geografické DSVM se podporuje jenom v systému Windows 2016 DSVM. 

Nástroje AI, které jsou zahrnuté v geograficky DSVM, obsahují tyto možnosti:

- Edice GPU oblíbených hloubkových architektur, jako jsou Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 a chainer
- Nástroje pro získání a předzpracování obrázků a textových dat
- Nástroje pro vývojové aktivity, jako je Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter poznámkové bloky pro Python a R, IDEs pro Python a R a databáze SQL
- Desktopový software ArcGIS pro od Esri, společně s rozhraními Pythonu a R, která můžou pracovat s geoprostorovémi daty z aplikací AI
 

## <a name="create-your-geo-ai-data-science-vm"></a>Vytvoření Data Science VM geografické AI

Pokud chcete vytvořit instanci geografického AI Data Science VM, postupujte takto:

1. Přejít na virtuální počítač výpisu na [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. V dolní části vyberte **vytvořit** , aby se vygeneroval Průvodce:

   ![Vytvoření – geografická-AI – dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Průvodce vyžaduje vstup pro každý ze čtyř kroků. Podrobné informace o tomto vstupu najdete v následující části.

### <a name="wizard-details"></a>Podrobnosti Průvodce ###

**Základy**:

- **Name (název**): název serveru pro datové vědy, který vytváříte.
    
- **Uživatelské jméno**: přihlašovací ID účtu správce.
    
- **Heslo**: heslo účtu správce.
    
- **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se má počítač vytvořit a fakturovat.
    
- **Skupina prostředků**: můžete vytvořit novou nebo ve svém předplatném použít **prázdnou** existující skupinu prostředků Azure.
    
- **Umístění**: Vyberte datové centrum, které je vhodné. Obvykle je to ten, který má většinu vašich dat nebo který je nejblíže vašemu fyzickému umístění pro nejrychlejší přístup k síti. Pokud plánujete provozovat hlubokou výuku na GPU, musíte zvolit jedno z umístění v Azure, které má instance virtuálních počítačů GPU řady NC-Series. V současné době jsou to tato umístění: **východní USA, střed USA – sever, střed USA – jih, západní USA 2, Severní Evropa, západní Evropa**. V seznamu nejnovější Zkontrolujte stránku [produkty Azure podle oblasti](https://azure.microsoft.com/regions/services/) a vyhledejte v části **výpočetní**prostředí **řady NC-Series** . 
    
    
**Nastavení**: vyberte jednu z velikostí virtuálních počítačů GPU řady NC-Series, pokud plánujete provozovat hlubokou výuku na GPU v geografických DSVM. V opačném případě můžete zvolit jednu z instancí založenou na procesoru. Vytvořte účet úložiště pro váš virtuální počítač. 
       
**Shrnutí**: Ověřte, zda jsou všechny informace, které jste zadali, správné.
    
**Koupit**: Pokud chcete zahájit proces zřizování, klikněte na **koupit**. K dispozici je odkaz na podmínku služby. Pro velikost serveru, kterou jste zvolili v kroku **Velikost** , nemá tento virtuální počítač žádné další poplatky nad poplatky za výpočetní výkon. 
 
 >[!NOTE]
 > Zřizování by mělo trvat přibližně 20 až 30 minut. Stav zřizování se zobrazí na Azure Portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak získat přístup k geografickému AI Data Science Virtual Machine

 Po vytvoření virtuálního počítače budete připraveni začít používat nástroje, které jsou nainstalované a předem nakonfigurované. Pro mnohé z nástrojů jsou k dispozici dlaždice nabídky Start a ikony na ploše. K virtuálnímu počítači můžete přistupovat pomocí vzdálené plochy pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v části **základy** .

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Použití ArcGIS pro nainstalovaného na virtuálním počítači

Na geograficky DSVM je předinstalována aplikace ArcGIS pro Desktop a prostředí je předem nakonfigurované tak, aby fungovalo se všemi nástroji v DSVM. Po spuštění ArcGIS budete vyzváni k zadání přihlašovacích údajů k účtu ArcGIS. Pokud již máte účet ArcGIS a máte licence na software, můžete použít stávající přihlašovací údaje.  

![Oblouk – GIS – přihlášení](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Jinak si můžete zaregistrovat nový účet ArcGIS a licenci nebo si můžete stáhnout [bezplatnou zkušební verzi](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS – bezplatná zkušební verze](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po registraci standardního účtu ArcGIS nebo bezplatné zkušební verze můžete pro svůj účet autorizovat ArcGIS pro, a to podle pokynů uvedených v tématu [Začínáme s ArcGIS pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Po přihlášení k desktopu ArcGIS pro z účtu ArcGIS jste připraveni začít používat nástroje pro datové vědy, které jsou nainstalované a nakonfigurované na VIRTUÁLNÍm počítači pro geoprostorové analýzy a projekty strojového učení.

## <a name="next-steps"></a>Další kroky

Začněte používat geografické AI Data Science VM s pokyny z tohoto prostředku:

* [Použití geografického AI Data Science VM](use-geo-ai-dsvm.md)
