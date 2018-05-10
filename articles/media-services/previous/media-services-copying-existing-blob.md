---
title: Kopírování objektů blob z účtu úložiště do prostředek služby Azure Media Services | Microsoft Docs
description: Toto téma ukazuje, jak chcete zkopírovat existující objekt blob do služby Asset média. V příkladu rozšíření Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 9305b3cb810af9f0653d980328c46e41a540bf1a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>Kopírování do služby Asset média existující objekty BLOB
Tento článek ukazuje, jak chcete zkopírovat objekty BLOB z účtu úložiště do nového prostředku Azure Media Services (AMS) pomocí [rozšíření Azure Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services-extensions/).

Rozšiřující metody pracovat:

- Regulární prostředky.
- Živé archivu prostředky (FragBlob format).
- Zdrojové a cílové prostředky, které patří do různých účtech Media Services (i v rámci různých datových střediscích). Však může být poplatky za tímto způsobem. Další informace o cenách najdete v tématu [přenosů dat](https://azure.microsoft.com/pricing/#header-11).

> [!NOTE]
> Chcete-li změnit obsah kontejnery objektů blob, které byly vygenerovány Media Services bez použití rozhraní API služby Media byste neměli.
> 

Článek ukazuje dva ukázky kódu:

1. Zkopírujte objekty BLOB z prostředek v jednom účtu AMS do nového prostředku na jiném účtu AMS.
2. Zkopírujte objekty BLOB z některé účtu úložiště do nového prostředku v účtu AMS.

## <a name="copy-blobs-between-two-ams-accounts"></a>Kopírování objektů BLOB mezi dva účty AMS  

### <a name="prerequisites"></a>Požadavky

Dva účty služby Media Services. Najdete v článku [postup vytvoření účtu Media Services](media-services-portal-create-account.md).

### <a name="download-sample"></a>Stažení ukázky
Můžete provést kroky v tomto článku nebo si můžete stáhnout vzorek, který obsahuje kód popsané v tomto článku [zde](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/).

### <a name="set-up-your-project"></a>Nastavení projektu

1. Nastavení vývojového prostředí, jak je popsáno v [vývoj pro Media Services s .NET](media-services-dotnet-how-to-use.md). 
2. Přidejte oddíl appSettings do souboru .config a aktualizujte hodnoty na základě svých účtů Media Services, cílový účet úložiště a ID zdroje asset.  

```xml
<appSettings>
    <add key="AMSSourceAADTenantDomain" value="tenant"/>
    <add key="AMSSourceRESTAPIEndpoint" value="endpoint"/>

    <add key="SourceAMSClientId" value="clientID"/>
    <add key="SourceAMSClientSecret" value="clientSecret"/>

    <add key="SourceAssetID" value="nb:cid:UUID:<id>"/>

    <add key="AMSDestAADTenantDomain" value="tenant"/>
    <add key="AMSDestRESTAPIEndpoint" value="endpoint"/>

    <add key="DestAMSClientId" value="clientID"/>
    <add key="DestAMSClientSecret" value="clientSecret"/>

    <add key="DestStorageAccountName" value="name"/>
    <add key="DestStorageAccountKey" value="key"/>

</appSettings>
```

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>Kopírování objektů blob z prostředek v jednom účtu AMS do prostředku na jiném účtu AMS

Následující kód používá rozšíření **IAsset.Copy** metoda zkopíruje všechny soubory v prostředku zdrojového do cílového prostředku pomocí jedné rozšíření.

```csharp
using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Linq;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;

namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        static string _sourceAADTenantDomain = 
            ConfigurationManager.AppSettings["AMSSourceAADTenantDomain"];
        static string _sourceRESTAPIEndpoint = 
            ConfigurationManager.AppSettings["AMSSourceRESTAPIEndpoint"];
        static string _sourceClientId = 
            ConfigurationManager.AppSettings["SourceAMSClientId"];
        static string _sourceClientSecret = 
            ConfigurationManager.AppSettings["SourceAMSClientSecret"];

        static string _destAADTenantDomain = 
            ConfigurationManager.AppSettings["AMSDestAADTenantDomain"];
        static string _destRESTAPIEndpoint = 
            ConfigurationManager.AppSettings["AMSDestRESTAPIEndpoint"];
        static string _destClientId = 
            ConfigurationManager.AppSettings["DestAMSClientId"];
        static string _destClientSecret = 
            ConfigurationManager.AppSettings["DestAMSClientSecret"];

        static string _destStorageAccountName = 
            ConfigurationManager.AppSettings["DestStorageAccountName"];
        static string _destStorageAccountKey = 
            ConfigurationManager.AppSettings["DestStorageAccountKey"];
        static string _sourceAssetID = 
            ConfigurationManager.AppSettings["SourceAssetID"];

        private static CloudMediaContext _sourceContext = null;
        private static CloudMediaContext _destContext = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials1 = new AzureAdTokenCredentials(_sourceAADTenantDomain,
                   new AzureAdClientSymmetricKey(_sourceClientId, _sourceClientSecret),
                   AzureEnvironments.AzureCloudEnvironment);

            AzureAdTokenCredentials tokenCredentials2 = new AzureAdTokenCredentials(_destAADTenantDomain,
                   new AzureAdClientSymmetricKey(_destClientId, _destClientSecret),
                   AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider1 = new AzureAdTokenProvider(tokenCredentials1);
            var tokenProvider2 = new AzureAdTokenProvider(tokenCredentials2);

            // Create the context for your source Media Services account.
            _sourceContext = new CloudMediaContext(new Uri(_sourceRESTAPIEndpoint), tokenProvider1);

            // Create the context for your destination Media Services account.
            _destContext = new CloudMediaContext(new Uri(_destRESTAPIEndpoint), tokenProvider2);

            // Get the credentials of the default Storage account bound to your destination Media Services account.
            StorageCredentials destinationStorageCredentials =
                new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);

            // Get a reference to the source asset in the source context.
            IAsset sourceAsset = _sourceContext.Assets.Where(asset => asset.Id == _sourceAssetID).First();

            // Create an empty destination asset in the destination context.
            IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);

            // Copy the files in the source asset instance into the destination asset instance.
            // There is an additional overload with async support.
            sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

            Console.WriteLine("Done");
        }
    }
}
```

## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>Kopírování objektů blob z účtu úložiště k účtu AMS 

### <a name="prerequisites"></a>Požadavky

- Jeden účet úložiště, ze kterého chcete zkopírovat objekty BLOB.
- Jeden účet AMS, do kterého chcete zkopírovat objekty BLOB.

### <a name="set-up-your-project"></a>Nastavení projektu

1. Nastavení vývojového prostředí, jak je popsáno v [vývoj pro Media Services s .NET](media-services-dotnet-how-to-use.md). 
2. Přidejte oddíl appSettings do souboru .config a aktualizujte hodnoty na základě vaší zdrojové úložiště a cílový účtů AMS.

```xml
<appSettings>
    <add key="SourceStorageAccountName" value="name" />
    <add key="SourceStorageAccountKey" value="key" />
    <add key="NameOfBlobContainerYouWantToCopy" value="BlobContainerName"/>
    
    <add key="AMSAADTenantDomain" value="tenant"/>
    <add key="AMSRESTAPIEndpoint" value="endpoint"/>
    <add key="AMSClientId" value="clientID"/>
    <add key="AMSClientSecret" value="clientSecret"/>
    <add key="AMSStorageAccountName" value="name"/>
    <add key="AMSStorageAccountKey" value="key"/>
</appSettings>
```

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-an-ams-account"></a>Kopírování objektů blob z některé účtu úložiště do nového prostředku v účtu AMS

Následující kód zkopíruje objekty BLOB z účtu úložiště do asset Media Services. 

>[!NOTE]
>Je stanovený limit 1 000 000 různých zásad AMS (třeba zásady lokátoru nebo ContentKeyAuthorizationPolicy). Pokud vždy používáte stejné dny / přístupová oprávnění, například zásady pro lokátory, které mají zůstat na místě po dlouhou dobu (zásady bez odeslání), měli byste použít stejné ID zásad. Další informace najdete v [tomto](media-services-dotnet-manage-entities.md#limit-access-policies) článku.

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
    
namespace CopyExistingBlobsIntoAsset
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _sourceStorageAccountName =
            ConfigurationManager.AppSettings["SourceStorageAccountName"];
        private static readonly string _sourceStorageAccountKey =
            ConfigurationManager.AppSettings["SourceStorageAccountKey"];
        private static readonly string _NameOfBlobContainerYouWantToCopy =
            ConfigurationManager.AppSettings["NameOfBlobContainerYouWantToCopy"];

        private static readonly string _AMSAADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _AMSRESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];
        private static readonly string _AMSStorageAccountName =
            ConfigurationManager.AppSettings["AMSStorageAccountName"];
        private static readonly string _AMSStorageAccountKey =
            ConfigurationManager.AppSettings["AMSStorageAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static CloudStorageAccount _sourceStorageAccount = null;
        private static CloudStorageAccount _destinationStorageAccount = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain,
               new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
               AzureEnvironments.AzureCloudEnvironment);
            
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Create the context for your source Media Services account.
            _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);

            _sourceStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName,
                    _sourceStorageAccountKey), true);

            _destinationStorageAccount =
                new CloudStorageAccount(new StorageCredentials(_AMSStorageAccountName,
                    _AMSStorageAccountKey), true);

            CloudBlobClient sourceCloudBlobClient =
                _sourceStorageAccount.CreateCloudBlobClient();
            CloudBlobContainer sourceContainer =
                sourceCloudBlobClient.GetContainerReference(_NameOfBlobContainerYouWantToCopy);

            CreateAssetFromExistingBlobs(sourceContainer);

            Console.WriteLine("Done");
        }

        static private IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
        {
            CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

            // Create a new asset. 
            IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);

            IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                TimeSpan.FromHours(24), AccessPermissions.Write);

            ILocator destinationLocator =
                _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);

            // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
            CloudBlobContainer destAssetContainer =
                destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);

            if (destAssetContainer.CreateIfNotExists())
            {
                destAssetContainer.SetPermissions(new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
            }

            var blobList = sourceBlobContainer.ListBlobs();

            foreach (CloudBlockBlob sourceBlob in blobList)
            {
                var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);

                ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);

                CopyBlob(sourceBlob, destAssetContainer);
                
                sourceBlob.FetchAttributes();
                assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                assetFile.Update();
                Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
            }

            asset.Update();

            destinationLocator.Delete();
            writePolicy.Delete();

            // Set the primary asset file.
            // If, for example, we copied a set of Smooth Streaming files, 
            // set the .ism file to be the primary file. 
            // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
            var ismAssetFile = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray().FirstOrDefault();

            // The following code assigns the first .ism file as the primary file in the asset.
            // An asset should have one .ism file.  
            if (ismAssetFile != null)
            {
                ismAssetFile.IsPrimary = true;
                ismAssetFile.Update();
            }

            return asset;
        }

        /// <summary>
        /// Copies the specified blob into the specified container.
        /// </summary>
        /// <param name="sourceBlob">The source container.</param>
        /// <param name="destinationContainer">The destination container.</param>
        static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
        {
            var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
            {
                Permissions = SharedAccessBlobPermissions.Read,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
            });

            var destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

            if (destinationBlob.Exists())
            {
                Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
            }
            else
            {

                // Display the size of the source blob.
                Console.WriteLine(sourceBlob.Properties.Length);

                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                destinationBlob.StartCopy(new Uri(sourceBlob.Uri.AbsoluteUri + signature));

                while (true)
                {
                    // The StartCopyFromBlob is an async operation, 
                    // so we want to check if the copy operation is completed before proceeding. 
                    // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                    destinationBlob.FetchAttributes();
                    if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                    {
                        break;
                    }
                    //It's still not completed. So wait for some time.
                    System.Threading.Thread.Sleep(1000);
                }

                // Display the size of the destination blob.
                Console.WriteLine(destinationBlob.Properties.Length);

            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

Můžete také použít službu Azure Functions k aktivaci úlohy kódování při příchodu souboru do nakonfigurovaného kontejneru. Další informace najdete v [této ukázce](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

