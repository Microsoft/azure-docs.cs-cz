---
title: Zřízení virtuálního počítače umělé Intelligence geograficky ve službě Azure - Azure | Microsoft Docs
description: Jak zřídit geograficky AI virtuální počítač na platformě Azure.
keywords: přímý učení, AI, vědecké účely nástrojů data, data vědecké účely virtuálního počítače, geoprostorové analytics
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 93dfe6594aeaf45a6905fe8cb55c98dd37cc9599
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Zřízení virtuálního počítače umělé Intelligence geograficky v Azure 

Geograficky AI datové vědy virtuální počítač (Geo-DSVM) je rozšířením oblíbených [virtuální počítač Azure datové vědy](http://aka.ms/dsvm) speciálně nakonfigurovaný kombinovat AI a geoprostorové analytics. Analýza geoprostorové ve virtuálním počítači jsou zapnuté podle [ArcGIS Pro](https://www.arcgis.com/features/index.html). Virtuální počítač vědecké účely dat umožňuje rychlé školení machine learning modely a i z přímým učení modely na data, která je rozšíření s zeměpisné údaje. Je podporován v systému Windows 2016 DSVM pouze. 

Geo-DSVM obsahuje několik nástrojů pro AI včetně:

- Edice GPU hloubkové learning oblíbených rozhraní, například Microsoft kognitivní Toolkit, TensorFlow, Keras, Caffe2, zřetězeného souboru; 
- Nástroje pro získání a předem zpracování bitovou kopii, textových dat, 
- Nástroje pro vývoj aktivity, například Microsoft R Server Developer Edition, Anaconda Python, poznámkové bloky Jupyter pro jazyk Python a R a integrovaného vývojového prostředí pro Python a R, SQL databáze
- ArcGIS Pro desktopového softwaru na ESRI Python a R rozhraními, které můžete pracovat s daty geoprostorové ze svých aplikací AI. 


## <a name="create-your-geo-ai-data-science-vm"></a>Vytvoření vaší geograficky AI vědecké zpracování dat virtuálních počítačů

Tady je postup vytvoření instance virtuálního počítače geograficky AI Data vědecké účely: 


1. Přejděte k virtuálnímu počítači výpis na [portál Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Vyberte **vytvořit** tlačítko dole mají být provedeny do průvodce.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. Průvodce slouží k vytvoření Geo-DSVM vyžaduje **vstupy** pro každou z **čtyři kroky** uvedené na pravé straně tohoto obrázku. Zde jsou vstupy potřebná ke konfiguraci jednotlivých kroků:



   - **Základy**

      1. **Název**: název serveru data vědecké účely vytváříte.

      2. **Uživatelské jméno**: id přihlášení účtu správce.

      3. **Heslo**: heslo účtu správce.

      4. **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se tento počítač je vytvořen a účtují.

      5. **Skupina prostředků**: můžete vytvořit novou nebo použijte **prázdný** existující skupina prostředků Azure v rámci vašeho předplatného.

      6. **Umístění**: Vyberte datové centrum, která je nejvhodnější. Obvykle je datové centrum, které má většina vašich dat, nebo je nejblíže vašemu fyzické umístění pro nejrychlejší přístup k síti. Pokud potřebujete provést hloubkové learning na grafický procesor, musíte zvolit jedno z umístění v Azure, která má instance virtuálních počítačů GPU NC-Series. Aktuálně jsou umístění, které mají virtuální počítače GPU: **Východ USA, severní jihu USA, Jižní střední USA, západní USA 2, Severní Evropa, západní Evropa**. Nejnovější seznam, zkontrolujte [produkty Azure podle oblasti stránky](https://azure.microsoft.com/regions/services/) a vyhledejte **NC-Series** pod **výpočetní**. 


   - **Nastavení**: vyberte jednu z NC-Series GPU velikost virtuálního počítače, pokud plánujete spouštět hloubkové learning na grafický procesor na vaše geograficky DSVM. Jinak je možné, že jeden z procesoru na základě instance.  Vytvořte účet úložiště pro virtuální počítač. 
   
   - **Souhrn**: Zkontrolujte, zda jsou všechny informace, které jste zadali správné.

   - **Kupte si**: klikněte na tlačítko **koupit** zahájíte přidělení přístupových práv. Je k dispozici odkaz podmínky služby. Virtuální počítač nemá žádné další poplatky za výpočetní pro velikost serveru, který jste zvolili v **velikost** krok. 

>[!NOTE]
> Zřizování má trvat přibližně 20-30 minutách. Stav zřizování se zobrazí na portálu Azure.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Postup k virtuálnímu počítači geograficky AI Data vědecké účely

Po vytvoření virtuálního počítače, jste připraveni začít používat nástroje, které jsou nainstalované a předem nakonfigurované na něm. Existují dlaždice úvodní nabídky a ikony na ploše pro řadu nástrojů. Vzdálená plocha můžete do ní pomocí přihlašovacích údajů účtu správce, které jste nakonfigurovali v předchozím **Základy** části. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>Pomocí sady ArcGIS Pro nainstalovaná ve virtuálním počítači

Geo-DSVM již ArcGIS Pro desktop předinstalovaným a předem nakonfigurované pro práci s všechny nástroje DSVM prostředí. Když spustíte ArcGIS budete vyzváni pro přihlášení ke svému účtu ArcGIS. Pokud již máte účet ArcGIS a licence k softwaru, můžete použít stávající pověření.  

![Oblouk. GIS přihlášení](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Jinak, můžete si zaregistrovat nový účet ArcGIS a licence nebo získat [bezplatnou zkušební verzi](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Až budete mít registrace pro buď placená nebo Bezplatný zkušební účet ArcGIS, můžete ArcGIS Pro autorizaci pro svůj účet podle pokynů v [Začínáme s dokumentace ArcGIS Pro](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Po přihlášení k ploše ArcGIS Pro s vaším účtem ArcGIS, jste připraveni začít pomocí nástrojů data vědecké účely, které jsou nainstalované a nakonfigurované na virtuálním počítači pro geoprostorové analýzy a strojového učení projekty.

## <a name="next-steps"></a>Další postup

Začínáme používat počítač vědecké účely geograficky AI dat s pokyny z v následujících tématech:

* [Použití vědecké zpracování dat AI geograficky virtuálních počítačů](use-geo-ai-dsvm.md)