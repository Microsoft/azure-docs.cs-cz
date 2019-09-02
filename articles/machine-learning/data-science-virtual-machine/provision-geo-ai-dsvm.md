---
title: 'Rychlý start: Nastavení geograficky umělé inteligentní analýzy'
titleSuffix: Azure Data Science Virtual Machine
description: Zjistěte, jak vytvořit a nakonfigurovat virtuální počítač geograficky AI datové vědy. Počítač pro virtuální Geo AI datové vědy poskytuje nástroje pro vytváření řešení AI a ML pomocí zeměpisné údaje.
keywords: obsáhlý learning, AI, nástrojů pro datové vědy, virtuální počítač pro datové vědy, geoprostorové analýzy
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/05/2018
ms.openlocfilehash: 7afeec3f71cd1af30093801fedabf3f0357ae3d0
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208062"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Rychlý start: Nastavení geograficky umělého virtuálního počítače s logikou v Azure 

Geografické AI Data Science Virtual Machine (GEO-DSVM) je rozšíření oblíbeného [Azure Data Science Virtual Machine](https://aka.ms/dsvm) , které je speciálně nakonfigurované pro kombinování AI a geoprostorové analýzy. Geoprostorové analýzy ve virtuálním počítači využívají [ArcGIS Pro](https://www.arcgis.com/features/index.html). Data Science Virtual Machine (DSVM) umožňuje rychlé školení modelů strojového učení a dokonce i obsáhlého učení. Pro vývoj těchto modelů používá data, která jsou obohacena o geografické informace. Geografické DSVM se podporuje jenom v systému Windows 2016 DSVM. 

Nástroje AI, které jsou zahrnuté v geograficky DSVM, obsahují tyto možnosti:

- Edice GPU oblíbených hloubkových architektur, jako jsou Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 a chainer
- Nástroje pro získání a předzpracování obrázků a textových dat
- Nástroje pro vývojové aktivity, jako je Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter poznámkové bloky pro Python a R, IDEs pro Python a R a databáze SQL
- Desktopový software ArcGIS pro od ESRI, společně s rozhraními Pythonu a R, která můžou pracovat s geoprostorovémi daty z aplikací AI
 

## <a name="create-your-geo-ai-data-science-vm"></a>Vytvoření vaší geografické AI pro datové vědy virtuálního počítače

Pokud chcete vytvořit instanci geografického AI Data Science VM, postupujte takto:

1. Přejít na virtuální počítač výpisu na [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. V dolní části vyberte **vytvořit** , aby se vygeneroval Průvodce:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Průvodce vyžaduje vstup pro každý ze čtyř kroků. Podrobné informace o tomto vstupu najdete v následující části.

### <a name="wizard-details"></a>Podrobnosti Průvodce ###

**Základy**:

- **Název**: Název serveru datové vědy vytváříte.
    
- **Uživatelské jméno**: Přihlašovací ID účtu správce
    
- **Heslo**: Heslo účtu správce.
    
- **Předplatné:** Pokud máte více předplatných, vyberte ten, ve které je vytvořené a fakturuje počítač.
    
- **Skupina prostředků**: V předplatném můžete vytvořit novou nebo použít **prázdnou** skupinu prostředků Azure.
    
- **Umístění**: Vyberte datové centrum, které je vhodné. Obvykle je to ten, který má většinu vašich dat nebo který je nejblíže vašemu fyzickému umístění pro nejrychlejší přístup k síti. Pokud plánujete provozovat hlubokou výuku na GPU, musíte zvolit jedno z umístění v Azure, které má instance virtuálních počítačů GPU řady NC-Series. V současné době jsou tato umístění: **Východní USA, střed USA – sever, střed USA – jih, západní USA 2, Severní Evropa**západní Evropa. V seznamu nejnovější Zkontrolujte stránku [produkty Azure podle oblasti](https://azure.microsoft.com/regions/services/) a vyhledejte v části **výpočetní**prostředí **řady NC-Series** . 
    
    
**Nastavení**: Pokud máte v plánu provozovat obsáhlý Learning na GPU v geografických DSVMch, vyberte jednu z velikostí virtuálních počítačů GPU řady NC-Series. V opačném případě můžete zvolit jednu z instancí založenou na procesoru. Vytvoření účtu úložiště pro virtuální počítač. 
       
**Souhrn**: Ověřte, zda všechny informace, které jste zadali správný.
    
**Koupit**: Chcete-li zahájit proces zřizování, klikněte na tlačítko **koupit**. Pomocí odkazu uvedeného na podmínky služby. Pro velikost serveru, kterou jste zvolili v kroku **Velikost** , nemá tento virtuální počítač žádné další poplatky nad poplatky za výpočetní výkon. 
 
 >[!NOTE]
 > Zřizování by mělo trvat přibližně 20 až 30 minut. Stav zřizování se zobrazí na portálu Azure portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak získat přístup k počítač pro virtuální Geo AI datové vědy

 Po vytvoření virtuálního počítače budete připraveni začít používat nástroje, které jsou nainstalované a předem nakonfigurované. Pro mnohé z nástrojů jsou k dispozici dlaždice nabídky Start a ikony na ploše. K virtuálnímu počítači můžete přistupovat pomocí vzdálené plochy pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v části **základy** .

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Použití ArcGIS Pro nainstalované ve virtuálním počítači

Na geograficky DSVM je předinstalována aplikace ArcGIS pro Desktop a prostředí je předem nakonfigurované tak, aby fungovalo se všemi nástroji v DSVM. Po spuštění ArcGIS budete vyzváni k zadání přihlašovacích údajů k účtu ArcGIS. Pokud již máte účet ArcGIS a máte licencemi na software, můžete vaše stávající přihlašovací údaje.  

![Oblouk. GIS přihlášení](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Jinak si můžete zaregistrovat nový účet ArcGIS a licenci nebo si můžete stáhnout [bezplatnou zkušební verzi](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po registraci standardního účtu ArcGIS nebo bezplatné zkušební verze můžete pro svůj účet autorizovat ArcGIS pro, a to podle pokynů uvedených v tématu [Začínáme s ArcGIS pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Po přihlášení k desktopu ArcGIS pro z účtu ArcGIS jste připraveni začít používat nástroje pro datové vědy, které jsou nainstalované a nakonfigurované na VIRTUÁLNÍm počítači pro geoprostorové analýzy a projekty strojového učení.

## <a name="next-steps"></a>Další kroky

Začněte používat geografické AI Data Science VM s pokyny z tohoto prostředku:

* [Použití v geograficky AI pro datové vědy virtuálního počítače](use-geo-ai-dsvm.md)
