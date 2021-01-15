---
title: Cloud Shell v Azure Virtual Network
description: Nasazení Cloud Shell do služby Azure Virtual Network
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 58f6c7a3b5d68d2825cead545ba1b683d1faf1af
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222798"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Nasazení Cloud Shell do služby Azure Virtual Network

Pravidelná Cloud Shell relace běží v kontejneru v síti Microsoftu oddělené od vašich prostředků. To znamená, že příkazy běžící uvnitř kontejneru nemají přístup k prostředkům, ke kterým lze přistupovat pouze z konkrétní virtuální sítě. Nemůžete třeba použít SSH k připojení z Cloud Shell k virtuálnímu počítači, který má jenom soukromou IP adresu, nebo použijte kubectl pro připojení ke clusteru Kubernetes, který má zamčený přístup. 

Tato volitelná funkce řeší tato omezení a umožňuje nasazení Cloud Shell do virtuální sítě Azure, kterou ovládáte. Odtud může kontejner pracovat s prostředky v rámci vybrané virtuální sítě.  

Níže vidíte architekturu prostředků, která se bude nasazovat a používat v tomto scénáři.

![Ukazuje Cloud Shell izolovanou architekturu virtuální sítě.](media/private-vnet/data-diagram.png)

Než budete moct použít Cloud Shell ve svém vlastním Virtual Network Azure, budete muset vytvořit několik prostředků, které tuto funkci podporují. V tomto článku se dozvíte, jak nastavit požadované prostředky pomocí šablony ARM.

> [!NOTE]
> Tyto prostředky je nutné nastavit pouze jednou pro virtuální síť. Pak je můžou sdílet všichni správci s přístupem k virtuální síti.

## <a name="required-network-resources"></a>Požadované síťové prostředky

### <a name="virtual-network"></a>Virtuální síť
Virtuální síť definuje adresní prostor, ve kterém se vytvoří jedna nebo víc podsítí.

Požadovaná virtuální síť, která se má použít pro Cloud Shell musí být identifikována. Obvykle se jedná o existující virtuální síť, která obsahuje prostředky, které chcete spravovat, nebo síť, která je v síti s sítěmi, které obsahují vaše prostředky.

### <a name="subnet"></a>Podsíť
V rámci vybrané virtuální sítě je nutné použít vyhrazenou podsíť pro kontejnery Cloud Shell. Tato podsíť je delegovaná na službu Azure Container Instances (ACI).  Když uživatel požádá o kontejner Cloud Shell ve virtuální síti, Cloud Shell používá ACI k vytvoření kontejneru, který se nachází v této delegované podsíti.  V této podsíti nelze vytvořit žádné další prostředky.

### <a name="network-profile"></a>Profil sítě
Profil sítě je šablona konfigurace sítě pro prostředky Azure, které určují určité vlastnosti sítě pro daný prostředek.

### <a name="azure-relay"></a>Azure Relay
[Azure Relay](../azure-relay/relay-what-is-it.md) umožňuje dva koncové body, které nejsou přímo dosažitelné pro komunikaci. V tomto případě se používá k tomu, aby prohlížeč správce mohl komunikovat s kontejnerem v privátní síti.

Instanci Azure Relay použitou pro Cloud Shell lze nakonfigurovat tak, aby mohla určovat, které sítě mají přístup k prostředkům kontejneru: 
- Přístup z veřejného Internetu: v této konfiguraci Cloud Shell poskytuje způsob, jak oslovit jiné interní prostředky zvenčí. 
- Dostupné ze zadaných sítí: v této konfiguraci budou mít správci přístup k Azure Portal z počítače, na kterém je spuštěná příslušná síť, aby bylo možné používat Cloud Shell.

## <a name="storage-requirements"></a>Požadavky na úložiště
Stejně jako ve standardních Cloud Shell se při použití Cloud Shell ve virtuální síti vyžaduje účet úložiště. Každý správce potřebuje sdílení souborů pro uložení svých souborů.  Účet úložiště musí být dostupný z virtuální sítě, kterou používá Cloud Shell. 

