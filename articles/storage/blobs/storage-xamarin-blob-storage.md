---
title: Jak používat úložiště objektu (Blob) z Xamarin | Dokumenty společnosti Microsoft
description: Klientská knihovna Azure Storage pro Xamarin umožňuje vývojářům vytvářet aplikace pro iOS, Android a Windows Store s jejich nativními uživatelskými rozhraními. Tento kurz ukazuje, jak pomocí Xamarinu vytvořit aplikaci, která používá úložiště objektů blob Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726344"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Jak používat úložiště objektů Blob z Xamarinu

Xamarin umožňuje vývojářům používat sdílený základ kódu C# k vytváření aplikací pro iOS, Android a Windows Store s jejich nativními uživatelskými rozhraními. Tento kurz ukazuje, jak používat úložiště objektů blob Azure s aplikací Xamarin. Pokud se chcete dozvědět víc o Azure Storage, než se ponoříte do kódu, [přečtěte si úvodní informace o microsoft azure storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Vytvoření nové aplikace Xamarin

V tomto kurzu vytvoříme aplikaci, která se zaměřuje na Android, iOS a Windows. Tato aplikace jednoduše vytvoří kontejner a nahraje objekt blob do tohoto kontejneru. Budeme používat Visual Studio ve Windows, ale stejné poznatky lze použít při vytváření aplikace pomocí Xamarin Studio na macOS.

Chcete-li vytvořit aplikaci, postupujte takto:

1. Pokud jste tak dosud neučinili, stáhněte a nainstalujte [Xamarin pro Visual Studio](https://www.xamarin.com/download).
2. Otevřete Visual Studio a vytvořte prázdnou aplikaci (nativní přenositelnost): **Soubor > nový > projekt > aplikace > aplikace pro > (nativní přenosné).**
3. Klepněte pravým tlačítkem myši na řešení v podokně Průzkumník řešení a vyberte **spravovat balíčky NuGet for Solution**. Vyhledejte **WindowsAzure.Storage** a nainstalujte nejnovější stabilní verzi do všech projektů ve vašem řešení.
4. Sestavení a spuštění projektu.

Nyní byste měli mít aplikaci, která umožňuje klepnout na tlačítko, které se zvětší čítač.

## <a name="create-container-and-upload-blob"></a>Vytvoření kontejneru a nahrání objektu blob

Dále v `(Portable)` rámci projektu přidáte nějaký kód `MyClass.cs`do aplikace . Tento kód vytvoří kontejner a nahraje objekt blob do tohoto kontejneru. `MyClass.cs`by měl vypadat takto:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Nezapomeňte nahradit "your_account_name_here" a "your_account_key_here" skutečným názvem účtu a klíčem účtu.

Vaše projekty iOS, Android a Windows Phone mají všechny odkazy na váš přenosný projekt – což znamená, že můžete napsat celý sdílený kód na jednom místě a používat jej ve všech svých projektech. Nyní můžete přidat následující řádek kódu ke každému projektu začít využívat:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Nyní můžete spustit tuto aplikaci v emulátoru Android nebo Windows Phone. Tuto aplikaci můžete také spustit v emulátoru iOS, ale to bude vyžadovat Mac. Konkrétní pokyny, jak to provést, naleznete v dokumentaci pro [připojení sady Visual Studio k Macu.](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Jakmile aplikaci spustíte, vytvoří `mycontainer` kontejner ve vašem účtu úložiště. Měl by obsahovat `myblob`objekt blob , `Hello, world!`který má text, . Můžete to ověřit pomocí [Průzkumníka úložiště Microsoft Azure](https://storageexplorer.com/).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit aplikaci pro různé platformy v Xamarinu, která používá Azure Storage, konkrétně se zaměřením na jeden scénář ve službě Blob Storage. Můžete však udělat mnohem více nejen s úložištěm objektů blob, ale také s table, file a queue storage. Další informace naleznete v následujících článcích:

* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](storage-dotnet-how-to-use-blobs.md)
* [Seznámení se Soubory Azure](../files/storage-files-introduction.md)
* [Vývoj pro Soubory Azure pomocí .NET](../files/storage-dotnet-how-to-use-files.md)
* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
