---
title: Vytvoření kontroleru dat
description: Vytvořte řadič dat ARC Azure na typickém clusteru s více uzly Kubernetes, který jste už nasadili.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a78c67892e2bccb7c98d680539a84c2cc1f49a95
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372289"
---
# <a name="create-the-azure-arc-data-controller"></a>Vytvoření kontroleru dat ARC Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Přehled vytvoření kontroleru dat ARC Azure

Datové služby s podporou ARC Azure je možné vytvářet na několika různých typech clusterů Kubernetes a spravovaných Kubernetes služeb pomocí několika různých přístupů.

V současné době podporuje seznam Kubernetes služeb a distribucí následující:

- Azure Kubernetes Service (AKS)
- Stroj služby Azure Kubernetes (AKS Engine) na Azure Stack
- Služba Azure Kubernetes na Azure Stack HCL
- Azure RedHat OpenShift (ARO)
- OpenShift Container Platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Modul GKE (Google Cloud Kubernetes Engine)
- Open source upstreamové prostředí Kubernetes obvykle nasazené s využitím nástroje kubeadm

> [!IMPORTANT]
> * Minimální podporovaná verze Kubernetes je v 1.14.
> * Podívejte se na [požadavky na připojení](connectivity.md) a zjistěte, co je mezi vaším prostředím a Azure vyžadováno připojení.
> * Podrobné informace o tom, jak nakonfigurovat trvalé úložiště, najdete v tématu [pokyny ke konfiguraci úložiště](storage-configuration.md) .
> * Pokud používáte službu Azure Kubernetes, měla by být velikost virtuálního počítače pracovního uzlu clusteru aspoň **Standard_D8s_v3** a používat **prémiové disky.** Cluster by neměl zahrnovat více zón dostupnosti. 
> * Pokud používáte jinou Kubernetes distribuci nebo službu, měli byste zajistit, abyste měli minimální velikost uzlů 8 GB paměti RAM a 4 jádra a celkovou kapacitu 32 GB paměti RAM dostupnou ve všech uzlech Kubernetes. Například můžete mít 1 uzel v 32 GB paměti RAM a 4 jádra nebo máte 2 uzly s 16GB pamětí RAM a 4 jádry.

> [!NOTE]
> Pokud používáte platformu Red Hat OpenShift Container Platform v Azure, doporučuje se použít nejnovější dostupnou verzi.

V závislosti na zvolené možnosti se budou _vyžadovat_některé nástroje, ale před zahájením vytváření kontroleru dat ARC Azure se doporučuje [nainstalovat všechny nástroje klienta](./install-client-tools.md) .

Bez ohledu na to, kterou možnost zvolíte, budete muset během procesu vytváření zadat následující informace:

- **Název kontroleru dat** – popisný název vašeho kontroleru dat – například "řadič provozních dat", "Seattle data Controller".
- **Uživatelské jméno kontroleru dat** – jakékoli uživatelské jméno pro uživatele správce kontroleru dat.
- **Heslo ke službě data Controller** – heslo pro uživatele správce kontroleru dat.
- **Název oboru názvů Kubernetes** – název oboru názvů Kubernetes, ve kterém chcete vytvořit řadič dat.
- **Režim připojení** – [režim připojení](./connectivity.md) vašeho clusteru. V současné době je podporována pouze "nepřímá".
- **ID předplatného Azure** – identifikátor GUID předplatného Azure, kde chcete vytvořit prostředek řadiče dat v Azure.
- **Název skupiny prostředků Azure** – název skupiny prostředků, ve které chcete vytvořit prostředek řadiče dat v Azure.
- **Umístění Azure** – umístění Azure, kde se metadata prostředku řadiče dat uloží do Azure. Seznam oblastí, které jsou k dispozici, najdete v tématu [globální infrastruktura/produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Další kroky

K dispozici je několik možností pro vytvoření kontroleru dat ARC Azure:

> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure!
> 
- [Vytvoření kontroleru dat pomocí [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Vytvoření kontroleru dat pomocí Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Z Azure Portal pomocí poznámkového bloku Jupyter v Azure Data Studio vytvořit řadič dat](create-data-controller-resource-in-azure-portal.md)
- [Vytvoření kontroleru dat s Kubernetes nástroji, jako je kubectl nebo oC](create-data-controller-using-kubernetes-native-tools.md)
- [Vytvoření kontroleru dat pomocí Azure ARC rychlé zprovoznění pro urychlené prostředí testovacího nasazení](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)
