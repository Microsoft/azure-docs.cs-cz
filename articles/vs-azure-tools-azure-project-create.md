---
title: Vytvoření projektu Azure cloud service pomocí sady Visual Studio | Dokumentace Microsoftu
description: Naučte se vytvořit projekt Azure cloud service pomocí sady Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: f41628c29f40443cc213892a0cc457eda110ebca
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054839"
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Vytvoření projektu Azure cloud service pomocí sady Visual Studio
Nástroje Azure pro sadu Visual Studio obsahuje šablony projektů, které umožňují vytvořit cloudovou službu Azure. Po vytvoření projektu sady Visual Studio umožňuje nakonfigurovat, ladit a nasadit cloudovou službu Azure.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Kroky pro vytvoření projektu Azure cloud service v sadě Visual Studio
Tato část vás provede vytvořením projektu cloudové služby Azure v sadě Visual Studio s jeden nebo více webových rolí.  

1. Spusťte sadu Visual Studio jako správce.

1. V hlavní nabídce vyberte **souboru** > **nový** > **projektu**.

1. Vyberte **cloudu** z jazyka Visual C# nebo Visual Basic projektu šablony uzlů a vyberte **Azure Cloud Service** ze seznamu šablon.

    ![Nová Cloudová služba Azure](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Určete, která verze rozhraní .NET Framework, kterou chcete použít k vývoji projektu.

1. Zadejte název a umístění pro váš projekt a název řešení. 

1. Vyberte **OK**.

1. V **nová Cloudová služba Microsoft Azure** dialogového okna, vyberte role, které chcete přidat a zvolte tlačítko se šipkou doprava a přidejte je do vašeho řešení.

    ![Vyberte nové role Azure cloud service](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Chcete-li přejmenovat roli, která jste přidali, najeďte myší na roli v **nová Cloudová služba Microsoft Azure** dialogového okna a v místní nabídce vyberte **přejmenovat**. Můžete také přejmenovat roli v rámci vašeho řešení (v **Průzkumníka řešení**) po byla přidána.

    ![Přejmenujte roli Azure cloud service](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Projekt sady Visual Studio v Azure má přiřazení k roli projekty v řešení. Projekt obsahuje také *definiční soubor služby* a *konfigurační soubor služby*:

- **Definiční soubor služby** -určuje nastavení běhového prostředí pro aplikace, včetně toho, jaké role jsou povinné, koncové body a velikost virtuálního počítače. 
- **Konfigurační soubor služby** – nakonfiguruje, kolik instancí role jsou spouštění a hodnoty k nastavení určenému pro roli. 

Další informace o těchto souborech najdete v tématu [nakonfigurovat role cloudové služby Azure pomocí sady Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Další postup
- [Správa rolí v Azure cloud service projektů pomocí sady Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
