---
title: 'Další informace: DSVM s Windows'
description: Podrobnosti o nástrojích, které jsou součástí Data Science VM Windows
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200008"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Další informace: Data Science Virtual Machine Windows

Níže najdete seznam dostupných nástrojů ve Windows Data Science Virtual Machine. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

Pro analýzu můžete použít Microsoft Enterprise Library, protože na virtuálním počítači je nainstalovaná verze Machine Learning Server Developer Edition. Dříve označované jako Microsoft R Server Machine Learning Server je široce nasazená analytická platforma. Je škálovatelná a komerčně podporovaná.

Machine Learning Server podporuje různé statistiky pro velké objemy dat, prediktivní modelování a strojové učení úlohy. Podporuje široké spektrum analytických: zkoumání, analýzu, vizualizaci a modelování. Díky používání a rozšiřování open-source jazyka R a Python, Machine Learning Server je kompatibilní se skripty R a Python a funkce. Je také kompatibilní s CRAN, pip a balíčků Conda, k analýze dat v podnikovém měřítku.

Machine Learning Server řeší přidáním paralelní a blokového zpracování dat v paměti omezení open-source systému R. To znamená, že můžete provádět analýzy mnohem většího množství dat, než kolik jich zapadá do hlavní paměti. 

Visual Studio Community je zahrnutá ve virtuálním počítači. Má rozšíření R Tools for Visual Studio a Python Tools for Visual Studio (PTVS), která poskytují úplné integrované vývojové prostředí (IDE) pro práci s R nebo Pythonem. Na virtuálním počítači poskytujeme také další prostředí pro IDEs, jako je [RStudio](https://www.rstudio.com) a [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) .

## <a name="python"></a>Python

Pro vývoj pomocí Pythonu jsou nainstalované distribuce Pythonu Anaconda 2,7 a 3,6. Tyto distribuce mít základní Python spolu s přibližně 300 nejoblíbenějších balíčků analytics matematické, inženýrství a data. Můžete použít PTVS, který je nainstalován v aplikaci Visual Studio Community 2017. Nebo můžete použít jeden ze svazků se systémem Anaconda, jako je nečinný nebo Spyder. Vyhledejte a otevřete jeden z těchto balíčků (klávesa S logem Windows + S).

> [!NOTE]
> Tak, aby odkazoval nástroje Pythonu pro Visual Studio na Anaconda Python 2.7, je potřeba vytvořit vlastní prostředí pro každou verzi. Pokud chcete nastavit tyto cesty prostředí v komunitě Visual Studio 2017, přečtěte si v **nabídce nástroje** > **Python Tools** > **Python prostředí**. Potom vyberte **+ vlastní**.

Anaconda Python 3,6 je nainstalován v C:\Anaconda. Anaconda Python 2,7 je nainstalován v C:\Anaconda\envs\python2. Podrobný postup najdete v [dokumentaci k PTVS](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters).

## <a name="the-jupyter-notebook"></a>Poznámkový blok Jupyter

Anaconda distribuce také přináší Jupyter Notebook, prostředí pro sdílení kódu a analýzy. Python 2.7 Python 3.x, PySpark, Julia a R jádrech je předem nakonfigurovány aplikace Jupyter Notebook server. Pokud chcete spustit server Jupyter a otevřít prohlížeč pro přístup k poznámkovému serveru, použijte ikonu **Jupyter notebook** Desktop.

Balíček jsme několik ukázkové poznámkové bloky v Pythonu a R. Po získání přístupu Jupyter poznámkových bloků ukazují, jak pracovat s tyto technologie:

* Machine Learning Server
* SQL Server Machine Learning Services, in-Database Analytics
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Další technologie Azure

Odkaz na ukázky na domovské stránce poznámkového bloku najdete po ověření k Jupyter Notebook pomocí hesla, které jste vytvořili dříve.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM zahrnuje Visual Studio Community. Toto je bezplatná verze oblíbeného integrovaného vývojového prostředí (IDE) od Microsoftu, kterou můžete použít pro účely testování a pro malé týmy. Přečtěte si [licenční smlouvy k softwaru společnosti Microsoft](https://www.visualstudio.com/support/legal/mt171547).

Otevřete Visual Studio pomocí ikony na ploše nebo nabídky **Start** . Vyhledejte programy (klávesu S logem Windows + S) a potom **Visual Studio**. Odtud můžete vytvářet projekty v jazycích, jako C#je, Python, R a Node. js. Nainstalovaných modulů plug-in usnadnit práci s těmito službami Azure:

* Katalog dat Azure
* Azure HDInsight pro Hadoop a Spark
* Azure Data Lake

Modul plug-in s názvem Azure Machine Learning pro Visual Studio Code se také integruje s Azure Machine Learning a pomáhá rychle sestavovat aplikace AI.

> [!NOTE]
> Může se zobrazit zpráva, že vypršela platnost vašeho zkušebního období. Zadejte svoje přihlašovací údaje účtu Microsoft. Nebo vytvořte nový bezplatný účet, abyste získali přístup k Visual Studio Community.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

DSVM obsahuje vývojářskou verzi SQL Server 2017 s Machine Learning Services. Tato edice SQL Server přichází v R nebo Pythonu a může běžet v analýze databáze. 

Služby Machine Learning poskytuje platformu pro vývoj a nasazování inteligentních aplikací. Tyto jazyky a mnoho balíčků od komunity slouží k vytváření modelů a generovat předpovědi pro data systému SQL Server. Analytics blízko data můžete zachovat, protože služby Machine Learning, v databázi, integruje jazyky R i Python v rámci SQL serveru. Tato integrace se eliminují náklady a bezpečnostní rizika spojená s přesuny dat.

> [!NOTE]
> SQL Server Developer edition je pouze pro účely vývoje a testování. Budete potřebovat licenci k použití v produkčním prostředí.

K SQL Server můžete přistupovat otevřením Microsoft SQL Server Management Studio. Název virtuálního počítače se naplní jako **název serveru**. Windows ověřování použijte, když se přihlásíte jako správce na Windows. Až budete v aplikaci SQL Server Management Studio, vytvořit další uživatele, vytvářet databáze, umožňuje importovat data a spouštět dotazy SQL.

Pokud chcete povolit analýzu v databázi pomocí SQL Server Machine Learning Services, spusťte následující příkaz jako jednorázovou akci v SQL Server Management Studio po přihlášení jako správce serveru:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Nahraďte `%COMPUTERNAME%` názvem svého virtuálního počítače.

## <a name="azure"></a>Azure

Několik nástrojů pro Azure jsou nainstalované na virtuálním počítači:

* Zástupce na ploše přejde na dokumentaci k sadě Azure SDK.
* Pomocí AzCopy můžete kopírovat data z účtu služby Azure Storage a z něj. Pokud chcete zobrazit využití, zadejte **Azcopy** z příkazového řádku.
* Pomocí Průzkumník služby Azure Storage můžete procházet objekty, které ukládáte v účtu úložiště Azure. Také kopíruje data do a z Azure Storage. Chcete-li získat přístup k tomuto nástroji, zadejte do **vyhledávacího** pole **Průzkumník služby Storage** . Nebo vyhledejte ji na Windows **Start** nabídky.
* AdlCopy kopíruje data do Azure Data Lake. Pokud chcete zobrazit využití, zadejte **adlcopy** v příkazovém řádku.
* Nástroj dtui kopíruje data do a z Azure Cosmos DB databáze NoSQL v cloudu. Zadejte **dtui** v příkazovém řádku.
* Prostředí Integration runtime kopíruje data mezi místními zdroji dat a cloudem. Používá se v rámci nástroje, jako je Azure Data Factory.
* Pomocí Azure PowerShell můžete spravovat prostředky Azure ve skriptovacím jazyce PowerShellu. Také se nainstaluje na váš virtuální počítač.

## <a name="power-bi"></a>Power BI

DSVM se dodává s nainstalovanou Power BI Desktop, která vám pomůžou vytvářet řídicí panely a vizualizace. Pomocí tohoto nástroje k získání dat z různých zdrojů, vytvářet řídicí panely a sestavy a publikovat je do cloudu. Další informace najdete na [webu Power BI](https://powerbi.microsoft.com). Můžete najít Power BI Desktop v nabídce **Start** .

> [!NOTE]
> Budete potřebovat účet Microsoft Office 365 pro přístup k Power BI.

## <a name="azure-machine-learning-sdk-for-python"></a>Sada SDK Azure Machine Learning pro Python

Odborníci přes data a vývojáři AI používají Azure Machine Learning SDK pro Python k sestavování a spouštění pracovních postupů strojového učení pomocí [služby Azure Machine Learning](../service/overview-what-is-azure-ml.md). Můžete komunikovat se službou v poznámkových blocích Jupyter nebo jiném integrovaném vývojovém prostředí Pythonu s využitím open source platforem, jako je TensorFlow a scikit-učení.

Python SDK je předinstalován v Microsoftu virtuální počítač pro datové vědy. Pokud chcete začít používat sadu Python SDK, přečtěte si téma [použití Pythonu a začněte s Azure Machine Learning](../service/quickstart-create-workspace-with-python.md).

## <a name="more-microsoft-development-tools"></a>Další vývojové nástroje Microsoftu

Pomocí [Instalace webové platformy Microsoft](https://www.microsoft.com/web/downloads/platform.aspx) můžete najít a stáhnout další vývojové nástroje Microsoftu. Je také zástupce nástroje na webu Microsoft Data Science Virtual Machine Desktop.  

## <a name="important-directories-on-the-virtual-machine"></a>Důležité adresáře na virtuálním počítači

| Položka | Adresář |
| --- | --- |
| Konfigurace serveru Poznámkový blok Jupyter | C:\ProgramData\jupyter |
| Domovský adresář pro ukázky Poznámkový blok Jupyter | C:\dsvm\notebooks a c:\Users\\< UserName\>\notebooks |
| Další ukázky | C:\dsvm\samples |
| Anaconda, výchozí: Python 3,6 | C:\Anaconda |
| Anaconda Python 2.7 prostředí | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (samostatný) pro Python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Výchozí instance jazyka R, Machine Learning Server (samostatně) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| Adresář instance databáze SQL Server Machine Learning Services | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Různé nástroje | C:\dsvm\tools |

> [!NOTE]
> V systému Windows Server 2012 verze DSVM a edice Windows Server 2016 do března 2018 je výchozím prostředím Anaconda Python 2,7. Sekundární prostředí je Python 3,5, který se nachází na adrese C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Další kroky

Máte další otázky? Zvažte vytvoření [lístku podpory](https://azure.microsoft.com/support/create-ticket/).
