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
ms.openlocfilehash: 634143f56bc9134f240ff42b7b5989605c8bffde
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979394"
---
## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část vás provede nastavení vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET MVC, přidává se připojení připojené služby, přidání kontroleru a určení direktivy požadovaný obor názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření projektu aplikace ASP.NET MVC

1. Otevřete sadu Visual Studio.

1. V hlavní nabídce vyberte **souboru** > **nový** > **projektu**.

1. V **nový projekt** dialogu **webové** > **webová aplikace ASP.NET (.NET Framework)**. V **název** uveďte **StorageAspNet**. Vyberte **OK**.

    ![Dialogové okno snímek obrazovky nového projektu](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. V **nová webová aplikace ASP.NET** dialogu **MVC**a pak vyberte **OK**.

    ![Dialogové okno snímek obrazovky z nová webová aplikace ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Připojte se k účtu služby Azure storage pomocí připojených služeb

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt.

2. V místní nabídce vyberte **přidat** > **připojenou službu**.

1. V **připojené služby** dialogu **cloudové úložiště se službou Azure Storage**.

    ![Dialogové okno snímek obrazovky připojené služby](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. V **služby Azure Storage** dialogovém okně vyberte účet úložiště Azure se použije pro účely tohoto kurzu. Chcete-li vytvořit nový účet úložiště Azure, vyberte **vytvořit nový účet úložiště**a vyplňte formulář. Po výběru buď existující účet úložiště nebo vytvoří nový, vyberte **přidat**. Visual Studio nainstaluje balíček NuGet pro služby Azure Storage a připojovací řetězec úložiště do **Web.config**.

> [!TIP]
> Další informace o vytvoření účtu úložiště pomocí [webu Azure portal](https://portal.azure.com), naleznete v tématu [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Můžete také vytvořit účet úložiště pomocí [prostředí Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](../articles/storage/common/storage-azure-cli.md), nebo [Azure Cloud Shell](../articles/cloud-shell/overview.md).

