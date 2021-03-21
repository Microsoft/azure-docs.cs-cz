---
title: Nastavení Service Fabric back-endu v Azure API Management | Microsoft Docs
description: Postup vytvoření back-endu služby Service Fabric v Azure API Management pomocí Azure Portal
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500627"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Nastavení Service Fabricho back-endu v API Management pomocí Azure Portal

Tento článek popisuje, jak nakonfigurovat službu [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) jako vlastní back-end rozhraní API pomocí Azure Portal. Pro demonstrační účely ukazuje, jak nastavit základní bezstavovou ASP.NET Core spolehlivou službu jako Service Fabric back-endu.

Pozadí naleznete v tématu [back-endy v API Management](backends.md).

## <a name="prerequisites"></a>Předpoklady

Požadavky na konfiguraci ukázkové služby v Service Fabricovém clusteru se systémem Windows jako vlastní back-end:

* **Vývojové prostředí Windows** – nainstalujte si [Visual Studio 2019](https://www.visualstudio.com) a vývoj pro vývoj pro **Azure**, vývoj pro **web v ASP.NET a vývoji webů** a .NET Core pro vývoj aplikací pro **různé platformy** . Potom nastavte [vývojové prostředí .NET](../service-fabric/service-fabric-get-started.md).

* **Service Fabric cluster** – viz [kurz: nasazení Service Fabric clusteru se systémem Windows do služby Azure Virtual Network](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Můžete vytvořit cluster s existujícím certifikátem X. 509 nebo pro účely testování vytvořit nový certifikát podepsaný svým držitelem. Cluster se vytvoří ve virtuální síti.

* **Ukázka Service Fabric aplikace** – vytvořte aplikaci webového rozhraní API a nasaďte ji do clusteru Service Fabric, jak je popsáno v tématu [integrace API Management s Service Fabric v Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    Tyto kroky vytvoří základní bezstavovou ASP.NET Core spolehlivou službu pomocí výchozí šablony projektu webového rozhraní API. Později vystavíte koncový bod HTTP pro tuto službu prostřednictvím Azure API Management.

    Poznamenejte si název aplikace, například `fabric:/myApplication/myService` . 

* **Instance API Management** – existující nebo nová instance API Management v úrovni **Premium** nebo  **Developer** a ve stejné oblasti jako cluster Service Fabric. Pokud ho potřebujete, [vytvořte instanci API Management](get-started-create-service-instance.md).

* **Virtuální síť** – přidejte svou instanci API Management do virtuální sítě, kterou jste vytvořili pro cluster Service Fabric. API Management vyžaduje vyhrazenou podsíť ve virtuální síti.

  Postup pro povolení připojení virtuální sítě pro instanci API Management najdete v tématu [Jak používat Azure API Management s virtuálními sítěmi](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Vytvoření back-endu – portál

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Přidat Service Fabric certifikát clusteru do API Management

Certifikát Service Fabric clusteru je uložený a spravovaný v trezoru klíčů Azure přidruženém ke clusteru. Tento certifikát přidejte do instance API Management jako klientský certifikát.

Postup přidání certifikátu do instance API Management najdete v tématu [Postup zabezpečení back-end služeb pomocí ověřování klientských certifikátů v Azure API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Doporučujeme přidat certifikát do API Management odkazem na certifikát trezoru klíčů. 

### <a name="add-service-fabric-backend"></a>Přidat Service Fabric back-end

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V části **rozhraní API** vyberte **back-endy**  >  **+ Přidat**.
1. Zadejte název back-endu a volitelný popis.
1. V **typu** vyberte **Service Fabric**.
1. Do pole **Adresa URL modulu runtime** zadejte název služby Service Fabric back-end, na kterou API Management předají požadavky. Příklad: `fabric:/myApplication/myService`. 
1. Do **maximálního počtu opakování překladu oddílu** zadejte číslo od 0 do 10.
1. Zadejte koncový bod správy clusteru Service Fabric. Tento koncový bod je adresa URL clusteru na portu `19080` , například `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. V části **klientský certifikát** vyberte Service Fabric certifikát clusteru, který jste přidali do své instance API Management v předchozí části.
1. Do pole **autorizační metoda koncového bodu správy** zadejte kryptografický otisk nebo název x509 serveru certifikátu používaného službou správy clusterů Service Fabric pro komunikaci TLS.
1. Povolte možnost **ověřit řetěz certifikátů** a **Ověřte nastavení názvu certifikátu** .
1. V části **autorizační pověření** zadejte přihlašovací údaje, pokud je to nutné, pro dosažení nakonfigurované back-end služby v Service Fabric. Pro ukázkovou aplikaci použitou v tomto scénáři nejsou autorizační přihlašovací údaje potřeba.
1. Vyberte **Vytvořit**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Vytvoření back-endu Service fabricu":::

## <a name="use-the-backend"></a>Použití back-endu

Pokud chcete použít vlastní back-end, odkažte ho pomocí [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zásad. Tato zásada transformuje výchozí základní adresu URL služby back-end pro příchozí požadavek rozhraní API na zadaný back-end, v tomto případě Service Fabric back-end. 

`set-backend-service`Zásada může být užitečná s existujícím rozhraním API pro transformaci příchozího požadavku na jiný back-end, než je ta zadaná v nastavení rozhraní API. Pro demonstrační účely v tomto článku vytvořte testovací rozhraní API a nastavte zásady na přímé požadavky rozhraní API na Service Fabric back-end. 

### <a name="create-api"></a>Vytvoření rozhraní API

Pokud chcete vytvořit prázdné rozhraní API, postupujte podle kroků v části [Ruční přidání rozhraní API](add-api-manually.md) .

* V nastavení rozhraní API ponechte **adresu URL webové služby** prázdnou.
* Přidejte **příponu adresy URL rozhraní API**, jako je například *Fabric*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Vytvoření prázdného rozhraní API":::

### <a name="add-get-operation-to-the-api"></a>Přidání operace GET do rozhraní API

Jak je znázorněno v části [nasazení back-endové služby Service Fabric](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service), ukázková ASP.NET Core služba nasazená v clusteru Service Fabric podporuje jednu operaci HTTP GET v cestě URL `/api/values` .

Výchozí odpověď v této cestě je pole JSON dvou řetězců:

```json
["value1", "value2"]
```

Pokud chcete otestovat integraci API Management s clusterem, přidejte do rozhraní API odpovídající operaci GET v cestě `/api/values` :

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
1. Vyberte **+ Přidat operaci**.
1. V okně **front-end** zadejte následující hodnoty a vyberte **Uložit**.

     | Nastavení             | Hodnota                             | 
    |---------------------|-----------------------------------|
    | **Zobrazovaný název**    | *Testovací back-end*                       |  
    | **Adresa URL** | GET                               | 
    | **Adresa URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Přidat operaci GET do rozhraní API":::

### <a name="configure-set-backend-policy"></a>Konfigurace `set-backend` zásad

Přidejte [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zásadu do testovacího rozhraní API.

1. Na kartě **Návrh** v části **příchozí zpracování** vyberte ikonu Editor kódu ( **</>** ). 
1. Umístit kurzor dovnitř elementu **&lt; vstupu &gt;**
1. Přidejte následující prohlášení o zásadách. V nástroji `backend-id` nahraďte název vašeho Service Fabric back-end.

   `sf-resolve-condition`Je-li oddíl clusteru vyřešen, je podmínka opakování. Počet pokusů, které se nastavily při konfiguraci back-endu.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Vyberte **Uložit**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Konfigurace nastavení-back-endu – zásady služby":::

### <a name="test-backend-api"></a>Test back-endu rozhraní API

1. Na kartě **test** vyberte operaci **získat** , kterou jste vytvořili v předchozí části.
1. Vyberte **Odeslat**.

V případě správné konfigurace zobrazuje odpověď HTTP kód úspěchu protokolu HTTP a zobrazí kód JSON vrácený službou back-end Service Fabric.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Test Service Fabric back-endu":::

## <a name="next-steps"></a>Další kroky

* Naučte se [Konfigurovat zásady](api-management-advanced-policies.md) pro přeposílání požadavků do back-endu.
* Back-endy lze také konfigurovat pomocí šablon API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)nebo [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

