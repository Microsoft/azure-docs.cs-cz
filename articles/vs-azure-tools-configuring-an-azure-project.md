---
title: Konfigurace projektu Azure cloud service pomocí sady Visual Studio | Microsoft Docs
description: Informace o konfiguraci projektu Azure cloud service v sadě Visual Studio, v závislosti na požadavcích pro tento projekt.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/06/2017
ms.author: ghogen
ms.openlocfilehash: 0b2b39897407875d2722c7fbaebebc1698f931a9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Konfigurace projektu Azure cloud service pomocí sady Visual Studio
Projekt Azure cloud service můžete nakonfigurovat v závislosti na požadavcích pro tento projekt. Můžete nastavit vlastnosti projektu v těchto kategoriích:

- **Publikování Cloudová služba Azure** – můžete nastavit vlastnost a ujistěte se, že není omylem odstranit stávající cloudovou službu nasadit do Azure.
- **Spustit nebo ladění cloudové služby na místním počítači** – můžete vybrat konfiguraci služby, kterou chcete použít a uvést, zda chcete spustit emulátoru úložiště Azure.
- **Ověřit balíček cloudové služby, když je vytvořeno** -rozhodnete zacházet s všechna upozornění jako chyby, tak, aby bylo možné zajistit, že balíček služby cloud nasadí bez problémů. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Postup konfigurace projektu Azure cloud service
1. Otevřít nebo vytvořit projekt cloudové služby v sadě Visual Studio

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **vlastnosti**.
   
1. Na stránce vlastností projektu, vyberte **vývoj** kartě.

    ![Nabídka Vlastnosti projektu](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Nastavit **výzva před odstraněním stávajícího nasazení** k **True**. Toto nastavení zásady pomáhají zajistit, že nemáte omylem odstranit stávajícího nasazení v Azure

1. Vyberte požadovanou **konfigurace služby** udávajících konfiguraci služby, kterou chcete použít při spuštění nebo ladění cloudové služby místně. Další informace o tom, jak změnit konfiguraci služby pro role, naleznete v části [postup konfigurace role pro cloudové služby Azure pomocí sady Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Nastavit **emulátoru úložiště Azure spustit** k **True** při spouštění nebo ladění cloudové služby místně spusťte emulátor úložiště Azure.

1. Nastavit **považovat upozornění jako chyby** k **True** a ujistěte se, pokud nejsou chyby ověření balíček nelze publikovat.

1. Nastavit **použít webový projekt porty** k **True** a ujistěte se, že vaši webovou roli používá stejný port pokaždé, když ji spustí místně v IIS Express.

1. Na panelu nástrojů Visual Studio vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
- [Konfigurace projektu Azure pomocí více konfigurace služby](vs-azure-tools-multiple-services-project-configurations.md)

