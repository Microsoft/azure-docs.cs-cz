---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160780"
---
## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část vás provede nastavení vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET MVC, přidává se připojení připojené služby, přidání kontroleru a určení direktivy požadovaný obor názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření projektu aplikace ASP.NET MVC

1. Otevřít Visual Studio.

1. V hlavní nabídce vyberte **souboru** > **nový** > **projektu**.

1. V **nový projekt** dialogu **webové** > **webová aplikace ASP.NET (.NET Framework)**. V **název** uveďte **StorageAspNet**. Vyberte **OK**.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. V **nová webová aplikace ASP.NET** dialogu **MVC**a pak vyberte **OK**.

    ![Dialogové okno snímek obrazovky z nová webová aplikace ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Připojte se k účtu služby Azure storage pomocí připojených služeb

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.

1. V místní nabídce vyberte **přidat** > **připojenou službu**.

1. V **připojené služby** dialogu **cloudové úložiště se službou Azure Storage**.

    ![Dialogové okno snímek obrazovky připojené služby](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. V **služby Azure Storage** dialogovém okně vyberte účet úložiště Azure se použije pro účely tohoto kurzu. Chcete-li vytvořit nový účet úložiště Azure, vyberte **vytvořit nový účet úložiště**a vyplňte formulář. Po výběru buď existující účet úložiště nebo vytvoří nový, vyberte **přidat**. Visual Studio nainstaluje balíček NuGet pro služby Azure Storage a připojovací řetězec úložiště do **Web.config**.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **závislosti**, zvolte **spravovat balíčky NuGet**a přidejte odkaz na balíček NuGet na nejnovější verzi Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Další informace o vytvoření účtu úložiště pomocí [webu Azure portal](https://portal.azure.com), naleznete v tématu [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Můžete také vytvořit účet úložiště pomocí [prostředí Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](../articles/storage/common/storage-azure-cli.md), nebo [Azure Cloud Shell](../articles/cloud-shell/overview.md).
