## <a name="set-up-the-development-environment"></a>Nastavení vývojového prostředí

Tato část vás provede nastavení vývojového prostředí. To zahrnuje vytvoření aplikace ASP.NET MVC, přidává se připojení připojených služeb, přidávání řadiče a zadání direktivy požadované oboru názvů.

### <a name="create-an-aspnet-mvc-app-project"></a>Vytvoření projektu aplikace ASP.NET MVC

1. Otevřete sadu Visual Studio.

1. V hlavní nabídce vyberte **soubor** > **nový** > **projektu**.

1. V **nový projekt** dialogové okno, vyberte **webové** > **webové aplikace ASP.NET (rozhraní .NET Framework)**. V **název** pole, zadejte **StorageAspNet**. Vyberte **OK**.

    ![Dialogové okno snímek obrazovky nový projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. V **nové webové aplikace ASP.NET** dialogové okno, vyberte **MVC**a potom vyberte **OK**.

    ![Dialogové okno snímek obrazovky z nové webové aplikace ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Použít připojených služeb k připojení k účtu úložiště Azure

1. V **Průzkumníku**, klikněte pravým tlačítkem na projekt.

2. V místní nabídce vyberte **přidat** > **připojení službě**.

1. V **připojené služby** dialogové okno, vyberte **úložiště v cloudu s Azure Storage**.

    ![Dialogové okno snímek obrazovky připojení služby](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. V **Azure Storage** dialogové okno, vyberte účet úložiště Azure má být použit pro tento kurz. Chcete-li vytvořit nový účet úložiště Azure, vyberte **vytvořit nový účet úložiště**a vyplňte formulář. Po výběru buď existující účet úložiště nebo vytvořením nové, vyberte **přidat**. Visual Studio nainstaluje balíček NuGet pro Azure Storage a připojovací řetězec úložiště do **Web.config**.

> [!TIP]
> Další informace o vytvoření účtu úložiště s [portál Azure](https://portal.azure.com), najdete v části [vytvořit účet úložiště](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Můžete také vytvořit účet úložiště pomocí [prostředí Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [rozhraní příkazového řádku Azure](../articles/storage/common/storage-azure-cli.md), nebo [prostředí cloudu Azure](../articles/cloud-shell/overview.md).

