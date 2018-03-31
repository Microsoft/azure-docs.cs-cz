---
title: Vytvoření projektu Azure cloud service pomocí sady Visual Studio | Microsoft Docs
description: Naučte se vytvářet projekt Azure cloud service pomocí sady Visual Studio
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 41c2bd0dc1f609b6b7b9cfda43c2378feb1c3b6c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Vytvoření projektu Azure cloud service pomocí sady Visual Studio
Nástroje Azure pro sadu Visual Studio poskytuje šablony projektu, který umožňuje vytvářet cloudové služby Azure. Po vytvoření projektu sady Visual Studio umožňuje nakonfigurovat, ladění a nasazení cloudové služby pro Azure.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Postup vytvoření projektu Azure cloud service v sadě Visual Studio
Tato část vás provede procesem vytvoření projektu Azure cloud service v sadě Visual Studio pomocí jedné nebo více webových rolí.  

1. Spusťte sadu Visual Studio jako správce.

1. V hlavní nabídce vyberte **soubor** > **nový** > **projektu**.

1. Vyberte **cloudu** z Visual C# nebo Visual Basic projektu šablony uzly a vyberte **Azure Cloud Service** ze seznamu šablon.

    ![Nové cloudové služby Azure](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Určete, která verze rozhraní .NET Framework, kterou chcete použít k vývoji projektu.

1. Zadejte název a umístění projektu a název řešení. 

1. Vyberte **OK**.

1. V **nová Cloudová služba Microsoft Azure** dialogovém okně, vyberte role, které chcete přidat a zvolte tlačítko se šipkou doprava je přidáte do vašeho řešení.

    ![Vyberte nové role Azure cloudové služby](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Pokud chcete přejmenovat role, kterou jste přidali, najeďte na roli v **nová Cloudová služba Microsoft Azure** dialogové okno a v místní nabídce vyberte **přejmenovat**. Můžete také přejmenovat roli v rámci vašeho řešení (v **Průzkumníku řešení**) po byla přidána.

    ![Přejmenujte roli služby Azure cloud](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Projekt Visual Studio Azure má přidružení k roli projekty v řešení. Tento projekt taky zahrnuje *souboru definice služby* a *konfigurační soubor služby*:

- **Soubor definice služby** -definuje nastavení běhového prostředí pro aplikaci, včetně toho, jaké role jsou požadovány, koncových bodů a velikost virtuálního počítače. 
- **Konfigurační soubor služby** – konfiguruje, jak velký počet instancí role jsou spustit a hodnoty k nastavení určenému pro roli. 

Další informace o těchto souborech najdete v tématu [konfiguraci rolí pro cloudové služby Azure pomocí sady Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Další postup
- [Správa rolí v projektech Azure cloud service pomocí sady Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
