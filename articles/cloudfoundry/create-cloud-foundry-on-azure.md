---
title: Vytvoření clusteru Pivotal Cloud Foundry v Azure
description: Zjistěte, jak nastavit parametry potřebné ke zřízení clusteru Pivotal Cloud Foundry (PCF) v Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 9514118e1f29faab937ed01899b5947789ca9735
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101394"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Vytvoření clusteru Pivotal Cloud Foundry v Azure

Tento kurz obsahuje rychlé kroky pro vytvoření a generování parametrů, budete muset zřídit cluster Pivotal Cloud Foundry (PCF) v Azure. Najít řešení Pivotal Cloud Foundry, provádět vyhledávání ve službě Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Hledání Pivotal Cloud Foundry v Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Vygenerování veřejného klíče SSH

Existuje několik způsobů, jak vygenerovat klíč veřejné (SSH secure shell) pomocí Windows, Mac nebo Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Další informace najdete v tématu [používat klíče SSH s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

> [!NOTE]
>
> Pokud chcete vytvořit instanční objekt služby, budete potřebovat oprávnění vlastníka účtu. Také můžete napsat skript k automatizaci vytváření instančního objektu služby. Například můžete použít rozhraní příkazového řádku Azure [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Přihlaste se ke svému účtu Azure.

    `az login`

    ![Přihlášení k Azure CLI](media/deploy/az-login-output.png )
 
    Zkopírujte hodnotu "id" jako vaše **ID předplatného**a zkopírujte hodnotu "tenantId" pro pozdější použití.

2. Nastavte své výchozí předplatné pro tuto konfiguraci.

    `az account set -s {id}`

3. Vytvoření aplikace Azure Active Directory pro vaše PCF. Zadejte jedinečný alfanumerické heslo. Store heslo jako vaše **clientSecret** pro pozdější použití.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Zkopírujte hodnotu "appId" ve výstupu jako vaše **clientID** pro pozdější použití.

    > [!NOTE]
    >
    > Zvolte vlastní domovskou stránku aplikace a identifikátor URI, například http://www.contoso.com.

4. Vytvoření instančního objektu s ID aplikace.

    `az ad sp create --id {appId}`

5. Nastavte jako přispěvatele roli oprávnění instančního objektu vaší služby.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Nebo můžete použít také

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Přiřazení rolí instančního objektu služby](media/deploy/svc-princ.png )

6. Ověřte, že se můžete úspěšně přihlásit instančního objektu služby s použitím ID aplikace, heslo a ID tenanta.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Vytvořte soubor .json v následujícím formátu. Použití **ID předplatného**, **tenantID**, **clientID**, a **clientSecret** hodnoty, které jste zkopírovali dříve. Uložte soubor.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Získání tokenu Pivotal sítě

1. Registrace nebo přihlášení k vaší [Pivotal sítě](https://network.pivotal.io) účtu.
2. Vyberte název svého profilu v pravém horním rohu stránky. Vyberte **upravit profil**.
3. Přejděte do dolní části stránky a zkopírujte **starší verze rozhraní API TOKENŮ** hodnotu. Tato hodnota je váš **Pivotal sítě Token** hodnotu, kterou použijete později.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Zřízení clusteru Cloud Foundry v Azure

Nyní máte všechny parametry, které potřebujete ke zřízení vašich [clusteru Pivotal Cloud Foundry v Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Zadejte parametry a vytvořte PCF cluster.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Ověření nasazení a přihlášení do správce Pivotal Ops

1. Váš cluster PCF zobrazí stav nasazení.

    ![Stav nasazení v Azure](media/deploy/deployment.png )

2. Vyberte **nasazení** odkaz v navigačním panelu na levé straně, chcete-li získat přihlašovací údaje pro správce PCF Ops. Vyberte **název nasazení** na další stránku.
3. V navigačním panelu na levé straně, vyberte **výstupy** odkaz na adresu URL, uživatelské jméno a heslo pro správce PCF Ops. Hodnota "OPSMAN – plně kvalifikovaný název domény" je adresa URL.
 
    ![Cloud Foundry nasazení výstupu](media/deploy/deploy-outputs.png )
 
4. Spusťte adresu URL ve webovém prohlížeči. Zadejte přihlašovací údaje z předchozího kroku k přihlášení.

    ![Pivotal přihlašovací stránky](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Pokud prohlížeč Internet Explorer se nepovedlo kvůli upozornění "Web nezabezpečenou", vyberte **informace** a přejděte na webovou stránku. Pro Firefox, vyberte **zálohy** a přidejte na certifikaci, aby bylo možné pokračovat.

5. Správce Ops PCF zobrazí nasazené instance Azure. Teď můžete nasadit a spravovat vaše aplikace zde.
               
    ![Nasazená instance Azure ve společnosti Pivotal](media/deploy/ops-mgr.png )
 