## <a name="virtual-network-deployment-limitations"></a>Omezení nasazení virtuální sítě
* Vzhledem k dalším zapojení síťových prostředků je spuštění Cloud Shell ve virtuální síti obvykle pomalejší než standardní relace Cloud Shell.

* Všechny Cloud Shell oblasti od Střed Indie jsou aktuálně podporovány. 

* [Azure Relay](../azure-relay/relay-what-is-it.md) není bezplatná služba, podívejte se prosím na jejich [ceny](https://azure.microsoft.com/pricing/details/service-bus/). Ve scénáři Cloud Shell se jedno hybridní připojení používá pro každého správce, zatímco používají Cloud Shell. Po dokončení relace Cloud Shell bude připojení automaticky ukončeno.

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

V rámci předplatného, které obsahuje virtuální síť, kterou chcete použít, musí být zaregistrován poskytovatel prostředků Microsoft. ContainerInstances. Vyberte příslušné předplatné s nástrojem `Set-AzContext -Subscription {subscriptionName}` a pak spusťte:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Pokud **RegistrationState** je `Registered` , není vyžadována žádná akce. Pokud je `NotRegistered` , spusťte příkaz `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Nasazení síťových prostředků
 
### <a name="create-a-resource-group-and-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě
Pokud už máte požadovanou virtuální síť, ke které se chcete připojit, přeskočte tuto část.

V Azure Portal nebo pomocí rozhraní příkazového řádku Azure CLI, Azure PowerShell atd. Vytvořte skupinu prostředků a virtuální síť v nové skupině prostředků, **musí být skupina prostředků a virtuální síť ve stejné oblasti**.

### <a name="arm-templates"></a>Šablony ARM
Využijte [šablonu Azure pro rychlý Start](https://aka.ms/cloudshell/docs/vnet/template) k vytvoření prostředků Cloud Shell ve virtuální síti a [šablonu pro rychlý Start Azure](https://aka.ms/cloudshell/docs/vnet/template/storage) pro vytvoření potřebného úložiště. Poznamenejte si názvy prostředků, a to hlavně podle názvu sdílené složky.

### <a name="open-relay-firewall"></a>Otevřít bránu firewall pro předávání
Přejděte do přenosu vytvořeného pomocí výše uvedené šablony, v nastavení vyberte "sítě" a povolte přístup z vaší sítě v prohlížeči ke službě Relay. Ve výchozím nastavení je přenos dostupný jenom z virtuální sítě, ve které je vytvořený. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Konfigurace Cloud Shell pro použití virtuální sítě.
> [!NOTE]
> Tento krok je nutné provést pro každého správce, který bude používat Cloud Shell.

Po nasazení výše uvedeného postupu přejděte na Cloud Shell v Azure Portal nebo na https://shell.azure.com . Jedno z těchto prostředí je nutné použít pokaždé, když se chcete připojit k izolovanému Cloud Shellmu prostředí.

> [!NOTE]
> Pokud byl v minulosti použit Cloud Shell, musí být stávající clouddrive odpojeny. Pokud chcete tento postup spustit `clouddrive unmount` z aktivní relace Cloud Shell, aktualizujte svou stránku.

Připojení k Cloud Shell se zobrazí výzva s prvním prostředím pro spuštění. Vyberte preferované prostředí prostředí, vyberte Zobrazit upřesňující nastavení a zaškrtněte políčko Zobrazit nastavení izolace virtuální sítě. Vyplňte pole v automaticky otevíraném okně.  Většina polí bude provádět automatické vyplňování dostupných prostředků, které je možné přidružit k Cloud Shell ve virtuální síti.  Název sdílené složky bude muset zadat uživatel.


![Ukazuje nastavení pro první prostředí s izolací virtuální sítě Cloud Shell.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Další kroky
[Další informace o virtuálních sítích Azure](../virtual-network/virtual-networks-overview.md)
