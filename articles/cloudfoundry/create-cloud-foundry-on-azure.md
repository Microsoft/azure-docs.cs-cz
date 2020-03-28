---
title: Vytvoření klíčového clusteru slévár na slétovce cloudu v Azure
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
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 5d4ac5435281f521c71556123f77d737ee6916e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161775"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Vytvoření klíčového clusteru slévár na slétovce cloudu v Azure

Tento kurz obsahuje rychlé kroky k vytvoření a generování parametrů, které potřebujete k zřízení clusteru Pivotal Cloud Foundry (PCF) v Azure. Pokud chcete najít řešení Pivotal Cloud Foundry, proveďte vyhledávání na Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Hledání slévárna pivotal cloud v Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Vygenerování veřejného klíče SSH

Existuje několik způsobů, jak generovat veřejný zabezpečený shell (SSH) klíč pomocí Windows, Mac nebo Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Další informace najdete [v tématu Použití klíčů SSH s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

> [!NOTE]
>
> Chcete-li vytvořit instanční objekt, potřebujete oprávnění účtu vlastníka. Můžete také napsat skript pro automatizaci vytváření instančního objektu. Můžete například použít azure CLI [az az sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Přihlaste se ke svému účtu Azure.

    `az login`

    ![Přihlášení k nastavení příkazového uživatelského nastavení Azure](media/deploy/az-login-output.png )
 
    Zkopírujte hodnotu "id" jako **ID předplatného**a zkopírujte hodnotu "tenantId", která se použije později.

2. Nastavte své výchozí předplatné pro tuto konfiguraci.

    `az account set -s {id}`

3. Vytvořte aplikaci Azure Active Directory pro svůj PCF. Zadejte jedinečné alfanumerické heslo. Uložit heslo jako **klientASecret** použít později.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Zkopírujte hodnotu "appId" ve výstupu jako **vaše clientID** použít později.

    > [!NOTE]
    >
    > Zvolte vlastní domovskou stránku aplikace a\:identifikátor\.URI, například http //www contoso.com.

4. Vytvořte instanční objekt s novým ID aplikace.

    `az ad sp create --id {appId}`

5. Nastavte oprávnění vašeho instančního objektu na roli Přispěvatel.

    `az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"`

    Nebo můžete také použít

    `az role assignment create --assignee {service-principal-name} --role "Contributor"`

    ![Přiřazení role hlavního uživatele servisu](media/deploy/svc-princ.png )

6. Ověřte, zda se můžete úspěšně přihlásit k instančnímu objektu pomocí ID aplikace, hesla a ID klienta.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Vytvořte soubor JSON v následujícím formátu. Použijte **ID předplatného**, **ID klienta**, **clientID**a **clientSecret** hodnoty, které jste zkopírovali dříve. Uložte soubor.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Získání tokenu klíčové sítě

1. Zaregistrujte se nebo se přihlaste ke svému [účtu pivotal network.](https://network.pivotal.io)
2. V pravém horním rohu stránky vyberte název svého profilu. Vyberte **Upravit profil**.
3. Přejděte na konec stránky a zkopírujte hodnotu **TOKEN ROZHRANÍ API.** Tato hodnota je hodnota **tokenu pivotal sítě,** které budete později použít.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Zřídit cluster Cloud Foundry v Azure

Teď máte všechny parametry, které potřebujete k zřízení [clusteru Pivotal Cloud Foundry v Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Zadejte parametry a vytvořte cluster PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Ověřte nasazení a přihlaste se ke Správci klíčových operací

1. Cluster PCF zobrazuje stav nasazení.

    ![Stav nasazení Azure](media/deploy/deployment.png )

2. V yberte odkaz **Nasazení** v navigaci vlevo, chcete-li získat přihlašovací údaje pro správce operačních jednotek PCF. Na další stránce vyberte **název nasazení.**
3. V navigaci vlevo vyberte odkaz **Výstupy,** chcete-li zobrazit adresu URL, uživatelské jméno a heslo správce operačních operací PCF. Hodnota "OPSMAN-FQDN" je adresa URL.
 
    ![Výstup nasazení slévárna cloudu](media/deploy/deploy-outputs.png )
 
4. Spusťte adresu URL ve webovém prohlížeči. Zadejte přihlašovací údaje z předchozího kroku pro přihlášení.

    ![Klíčová přihlašovací stránka](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Pokud prohlížeč Internet Explorer selže z důvodu varovné zprávy "Web není zabezpečený", vyberte **další informace** a přejděte na webovou stránku. Ve Firefoxu vyberte **Advance** a přidejte certifikaci, abyste pokračovali.

5. Správce operačních operací PCF zobrazí nasazené instance Azure. Nyní můžete nasadit a spravovat své aplikace zde.
               
    ![Nasazená instance Azure v klíčovém](media/deploy/ops-mgr.png )
 
