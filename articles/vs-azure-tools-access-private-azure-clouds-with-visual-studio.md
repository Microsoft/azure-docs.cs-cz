---
title: Přístup k privátní cloud Azure pomocí sady Visual Studio | Microsoft Docs
description: Zjistěte, jak získat přístup k prostředkům privátního cloudu pomocí sady Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: ghogen
ms.openlocfilehash: 0160ac6db2b92d5a30a19dd444d01a8558b3eed3
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792986"
---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Přístup k privátní cloud Azure pomocí sady Visual Studio

Ve výchozím nastavení Visual Studio podporuje koncové body REST cloudu Azure. V tomto článku zjistěte, jak používat privátní cloud certifikát pro přístup a interakci s privátní cloud ze sady Visual Studio.

1. Pro privátní cloud na portálu Azure stáhněte soubor nastavení publikování, nebo se obraťte na svého správce pro soubor nastavení publikování. (Soubor má příponu `.publishsettings`.)

1. V sadě Visual Studio **Průzkumníka serveru**, klikněte pravým tlačítkem myši **Azure** uzel a vyberte možnost **spravovat a odběry filtru**.

    ![Spravovat odběry příkaz](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. V **spravovat předplatná Microsoft Azure** dialogovém okně, vyberte **certifikáty** a potom vyberte **Import**.

    ![Import certifikátů Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. V **předplatná Microsoft Azure Import** dialogovém okně, vyberte **Procházet**.

    ![V dialogovém okně předplatná Microsoft Azure Import tlačítko Procházet](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. V **otevřete** dialogové okno, přejděte do adresáře, kam jste uložili soubor nastavení publikování, vyberte soubor a pak vyberte **otevřete**.

    ![Vyberte soubor nastavení publikování](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Když se vrátíte na **předplatná Microsoft Azure Import** dialogovém okně, vyberte **Import**.

    ![Importujte soubor nastavení publikování](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Certifikáty jsou importovány ze souboru nastavení publikování do sady Visual Studio a nyní můžete pracovat s vaší privátní cloudové prostředky.

