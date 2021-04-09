---
title: Rychlé zprovoznění – WildFly v CentOS
description: Nasazení aplikací Java pro WildFly na virtuálním počítači s CentOS
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: 533d2881688598430ca05e964595352edf993dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675998"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Rychlý Start: WildFly na CentOS 8

V tomto rychlém startu se dozvíte, jak nasadit samostatný uzel WildFly virtuálního počítače CentOS 8. Je ideální pro vývoj a testování podnikových aplikací v jazyce Java v Azure. K nasazení tohoto rychlého startu není předplatné aplikačního serveru nutné.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. Pokud nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si [účet zdarma vytvořit](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Případ použití

WildFly je ideální pro vývoj a testování podnikových aplikací v jazyce Java v Azure. V [průvodci Začínáme WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)jsou k dispozici seznam technologií dostupných v konfiguračních profilech serveru WildFly 18.

WildFly můžete použít buď samostatně, nebo v režimu clusteru na základě vašeho případu použití. V clusteru uzlů můžete zajistit vysokou dostupnost kritických aplikací Jakarta EE pomocí WildFly. Vytvořte malý počet změn konfigurace aplikace a pak v clusteru Nasaďte aplikaci. Další informace najdete v [Průvodci vysokou dostupností WildFly](https://docs.wildfly.org/18/High_Availability_Guide.html).

## <a name="configuration-choice"></a>Volba konfigurace

WildFly je možné spustit v **samostatném režimu serveru** – samostatná instance serveru je nezávislý proces, podobně jako JBoss aplikační server (as) 3, 4, 5 nebo 6 instance. Samostatné instance lze spustit pomocí standalone.sh nebo skriptů spouštěných na standalone.bat. Pro více než jednu samostatnou instanci je úkolem uživatele koordinovat správu více serverů napříč servery.

Můžete také spustit instanci WildFly s alternativní konfigurací pomocí konfiguračních souborů dostupných v konfigurační složce.

Následující jsou soubory konfigurace samostatného serveru:

- standalone.xml (výchozí) – Tato konfigurace je výchozím souborem použitým pro spuštění instance WildFly. Obsahuje konfiguraci s certifikací webového profilu s požadovanými technologiemi.
   
- standalone-ha.xml – Jakarta EE webový profil 8 Certified konfigurace s vysokou dostupností (cílený na webové aplikace).
   
- standalone-full.xml-Jakarta EE Platform 8 s certifikací konfigurace, včetně všech požadovaných technologií pro hostování aplikací Jakarta EE.

- standalone-full-ha.xml-Jakarta EE Platform 8 s certifikací konfigurace s vysokou dostupností pro hostování aplikací Jakarta EE.

K zahájení samostatného serveru WildFly s jinou poskytnutou konfigurací použijte argument--Server-config se souborem Server-config.

Chcete-li například použít platformu Jakarta EE Platform 8 s možnostmi clusteringu, použijte následující příkaz:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Další informace o konfiguracích najdete v [příručce WildFly Začínáme](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Poznámky k licencování, podpoře a předplatnému

Image Azure CentOS 8 je image virtuálního počítače s průběžnými platbami (PAYG), která nevyžaduje, aby uživatel získal licenci. Při prvním spuštění virtuálního počítače se licence na operační systém virtuálního počítače automaticky aktivuje a účtuje se hodinovou sazbou. Toto je více než hodinová sazba za počítač společnosti Microsoft pro Linux. Podrobnosti získáte kliknutím na [ceny za virtuální počítače se systémem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) . WildFly je zdarma ke stažení a používání a nevyžaduje předplatné nebo licenci Red Hat.

## <a name="how-to-consume"></a>Jak spotřebovávat

Šablonu můžete nasadit následujícími třemi způsoby:

- Použití PowerShellu – nasaďte šablonu spuštěním následujících příkazů: (informace o instalaci a konfiguraci Azure PowerShell najdete v [Azure PowerShell](/powershell/azure/) ).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Použití rozhraní příkazového řádku Azure – Nasaďte tuto šablonu spuštěním následujících příkazů: (podrobnosti o instalaci a konfiguraci Command-Line rozhraní Azure pro víc platforem najdete v části [příkazový řádek Azure pro více platforem](/cli/azure/install-azure-cli) ).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Použijte Azure Portal – nasaďte šablonu kliknutím <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">sem</a> a přihlaste se k Azure Portal.

## <a name="arm-template"></a>Šablona ARM

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 v CentOS 8 (samostatný virtuální počítač)</a> – jedná se o šablonu pro rychlý Start, která vytvoří samostatný uzel WildFly 18.0.1. finální na CentOS 8 VM ve vaší skupině prostředků (RG), který zahrnuje privátní IP adresu pro virtuální počítač, Virtual Network a účet úložiště diagnostiky. Nasadí také ukázkovou aplikaci Java s názvem JBoss-EAP v Azure v WildFly.

## <a name="resource-links"></a>Odkazy na prostředky

* Další informace o [WildFly 18](https://docs.wildfly.org/18/)
* Další informace o [distribucích systému Linux v Azure](../../linux/endorsed-distros.md)
* [Dokumentace k Azure pro vývojáře v jazyce Java](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Další kroky

V produkčním prostředí se můžete podívat na šablony standardu JBoss pro ověřování pomocí protokolu EAP v Red Hat.

Samostatný RHEL virtuální počítač s ukázkovou aplikací:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP na RHEL (samostatný virtuální počítač)</a>

Clusterované virtuální počítače s RHEL s ukázkovou aplikací:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP na RHEL (clusterové virtuální počítače)</a>

Clusterovaná sada škálování virtuálních počítačů s RHEL s využitím ukázkové aplikace:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP v RHEL (clusterovaná sada škálování virtuálního počítače)</a>