---
title: Avere vFXT – požadavky na Azure
description: Předpoklady pro Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 823bf50a54ff43fa95f7136c137e3d8f3303c3e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633806"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Příprava k tvorbě Avere vFXT

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
* Povolit uzlu clusteru řadiče pro správu skupin prostředků a virtuální sítě 
* Povolit řadič clusteru k vytvoření a úprava uzlů clusteru 

Pokud nechcete dát přístup vlastníka uživatele, kteří vytvářejí vFXT existují dvě alternativní řešení:

* Vlastník skupiny prostředků můžete vytvořit cluster, pokud jsou splněny tyto podmínky:

  * Vlastník předplatného musí [přijměte podmínky pro software vFXT Avere](#accept-software-terms-in-advance) a [vytvořit role clusteru uzel přístupu](avere-vfxt-deploy.md#create-the-cluster-node-access-role).
  * Všechny prostředky vFXT Avere se musí nasadit do skupiny prostředků, včetně:
    * Kontroler clusteru
    * Uzly clusteru
    * Blob Storage
    * Prvky sítě
 
* Uživatel s oprávněními bez vlastníka můžete vytvářet clustery vFXT, pokud je vytvořen a přiřazená uživateli, koordinovat předem s rolí přístup. Ale tato role poskytuje významná oprávnění pro tyto uživatele. [Tento článek](avere-vfxt-non-owner.md) vysvětluje, jak autorizovat bez vlastníků k vytvoření clusterů.

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta pro vFXT clusteru

Musíte mít dostatečnou kvótu pro následující komponenty Azure. V případě potřeby [požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Pro samotný cluster vFXT jsou virtuální počítače a komponenty SSD zde uvedené. Budete potřebovat další kvótu pro virtuální počítače a SSD, kterou chcete použít pro výpočetní farmy.  Ujistěte se, že je povolená kvótu pro oblast, ve kterém chcete spustit pracovní postup.

|Komponenta Azure|Kvóta|
|----------|-----------|
|Virtuální počítače|3 D16s_v3 nebo E32s_v3|
|SSD storage úrovně Premium|200 GB volného místa na operační systém plus 1 TB na 4 TB místa v mezipaměti na jeden uzel |
|Účet úložiště (volitelné) |v2|
|Back-endového úložiště dat (volitelné) |Jeden nový kontejner objektů Blob LRS |

## <a name="accept-software-terms-in-advance"></a>Přijměte podmínky předem softwaru

> [!NOTE] 
> Tento krok není povinný, pokud Avere vFXT cluster vytvoří vlastník předplatného.

Před vytvořením clusteru, musíte přijmout podmínky pro Avere vFXT software služby. Pokud nejste vlastníkem předplatného, jste vlastníkem předplatného, přijímáte předem. Tento krok pouze je nutné provést jednou na jedno předplatné.

Přijmout podmínky předem software: 

1. Otevřete cloud shell na webu Azure Portal nebo procházením <https://shell.azure.com>. Přihlaste se pomocí ID vašeho předplatného.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Přijměte podmínky služby a povolit programový přístup pro vFXT Avere pro Image softwaru Azure používat tento příkaz: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-node:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>Další krok: Vytvořte vFXT cluster

Po dokončení těchto nezbytných podmínkách, můžete přejít do vytváření samotného clusteru. Čtení [nasazení clusteru vFXT](avere-vfxt-deploy.md) pokyny.