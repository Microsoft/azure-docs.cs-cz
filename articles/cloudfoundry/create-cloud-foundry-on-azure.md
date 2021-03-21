---
title: Vytvoření Cloud Foundryho clusteru s Pivotem v Azure
description: Naučte se, jak nastavit parametry potřebné k zřízení clusteru Pivoted Cloud Foundry (PCF) v Azure.
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 3444d6c62b5e36b7e68cfaf6da1ec534e2ea4ec6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551449"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Vytvoření Cloud Foundryho clusteru s Pivotem v Azure

V tomto kurzu najdete rychlé kroky k vytvoření a vytvoření parametrů, které potřebujete k zřízení clusteru PCF (Pivoted Cloud Foundry) v Azure. Pokud chcete najít Cloud Foundry řešení pro Pivoting, proveďte hledání na webu Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Hledání v Cloud Foundry Pivot v Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Vygenerování veřejného klíče SSH

Existuje několik způsobů, jak vygenerovat klíč SSH (Secure Shell) pomocí systémů Windows, Mac nebo Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Další informace najdete v tématu [použití klíčů ssh s Windows v Azure](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

> [!NOTE]
>
> K vytvoření instančního objektu potřebujete oprávnění k účtu vlastníka. Můžete také napsat skript pro automatizaci vytváření instančního objektu. Můžete například použít Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp).

1. Přihlaste se ke svému účtu Azure.

    ```azurecli
    az login
    ```

    ![Přihlášení Azure CLI](media/deploy/az-login-output.png )
 
    Zkopírujte hodnotu ID jako **ID vašeho předplatného** a zkopírujte hodnotu "tenantId", která se použije později.

2. Nastavte své výchozí předplatné pro tuto konfiguraci.

    ```azurecli
    az account set -s {id}
    ```

3. Vytvořte aplikaci Azure Active Directory pro PCF. Zadejte jedinečné alfanumerické heslo. Uložte heslo jako **clientSecret** pro pozdější použití.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    Zkopírujte hodnotu appId ve výstupu jako své **clientID** pro pozdější použití.

    > [!NOTE]
    >
    > Vyberte svou vlastní domovskou stránku aplikace a identifikátor URI identifikátoru, například http \: //www \. contoso.com.

4. Vytvořte instanční objekt s novým ID aplikace.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. Nastavte oprávnění vašeho instančního objektu na roli Přispěvatel.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    Nebo můžete také použít

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![Přiřazení role objektu služby](media/deploy/svc-princ.png )

6. Ověřte, že se můžete úspěšně přihlásit k instančnímu objektu pomocí ID aplikace, hesla a ID tenanta.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. Vytvořte soubor. JSON v následujícím formátu. Použijte hodnoty **ID předplatného**, **tenantID**, **clientID** a **clientSecret** , které jste zkopírovali dříve. Soubor uložte.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Získání tokenu pro Pivoting Network

1. Zaregistrujte se nebo se přihlaste ke svému účtu [pivoted Network](https://network.pivotal.io) .
2. V pravém horním rohu stránky vyberte název vašeho profilu. Vyberte **Upravit profil**.
3. Posuňte se do dolní části stránky a zkopírujte hodnotu **tokenu starší verze API** . Tato hodnota je hodnota **tokenu vaší kontingenční sítě** , kterou použijete později.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Zřízení clusteru Cloud Foundry v Azure

Teď máte všechny parametry, které potřebujete ke zřízení [Cloud Foundry clusteru v Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Zadejte parametry a vytvořte cluster PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Ověření nasazení a přihlášení k nástroji pro Pivotovou správu OPS

1. Cluster PCF zobrazuje stav nasazení.

    ![Stav nasazení Azure](media/deploy/deployment.png )

2. Kliknutím na odkaz **nasazení** v navigaci vlevo získáte přihlašovací údaje pro správce OPS PCF. Na další stránce vyberte **název nasazení** .
3. V navigaci na levé straně vyberte odkaz **výstupy** , abyste zobrazili adresu URL, uživatelské jméno a heslo pro správce OPS PCF. Hodnota "OPSMAN-FQDN" je adresa URL.
 
    ![Výstup nasazení Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Spusťte adresu URL ve webovém prohlížeči. Pokud se chcete přihlásit, zadejte přihlašovací údaje z předchozího kroku.

    ![Přihlašovací stránka pro Pivot](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Pokud se prohlížeč Internet Explorer nezdařil z důvodu varovné zprávy "Web není zabezpečen", vyberte **Další informace** a navštivte webovou stránku. Pro Firefox vyberte pokračovat **a přidejte** certifikát pro pokračování.

5. Správce OPS PCF zobrazí nasazené instance Azure. Nyní můžete nasazovat a spravovat aplikace zde.
               
    ![Nasadila se instance Azure v pivotu.](media/deploy/ops-mgr.png )
