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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174908"
---
## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část prochází nastavení vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET MVC, přidání připojení připojených služeb, přidání řadiče a určení požadovaných směrnic oboru názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření ASP.NET projektu aplikace MVC

1. Otevřete sadu Visual Studio.

1. V hlavní nabídce vyberte **Soubor** > **nový** > **projekt**.

1. V dialogovém okně **Nový projekt** vyberte **možnost WebASP.NET** > **webová aplikace (.NET Framework).** V poli **Název** zadejte **StorageAspNet**. Vyberte **OK**.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. V dialogovém **okně Nová ASP.NET webová aplikace** vyberte **MVC**a pak vyberte **OK**.

    ![Snímek obrazovky s dialogovým oknem Nová ASP.NET webová aplikace](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Připojení k účtu úložiště Azure pomocí připojených služeb

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt.

1. V místní nabídce vyberte **Přidat** > **připojenou službu**.

1. V dialogovém okně **Připojené služby** vyberte **Cloudové úložiště s Azure Storage**.

    ![Snímek obrazovky s dialogovým oknem Připojené služby](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. V dialogovém okně **Azure Storage** vyberte účet úložiště Azure, který se použije pro tento kurz. Pokud chcete vytvořit nový účet úložiště Azure, vyberte **Vytvořit nový účet úložiště**a vyplňte formulář. Po výběru existujícího účtu úložiště nebo vytvoření nového účtu vyberte **Přidat**. Visual Studio nainstaluje balíček NuGet pro Azure Storage a připojovací řetězec úložiště na **web.config**.

1. V **Průzkumníku řešení**klikněte pravým **tlačítkem**myši na závislosti , zvolte **Spravovat balíčky NuGet**a přidejte odkaz na balíček NuGet na nejnovější verzi Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Informace o tom, jak vytvořit účet úložiště na [webu Azure Portal](https://portal.azure.com), najdete v [tématu Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Účet úložiště můžete také vytvořit pomocí [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md)nebo [Azure Cloud Shell](../articles/cloud-shell/overview.md).
