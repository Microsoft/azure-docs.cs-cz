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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174908"
---
## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část vás provede nastavením vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET MVC, přidání připojení připojené služby, přidání kontroleru a určení požadovaných direktiv oboru názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření projektu aplikace ASP.NET MVC

1. Otevřete sadu Visual Studio.

1. V hlavní nabídce vyberte **soubor** > **Nový** > **projekt**.

1. V dialogovém okně **Nový projekt** vyberte webová aplikace **Web** > **ASP.NET (.NET Framework)**. Do pole **název** zadejte **StorageAspNet**. Vyberte **OK**.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. V dialogovém okně **Nová webová aplikace ASP.NET** vyberte **MVC**a pak vyberte **OK**.

    ![Snímek obrazovky dialogového okna Nová webová aplikace v ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Připojení k účtu služby Azure Storage pomocí připojených služeb

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt.

1. V místní nabídce vyberte **Přidat** > **připojenou službu**.

1. V dialogovém okně **připojené služby** vyberte **cloudové úložiště s Azure Storage**.

    ![Snímek obrazovky dialogového okna připojené služby](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. V dialogovém okně **Azure Storage** vyberte účet služby Azure Storage, který se má použít pro tento kurz. Pokud chcete vytvořit nový účet úložiště Azure, vyberte **vytvořit nový účet úložiště**a vyplňte formulář. Po výběru některého z existujících účtů úložiště nebo vytvoření nového vyberte **Přidat**. Sada Visual Studio nainstaluje balíček NuGet pro Azure Storage a připojovací řetězec úložiště do **souboru Web. config**.

1. V **Průzkumník řešení**klikněte pravým tlačítkem na **závislosti**, vyberte **Spravovat balíčky NuGet**a přidejte odkaz na balíček NuGet do nejnovější verze Microsoft. Azure. ConfigurationManager.

> [!TIP]
> Informace o tom, jak vytvořit účet úložiště pomocí [Azure Portal](https://portal.azure.com), najdete v tématu [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Účet úložiště můžete vytvořit také pomocí [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), rozhraní příkazového [řádku Azure](../articles/storage/common/storage-azure-cli.md)nebo [Azure Cloud Shell](../articles/cloud-shell/overview.md).
