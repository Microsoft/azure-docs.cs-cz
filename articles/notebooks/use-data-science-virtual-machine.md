---
title: Použijte virtuální počítače pro Azure datové vědy
description: Připojení do Azure Data virtuálního počítače VĚDY můžete rozšířit do poznámkových bloků Azure k dispozici výpočetní výkon.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: getroyer
ms.openlocfilehash: ab3b742d50cc141420f9bffa1961a6e170b99d2a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234350"
---
# <a name="use-azure-data-science-virtual-machines"></a>Použijte virtuální počítače pro Azure datové vědy

Ve výchozím nastavení, spouštět projekty **bezplatné Compute** úroveň, která je omezena na 4 GB paměti a 1 GB dat, aby se zabránilo zneužití. Tato omezení můžete obejít a použít jiný virtuální počítač, který zřídíte v předplatném Azure. Pro tento účel, nejlepší volbou je Azure Data virtuálního počítače VĚDY pomocí **virtuální počítač pro datové vědy pro Linux (Ubuntu)** bitové kopie. Takové DSVM vybavená předem nakonfigurovaným rozhraním všechno, co potřebujete pro Azure poznámkových bloků a zobrazí se automaticky on **spustit** rozevíracího seznamu v poznámkových bloků Azure.

> [!Note]
> Poznámkových bloků Azure je podporována pouze na datové vytvořené pomocí na image Linux Ubuntu. Poznámkové bloky nejsou podporovány na imagích Windows 2012, Windows 2016 nebo Linux CentOS.

## <a name="create-a-dsvm-instance"></a>Vytvoření DSVM instance

Pokud chcete vytvořit novou instanci DSVM, postupujte podle pokynů [vytvoření virtuálního počítače s Ubuntu Data Science](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Další informace, včetně informace o cenách najdete v tématu [virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Připojte se k datové VĚDY

Po jejich vytvoření datové VĚDY, vyberte **spustit** rozevíracího seznamu v poznámkových blocích Azure řídicího panelu Projekt a vyberte příslušnou instanci DSVM. Rozevíracím seznamu zobrazuje DSVM instance, pokud jsou splněny následující podmínky:

- Jste se přihlásili do poznámkových bloků Azure pomocí účtu, který používá Azure Active Directory (AAD), jako je například účet společnosti.
- Váš účet připojený k předplatnému Azure.
- Máte jeden nebo více virtuálních počítačů v tomto předplatném s alespoň čtečky přístup, který používá virtuální počítač pro datové vědy pro Linux (Ubuntu) image.)

![Instance data Science virtuálních počítačů v rozevíracím seznamu v řídicím panelu Projekt](media/project-compute-tier-dsvm.png)

Při výběru DSVM instance poznámkových bloků Azure může výzvu pro konkrétní počítač pověření použitá při vytváření virtuálního počítače.

Pokud se některá z podmínek nesplní, můžete se připojit k datové VĚDY. V rozevíracím seznamu vyberte **přímé Compute** možnost, která vás vyzve k zadání názvu (Chcete-li zobrazit v seznamu), IP adresu Virtuálního počítače a port (obvykle 8000, výchozí port, na které naslouchá JupyterHub) a přihlašovací údaje virtuálního počítače:

![Výzva ke shromažďování informací serveru pro možnost přímého Compute](media/project-compute-tier-direct.png)

Tyto hodnoty získat z DSVM stránky na webu Azure Portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Přístup k souborům poznámkových bloků Azure z datové VĚDY

Chcete-li zachovat parity cesty k souborům s **– Compute úrovně Free** úroveň, budete moci otevřít pouze jeden projekt současně v DSVM. Chcete-li otevřít nový projekt, je nutné vypnout otevřít projekt nejprve.

![Tlačítko vypnutí v poznámkových bloků Azure](media/shutdown.png)

Při spuštění projektu na virtuálním počítači jsou připojené soubory na kořenovém adresáři serveru Jupyter (adresář ukazuje JupyterHub), nahradí výchozí soubory poznámkových bloků Azure. Když vypnete virtuální počítač pomocí **vypnutí** tlačítko v poznámkovém bloku uživatelského rozhraní, poznámkových bloků Azure obnoví výchozí soubory.

## <a name="next-steps"></a>Další postup

Další informace o datové na [Úvod k Azure virtuální počítače pro datové vědy](/azure/machine-learning/data-science-virtual-machine/overview).
