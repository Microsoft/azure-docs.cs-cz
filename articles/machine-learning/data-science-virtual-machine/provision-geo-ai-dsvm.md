---
title: 'Úvodní příručka: Vytvoření virtuálního počítače geoai datové vědy'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurace a vytvoření virtuálního počítače geoaiální datové vědy v Azure pro geoprostorovou analýzu a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77525885"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Úvodní příručka: Nastavení virtuálního počítače geografické umělé inteligence v Azure 

Virtuální počítač Geo AI Data Science (Geo-DSVM) je rozšířením oblíbeného [virtuálního počítače Azure Data Science,](https://aka.ms/dsvm) který je speciálně nakonfigurovaný tak, aby kombinoval analýzu ai a geoprostorové analýzy. Geoprostorové analýzy ve virtuálním provozu jsou založené na [ArcGIS Pro](https://www.arcgis.com/features/index.html). Virtuální počítač pro datové vědy (DSVM) umožňuje rychlé školení modelů strojového učení a dokonce i hloubkového učení. K vývoji těchto modelů používá data obohacená o geografické informace. Geo-DSVM je podporován pouze v systému Windows 2016 DSVM. 

Nástroje AI, které jsou součástí geo-DSVM patří následující:

- Edice GPU populárních rámců pro hluboké učení, jako jsou Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 a Chainer
- Nástroje pro získání a předběžné zpracování obrazových a textových dat
- Nástroje pro vývojové aktivity, jako jsou Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter notebooky pro Python a R, INEŽ pro Python a R a SQL databáze
- Stolní software ArcGIS Pro od společnosti ESRI spolu s rozhraními Pythona a R, které mohou pracovat s geoprostorovými daty z vašich aplikací AI
 

## <a name="create-your-geo-ai-data-science-vm"></a>Vytvořte si virtuální počítač geo ai datové vědy

Pokud chcete vytvořit instanci virtuálního soudu geo ai datové vědy, postupujte takto:

1. Přejděte na výpis virtuálního počítače na [webu Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Chcete-li v dolní části generovat **průvodce,** vyberte vytvořit v dolní části:

   ![vytvořit-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. Průvodce vyžaduje vstup pro každý ze čtyř kroků. Podrobné informace o tomto vstupu naleznete v následující části.

### <a name="wizard-details"></a>Podrobnosti průvodce ###

**Základy**:

- **Název**: Název serveru pro datové vědy, který vytváříte.
    
- **Uživatelské jméno**: ID přihlášení k účtu správce.
    
- **Heslo**: Heslo účtu správce.
    
- **Předplatné**: Pokud máte více než jedno předplatné, vyberte předplatné, ve kterém má být počítač vytvořen a fakturován.
    
- **Skupina prostředků**: Můžete vytvořit novou nebo použít **prázdnou** existující skupinu prostředků Azure ve vašem předplatném.
    
- **Umístění**: Vyberte nejvhodnější datové centrum. Obvykle je to ten, který má většinu vašich dat nebo který je nejblíže k vašemu fyzickému umístění pro nejrychlejší přístup k síti. Pokud máte v plánu spustit hloubkové učení na GPU, musíte zvolit jedno z umístění v Azure, který má NC-Series GPU instance. V současné době jsou tato místa: **Východní USA, Severní střed USA, Jižní Střed USA, Západní USA 2, Severní Evropa, Západní Evropa**. Nejnovější seznam najdete na stránce [Produkty Azure podle oblastí](https://azure.microsoft.com/regions/services/) a vyhledejte **NC-Series** v části **Výpočetní výkon**. 
    
    
**Nastavení**: Pokud plánujete spustit hloubkové učení na GPU na geo dsvm, vyberte jednu z velikostí virtuálních strojů GPU řady NC. V opačném případě můžete zvolit jednu z instancí založených na procesoru. Vytvořte účet úložiště pro váš virtuální počítač. 
       
**Shrnutí**: Ověřte, zda jsou všechny zadané informace správné.
    
**Koupit**: Chcete-li zahájit proces zřizování, klepněte na tlačítko **Koupit**. Odkaz je uveden na podmínky služby. Virtuální počítač nemá žádné další poplatky nad rámec výpočetních poplatků za velikost serveru, kterou jste zvolili v kroku **Velikost.** 
 
 >[!NOTE]
 > Zřizování by mělo trvat přibližně 20 až 30 minut. Stav zřizování se zobrazí na webu Azure Portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Jak získat přístup k virtuálnímu počítači Geo AI Data Science

 Po vytvoření virtuálního počítače můžete začít používat nástroje, které jsou na něj nainstalované a předkonfigurované. K dispozici jsou dlaždice nabídky Start a ikony na ploše pro mnoho nástrojů. K virtuálnímu počítači můžete přistupovat pomocí vzdálené plochy pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v části **Základy.**

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Použití ArcGIS Pro nainstalované ve virtuálním mísu

Na geo-DSVM je předinstalována plocha ArcGIS Pro a prostředí je předkonfigurováno pro práci se všemi nástroji v DSVM. Při spuštění ArcGIS budete vyzváni k zadání přihlašovacích údajů k účtu ArcGIS. Pokud již máte účet ArcGIS a máte licence k softwaru, můžete použít svá stávající pověření.  

![Přihlášení k Oblouku-GIS](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

V opačném případě se můžete zaregistrovat k novému účtu ArcGIS a licenci nebo získat [bezplatnou zkušební verzi](https://www.arcgis.com/features/free-trial.html). 

![Zkušební verze bez oblouku GIS](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Po registraci buď standardního účtu ArcGIS nebo bezplatné zkušební verze, můžete autorizovat ArcGIS Pro pro svůj účet podle pokynů na [Začínáme s ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Po přihlášení k ploše ArcGIS Pro prostřednictvím účtu ArcGIS můžete začít používat nástroje pro datové vědy, které jsou nainstalované a nakonfigurované na virtuálním počítači pro vaše projekty geoprostorové analýzy a strojového učení.

## <a name="next-steps"></a>Další kroky

Začněte používat virtuální ms geo ai datové vědy s pokyny z následujícího zdroje:

* [Použití virtuálního virtuálního provozu geo ai dat](use-geo-ai-dsvm.md)
