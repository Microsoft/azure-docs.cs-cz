---
title: Vytvoření Cloud Foundry v Azure
description: Zjistěte, jak nastavit parametry potřebné ke zřízení clusteru Cloud Foundry (PCF) v Azure.
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
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250578"
---
# <a name="create-cloud-foundry-on-azure"></a>Vytvoření Cloud Foundry v Azure

Tento kurz poskytuje rychlé kroky k vytvoření a vygenerování parametrů potřebných ke zřízení clusteru Pivotal Cloud Foundry (PCF) v Azure.  Řešení Pivotal Cloud Foundry můžete vyhledat na webu Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Alternativní text k obrázku](media/deploy/pcf-marketplace.png "Vyhledání Pivotal Cloud Foundry v Azure")


## <a name="generate-an-ssh-public-key"></a>Vygenerování veřejného klíče SSH

V systémech Windows, Mac a Linux můžete veřejný klíč SSH vygenerovat několika způsoby.

```Bash
ssh-keygen -t rsa -b 2048
```
- Kliknutím sem zobrazíte [pokyny]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) pro vaše prostředí.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

> [!NOTE]
>
> K vytvoření instančního objektu se vyžaduje oprávnění vlastníka účtu.  Navíc můžete napsat skript, který vytvoření instančního objektu automatizuje. Například pomocí příkazu [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) v Azure CLI.

1. Přihlaste se ke svému účtu Azure.

    `az login`

    ![Alternativní text k obrázku](media/deploy/az-login-output.png "Přihlášení k Azure CLI")
 
    Zkopírujte hodnotu id jako vaše **ID předplatného** a hodnotu **ID tenanta** pro pozdější použití.

2. Nastavte své výchozí předplatné pro tuto konfiguraci.

    `az account set -s {id}`

3. Vytvořte pro svůj cluster PCF aplikaci AAD a zadejte jedinečné alfanumerické heslo.  Uložte heslo jako váš **tajný klíč klienta** pro pozdější použití.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Zkopírujte hodnotu appId ve výstupu jako vaše **ID klienta** pro pozdější použití.

    > [!NOTE]
    >
    > Zvolte vlastní identifikátor URI a domovskou stránku aplikace.  Například http://www.contoso.com.

4. Vytvořte instanční objekt s vaším novým ID aplikace.

    `az ad sp create --id {appId}`

5. Nastavte oprávnění vašeho instančního objektu na roli **Přispěvatel**.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Můžete použít také tento příkaz:

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![Alternativní text k obrázku](media/deploy/svc-princ.png "Přiřazení role instančního objektu")

6. Ověřte, že se můžete úspěšně přihlásit ke svému instančnímu objektu pomocí ID aplikace, hesla a ID tenanta.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. Vytvořte soubor .json v následujícím formátu s použitím hodnot **ID předplatného**, **ID tenanta**, **ID klienta** a **tajného klíče klienta**, které jste si zkopírovali výše.  Uložte soubor.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Získání tokenu Pivotal Network

1. Zaregistrujte se nebo se přihlaste ke svému účtu [Pivotal Network](https://network.pivotal.io).
2. V pravém horním rohu stránky klikněte na název vašeho profilu a pak vyberte Edit Profile (Upravit profil).
3. Přejděte do dolní části stránky a zkopírujte hodnotu **LEGACY API TOKEN** (STARŠÍ VERZE TOKENU API).  Toto je hodnota vašeho **tokenu Pivotal Network**, kterou použijete později.

## <a name="provision-your-cloud-foundry-on-azure"></a>Zřízení Cloud Foundry v Azure

1. Teď máte všechny parametry, které potřebujete ke zřízení clusteru [Pivotal Cloud Foundry v Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
2. Zadejte parametry a vytvořte cluster PCF.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>Ověření nasazení a přihlášení k Pivotal Ops Manageru

1. U vašeho clusteru PCF by se měl zobrazovat stav nasazení.

    ![Alternativní text k obrázku](media/deploy/deployment.png "Stav nasazení Azure")

2. V levém navigačním panelu klikněte na odkaz **Nasazení** a získejte přihlašovací údaje k PCF Ops Manageru. Na další stránce pak klikněte na **Název nasazení**.
3. Kliknutím na odkaz **Výstupy** v levém navigačním panelu zobrazte adresu URL, uživatelské jméno a heslo pro PCF Ops Managera.  Adresou URL je hodnota OPSMAN-FQDN.
 
    ![Alternativní text k obrázku](media/deploy/deploy-outputs.png "Výstup nasazení Cloud Foundry")
 
4. Otevřete adresu URL ve webovém prohlížeči a přihlaste se zadáním přihlašovacích údajů z předchozího kroku.

    ![Alternativní text k obrázku](media/deploy/pivotal-login.png "Přihlašovací stránka Pivotal")
         
    > [!NOTE]
    >
    > Pokud prohlížeč Internet Explorer selže kvůli zprávě upozornění Tento web není zabezpečený, klikněte na Další informace a pak na Pokračovat na web.  V případě prohlížeče Firefox klikněte na Rozšířené a přidejte certifikaci, abyste mohli pokračovat.

5. V PCF Ops Manageru by se měly zobrazit nasazené instance Azure. Teď odsud můžete začít nasazovat a spravovat své aplikace.
               
    ![Alternativní text k obrázku](media/deploy/ops-mgr.png "Nasazené instance Azure v Pivotal")
 
