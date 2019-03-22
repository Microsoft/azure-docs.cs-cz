---
title: Avere vFXT – požadavky na Azure
description: Předpoklady pro Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 5642f3acd108d0d3f504fc132522936d1b5ab870
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082581"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Příprava k vytvoření Avere vFXT

Tento článek vysvětluje požadované úkoly pro vytváření Avere vFXT clusteru.

## <a name="create-a-new-subscription"></a>Vytvořte nové předplatné

Začněte vytvořením nového předplatného Azure. Použití samostatné předplatné pro každý projekt vFXT Avere vám umožní snadno sledovat všechny prostředky projektu a výdaje, chránit jiné projekty z možných omezování během zřizování prostředků a zjednodušení vyčištění.  

Vytvoření nového předplatného Azure na webu Azure Portal:

* Přejděte [okně předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Klikněte na tlačítko **+ přidat** tlačítko v horní části
* Přihlášení po zobrazení výzvy
* Vyberte nabídky a projdete si kroky k vytvoření nového předplatného

## <a name="configure-subscription-owner-permissions"></a>Konfigurace předplatného oprávnění vlastníka

Uživatel s oprávněními vlastníka předplatného by měl vytvořit vFXT cluster. Oprávnění vlastníka předplatného jsou potřeba pro tyto akce, mimo jiné:

* Přijměte podmínky pro Avere vFXT software
* Umožňuje vytvořit roli přístup k uzlu clusteru 

Pokud nechcete dát přístup vlastníka uživatele, kteří vytvářejí vFXT existují dvě alternativní řešení:

* Vlastník skupiny prostředků můžete vytvořit cluster, pokud jsou splněny tyto podmínky:

  * Vlastník předplatného musí [přijměte podmínky pro software vFXT Avere](#accept-software-terms) a [vytvořit role clusteru uzel přístupu](#create-the-cluster-node-access-role). 
  * Všechny prostředky vFXT Avere se musí nasadit do skupiny prostředků, včetně:
    * Kontroler clusteru
    * Uzly clusteru
    * Blob Storage
    * Prvky sítě
 
* Uživatel s oprávněními bez vlastníka můžete vytvořit clustery vFXT pomocí řízení přístupu na základě rolí (RBAC) předem přiřadit oprávnění uživateli. Tato metoda poskytuje významná oprávnění pro tyto uživatele. [Tento článek](avere-vfxt-non-owner.md) vysvětluje, jak vytvořit roli přístup k autorizaci bez vlastníků k vytvoření clusterů.

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta pro vFXT clusteru

Musíte mít dostatečnou kvótu pro následující komponenty Azure. V případě potřeby [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Pro samotný cluster vFXT jsou virtuální počítače a komponenty SSD zde uvedené. Budete potřebovat další kvótu pro virtuální počítače a SSD, kterou chcete použít pro výpočetní farmy.  Ujistěte se, že je povolená kvótu pro oblast, ve kterém chcete spustit pracovní postup.

|Komponenta Azure|Kvóta|
|----------|-----------|
|Virtuální počítače|3 nebo více E32s_v3|
|Úložiště SSD úrovně Premium|200 GB místa na operační systém a 1 až 4 TB místa v mezipaměti na uzel |
|Účet úložiště (volitelné) |v2|
|Back-endové úložiště dat (volitelné) |Jeden nový kontejner objektů Blob LRS |

## <a name="accept-software-terms"></a>Přijměte podmínky pro software

> [!NOTE] 
> Tento krok není povinný, pokud Avere vFXT cluster vytvoří vlastník předplatného.

Při vytváření clusteru musíte přijmout podmínky pro Avere vFXT software služby. Pokud nejste vlastníkem předplatného, jste vlastníkem předplatného, přijímáte předem. Tento krok pouze je nutné provést jednou na jedno předplatné.

Přijmout podmínky předem software: 

1. Otevřete cloud shell na webu Azure Portal nebo procházením <https://shell.azure.com>. Přihlaste se pomocí ID vašeho předplatného.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Přijměte podmínky služby a povolit programový přístup pro vFXT Avere pro image Azure softwaru používat tento příkaz: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-access-roles"></a>Vytvoření role zabezpečeného přístupu 

[Řízení přístupu na základě rolí](../role-based-access-control/index.yml) (RBAC) poskytuje vFXT clusteru kontroleru a clusterovým uzlem oprávnění k provedení nezbytných úloh.

* Kontroler clusteru potřebuje oprávnění k vytvoření a úprava virtuální počítače k vytvoření clusteru. 

* Jednotlivé vFXT uzly se muset zabývat záležitostmi, jako je čtení vlastností prostředků Azure, spravovat úložiště a řídit ostatní uzly nastavení síťového rozhraní jako součást operace normální clusteru.

Před vytvořením clusteru vFXT Avere, je nutné definovat vlastní role pro použití s uzly clusteru. 

Pro kontroler clusteru můžete přijmout výchozí role ze šablony. Výchozí hodnota poskytuje clusteru oprávnění vlastníka skupiny prostředků kontroleru. Pokud budete chtít vytvořit vlastní roli pro kontroler, přečtěte si téma [vlastní kontroler přístup role](avere-vfxt-controller-role.md).

> [!NOTE] 
> Pouze vlastník předplatného, nebo uživatel s rolí vlastník nebo správce přístupu uživatelů, mohou vytvářet role. Role je možné vytvořit předem.  

### <a name="create-the-cluster-node-access-role"></a>Umožňuje vytvořit roli přístup k uzlu clusteru

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Než budete moct vytvořit vFXT Avere pro Azure cluster, je nutné vytvořit roli uzlu clusteru.

> [!TIP] 
> Interní uživatele Microsoftu by měl použít existující roli s názvem "Operátor modulu Runtime clusteru Avere" namísto pokusu o k jejímu vytvoření. 

1. Zkopírujte tento soubor. Přidejte své ID předplatného v řádku AssignableScopes.

   (Aktuální verze tohoto souboru je uložen v úložišti github.com/Azure/Avere jako [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt).)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. Uložte soubor jako ``avere-operator.json`` nebo podobný soubor snadno zapamatovatelné jméno. 


1. Otevřete Azure Cloud shell a přihlaste se pod svým ID předplatného (popsané [výše v tomto dokumentu](#accept-software-terms)). Použijte tento příkaz pro vytvoření role:

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

Název role se používá při vytváření clusteru. V tomto příkladu je název ``avere-operator``.

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Vytvoření koncového bodu služby úložiště ve vaší virtuální síti (v případě potřeby)

A [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) udržuje objektů Blob v Azure provoz místní namísto směrování mimo virtuální síť. Doporučuje se pro všechny Avere vFXT pro cluster Azure, která využívá back endovým datům úložiště objektů Blob v Azure. 

Pokud poskytujete existující virtuální síť a vytvořili nový kontejner objektů Blob v Azure pro váš back endové úložné jako součást vytváření clusteru, musí mít koncový bod služby ve virtuální síti pro úložiště od Microsoftu. Tento koncový bod, musí existovat před vytvořením clusteru nebo vytváření se nezdaří. 

Koncový bod služby storage se doporučuje pro všechny Avere vFXT pro cluster Azure, která používá úložiště objektů Blob v Azure i v případě, že přidáte úložiště později. 

> [!TIP] 
> * Tento krok přeskočte, pokud vytváříte nové virtuální sítě jako součást vytváření clusteru. 
> * Tento krok je volitelný, pokud nevytváříte úložiště objektů Blob při vytváření clusteru. V takovém případě můžete vytvořit koncový bod služby později Pokud se rozhodnete používat Azure Blob.

Vytvoření koncového bodu služby úložiště na webu Azure Portal. 

1. Z portálu, klikněte na tlačítko **virtuální sítě** na levé straně.
1. Vyberte virtuální síť pro cluster. 
1. Klikněte na tlačítko **koncové body služby** na levé straně.
1. Klikněte na tlačítko **přidat** v horní části.
1. Nechte službu jako ``Microsoft.Storage`` a zvolte podsíť clusteru.
1. V dolní části, klikněte na tlačítko **přidat**.

   ![Azure portal – snímek obrazovky s poznámkami postup vytvoření koncového bodu služby](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Další krok: Vytvoření clusteru vFXT

Po dokončení těchto nezbytných podmínkách, můžete přejít do vytváření samotného clusteru. Čtení [nasazení clusteru vFXT](avere-vfxt-deploy.md) pokyny.