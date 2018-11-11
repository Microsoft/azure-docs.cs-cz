---
title: Vývojových prostředí pro Azure Machine Learning | Dokumentace Microsoftu
description: Přehled o vývojových prostředích, která vám pomůže se služby Azure Machine Learning. Python 3 je jediným požadavkem pro vaše vývojové prostředí, ale doporučujeme také používat prostředí Conda. Vývojové nástroje doporučujeme poznámkových bloků Jupyter, poznámkových bloků Azure a editory IDE nebo kódu.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 4d25e147044053aa76afe2da482b71c24efc2325
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242870"
---
# <a name="development-environment-for-azure-machine-learning"></a>Vývojové prostředí pro Azure Machine Learning 

Další informace o vývojových prostředích, které můžete použít se službou Azure Machine Learning. 

Služba Azure Machine Learning je pro více platforem a nevyžaduje konkrétní vývojové prostředí. Vyžaduje __Python 3__, a doporučujeme používat __Conda__ vytvářet izolované prostředí. __Pokud už máte prostředí pro vývoj, který splňuje tyto požadavky__, můžete přeskočit tento dokument a Přejít [konfigurace vývojového prostředí](how-to-configure-environment.md) dokumentu.

Zbývající část tohoto dokumentu popisuje vývojová prostředí, které doporučujeme:

* __Poznámkové bloky Jupyter__
* __Poznámkových bloků Azure__
* __Integrované vývojové prostředí (IDE) a editory kódu__
* __Virtuální počítač pro datové vědy__

Porovnání mezi těmito prostředími je obtížné, jak je možné rozšířit poznámkových bloků a integrovanými vývojovými prostředími. Některé Integrovaná vývojová prostředí může například sloužit jako klientům poznámkové bloky Jupyter. Obecně řečeno __poznámkových bloků__ jsou určeny pro __interaktivní experimentování__ a __vizualizace__. __Integrovaná vývojová prostředí a kód editory__ poskytují nástroje, které __zlepšení kvality kódu__ a __integrovat s externími systémy__ jako je například Správa verzí.

> [!TIP]
> Pomocí jednoho prostředí nemá k zablokování pomocí druhé. Poznámkové bloky a Integrovaná vývojová prostředí jsou jenom nástroje a lze kombinovat, podle potřeby. Například může začít experimentovat v poznámkovém bloku a pak je exportovat do skriptu v jazyce python pro úpravy a ladění v rozhraní IDE.
>
> To je důvod, proč virtuální počítač pro datové vědy poskytuje poznámkové bloky Jupyter a několika oblíbenými integrovanými vývojovými prostředími Pythonu.

## <a name="jupyter-notebooks"></a>Poznámkové bloky Jupyter

Poznámkové bloky Jupyter je součástí [Jupyter projektu](https://jupyter.org/). Tyto jsou zaměřené na poskytnutí interaktivní prostředí pro psaní kódu ve kterém vytvoříte dokumenty, které kombinovat živého kódu s vyprávěného textu a grafiky. Poznámkové bloky Jupyter můžete nainstalovat na různých platformách.

Instalace aplikace Jupyter Notebook umožňuje k instalaci a konfiguraci prostředí podle potřeby. Ale budete muset pro údržbu systému.

## <a name="azure-notebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com) (preview) je prostředí poznámkových bloků v cloudu Azure. Vychází z Jupyter a poskytuje prostředí, které je předem načtená Oblíbené knihovny. Sada SDK Azure Machine Learning je již nainstalován v poznámkových bloků Azure, abyste je mohli začít experimentovat s téměř žádné nastavení.

Poznámkových bloků Azure také zjednodušuje proces sdílení vašich poznámkových bloků. Můžete sdílet adresu URL do vašich poznámkových bloků, zveřejněte své knihovny nebo sdílení na sociálních médií z rozhraní poznámkových bloků Azure.

Nevýhodou poznámkových bloků Azure je, že nemáte úplnou kontrolu nad prostředím a nebudou moct instalovat vlastní software, které potřebujete. Je také sdíleném prostředí, tak může probíhat vašich poznámkových bloků pomalejší než u vyhrazené instalace Poznámkový blok Jupyter.

## <a name="ides-and-code-editors"></a>Editory integrovaného vývojového prostředí a kód

Existují mnohé editory Integrovaná vývojová prostředí a kód, které budou fungovat s Azure Machine Learning. Požadavek na pouze software je Azure Machine Learning SDK, které lze nainstalovat pomocí `pip` nástroj.

Doporučujeme [Visual Studio Code](https://code.visualstudio.com/), protože nabízí nástroje pro práci s Azure Machine Learning i jazyk Python. Je dostupná pro platformy Linux, macOS a Windows.

## <a name="data-science-virtual-machine"></a>Virtuální počítač pro datové vědy

Na Data virtuálního počítače VĚDY je kombinací předchozí prostředí. Je virtuální počítač na platformě Azure, který má poznámkové bloky Jupyter, Visual Studio Code a sadu SDK Azure Machine Learning předinstalovaným. Vzhledem k tomu, že požadovaný software je předinstalovaná v image virtuálního počítače, můžete začít experimentovat s Azure Machine Learning rychle po vytvoření virtuálního počítače.

Datové VĚDY umožňuje vybrat výpočetní prostředky, které potřebujete, například procesoru, paměti a GPU. To je také s předinstalovanou jiném editoru, jako je například PyCharm a také oblíbené strojového učení software, jako je TensorFlow, Keras a PyTorch. Pokud není nainstalován software, který potřebujete, můžete ho nainstalovat sami.

Další informace na to, co je předem nainstalovaný, najdete v článku [co je virtuální počítač pro datové vědy](../data-science-virtual-machine/overview.md) dokumentu.

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o vývojových prostředích, můžete zjistit [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md).

